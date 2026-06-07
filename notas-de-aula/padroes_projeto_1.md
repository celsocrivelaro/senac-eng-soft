# Padrões de Projeto 1

## 1. Por que padrões?

### 1.1 O problema dos problemas recorrentes

Conforme você ganha experiência projetando software orientado a objetos, percebe um fato curioso: **os mesmos problemas reaparecem**, em contextos diferentes, em domínios diferentes, em empresas diferentes. Como permitir que o algoritmo de cálculo de imposto seja trocado em tempo de execução? Como fazer com que vários módulos da interface sejam notificados quando o modelo muda? Como criar objetos sem o cliente precisar saber qual classe concreta está sendo instanciada?

Programadores experientes desenvolvem, ao longo dos anos, **soluções padronizadas** para esses problemas. O mesmo aluno que, no segundo semestre, escrevia um `if`/`else` gigante para diferentes formas de pagamento, no quinto semestre cria uma interface `FormaPagamento` e injeta a implementação correta. Esse conhecimento, contudo, ficava por décadas restrito à tradição oral — passado de mentor para aprendiz.

### 1.2 Christopher Alexander e a origem da ideia

A ideia de catalogar e nomear soluções recorrentes vem de fora da computação. O arquiteto **Christopher Alexander**, no livro *A Pattern Language* (1977), propôs um vocabulário de 253 padrões para arquitetura de cidades e edifícios — desde "Praça Pública" até "Janela em Esquina". Cada padrão tinha um nome, descrevia um problema recorrente e oferecia uma solução com suas consequências.

A ideia foi adaptada para software por um grupo de pesquisadores e praticantes ao longo dos anos 1980, culminando no livro seminal:

> Gamma, E.; Helm, R.; Johnson, R.; Vlissides, J. *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley, 1994.
> 

Esses quatro autores são até hoje conhecidos como **Gang of Four** (GoF). O livro catalogou 23 padrões e estabeleceu o vocabulário que dominou as duas décadas seguintes.

### 1.3 O que um padrão *não é*

Antes de avançarmos, esclareçamos o que padrões **não são**:

- **Não são bibliotecas ou frameworks.** Você não importa um padrão; você o aplica.
- **Não são algoritmos.** Algoritmos resolvem problemas computacionais; padrões resolvem problemas de *design*.
- **Não são receitas para copiar e colar.** São esqueletos conceituais que você adapta ao contexto.
- **Não são leis.** Aplicar um padrão errado é pior do que não aplicar nenhum.

> 💡 **Intuição:** Pense em padrões como *vocabulário compartilhado de experts*. Quando dois engenheiros experientes conversam e um diz "vamos aplicar Observer aqui", o outro entende imediatamente uma estrutura inteira — sem precisar desenhar diagramas. O ganho de comunicação é tão importante quanto o ganho técnico.
> 

---

## 2. Anatomia de um padrão

Todo padrão, na tradição GoF, é descrito seguindo uma estrutura formal. Vamos usar uma versão pedagogicamente simplificada:

| Campo | O que descreve |
| --- | --- |
| **Nome** | Termo único e evocativo. Faz parte do vocabulário. |
| **Intenção** | Frase curta dizendo o que o padrão *faz*. |
| **Problema / Motivação** | Situação concreta em que ele se aplica. |
| **Estrutura** | Diagrama de classes mostrando os participantes. |
| **Participantes** | Os papéis abstratos (não as classes específicas). |
| **Colaborações** | Como os participantes interagem em tempo de execução. |
| **Consequências** | Benefícios *e* custos da adoção. |
| **Implementação** | Decisões e armadilhas práticas. |
| **Usos conhecidos** | Onde o padrão aparece em sistemas reais. |

A descrição completa do GoF inclui também *aplicabilidade*, *padrões relacionados* e *código de exemplo*. Para essa aula introdutória, focaremos no essencial: nome, intenção, estrutura, código e consequências.

---

## 3. Taxonomia GoF

