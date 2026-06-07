# Padrões de Projeto 3 - Comportamentais

## 1. O que une os padrões comportamentais?

Padrões comportamentais respondem à pergunta:

> *Como organizar a colaboração entre objetos e a distribuição de responsabilidades, mantendo o sistema flexível?*
> 

Eles tratam de questões como:

- **Quem decide o quê?** Quem invoca a operação, quem a executa, quem decide quando.
- **Como variar comportamento sem variar estrutura?**
- **Como tornar fluxos de controle complexos legíveis e modificáveis?**

Você já viu três padrões comportamentais em aulas anteriores: **Strategy** (algoritmos intercambiáveis) e **Observer** (notificação um-para-muitos). Esta aula completa o quadro com mais quatro.

> 💡 **Intuição:** Se padrões estruturais são "como as peças se encaixam", padrões comportamentais são "como as peças conversam". Comportamentais aparecem onde há fluxo, decisão, sequenciamento — temas centrais de qualquer programa não-trivial.
> 

---

## 2. Command (Comando)

### 2.1 Intenção

> Encapsular uma solicitação como um objeto, permitindo parametrizar clientes com diferentes solicitações, enfileirar ou registrar solicitações, e suportar operações que podem ser desfeitas.
> 

### 2.2 Motivação

Considere o **painel administrativo** do nosso e-commerce. Operadores executam ações como: cancelar pedido, estornar pagamento, alterar endereço de entrega, alterar quantidade de um item. Para cada ação, o sistema precisa:

- **Auditar** quem fez o quê e quando (compliance).
- **Permitir desfazer** (`Ctrl+Z`) — operadores são humanos e cometem erros.
- **Verificar permissões** (apenas gerentes podem estornar acima de R$ 1.000).
- **Enfileirar para execução assíncrona** quando convém (ex.: ações em lote noturno).

Solução ingênua: cada botão chama diretamente o método correspondente. Resultado: auditoria espalhada, *undo* impossível, permissões duplicadas em vinte lugares.

A ideia genial do Command é simples: **transforme cada ação em um objeto**. Aí ela passa a ter as propriedades de qualquer outro objeto — pode ser passada como parâmetro, armazenada em uma lista, registrada em log, desfeita por um método irmão.

### 2.3 Estrutura

```
   ┌──────────────┐     ┌─────────────────────┐
   │  Invoker     │────▶│ <<interface>>       │
   │ (Painel,     │     │ Comando             │
   │  Histórico)  │     ├─────────────────────┤
   └──────────────┘     │ + executar()        │
                        │ + desfazer()        │
                        └─────────────────────┘
                                  △
                          ┌───────┴────────────────┐
                          │                        │
              ┌────────────────────────┐ ┌────────────────────────┐
              │ CancelarPedidoCommand  │ │ EstornarPagtoCommand   │
              │ (ConcreteCommand)      │ │ (ConcreteCommand)      │
              ├────────────────────────┤ ├────────────────────────┤
              │ - pedido: Pedido       │ │ - pagto: Pagamento     │
              │ - estadoAnterior       │ │ - valor: double        │
              │ + executar()           │ │ + executar()           │
              │ + desfazer()           │ │ + desfazer()           │
              └────────────────────────┘ └────────────────────────┘
                          │                        │
                          ▼                        ▼
                    ┌──────────┐              ┌──────────────┐
                    │ Pedido   │              │ Pagamento    │
                    │(Receiver)│              │ (Receiver)   │
                    └──────────┘              └──────────────┘
```

Os participantes:

- **Comando**: a interface comum (com `executar` e — opcionalmente — `desfazer`).
- **ConcreteCommand**: implementa cada ação específica. Carrega referências para o *receiver* e os parâmetros necessários.
- **Receiver**: o objeto que sabe efetivamente realizar a operação (no nosso caso, `Pedido`, `Pagamento`).
- **Invoker**: quem dispara o comando (botão na interface, escalonador de tarefas, histórico de *undo*).
- **Cliente**: cria o ConcreteCommand e o entrega ao Invoker.

### 2.4 Código

**Interface:**

```java
public interface Comando {
    void executar();
    void desfazer();
    String getDescricao();  // útil para auditoria e logs
}
```

**Comando concreto — cancelar pedido:**

```java
public class CancelarPedidoCommand implements Comando {
    private final Pedido pedido;
    private EstadoPedido estadoAnterior;  // para desfazer

    public CancelarPedidoCommand(Pedido pedido) {
        this.pedido = pedido;
    }

    @Override
    public void executar() {
        // Salva o estado anterior antes de modificar
        this.estadoAnterior = pedido.getEstado();
        pedido.cancelar();
    }

    @Override
    public void desfazer() {
        pedido.restaurarEstado(estadoAnterior);
    }

    @Override
    public String getDescricao() {
        return "Cancelar pedido #" + pedido.getId();
    }
}
```

**Invoker — histórico de comandos:**

```java
public class HistoricoComandos {
    private final Deque<Comando> pilhaUndo = new ArrayDeque<>();
    private final Deque<Comando> pilhaRedo = new ArrayDeque<>();

    public void executar(Comando c) {
        c.executar();
        pilhaUndo.push(c);
        pilhaRedo.clear();  // nova ação invalida redo
        log("EXECUTOU: " + c.getDescricao());
    }

    public void desfazer() {
        if (pilhaUndo.isEmpty()) return;
        Comando c = pilhaUndo.pop();
        c.desfazer();
        pilhaRedo.push(c);
        log("DESFEZ: " + c.getDescricao());
    }

    public void refazer() {
        if (pilhaRedo.isEmpty()) return;
        Comando c = pilhaRedo.pop();
        c.executar();
        pilhaUndo.push(c);
        log("REFEZ: " + c.getDescricao());
    }

    private void log(String msg) { /* registra em arquivo/banco */ }
}
```

**Uso pelo painel:**