Os 23 padrões originais foram organizados em três famílias, conforme o tipo de problema que resolvem:

| Categoria | O que tratam | Exemplos |
| --- | --- | --- |
| **Criacionais** | *Como* objetos são criados; abstração do processo de instanciação. | Singleton, Factory Method, Abstract Factory, Builder, Prototype |
| **Estruturais** | *Como* classes e objetos são compostos para formar estruturas maiores. | Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy |
| **Comportamentais** | *Como* objetos colaboram, distribuindo responsabilidades. | Chain of Responsibility, Command, Iterator, Mediator, Memento, **Observer**, State, **Strategy**, Template Method, Visitor |

Há ainda uma classificação ortogonal por **escopo**:

- **Padrões de classe**: a relação principal é por *herança* (estática, definida em compilação).
- **Padrões de objeto**: a relação principal é por *composição* (dinâmica, definida em execução).

A maioria dos padrões úteis na prática moderna é de objeto — refletindo o consenso de que **composição é geralmente mais flexível que herança**. Voltaremos a esse ponto.

---

## 4. Padrão **Strategy** (Comportamental)

### 4.1 Intenção

> Definir uma família de algoritmos, encapsular cada um deles e torná-los intercambiáveis. Strategy permite que o algoritmo varie independentemente dos clientes que o utilizam.
> 

### 4.2 Motivação

Imagine que, no nosso sistema de e-commerce, o cliente pode pagar com cartão de crédito, boleto, Pix ou criptomoeda. Uma implementação ingênua é:

```java
public class Pedido {
    public void cobrar(String formaPagamento) {
        if (formaPagamento.equals("CREDITO")) {
            // ... lógica longa de cartão ...
        } else if (formaPagamento.equals("BOLETO")) {
            // ... lógica longa de boleto ...
        } else if (formaPagamento.equals("PIX")) {
            // ... lógica longa de Pix ...
        }
        // ...
    }
}
```

Esse código viola dois princípios fundamentais:

1. **OCP (Open/Closed Principle)**: cada nova forma de pagamento exige *modificar* a classe `Pedido`.
2. **SRP (Single Responsibility Principle)**: `Pedido` agora é responsável também por orquestrar todas as integrações de pagamento.

### 4.3 Estrutura

```
       ┌─────────────────────┐         ┌─────────────────────┐
       │      Pedido         │         │  <<interface>>      │
       │   (Context)         │◇────────│  FormaPagamento     │
       ├─────────────────────┤         │  (Strategy)         │
       │ - estrategia: ...   │         ├─────────────────────┤
       ├─────────────────────┤         │ + cobrar(v: double) │
       │ + cobrar()          │         └─────────────────────┘
       │ + setEstrategia(s)  │                   △
       └─────────────────────┘                   │
                                  ┌──────────────┼──────────────┐
                                  │              │              │
                          ┌──────────────┐ ┌──────────┐ ┌──────────────┐
                          │ PagtoCredito │ │ PagtoPix │ │ PagtoBoleto  │
                          │ (Concrete)   │ │          │ │              │
                          └──────────────┘ └──────────┘ └──────────────┘
```

### 4.4 Código

**Interface da estratégia:**

```java
public interface FormaPagamento {
    void cobrar(double valor);
}
```

**Implementações concretas:**

```java
public class PagamentoCredito implements FormaPagamento {
    private final String numeroCartao;
    private final String validade;

    public PagamentoCredito(String numeroCartao, String validade) {
        this.numeroCartao = numeroCartao;
        this.validade = validade;
    }

    @Override
    public void cobrar(double valor) {
        // Comunica com a operadora de cartão
        System.out.println("Cobrando R$ " + valor + " no cartão " + numeroCartao);
    }
}

public class PagamentoPix implements FormaPagamento {
    private final String chavePix;

    public PagamentoPix(String chavePix) {
        this.chavePix = chavePix;
    }

    @Override
    public void cobrar(double valor) {
        // Gera QR code Pix
        System.out.println("Gerando Pix de R$ " + valor + " para chave " + chavePix);
    }
}
```

**Contexto** (que usa a estratégia):

```java
public class Pedido {
    private FormaPagamento formaPagamento;
    private double total;

    public void setFormaPagamento(FormaPagamento f) {
        this.formaPagamento = f;
    }

    public void finalizar() {
        formaPagamento.cobrar(total);
    }
}
```

**Uso pelo cliente:**

```java
Pedido pedido = new Pedido();
pedido.setFormaPagamento(new PagamentoPix("usuario@email.com"));
pedido.finalizar();
```

### 4.5 Consequências

**Benefícios:**

- **Aderência ao OCP**: novas formas de pagamento *adicionam* classes, sem modificar `Pedido`.
- **Testabilidade**: cada estratégia pode ser testada isoladamente; em testes de `Pedido`, podemos injetar uma estratégia de mentira (*mock*).
- **Troca em tempo de execução**: o cliente pode mudar a estratégia depois que o objeto foi criado.

**Custos:**

- **Aumento do número de classes**: três `if`s viram quatro classes. Em problemas pequenos, isso é exagero.
- **O cliente precisa conhecer as estratégias**: alguém precisa saber qual instanciar (problema que motiva o próximo padrão!).

> 💡 **Intuição:** Strategy é, para muitos, *o* padrão didático por excelência. Ele encapsula em forma cristalina o princípio "**programe para uma interface, não para uma implementação**" — talvez o mantra mais importante da OO. Quase todo *if/else* gigante baseado em "tipo" de algo é candidato a virar Strategy.
> 

---

## 5. Padrão **Factory Method** (Criacional)

### 5.1 Intenção

> Definir uma interface para criar um objeto, mas deixar as subclasses decidirem qual classe instanciar. Factory Method permite que uma classe delegue a instanciação para suas subclasses.
> 

### 5.2 Motivação

No exemplo de Strategy, ficou pendente: *quem decide qual `FormaPagamento` instanciar?* Se for o cliente diretamente, ele precisa importar todas as classes concretas — voltamos ao acoplamento que tentávamos evitar. A solução é introduzir um intermediário responsável pela criação.

Há, na prática, três variantes deste padrão, frequentemente confundidas:

| Variante | Descrição |
| --- | --- |
| **Simple Factory** (não está no GoF, mas é amplamente usada) | Uma classe com um método estático que retorna instâncias concretas baseadas em um parâmetro. |
| **Factory Method** (GoF) | Define-se um método abstrato em uma classe; subclasses o implementam para retornar tipos específicos. |
| **Abstract Factory** (GoF) | Fornece uma interface para criar *famílias* de objetos relacionados. |

Para fins desta aula, focaremos na variante simples (mais comum no dia a dia) e mencionamos as outras.

### 5.3 Estrutura (Simple Factory)

```
           ┌──────────────────────────────────┐
           │     FabricaFormaPagamento        │
           ├──────────────────────────────────┤
           │ + criar(tipo: TipoPgto): F.Pgto. │
           └──────────────────────────────────┘
                          │
                          │ retorna
                          ▼
                ┌───────────────────────┐
                │  <<interface>>        │
                │  FormaPagamento       │
                └───────────────────────┘
                          △
              ┌───────────┼───────────┐
              │           │           │
        PagtoCredito   PagtoPix   PagtoBoleto
```

### 5.4 Código

```java
public enum TipoPagamento { CREDITO, PIX, BOLETO }

public class FabricaFormaPagamento {

    public static FormaPagamento criar(TipoPagamento tipo, DadosPagamento dados) {
        switch (tipo) {
            case CREDITO:
                return new PagamentoCredito(dados.getNumeroCartao(), dados.getValidade());
            case PIX:
                return new PagamentoPix(dados.getChavePix());
            case BOLETO:
                return new PagamentoBoleto(dados.getCpf());
            default:
                throw new IllegalArgumentException("Tipo de pagamento desconhecido: " + tipo);
        }
    }
}
```