```java
HistoricoComandos historico = new HistoricoComandos();

// Operador clica em "Cancelar Pedido"
historico.executar(new CancelarPedidoCommand(pedido));

// Operador clica em Ctrl+Z
historico.desfazer();
```

### 2.5 Consequências

**Benefícios:**

- **Desacoplamento total** entre quem invoca (botão) e quem executa (`Pedido`). O botão não precisa saber sobre `Pedido` — só sobre `Comando`.
- **Histórico, undo/redo, log de auditoria, fila de execução** ficam triviais — todos manipulam `Comando` genericamente.
- **Macros**: um composite de comandos é... outro Command (boa hora para reler Composite).
- **Transações**: se um comando falha, todos os já executados podem ser desfeitos.

**Custos:**

- **Inflação de classes**: cada ação é uma classe nova. Em sistemas com 50 ações, são 50 classes.
- **Lambdas absorvem boa parte do padrão**: em Java 8+, `Runnable` ou `Consumer<T>` resolvem o caso simples (sem `desfazer`) com uma linha de código. A versão completa (com undo) ainda exige classes.

> 💡 **Intuição:** Pense em Command como o substantivo "ação" tornado objeto. Em vez de um verbo (`pedido.cancelar()`), você tem um substantivo (`new CancelarPedidoCommand(pedido)`) — que pode ser colocado em uma lista, mandado para outra thread, registrado em arquivo. Tudo o que se pode fazer com substantivos, pode-se fazer com ações comandificadas.
> 

---

## 3. Template Method (Método Modelo)

### 3.1 Intenção

> Definir o esqueleto de um algoritmo em uma operação, postergando alguns passos para subclasses. Template Method permite que subclasses redefinam certos passos de um algoritmo sem mudar sua estrutura.
> 

### 3.2 Motivação

No e-commerce, o **processamento de um pedido finalizado** segue um esqueleto fixo:

1. Validar o pedido.
2. Calcular impostos.
3. Aplicar descontos.
4. Cobrar o pagamento.
5. Notificar o cliente.

Esse esqueleto é universal — vale para pedidos no Brasil, EUA ou Japão. Mas **alguns passos variam** conforme o país: o cálculo de impostos no Brasil envolve ICMS, no EUA envolve *sales tax* municipal, no Japão envolve consumption tax.

A solução: definir o esqueleto em uma classe abstrata e deixar os passos variáveis como métodos abstratos a serem implementados por subclasses.

### 3.3 Estrutura

```
       ┌────────────────────────────────────┐
       │  ProcessadorPedido  {abstract}     │
       ├────────────────────────────────────┤
       │ + processar()  {final}             │  ← Template Method (não sobrescrever)
       │ # validar()                        │
       │ # calcularImpostos() {abstract}    │  ← passo variável
       │ # aplicarDescontos() {abstract}    │  ← passo variável
       │ # cobrar()                         │
       │ # notificar()                      │
       └────────────────────────────────────┘
                          △
              ┌───────────┴───────────┐
              │                       │
   ┌──────────────────────┐ ┌──────────────────────┐
   │ ProcessadorBrasil    │ │ ProcessadorEUA       │
   ├──────────────────────┤ ├──────────────────────┤
   │ # calcularImpostos() │ │ # calcularImpostos() │
   │ # aplicarDescontos() │ │ # aplicarDescontos() │
   └──────────────────────┘ └──────────────────────┘
```

### 3.4 Código

**Classe abstrata com o template method:**

```java
public abstract class ProcessadorPedido {

    // O Template Method é final: define a ordem fixa do algoritmo
    public final void processar(Pedido pedido) {
        validar(pedido);
        double impostos = calcularImpostos(pedido);    // varia
        double desconto = aplicarDescontos(pedido);    // varia
        double total = pedido.getSubtotal() + impostos - desconto;
        cobrar(pedido, total);
        notificar(pedido);
    }

    // Passo padrão: comum a todos
    protected void validar(Pedido p) {
        if (p.getItens().isEmpty()) {
            throw new PedidoInvalidoException("Pedido vazio");
        }
    }

    // Passos variáveis: cada subclasse decide
    protected abstract double calcularImpostos(Pedido p);
    protected abstract double aplicarDescontos(Pedido p);

    // Passos comuns
    protected void cobrar(Pedido p, double total) {
        p.getFormaPagamento().cobrar(total);
    }

    protected void notificar(Pedido p) {
        p.marcarComoPago();  // dispara observers (lembra deles?)
    }
}
```

**Subclasse para o Brasil:**

```java
public class ProcessadorBrasil extends ProcessadorPedido {

    @Override
    protected double calcularImpostos(Pedido p) {
        // ICMS varia por estado de origem/destino — simplificação:
        return p.getSubtotal() * 0.18;
    }

    @Override
    protected double aplicarDescontos(Pedido p) {
        // No Brasil, descontos para pagamento à vista (Pix/boleto)
        if (p.getFormaPagamento() instanceof PagamentoPix) {
            return p.getSubtotal() * 0.05;
        }
        return 0;
    }
}
```

**Subclasse para os EUA:**

```java
public class ProcessadorEUA extends ProcessadorPedido {

    @Override
    protected double calcularImpostos(Pedido p) {
        // Sales tax depende do estado — simplificação:
        return p.getSubtotal() * 0.0875;
    }

    @Override
    protected double aplicarDescontos(Pedido p) {
        // Nos EUA, costuma haver descontos por volume
        if (p.getItens().size() > 10) {
            return p.getSubtotal() * 0.1;
        }
        return 0;
    }
}
```

**Uso:**

```java
ProcessadorPedido processador = new ProcessadorBrasil();
processador.processar(pedidoDoCliente);
```

### 3.5 *Hooks*: pontos de extensão opcional

Uma variação útil é definir, na classe abstrata, métodos vazios (não abstratos) que subclasses *podem* sobrescrever, mas não precisam. São os chamados **hooks**.