**Uso:**

```java
FormaPagamento f = FabricaFormaPagamento.criar(TipoPagamento.PIX, dados);
pedido.setFormaPagamento(f);
pedido.finalizar();
```

### 5.5 Factory Method "verdadeiro" (GoF)

A versão GoF é mais sofisticada: ao invés de um método estático com `switch`, usa-se herança. Uma classe abstrata declara um método de criação; subclasses o implementam.

```java
public abstract class ProcessadorPedido {

    // Factory Method: subclasses decidem qual forma criar
    protected abstract FormaPagamento criarFormaPagamento();

    // Algoritmo que usa a forma
    public final void processar(double valor) {
        FormaPagamento f = criarFormaPagamento();
        f.cobrar(valor);
    }
}

public class ProcessadorPedidoCredito extends ProcessadorPedido {
    @Override
    protected FormaPagamento criarFormaPagamento() {
        return new PagamentoCredito(/* ... */);
    }
}
```

A vantagem: nenhum `switch` para manter; cada caso é uma subclasse. A desvantagem: hierarquia de classes paralela à hierarquia de produtos.

### 5.6 Consequências

**Benefícios:**

- **Desacoplamento**: o cliente não importa as classes concretas — só conhece a interface e a fábrica.
- **Centralização**: a lógica de "qual classe usar" fica em um único ponto.
- **Aderência ao DIP** (Dependency Inversion Principle): código de alto nível depende de abstrações, não de classes concretas.

**Custos:**

- **Indireção adicional**: para entender o sistema, o leitor precisa rastrear a fábrica.
- **Acoplamento residual** na fábrica: ela ainda precisa conhecer todas as classes concretas. Mas é apenas *um* ponto que conhece — não o código todo.

---

## 6. Padrão **Observer** (Comportamental)

### 6.1 Intenção

> Definir uma dependência um-para-muitos entre objetos, de modo que quando um objeto muda de estado, todos os seus dependentes são notificados e atualizados automaticamente.
> 

### 6.2 Motivação

Suponha que, quando um `Pedido` muda de estado (lembrem da máquina de estados das aulas anteriores: `Pendente → Pago → Enviado → Entregue`), múltiplos componentes precisem reagir:

- O **serviço de e-mail** envia uma notificação ao cliente.
- O **serviço de SMS** envia um alerta.
- O **sistema de logística** começa a separar os itens.
- O **dashboard de BI** atualiza métricas em tempo real.

Solução ingênua: a classe `Pedido` chama todos esses serviços diretamente. Resultado: `Pedido` acopla-se a *todo* o resto do sistema, e adicionar um novo "ouvinte" exige modificá-la.

### 6.3 Estrutura

```
   ┌────────────────────────┐         ┌────────────────────────┐
   │   <<interface>>        │         │   <<interface>>        │
   │   Subject              │◇──────▶ │   Observer             │
   ├────────────────────────┤    *    ├────────────────────────┤
   │ + adicionar(o)         │         │ + atualizar(evento)    │
   │ + remover(o)           │         └────────────────────────┘
   │ + notificar()          │                       △
   └────────────────────────┘                       │
              △                          ┌──────────┼──────────┐
              │                          │          │          │
   ┌────────────────────┐         ┌──────────┐┌──────────┐┌────────┐
   │ Pedido             │         │ Email    ││ SMS      ││ BI     │
   │ (ConcreteSubject)  │         └──────────┘└──────────┘└────────┘
   ├────────────────────┤
   │ - estado           │
   │ + setEstado()      │
   └────────────────────┘
```

### 6.4 Código

**Interfaces:**

```java
public interface Observer {
    void atualizar(EventoPedido evento);
}

public interface Subject {
    void adicionarObserver(Observer o);
    void removerObserver(Observer o);
    void notificar(EventoPedido evento);
}
```

**Sujeito concreto:**