```java
public abstract class ProcessadorPedido {
    public final void processar(Pedido p) {
        validar(p);
        // ...
        antesDeNotificar(p);  // hook opcional
        notificar(p);
        depoisDeNotificar(p); // hook opcional
    }

    // Hooks com implementação vazia
    protected void antesDeNotificar(Pedido p) { /* override se quiser */ }
    protected void depoisDeNotificar(Pedido p) { /* override se quiser */ }
}
```

Hooks são onipresentes em frameworks: Spring, Hibernate, JUnit (`@Before`, `@After`), Android (`onCreate`, `onResume`, `onDestroy`).

### 3.6 Template Method vs Strategy — duas formas de variar

Os dois padrões resolvem problemas parecidos: *como variar parte de um algoritmo*. Mas usam mecanismos diferentes:

| Aspecto | Template Method | Strategy |
| --- | --- | --- |
| **Mecanismo** | Herança (padrão de classe) | Composição (padrão de objeto) |
| **Quando varia?** | Em compilação (qual subclasse) | Em execução (qual instância) |
| **Granularidade** | Passos *dentro* de um algoritmo | O algoritmo *inteiro* |
| **Vínculo** | Forte (subclasse vinculada à hierarquia) | Fraco (qualquer objeto que implemente a interface) |
| **Múltiplas variações?** | Difícil (Java não tem herança múltipla) | Fácil (componha quantas estratégias quiser) |

> 💡 **Intuição:** Use Template Method quando você tem um *workflow estável* com poucos pontos de variação previsíveis e há claramente "um pai e seus filhos". Use Strategy quando o algoritmo varia *como um todo* e o cliente quer escolher qual usar. Quando em dúvida, prefira Strategy — composição é mais flexível, e linguagens modernas reforçam essa preferência.
> 

### 3.7 Consequências

**Benefícios:**

- **Reuso de código** ao máximo: o esqueleto fica em um lugar só.
- **Inversão de controle**: a classe abstrata chama os métodos da subclasse — não o contrário. É o "Princípio de Hollywood": *Don't call us, we'll call you*. É a base de muitos frameworks.
- **Garantia de ordem**: o `final` no template method impede que subclasses bagunçem a sequência.

**Custos:**

- **Acoplamento por herança**: subclasses dependem fortemente da classe pai. Mudanças no esqueleto podem quebrar todas as subclasses.
- **Difícil de testar**: você não testa o template method diretamente; testa cada subclasse, e os testes acabam exercitando muito código compartilhado.
- **Não composável em tempo de execução**: para mudar o comportamento, você muda a classe — não basta trocar uma instância.

---

## 4. State (Estado)

### 4.1 Intenção

> Permitir que um objeto altere seu comportamento quando seu estado interno muda. O objeto parecerá ter mudado de classe.
> 

### 4.2 Motivação — e a conexão com diagramas de estados

Lembra da aula sobre diagramas comportamentais? Modelamos o ciclo de vida do `Pedido` assim:

```
   ●──▶ Pendente ──pagar()──▶ Pago ──enviar()──▶ Enviado ──confirmar()──▶ Entregue
            │                  │
            │ cancelar()       │ cancelar()
            ▼                  ▼
        Cancelado          Cancelado
```

Aquele diagrama era a *especificação*. Como **implementá-lo** em código? Versão ingênua:

```java
public class Pedido {
    private String estado;  // "PENDENTE", "PAGO", "ENVIADO", "ENTREGUE", "CANCELADO"

    public void pagar() {
        if (estado.equals("PENDENTE")) {
            estado = "PAGO";
        } else {
            throw new EstadoInvalidoException("Não pode pagar em " + estado);
        }
    }

    public void enviar() {
        if (estado.equals("PAGO")) {
            estado = "ENVIADO";
        } else {
            throw new EstadoInvalidoException("Não pode enviar em " + estado);
        }
    }

    public void cancelar() {
        if (estado.equals("PENDENTE") || estado.equals("PAGO")) {
            estado = "CANCELADO";
        } else {
            throw new EstadoInvalidoException("Não pode cancelar em " + estado);
        }
    }

    // ... mais métodos com o mesmo padrão de if/else por estado ...
}
```

Esse código tem três problemas graves:

1. **Cada novo método é uma cascata de `if`s/`switch`s sobre estado** — duplicação massiva.
2. **Adicionar um estado novo** (digamos, `EmDevolucao`) exige modificar **todos** os métodos.
3. **A máquina de estados fica espalhada** pelo código — difícil de validar contra o diagrama original.

A solução do padrão State: **cada estado é uma classe**. O `Pedido` delega o comportamento ao objeto-estado atual. Quando o estado muda, o objeto muda — e o comportamento muda junto.

### 4.3 Estrutura

```
   ┌────────────────────────┐         ┌─────────────────────────┐
   │  Pedido (Context)      │◇────────│ <<interface>>           │
   ├────────────────────────┤         │ EstadoPedido            │
   │ - estado: EstadoPedido │         ├─────────────────────────┤
   ├────────────────────────┤         │ + pagar(p: Pedido)      │
   │ + pagar()              │         │ + enviar(p: Pedido)     │
   │ + enviar()             │         │ + confirmar(p: Pedido)  │
   │ + cancelar()           │         │ + cancelar(p: Pedido)   │
   │ + setEstado(e)         │         └─────────────────────────┘
   └────────────────────────┘                    △
                                                 │
                       ┌────────────┬────────────┼────────────┬────────────┐
                       │            │            │            │            │
                  Pendente        Pago        Enviado     Entregue    Cancelado
                  (ConcreteState) (...)        (...)        (...)        (...)
```

Cada `ConcreteState` implementa apenas as transições válidas a partir dele; as inválidas lançam exceção (ou são *no-ops*).

### 4.4 Código

**Interface do estado:**