```java
public class Pedido implements Subject {
    private final List<Observer> observers = new ArrayList<>();
    private EstadoPedido estado;

    @Override
    public void adicionarObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removerObserver(Observer o) {
        observers.remove(o);
    }

    @Override
    public void notificar(EventoPedido evento) {
        for (Observer o : observers) {
            o.atualizar(evento);
        }
    }

    public void marcarComoPago() {
        this.estado = EstadoPedido.PAGO;
        notificar(new EventoPedido(this, "PEDIDO_PAGO"));
    }
}
```

**Observadores concretos:**

```java
public class NotificadorEmail implements Observer {
    @Override
    public void atualizar(EventoPedido evento) {
        if (evento.getTipo().equals("PEDIDO_PAGO")) {
            System.out.println("Enviando e-mail de confirmação...");
        }
    }
}

public class ServicoLogistica implements Observer {
    @Override
    public void atualizar(EventoPedido evento) {
        if (evento.getTipo().equals("PEDIDO_PAGO")) {
            System.out.println("Iniciando separação dos itens...");
        }
    }
}
```

**Uso:**

```java
Pedido pedido = new Pedido();
pedido.adicionarObserver(new NotificadorEmail());
pedido.adicionarObserver(new ServicoLogistica());
// Ao mudar o estado, todos os observadores reagem automaticamente
pedido.marcarComoPago();
```

### 6.5 Consequências

**Benefícios:**

- **Acoplamento fraco**: `Pedido` não conhece `NotificadorEmail` ou `ServicoLogistica` — só conhece `Observer`.
- **Extensibilidade**: novos observadores podem ser adicionados sem mexer em `Pedido`.
- **Suporte a *broadcast***: um evento, múltiplos consumidores.

**Custos:**

- **Notificações inesperadas**: se a cadeia de observadores for longa e complexa, depurar pode ser um pesadelo. Você atualiza um campo e *coisas acontecem* em outros lugares.
- **Ordem de notificação não-determinística** (em geral): se um observador depende de outro ter agido primeiro, há acoplamento implícito.
- **Risco de vazamento de memória**: se observadores não forem removidos, o `Subject` segura referências para sempre.

> 💡 **Intuição:** Observer é o coração de quase todo sistema "reativo" moderno: o `addEventListener` do JavaScript, o `@Observable` do RxJava, os *signals* do Vue, o pub/sub do Kafka — todos são variações do mesmo padrão. O modelo MVC (Model-View-Controller), que estrutura a maioria das aplicações com interface gráfica desde os anos 1980, nasceu como uma aplicação direta de Observer.
> 

---

## 7. Padrões e a evolução das linguagens

Algo curioso aconteceu nas três décadas desde o livro do GoF: **algumas linguagens evoluíram a ponto de tornar certos padrões obsoletos ou triviais**. Exemplos:

- **Iterator**, um dos 23 padrões originais, é hoje sintático em Java (`for-each`), Python (`for x in lista`) e em praticamente todas as linguagens modernas.
- **Strategy**, em linguagens com *funções como cidadãos de primeira classe* (Python, JavaScript, Kotlin, e Java a partir do 8 com *lambdas*), pode ser implementado em uma linha:

```java
// Java moderno: Strategy reduzido a uma lambda
pedido.setFormaPagamento(valor -> System.out.println("Pix de " + valor));
```

- **Command**, similarmente, pode virar um simples `Runnable` ou `Function<T,R>`.
- **Singleton**, sempre controverso, é hoje frequentemente substituído por *injeção de dependência*.

Isso significa que padrões "morreram"? Não exatamente. Significa que: **(a)** o vocabulário continua útil para comunicação, mesmo quando a implementação é trivial; e **(b)** os padrões originais foram, em parte, *esquemas para superar limitações de C++ e Smalltalk dos anos 90*. Linguagens modernas absorveram alguns dos padrões em sua sintaxe.