```java
public interface EstadoPedido {
    void pagar(Pedido pedido);
    void enviar(Pedido pedido);
    void confirmar(Pedido pedido);
    void cancelar(Pedido pedido);
    String getNome();
}
```

**Estados concretos:**

```java
public class Pendente implements EstadoPedido {
    @Override
    public void pagar(Pedido pedido) {
        System.out.println("Processando pagamento...");
        pedido.setEstado(new Pago());
    }

    @Override
    public void enviar(Pedido pedido) {
        throw new TransicaoInvalidaException("Não posso enviar pedido pendente.");
    }

    @Override
    public void confirmar(Pedido pedido) {
        throw new TransicaoInvalidaException("Não posso confirmar pedido pendente.");
    }

    @Override
    public void cancelar(Pedido pedido) {
        pedido.setEstado(new Cancelado());
    }

    @Override
    public String getNome() { return "Pendente"; }
}

public class Pago implements EstadoPedido {
    @Override
    public void pagar(Pedido p) {
        throw new TransicaoInvalidaException("Pedido já está pago.");
    }

    @Override
    public void enviar(Pedido pedido) {
        System.out.println("Enviando pedido...");
        pedido.setEstado(new Enviado());
    }

    @Override
    public void confirmar(Pedido p) {
        throw new TransicaoInvalidaException("Não posso confirmar antes de enviar.");
    }

    @Override
    public void cancelar(Pedido pedido) {
        // Cancelar pago → estornar pagamento
        System.out.println("Estornando pagamento...");
        pedido.setEstado(new Cancelado());
    }

    @Override
    public String getNome() { return "Pago"; }
}

// Análogo para Enviado, Entregue, Cancelado
```

**Contexto:**

```java
public class Pedido {
    private EstadoPedido estado;

    public Pedido() {
        this.estado = new Pendente();  // estado inicial
    }

    public void setEstado(EstadoPedido novo) {
        System.out.println("Transição: " + estado.getNome() + " → " + novo.getNome());
        this.estado = novo;
    }

    // Métodos do contexto delegam ao estado
    public void pagar()     { estado.pagar(this); }
    public void enviar()    { estado.enviar(this); }
    public void confirmar() { estado.confirmar(this); }
    public void cancelar()  { estado.cancelar(this); }

    public String getEstadoAtual() { return estado.getNome(); }
}
```

**Uso:**

```java
Pedido p = new Pedido();
p.pagar();      // Pendente → Pago
p.enviar();     // Pago → Enviado
p.confirmar();  // Enviado → Entregue

Pedido p2 = new Pedido();
p2.cancelar();  // Pendente → Cancelado
p2.pagar();     // Lança TransicaoInvalidaException
```

### 4.5 State vs Strategy — gêmeos não-idênticos

Compare o UML do State com o do Strategy (aula passada): **são estruturalmente idênticos**. Mesmo padrão de delegação, mesma interface, mesmo `set` para trocar implementação.

A diferença é **conceitual / de intenção**:

| Aspecto | State | Strategy |
| --- | --- | --- |
| **Quem decide a "implementação"?** | O próprio objeto, ao transicionar | O cliente externo |
| **As implementações se conhecem?** | Sim — `Pendente` aponta para `Pago` ao executar `pagar()` | Não — cada estratégia é independente |
| **Quando muda?** | Sempre que o estado interno muda | Quando o cliente quiser |
| **Existe uma "máquina"?** | Sim, geralmente espelha um diagrama de estados | Não, são apenas alternativas |

> 💡 **Intuição:** Um sistema com `Strategy` parece dizer ao cliente: *"Que algoritmo você quer? Eu uso o que você escolher."* Um sistema com `State` parece dizer: *"Eu sei o que estou fazendo agora. Você pede; eu faço se for válido."* O segundo é mais autônomo. Quando você modela um diagrama de estados como o do `Pedido`, o padrão State é quase sempre a tradução natural para código.
> 

### 4.6 Consequências

**Benefícios:**

- **Eliminação de `if/switch` sobre estado** em todo lugar. Cada estado encapsula seu próprio comportamento.
- **Princípio Aberto/Fechado**: novos estados são novas classes — nenhuma classe antiga muda (exceto talvez algum estado que tenha transição para o novo).
- **A máquina de estados vira documentação viva**: o código espelha 1:1 o diagrama, e a equivalência pode ser verificada visualmente.
- **Estados podem ter atributos próprios**: o estado `EmDevolucao`, por exemplo, pode carregar dados de rastreio do produto retornando — sem poluir o `Pedido`.

**Custos:**

- **Aumento de classes**: 5 estados → 5 classes. Em máquinas com 20+ estados, é muita classe.
- **Estados precisam conhecer outros estados**: `Pendente` cria `Pago` em sua transição. Isso introduz acoplamento entre estados, que pode virar uma teia. Solução: centralizar transições em uma fábrica ou em uma tabela de transições.
- **Estado compartilhado entre instâncias**: se um estado não tem dados próprios (apenas comportamento), é desperdício criar uma instância nova por pedido. Pode-se aplicar **Singleton** ou **Flyweight** para reusar.

---

## 5. Iterator (Iterador)

### 5.1 Intenção

> Fornecer uma maneira de acessar os elementos de um objeto agregado sequencialmente sem expor sua representação subjacente.
> 

### 5.2 Motivação

Lembra do Composite da aula passada? Tínhamos `Categoria` contendo `Produto` e outras `Categoria`s. Agora queremos **percorrer todos os produtos** de uma categoria-raiz, em ordem de profundidade (DFS) ou em ordem de largura (BFS), sem que o cliente precise saber se a estrutura interna usa `ArrayList`, `LinkedList`, árvore, *hashmap*, ou seja lá o que for.

Solução: encapsular a lógica de travessia em um objeto separado — o **Iterator**. O cliente apenas pergunta `hasNext()` e `next()`; a implementação interna é invisível.

### 5.3 Estrutura

```
   ┌────────────────────────┐         ┌─────────────────────┐
   │  Iteravel (Aggregate)  │────────▶│ <<interface>>       │
   ├────────────────────────┤  cria   │ Iterator            │
   │ + criarIterator()      │         ├─────────────────────┤
   └────────────────────────┘         │ + hasNext(): boolean│
              △                       │ + next(): T         │
              │                       └─────────────────────┘
   ┌────────────────────────┐                   △
   │ Categoria              │                   │
   │ (ConcreteAggregate)    │       ┌───────────┴────────────┐
   └────────────────────────┘       │                        │
                              IteratorDFS              IteratorBFS
                              (depth-first)            (breadth-first)
```

### 5.4 Código

**Interface (Java já tem `java.util.Iterator`):**

```java
public interface Iterator<T> {
    boolean hasNext();
    T next();
}
```

**Iterator concreto — DFS sobre catálogo:**

```java
public class IteratorCatalogoDFS implements Iterator<Produto> {
    private final Deque<ComponenteCatalogo> pilha = new ArrayDeque<>();

    public IteratorCatalogoDFS(ComponenteCatalogo raiz) {
        pilha.push(raiz);
    }

    @Override
    public boolean hasNext() {
        // Avança a pilha até achar o próximo Produto
        while (!pilha.isEmpty() && !(pilha.peek() instanceof Produto)) {
            ComponenteCatalogo topo = pilha.pop();
            if (topo instanceof Categoria) {
                Categoria cat = (Categoria) topo;
                // Empilha filhos em ordem reversa para DFS esquerda-direita
                List<ComponenteCatalogo> filhos = cat.getFilhos();
                for (int i = filhos.size() - 1; i >= 0; i--) {
                    pilha.push(filhos.get(i));
                }
            }
        }
        return !pilha.isEmpty();
    }

    @Override
    public Produto next() {
        if (!hasNext()) throw new NoSuchElementException();
        return (Produto) pilha.pop();
    }
}
```

**Agregado expondo o iterator:**

```java
public class Categoria implements ComponenteCatalogo, Iterable<Produto> {
    // ... campos e métodos da aula passada ...

    @Override
    public Iterator<Produto> iterator() {
        return new IteratorCatalogoDFS(this);
    }
}
```

**Uso — limpíssimo:**

```java
Categoria eletronicos = montarCatalogo();

for (Produto p : eletronicos) {
    System.out.println(p.getNome() + " - R$ " + p.getPreco());
}
```

### 5.5 Iterator e a evolução das linguagens

Iterator é o padrão GoF que **mais foi absorvido** pela sintaxe das linguagens modernas:

- **Java**: `Iterable<T>` + `Iterator<T>` na biblioteca padrão; sintaxe `for-each` desde Java 5; `Stream<T>` desde Java 8.
- **Python**: `__iter__` e `__next__` como protocolo nativo; `for x in obj` funciona em qualquer iterável; geradores (`yield`) tornam iteradores triviais.
- **C#**: `IEnumerable<T>` + `foreach`; `yield return` para geradores.
- **JavaScript (ES6+)**: protocolos `Symbol.iterator` e `next()`; `for...of` loop.

Em Python, o iterator DFS acima ficaria:

```python
def percorrer(componente):
    if isinstance(componente, Produto):
        yield componente
    else:
        for filho in componente.filhos:
            yield from percorrer(filho)

# Uso
for produto in percorrer(eletronicos):
    print(produto.nome, produto.preco)
```

Cinco linhas, sem classes. O padrão "desapareceu" — virou idioma da linguagem.

### 5.6 Consequências

**Benefícios:**

- **Encapsulamento da estrutura interna**: o cliente não precisa saber se é lista, árvore, grafo, ou consulta a banco.
- **Múltiplas estratégias de travessia coexistem** (DFS, BFS, em ordem, ordenada por preço, etc.) sem mudar o agregado.
- **Iteradores podem ser preguiçosos**: `Stream` em Java só processa elementos sob demanda — economia de memória para coleções enormes.
- **Composição com outros padrões**: junto com Composite (visto), com Visitor (não visto aqui), com Command (Iterator de comandos = histórico).

**Custos:**

- **Em casos triviais, é exagero**: para uma `List<Produto>` simples, criar Iterator próprio é desperdício. Use o nativo.
- **Iteradores podem ficar desatualizados**: se o agregado é modificado durante a iteração, comportamento indefinido. Java lança `ConcurrentModificationException`.

> 💡 **Intuição:** Iterator é o padrão da pergunta "*qual é o próximo?*". Sempre que você teve que escrever `for (int i = 0; i < n; i++)`, você foi seu próprio iterator. Sempre que escreveu `for (X x : colecao)`, você usou um iterator pronto. O padrão está tão integrado à programação moderna que mal o vemos — e é justamente esse o sinal de um padrão bem-sucedido.
> 

---

## 6. Os outros padrões comportamentais (visão sumária)

A família comportamental do GoF tem cinco padrões a mais, que merecem ao menos menção:

| Padrão | Intenção em uma frase | Quando você o veria |
| --- | --- | --- |
| **Chain of Responsibility** | Passar uma solicitação ao longo de uma cadeia até alguém tratá-la. | Filtros de servlet/middleware (Express, Spring), tratamento de exceções, *event bubbling* em UIs. |
| **Mediator** | Centralizar comunicação complexa entre objetos em um único objeto. | Controladores de UI complexa, *chat rooms*, torres de controle. |
| **Memento** | Capturar e restaurar o estado interno de um objeto sem violar encapsulamento. | Sistemas de *snapshot*, *undo* mais sofisticado que Command, *checkpoints* em jogos. |
| **Visitor** | Separar um algoritmo da estrutura de objetos sobre a qual ele opera. | Compiladores (AST + múltiplas operações sobre ela), análise estática, exportação para múltiplos formatos. |
| **Interpreter** | Definir uma representação para a gramática de uma linguagem e um interpretador para sentenças nela. | DSLs, regras de negócio configuráveis, expressões regulares. |