> 💡 **Intuição:** Conhecer padrões é importante. Mas é igualmente importante reconhecer que cada padrão tem um *contexto histórico*. Aplicar Strategy com classes verbosas em um código Python é, hoje, frequentemente sobre-engenharia. Aprenda os padrões para ter o vocabulário; aplique-os com discernimento.
> 

---

## 8. Anti-padrões e *patternitis*

Aplicar padrões em excesso é um vício conhecido — Joshua Kerievsky, em *Refactoring to Patterns* (2004), cunhou o termo **"patternitis"** para descrever sistemas em que cada classe é uma instância de algum padrão, transformando código simples em labirintos abstratos.

Sinais de *patternitis*:

- Sete níveis de indireção para fazer algo que poderia ser uma chamada direta.
- Classes chamadas `AbstractFactoryProxyFacade` (sem ironia).
- Discussões em *code review* dominadas pelo nome do padrão, não pelo problema do domínio.

A regra de ouro, atribuída ao próprio Kent Beck, é: **"refatore *para* padrões, não *com* padrões"**. Comece com a solução mais simples possível; quando o código apresentar *cheiros* específicos (acoplamento excessivo, repetição estrutural, instâncias de "muito *if*-else por tipo"), aí sim refatore na direção de um padrão.

---

## 9. Exemplos resolvidos

### Exemplo Resolvido 1 — Strategy para cálculo de frete (Introdutório) ⭐

**Enunciado:** No e-commerce dos exemplos anteriores, queremos calcular o frete de um pedido. As regras variam: pelos Correios (tabela própria), por transportadora privada (tabela própria), e retirada na loja (frete zero). Modele e implemente esta funcionalidade aplicando o padrão Strategy.

**Solução:**

**Diagrama:**

```
        ┌──────────────────┐         ┌──────────────────────┐
        │ CalculadoraFrete │         │ <<interface>>        │
        │   (Context)      │◇────────│ EstrategiaFrete      │
        ├──────────────────┤         ├──────────────────────┤
        │ - estrategia     │         │ + calcular(p): double│
        ├──────────────────┤         └──────────────────────┘
        │ + calcularFrete()│                    △
        └──────────────────┘                    │
                                ┌───────────────┼───────────────┐
                                │               │               │
                       FreteCorreios   FreteTransportadora   FreteRetirada
```

**Código:**

```java
public interface EstrategiaFrete {
    double calcular(Pedido pedido);
}

public class FreteCorreios implements EstrategiaFrete {
    @Override
    public double calcular(Pedido pedido) {
        // Tabela simplificada por peso
        return 10.0 + pedido.getPesoTotal() * 1.5;
    }
}

public class FreteTransportadora implements EstrategiaFrete {
    @Override
    public double calcular(Pedido pedido) {
        // Tabela simplificada por valor
        return pedido.getValorTotal() * 0.05;
    }
}

public class FreteRetirada implements EstrategiaFrete {
    @Override
    public double calcular(Pedido pedido) {
        return 0.0;
    }
}

public class CalculadoraFrete {
    private EstrategiaFrete estrategia;

    public void setEstrategia(EstrategiaFrete e) {
        this.estrategia = e;
    }

    public double calcularFrete(Pedido pedido) {
        return estrategia.calcular(pedido);
    }
}
```

**Uso:**

```java
CalculadoraFrete calc = new CalculadoraFrete();
calc.setEstrategia(new FreteCorreios());
double valor = calc.calcularFrete(pedido);
```

**Discussão:**

- Adicionar um quarto modo de frete (ex.: Frete Próprio com motorista da empresa) não exige tocar em `CalculadoraFrete` — basta criar uma nova classe que implementa `EstrategiaFrete`.
- Em testes unitários da `CalculadoraFrete`, podemos injetar uma `EstrategiaFrete` falsa (`p -> 42.0`) e verificar o comportamento isoladamente.
- Compare com a alternativa "ingênua" (um *enum* + `switch`): funciona, mas cada nova regra exige modificar o `switch`. Strategy externaliza essa decisão.