Em sistemas reais grandes, **Chain of Responsibility** e **Visitor** são os mais relevantes; **Interpreter** é o mais raro e geralmente substituído por bibliotecas de *parsing*.

---

## 7. Síntese — encerrando o tour pelos padrões GoF

Após três aulas, você viu *onze* dos vinte e três padrões originais. Vale uma síntese organizando-os pela frequência prática:

| Frequência | Padrões |
| --- | --- |
| **Onipresentes** (você os usa todo dia, mesmo que não perceba) | Strategy, Observer, Iterator, Adapter, Facade |
| **Muito comuns** (aparecem em qualquer projeto não-trivial) | Factory Method, Decorator, Composite, Command, State |
| **Comuns em contextos específicos** | Template Method (frameworks), Singleton (controverso), Proxy (ORMs, RPC) |
| **Especializados** | Bridge, Flyweight, Chain of Responsibility, Mediator, Memento, Visitor, Interpreter, Abstract Factory, Builder, Prototype |

Três princípios atravessam praticamente todos os padrões vistos:

1. **Programe para uma interface, não uma implementação.** (Strategy, State, Iterator, Adapter, Decorator, Composite, Observer, Command).
2. **Prefira composição a herança.** (Strategy, Decorator, State, Composite, Command).
3. **Encapsule o que varia.** (Strategy, State, Command — variabilidade isolada em classes próprias).

Esses três princípios — não os 23 padrões — são o verdadeiro tesouro do livro do GoF. Os padrões são *consequências* deles aplicados a contextos recorrentes.

> 💡 **Intuição final:** Quando você for sênior e estiver projetando software, raramente você dirá "vou aplicar o padrão X aqui". Mais frequente será: "preciso desacoplar essa criação", "preciso permitir variar esse comportamento em runtime", "preciso encapsular essa máquina de estados". O *vocabulário* dos padrões te ajuda a comunicar a decisão depois — mas a decisão vem dos princípios. Aprenda os princípios; os padrões serão consequências naturais.
> 

---

## 8. Exemplos resolvidos

### Exemplo Resolvido 1 — Command com undo no painel administrativo (Introdutório) ⭐

**Enunciado:** Estenda o exemplo de Command da Seção 2 com mais um comando: **`AlterarEnderecoCommand`**, que troca o endereço de entrega de um pedido (com possibilidade de desfazer). Em seguida, simule uma sessão de administração: cancelamento do pedido, alteração de endereço, undo, redo.

**Solução:**

**Novo comando:**

```java
public class AlterarEnderecoCommand implements Comando {
    private final Pedido pedido;
    private final Endereco novoEndereco;
    private Endereco enderecoAnterior;

    public AlterarEnderecoCommand(Pedido pedido, Endereco novo) {
        this.pedido = pedido;
        this.novoEndereco = novo;
    }

    @Override
    public void executar() {
        this.enderecoAnterior = pedido.getEnderecoEntrega();
        pedido.setEnderecoEntrega(novoEndereco);
    }

    @Override
    public void desfazer() {
        pedido.setEnderecoEntrega(enderecoAnterior);
    }

    @Override
    public String getDescricao() {
        return "Alterar endereço de entrega do pedido #" + pedido.getId();
    }
}
```

**Sessão simulada:**

```java
HistoricoComandos hist = new HistoricoComandos();

// Estado inicial: pedido com endereço A
Pedido p = repositorio.buscar(123);

// Operador altera o endereço
hist.executar(new AlterarEnderecoCommand(p, enderecoB));
// → log: "EXECUTOU: Alterar endereço de entrega do pedido #123"
// → estado: endereço B

// Operador percebe que digitou errado
hist.desfazer();
// → log: "DESFEZ: Alterar endereço de entrega do pedido #123"
// → estado: endereço A novamente

// Operador agora quer cancelar o pedido
hist.executar(new CancelarPedidoCommand(p));
// → log: "EXECUTOU: Cancelar pedido #123"
// → estado: cancelado

// Cliente liga reclamando: undo!
hist.desfazer();
// → log: "DESFEZ: Cancelar pedido #123"
// → estado: voltou ao anterior

// Operador confirma cancelamento: refazer
hist.refazer();
// → log: "REFEZ: Cancelar pedido #123"
// → estado: cancelado
```

**Lições:**

- Toda ação do operador passou pela mesma "porta" — `historico.executar(...)`. Isso significa que **um único ponto** captura o log de auditoria, aplica permissões e gerencia o histórico. Se amanhã o requisito virar "logar também o IP do operador", a mudança é localizada.
- Note como `AlterarEnderecoCommand` e `CancelarPedidoCommand` são **completamente independentes** entre si — não compartilham hierarquia além da interface. Adicionar um terceiro comando não toca em nenhum deles.
- O `HistoricoComandos` não conhece **nada** sobre pedidos, endereços, pagamentos. Ele só conhece `Comando`. É o desacoplamento canônico que o padrão promete.

### Exemplo Resolvido 2 — State implementando o ciclo completo do Pedido com retornos e devoluções (Intermediário) ⭐⭐

**Enunciado:** Na aula sobre diagramas de estados, modelamos um ciclo de vida do `Pedido` com transições para `Cancelado` e um estado composto `EmDevolucao` (com seus próprios subestados internos: `AguardandoColeta`, `ItemRecebido`, `Reembolsado`). Implemente esse ciclo *completo* usando o padrão State, dando atenção especial à modelagem do estado composto.

**Solução:**

A questão central: como representar um estado composto em código? Há duas estratégias:

- **(a) Achatar**: tratar cada subestado de `EmDevolucao` como um estado de primeira classe (`AguardandoColeta`, `ItemRecebido`, `Reembolsado`). A estrutura composta vira convenção de nomenclatura ou de agrupamento.
- **(b) Aninhar**: criar um `EmDevolucaoState` que internamente delega a sub-estados.

A opção (a) é mais simples e suficiente para a maioria dos casos. Vamos por ela.

**Estados — caminho principal (já visto, com pequena evolução):**

```java
public class Entregue implements EstadoPedido {
    @Override
    public void pagar(Pedido p)   { throw new TransicaoInvalidaException(); }
    @Override
    public void enviar(Pedido p)  { throw new TransicaoInvalidaException(); }
    @Override
    public void confirmar(Pedido p){ throw new TransicaoInvalidaException(); }
    @Override
    public void cancelar(Pedido p){ throw new TransicaoInvalidaException(); }

    // Nova transição: solicitar devolução
    public void solicitarDevolucao(Pedido p) {
        System.out.println("Devolução solicitada. Aguardando coleta.");
        p.setEstado(new AguardandoColeta());
    }

    @Override
    public String getNome() { return "Entregue"; }
}
```

**Subestados de devolução (achatados como estados de primeiro nível):**

```java
public class AguardandoColeta implements EstadoPedido {
    // Transições do ciclo principal: todas inválidas
    @Override public void pagar(Pedido p)    { throw new TransicaoInvalidaException(); }
    @Override public void enviar(Pedido p)   { throw new TransicaoInvalidaException(); }
    @Override public void confirmar(Pedido p){ throw new TransicaoInvalidaException(); }
    @Override public void cancelar(Pedido p) { throw new TransicaoInvalidaException(); }

    // Nova transição: item coletado pela transportadora
    public void registrarColeta(Pedido p) {
        System.out.println("Item coletado.");
        p.setEstado(new ItemRecebido());
    }

    @Override public String getNome() { return "AguardandoColeta"; }
}

public class ItemRecebido implements EstadoPedido {
    @Override public void pagar(Pedido p)    { throw new TransicaoInvalidaException(); }
    @Override public void enviar(Pedido p)   { throw new TransicaoInvalidaException(); }
    @Override public void confirmar(Pedido p){ throw new TransicaoInvalidaException(); }
    @Override public void cancelar(Pedido p) { throw new TransicaoInvalidaException(); }

    public void aprovarReembolso(Pedido p) {
        System.out.println("Reembolso aprovado.");
        p.setEstado(new Reembolsado());
    }

    @Override public String getNome() { return "ItemRecebido"; }
}

public class Reembolsado implements EstadoPedido {
    // Estado terminal: tudo gera exceção
    @Override public void pagar(Pedido p)    { throw new TransicaoInvalidaException(); }
    @Override public void enviar(Pedido p)   { throw new TransicaoInvalidaException(); }
    @Override public void confirmar(Pedido p){ throw new TransicaoInvalidaException(); }
    @Override public void cancelar(Pedido p) { throw new TransicaoInvalidaException(); }

    @Override public String getNome() { return "Reembolsado"; }
}
```

**Contexto enriquecido:**

```java
public class Pedido {
    private EstadoPedido estado = new Pendente();

    public void setEstado(EstadoPedido novo) { this.estado = novo; }

    // Transições do ciclo principal
    public void pagar()     { estado.pagar(this); }
    public void enviar()    { estado.enviar(this); }
    public void confirmar() { estado.confirmar(this); }
    public void cancelar()  { estado.cancelar(this); }

    // Transições do ramo de devolução
    public void solicitarDevolucao() {
        if (estado instanceof Entregue) {
            ((Entregue) estado).solicitarDevolucao(this);
        } else {
            throw new TransicaoInvalidaException("Só pode devolver pedido entregue.");
        }
    }
    // ... análogo para registrarColeta, aprovarReembolso ...

    public String getEstadoAtual() { return estado.getNome(); }
}
```

**Sessão de uso simulando o caminho completo:**

```java
Pedido p = new Pedido();
p.pagar();                  // Pendente → Pago
p.enviar();                 // Pago → Enviado
p.confirmar();              // Enviado → Entregue
p.solicitarDevolucao();     // Entregue → AguardandoColeta
p.registrarColeta();        // AguardandoColeta → ItemRecebido
p.aprovarReembolso();       // ItemRecebido → Reembolsado

System.out.println(p.getEstadoAtual());  // "Reembolsado"
```

**Discussão das decisões:**

- **Por que achatamos o estado composto?** Em código Java, hierarquias profundas de estados costumam aumentar a complexidade mais do que a documentação. Achatar mantém o código simples; o "agrupamento" do diagrama original é preservado conceitualmente (todos os três subestados representam fases da devolução, e isso pode até ser comentado nas classes).
- **Quando aninhar valeria a pena?** Se houvesse comportamento *comum a todos os subestados de devolução* (ex.: "a qualquer momento durante a devolução, o cliente pode contatar o suporte e pausar o processo"), aninhar via uma classe abstrata `EstadoDevolucao` ganharia força — você fatora o comum e cada subestado herda. Mas isso introduz herança, então pondere caso a caso.
- **Note o método `solicitarDevolucao` do `Pedido`**: ele faz `instanceof` para descobrir se está em `Entregue`. Isso é o tipo de código que o padrão State *deveria* eliminar! Como evitar? Adicionando `solicitarDevolucao` à interface `EstadoPedido`, com implementação default que lança exceção, e sobrescrevendo apenas em `Entregue`. (Em Java 8+, com métodos default em interfaces, isso fica elegante.) Esta é uma refatoração que vale a pena propor em sala como discussão.
- **Conexão com a aula anterior**: este exemplo é a *implementação fiel* daquele diagrama. O ganho concreto: bugs de transição inválida (ex.: enviar um pedido pendente) são impedidos *no compilador e em runtime*, não dependem de revisão manual.

> 💡 **Intuição:** Note como diagramas comportamentais e padrões comportamentais formam uma ponte natural: o diagrama é a *especificação visual*, o padrão é a *receita de implementação*. Modelar bem na fase de análise faz a fase de codificação trivial.
> 

---

## Exercícios Práticos

### Exercício 1 — Template Method para geração de relatórios ⭐

Em um sistema corporativo, geramos diferentes tipos de relatório (vendas mensais, estoque atual, performance de vendedores). Todos seguem a mesma estrutura:

> Cabeçalho com logo da empresa, título do relatório e data.Carregar dados (varia por relatório).Formatar dados em tabela (varia por relatório — colunas e formato diferentes).Rodapé com paginação e assinatura.
> 

Sua tarefa:

(a) Modele em **diagrama de classes UML** a solução com Template Method. Identifique a classe abstrata, o método-modelo (com `final`) e os métodos abstratos / *hooks*.
(b) Implemente em Java a classe abstrata `RelatorioBase` com o método `gerar()` como template, e ao menos duas subclasses concretas (`RelatorioVendasMensal`, `RelatorioEstoque`).
(c) Inclua pelo menos um *hook* opcional (ex.: `incluirGraficos()`) com implementação vazia na base, sobrescrita em uma das subclasses.
(d) Discuta em 3-5 linhas: este problema também poderia ser resolvido com **Strategy**? Compare as duas abordagens e justifique qual você escolheria, considerando que (i) o esqueleto é estável, (ii) novos relatórios serão criados frequentemente.

### Exercício 2 — Iterator + Command para macro recorder ⭐⭐

Um editor gráfico precisa permitir que o usuário **grave macros**: sequências de ações (desenhar linha, desenhar círculo, alterar cor) que podem ser depois reproduzidas em qualquer outro documento. O sistema deve permitir:

- Iniciar gravação.
- Executar várias ações enquanto grava (cada ação fica armazenada).
- Parar gravação — produz uma `Macro` que pode ser salva.
- Reproduzir uma `Macro` em outro contexto.
- Reproduzir apenas as primeiras N ações da macro.
- Iterar sobre as ações de uma macro para mostrar visualmente a lista no painel "Editar Macro".

Sua tarefa:

(a) Identifique e justifique quais padrões aplicar. Espera-se que você use **Command** (cada ação de desenho é um comando) e **Iterator** (para percorrer as ações de uma macro).
(b) Modele em UML o esqueleto da solução.
(c) Implemente em Java:

- A interface `ComandoDesenho` com `executar(Canvas c)`.
- Pelo menos dois comandos concretos (ex.: `DesenharLinhaCommand`, `MudarCorCommand`).
- A classe `Macro` que armazena uma lista de `ComandoDesenho` e implementa `Iterable<ComandoDesenho>`.
- Um método `reproduzir(Canvas c)` em `Macro` que executa todos os comandos.
- Um método `reproduzirParcial(Canvas c, int n)` que reproduz apenas os `n` primeiros comandos *usando o iterator*.

(d) Justifique em pelo menos 5 linhas: **(i)** por que Command é necessário aqui (e não simplesmente uma `List<Runnable>`); **(ii)** que vantagem o iterator traz em relação a expor a `List<ComandoDesenho>` interna; **(iii)** identifique uma extensão natural deste sistema que se beneficiaria do padrão **Composite** (dica: macros que contêm submacros).

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Resumo

Padrões comportamentais coordenam a colaboração entre objetos e a distribuição de responsabilidades. Vimos quatro em profundidade: **Command** (objetiva ações para permitir undo, log, fila), **Template Method** (define esqueleto de algoritmo postergando passos para subclasses), **State** (substitui `if/switch` sobre estados pelo polimorfismo entre objetos-estado, traduzindo elegantemente diagramas de estado em código), e **Iterator** (encapsula travessia de coleções, hoje absorvido pela sintaxe das linguagens modernas). As distinções mais importantes desta aula são as comparações: **State e Strategy** são estruturalmente idênticos mas diferem na intenção (transição autônoma vs. escolha do cliente); **Template Method e Strategy** resolvem problemas similares por mecanismos diferentes (herança vs. composição). O fechamento do tour pelos padrões GoF revela que o verdadeiro tesouro não são os 23 padrões em si, mas os três princípios que os atravessam: programar para interfaces, preferir composição a herança, e encapsular o que varia.

**Conceitos-chave:** `padrão comportamental`, `Command`, `Receiver`, `Invoker`, `undo/redo`, `Template Method`, `hook`, `inversão de controle`, `Princípio de Hollywood`, `State`, `máquina de estados`, `Iterator`, `Iterable`, `for-each`, `programar para interfaces`, `composição sobre herança`, `encapsular o que varia`.

---

## Referências

- Gamma, E.; Helm, R.; Johnson, R.; Vlissides, J. *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley, 1994. — Capítulo 5.
- Freeman, E.; Robson, E.; Bates, B.; Sierra, K. *Head First Design Patterns*. 2ª ed., O'Reilly, 2020. — Capítulos 6 (Command), 8 (Template Method), 10 (State), 9 (Iterator).
- Bloch, J. *Effective Java*. 3ª ed., Addison-Wesley, 2018. — Item 22 (interfaces como tipos), Item 18 (composição sobre herança), Item 47 (preferir Stream a Iterator quando aplicável).
- Fowler, M. *Refactoring: Improving the Design of Existing Code*. 2ª ed., Addison-Wesley, 2018. — Capítulo 10 ("Replace Conditional with Polymorphism", base teórica para State e Strategy).
- Guerra, E. *Design Patterns com Java: Projeto orientado a objetos guiado por padrões*. Casa do Código, 2014. — Capítulos sobre padrões comportamentais.
- Martin, R. *Clean Architecture*. Pearson, 2017. — Capítulos sobre os princípios SOLID, fundamentação teórica unificadora.

---