### Exemplo Resolvido 2 — Combinando Factory Method + Observer no e-commerce (Intermediário) ⭐⭐

**Enunciado:** Estenda o exemplo anterior. Agora, quando um pedido muda de estado para "PAGO", quatro coisas devem acontecer automaticamente:

1. O cliente recebe e-mail de confirmação.
2. O cliente recebe SMS.
3. O time de logística é notificado para iniciar separação.
4. O dashboard de BI registra a venda.

Adicionalmente, queremos permitir que, em ambientes diferentes (desenvolvimento, homologação, produção), os notificadores sejam *diferentes*: em desenvolvimento, ninguém quer mandar e-mails reais. Combine **Observer** (para a notificação) e **Factory Method** (para criar os observadores corretos por ambiente).

**Solução:**

**Estrutura conjunta:**

```
                  ┌─────────────────────────┐
                  │ FabricaObservers        │
                  │ (Simple Factory)        │
                  ├─────────────────────────┤
                  │ + criarParaPedido       │
                  │   (amb): List<Observer> │
                  └─────────────────────────┘
                            │ produz
                            ▼
        ┌────────────────────┐    ┌────────────────────┐
        │ Pedido             │◇──▶│ <<interface>>      │
        │ (Subject)          │  * │ Observer           │
        └────────────────────┘    └────────────────────┘
                                            △
                  ┌─────────────────────────┼──────────────────────┐
                  │                         │                      │
            EmailReal                  EmailFake              ServLogistica
            (produção)              (desenvolvimento)
            SMSReal                    SMSFake                BIReal
```

**Código:**

```java
public enum Ambiente { DESENVOLVIMENTO, HOMOLOGACAO, PRODUCAO }

public class FabricaObservers {

    public static List<Observer> criarParaPedido(Ambiente amb) {
        List<Observer> observers = new ArrayList<>();

        // Notificações ao cliente: troca implementação por ambiente
        if (amb == Ambiente.PRODUCAO) {
            observers.add(new NotificadorEmailReal());
            observers.add(new NotificadorSMSReal());
        } else {
            // Em dev/homolog, usa fakes que só logam
            observers.add(new NotificadorEmailFake());
            observers.add(new NotificadorSMSFake());
        }

        // Estes não dependem de ambiente externo
        observers.add(new ServicoLogistica());
        observers.add(new RegistradorBI());

        return observers;
    }
}
```

**Uso conjunto:**

```java
Ambiente amb = Ambiente.PRODUCAO; // viria de configuração
Pedido pedido = new Pedido();

// Factory Method cria os observadores apropriados
for (Observer o : FabricaObservers.criarParaPedido(amb)) {
    pedido.adicionarObserver(o);
}

// Quando o estado muda, Observer entra em ação
pedido.marcarComoPago();
```

**Discussão:**

- **Por que dois padrões juntos?** Observer resolve o problema de notificar muitos. Factory Method resolve o problema de *criar os notificadores corretos* — uma decisão que varia por ambiente. Sem a fábrica, todo cliente teria que repetir o `if (amb == ...) ...`. Com a fábrica, a decisão fica em um único lugar.
- **Acoplamento controlado**: `Pedido` continua sem saber de e-mail, SMS ou BI. A `FabricaObservers` é o único ponto que conhece todas as classes concretas.
- **Testabilidade**: para testar `Pedido`, podemos injetar uma lista vazia de observadores ou uma lista com mocks. Não há nenhuma chamada estática "escondida".
- **Conexão com aulas anteriores**: a transição "marcar como pago" corresponde a uma transição da máquina de estados que vimos. Os observers correspondem a ações de *entry* daquele estado, ou a sinais enviados a partir dele. Padrões são, em larga medida, formas de *implementar* o que diagramas comportamentais especificam.

> 💡 **Intuição:** Padrões raramente vivem sozinhos em sistemas reais — eles se combinam. Aprender padrões isoladamente é fase 1; aprender a combiná-los é fase 2; aprender quando *não* aplicá-los é fase 3.
> 

---

## Exercícios Práticos

### Exercício 1 — Strategy para cálculo de desconto ⭐

Em um sistema de e-commerce, a regra de desconto de um pedido pode variar:

- **Sem desconto**: o cliente paga o valor cheio.
- **Cliente VIP**: 10% de desconto sobre o total.
- **Cupom promocional**: desconto fixo em reais (ex.: R$ 20).
- **Black Friday**: 25% de desconto, mas apenas se o total ultrapassar R$ 500.

Sua tarefa:

(a) Modele em **diagrama de classes UML** uma solução aplicando o padrão Strategy. Identifique claramente o *Context*, a interface *Strategy* e as *ConcreteStrategies*.
(b) Implemente em Java (ou linguagem equivalente) a interface e ao menos duas estratégias concretas.
(c) Mostre, em um pequeno fragmento de código de uso, como um cliente usa diferentes estratégias para o mesmo pedido.
(d) Discuta em 3-5 linhas: este é um caso em que a versão "lambda" de Strategy seria adequada, ou as classes verbosas têm alguma vantagem? Justifique.

### Exercício 2 — Sistema de notificação de leilão ⭐⭐

Considere uma plataforma de leilões online com a seguinte regra:

> Cada leilão tem um item, um valor inicial e uma lista de participantes que se inscreveram para receber alertas. Quando alguém dá um lance, todos os participantes inscritos devem ser notificados (e-mail, push, SMS, conforme preferência de cada um). Quando o leilão termina (por tempo expirado), todos recebem o resultado final. Cada participante deve poder se inscrever e desinscrever de qualquer leilão a qualquer momento. Há ainda *bots de monitoramento* (do sistema antifraude e de BI) que precisam ser notificados de cada lance, mas que não são "participantes" propriamente — são processos automáticos.
> 

Sua tarefa:

(a) Modele em UML uma solução aplicando **Observer** para a notificação. Indique as interfaces, classes concretas e relacionamentos com multiplicidade.
(b) Aplique também **Strategy** dentro de cada participante para representar suas preferências de notificação (e-mail/push/SMS) — de modo que o mesmo `Participante` possa, em tempo de execução, mudar como recebe alertas.
(c) Aplique **Factory Method** (variante simples) para encapsular a criação dos `bots de monitoramento` — eles devem ser plugados automaticamente em cada leilão criado.
(d) Implemente o esqueleto em Java (não precisa lógica completa, apenas as classes, interfaces e métodos com comentários explicativos).
(e) Justifique em prosa, em pelo menos 5 linhas: **(i)** por que cada padrão foi necessário (e o que aconteceria se você não usasse); **(ii)** identifique pelo menos um *risco* de aplicar Observer aqui (volte à discussão de "consequências" do padrão) e proponha uma mitigação.

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Gamma, E.; Helm, R.; Johnson, R.; Vlissides, J. *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley, 1994. (O "livro da Gang of Four". Referência canônica.)
- Freeman, E.; Robson, E.; Bates, B.; Sierra, K. *Head First Design Patterns*. 2ª ed., O'Reilly, 2020. (Apresentação didática, ideal para primeira leitura.)
- Kerievsky, J. *Refactoring to Patterns*. Addison-Wesley, 2004. (Discute a aplicação evolutiva de padrões — origem do termo *patternitis*.)
- Fowler, M. *Refactoring: Improving the Design of Existing Code*. 2ª ed., Addison-Wesley, 2018. (Complemento natural a padrões.)
- Martin, R. *Clean Architecture*. Pearson, 2017. (Discute SOLID e a aplicação estrutural de padrões.)
- Guerra, E. *Design Patterns com Java: Projeto orientado a objetos guiado por padrões*. Casa do Código, 2014. (Tratamento em português, com exemplos contextualizados.)
- Alexander, C. *A Pattern Language*. Oxford University Press, 1977. (Origem da ideia de "padrões". Leitura cultural recomendada.)

---