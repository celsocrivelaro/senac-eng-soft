# Padrões de Projeto 2 - Estruturais

## 1. O que une os padrões estruturais?

Padrões criacionais respondem à pergunta *"como criar?"*. Padrões comportamentais respondem *"como interagir e distribuir responsabilidades?"*. Padrões **estruturais** respondem a uma terceira pergunta:

> *Como compor classes e objetos em estruturas maiores, mantendo essas estruturas flexíveis e eficientes?*
> 

Eles abordam quatro situações típicas:

- **Incompatibilidade de interface** (Adapter): "tenho A, preciso de B".
- **Complexidade de subsistemas** (Facade): "esconda essa bagunça e me dê uma porta única".
- **Extensão dinâmica de comportamento** (Decorator): "quero adicionar funcionalidade sem usar herança".
- **Tratamento uniforme de hierarquias** (Composite): "quero tratar a parte e o todo da mesma forma".

> 💡 **Intuição:** Os padrões estruturais são, em larga medida, *padrões de relacionamento*. Eles não inventam novos comportamentos; rearranjam objetos existentes em configurações que melhoram alguma propriedade — desacoplamento, simplicidade de uso, flexibilidade de composição. Em um diagrama UML, eles aparecem com mais setas e composições do que classes novas.
> 

---

## 2. Adapter (Adaptador)

### 2.1 Intenção

> Converter a interface de uma classe em outra interface esperada pelos clientes. Adapter permite que classes trabalhem juntas, apesar de interfaces incompatíveis.
> 

### 2.2 Motivação

No nosso e-commerce, definimos a interface `FormaPagamento` com o método `cobrar(double valor)`. Mas, ao integrar com o gateway PayPal, descobrimos que a API deles é diferente:

```java
// Nossa interface, definida internamente:
public interface FormaPagamento {
    void cobrar(double valor);
}

// SDK do PayPal, fora do nosso controle:
public class PayPalSDK {
    public Resposta autorizarPagamento(MoneyAmount amount, String currency) {
        // ... implementação do PayPal ...
    }
}
```

As duas interfaces são incompatíveis: nomes diferentes, parâmetros diferentes, retornos diferentes. Não podemos modificar o SDK do PayPal (é uma biblioteca externa), e não queremos modificar nosso sistema só por causa de um fornecedor. Solução: **um adaptador**.

### 2.3 Estrutura

Há duas variantes — *Adapter de classe* (via herança múltipla) e *Adapter de objeto* (via composição). Como Java não suporta herança múltipla, usamos a versão por composição, que é também a mais flexível:

```
        ┌─────────────────────┐         ┌──────────────────────┐
        │     Cliente         │────────▶│ <<interface>>        │
        │                     │         │ FormaPagamento  (Alvo)│
        └─────────────────────┘         ├──────────────────────┤
                                        │ + cobrar(v: double)  │
                                        └──────────────────────┘
                                                  △
                                                  │ implements
                                                  │
                                        ┌──────────────────────┐         ┌─────────────┐
                                        │ PayPalAdapter        │◇────────│ PayPalSDK   │
                                        │ (Adapter)            │         │ (Adaptee)   │
                                        ├──────────────────────┤         └─────────────┘
                                        │ - sdk: PayPalSDK     │
                                        │ + cobrar(v: double)  │
                                        └──────────────────────┘
```

### 2.4 Código

```java
public class PayPalAdapter implements FormaPagamento {
    private final PayPalSDK sdk;
    private final String moedaPadrao;

    public PayPalAdapter(PayPalSDK sdk, String moedaPadrao) {
        this.sdk = sdk;
        this.moedaPadrao = moedaPadrao;
    }

    @Override
    public void cobrar(double valor) {
        // Traduz a chamada do "nosso jeito" para o "jeito PayPal"
        MoneyAmount montante = new MoneyAmount(valor);
        Resposta resp = sdk.autorizarPagamento(montante, moedaPadrao);

        if (!resp.foiAprovada()) {
            throw new PagamentoRecusadoException(resp.getMotivo());
        }
    }
}
```

**Uso:**

```java
PayPalSDK sdk = new PayPalSDK(/* credenciais */);
FormaPagamento f = new PayPalAdapter(sdk, "BRL");
pedido.setFormaPagamento(f);  // O Pedido nem sabe que é PayPal por trás
pedido.finalizar();
```

### 2.5 Consequências

**Benefícios:**

- **Reuso de classes existentes** com interfaces "erradas".
- **Isolamento de dependências externas**: a única classe que conhece a API do PayPal é o adaptador. Trocar de gateway no futuro afeta um único arquivo.
- **Facilita testes**: o adaptador é uma camada *fina* — fácil de testar, mas mais ainda, fácil de *substituir por um mock* nos testes do `Pedido`.

**Custos:**

- **Mais uma camada de indireção**: o desenvolvedor precisa "saltar" pelo adaptador para entender o que está acontecendo.
- **Risco de tradução incompleta**: se a API adaptada tem comportamentos sutis (ex.: códigos de erro específicos), o adaptador pode "esconder" informação importante.

> 💡 **Intuição:** Adapter é o padrão mais usado em projetos que se conectam com sistemas legados ou bibliotecas de terceiros — ou seja, em *quase todos os projetos reais*. Quando você usa um ORM que mapeia banco relacional para objetos, está vendo Adapter em escala industrial. Quando o `java.util.Arrays.asList(array)` te dá uma `List`, é Adapter.
> 

---

## 3. Facade (Fachada)

### 3.1 Intenção

> Fornecer uma interface unificada para um conjunto de interfaces em um subsistema. Facade define uma interface de alto nível que torna o subsistema mais fácil de usar.
> 

### 3.2 Motivação

Considere o que acontece quando um cliente clica em "Finalizar Compra" no nosso e-commerce. Internamente, é necessário:

1. Validar o estoque de cada item (`EstoqueService`).
2. Calcular impostos (`TributacaoService`).
3. Calcular o frete (`CalculadoraFrete` — que vimos na aula passada).
4. Aplicar cupons de desconto (`PromocaoService`).
5. Cobrar o pagamento (`FormaPagamento`).
6. Reservar os itens em estoque (`EstoqueService` de novo).
7. Disparar evento de pedido criado (que aciona Observer, lembra?).
8. Enviar e-mail de confirmação (`NotificacaoService`).

Se cada controller (web, mobile, admin, API pública) tiver que orquestrar essa coreografia, teremos **duplicação massiva** e **acoplamento alto** com cada um dos oito serviços. A solução é introduzir uma **fachada**:

### 3.3 Estrutura

```
   ┌────────────────────────┐
   │      Cliente           │
   │ (Controller, REST API) │
   └────────────────────────┘
              │
              │ chama apenas isso
              ▼
   ┌──────────────────────────────┐
   │     CheckoutFacade           │
   │                              │
   │ + finalizarCompra(carrinho,  │
   │     cliente, formaPgto): ... │
   └──────────────────────────────┘
              │
   ┌──────────┼─────────┬──────────┬──────────┐
   ▼          ▼         ▼          ▼          ▼
 Estoque   Tribut.   Frete    Promocao   Pagamento ...
 Service   Service   Service  Service    Service
   (Subsistema)
```

### 3.4 Código

```java
public class CheckoutFacade {

    private final EstoqueService estoque;
    private final TributacaoService tributacao;
    private final CalculadoraFrete frete;
    private final PromocaoService promocao;
    private final NotificacaoService notificacao;

    // Construtor com injeção de dependências
    public CheckoutFacade(EstoqueService estoque, TributacaoService tributacao,
                          CalculadoraFrete frete, PromocaoService promocao,
                          NotificacaoService notificacao) {
        this.estoque = estoque;
        this.tributacao = tributacao;
        this.frete = frete;
        this.promocao = promocao;
        this.notificacao = notificacao;
    }

    public Pedido finalizarCompra(Carrinho carrinho, Cliente cliente,
                                   FormaPagamento formaPgto) {
        // 1. Validações
        estoque.validarDisponibilidade(carrinho.getItens());

        // 2. Cálculos
        double subtotal = carrinho.calcularSubtotal();
        double imposto = tributacao.calcular(subtotal, cliente.getEndereco());
        double valorFrete = frete.calcular(carrinho, cliente.getEndereco());
        double desconto = promocao.calcularDesconto(carrinho, cliente);
        double total = subtotal + imposto + valorFrete - desconto;

        // 3. Cobrança
        formaPgto.cobrar(total);

        // 4. Persistência e efeitos
        Pedido pedido = new Pedido(cliente, carrinho.getItens(), total);
        estoque.reservar(carrinho.getItens());
        pedido.marcarComoPago();  // Dispara observers (e-mail, BI, etc.)

        return pedido;
    }
}
```

**Uso pelo controller web:**

```java
// Controller fica trivial
@PostMapping("/checkout")
public PedidoDTO checkout(@RequestBody CheckoutRequest req) {
    Pedido p = checkoutFacade.finalizarCompra(req.getCarrinho(),
                                              req.getCliente(),
                                              req.getFormaPgto());
    return PedidoDTO.from(p);
}
```

### 3.5 Adapter vs Facade — não confunda

Os dois padrões têm aparência similar — ambos são "caras de fora" que escondem complexidade interna —, mas resolvem problemas distintos:

| Aspecto | Adapter | Facade |
| --- | --- | --- |
| **Problema** | Interface incompatível | Interface complexa demais |
| **Quantos objetos esconde?** | Geralmente um (o *adaptee*) | Vários (um subsistema inteiro) |
| **Inventa interface nova?** | Não — implementa uma já existente que o cliente espera | Sim — define uma interface simplificada de alto nível |
| **Quem aparece no UML?** | O cliente fala com a *interface alvo*, não com o adaptador diretamente | O cliente fala com a *fachada* explicitamente |

> 💡 **Intuição:** Adapter é um *tradutor*; Facade é um *concierge*. O tradutor existe porque você e seu interlocutor falam línguas diferentes e foi preciso achar um intermediário. O concierge existe porque você não quer (e não precisa) entender a complexidade interna do hotel — basta dizer o que você quer.
> 

### 3.6 Consequências

**Benefícios:**

- **Reduz acoplamento** entre clientes e subsistemas: clientes dependem da fachada, não dos serviços internos.
- **Encapsula complexidade**: o subsistema pode evoluir internamente sem quebrar clientes.
- **Ponto natural para *cross-cutting concerns***: logging, métricas, transações — tudo cabe na fachada.

**Custos:**

- **Pode virar uma "deus-classe"** (*God object*) se a fachada inchar e absorver demais.
- **Não esconde *de fato*** — clientes que precisam de mais controle podem (e às vezes precisam) acessar diretamente o subsistema. Isso quebra o "ideal" da fachada como única porta.

---

## 4. Decorator (Decorador)

### 4.1 Intenção

> Anexar responsabilidades adicionais a um objeto dinamicamente. Decorator oferece uma alternativa flexível ao uso de herança para estender funcionalidade.
> 

### 4.2 Motivação

Imagine que, no checkout, o cliente pode adicionar **opcionais** ao pedido: embalagem para presente (+R$ 10), entrega expressa (+R$ 25), seguro contra roubo (+5% do total), e mensagem personalizada (+R$ 5).

Solução ingênua via herança seria desastrosa: para combinar todas as opções, precisaríamos de classes como `PedidoComPresenteEExpressoESeguro`, e para *N* opcionais teríamos *2^N* classes. Inviável.

A herança é estática (decidida em compilação), mas as combinações de opcionais são dinâmicas (decididas pelo cliente em tempo de execução). Precisamos de uma solução que **componha** comportamentos em tempo de execução.

### 4.3 Estrutura

```
         ┌────────────────────────────────┐
         │  <<interface>>                 │
         │  ItemCobrável (Component)      │
         ├────────────────────────────────┤
         │ + getDescricao(): String       │
         │ + getValor(): double           │
         └────────────────────────────────┘
                        △
              ┌─────────┴────────────────────────┐
              │                                  │
   ┌──────────────────────┐         ┌──────────────────────────┐
   │ PedidoBase           │         │ DecoradorPedido          │
   │ (ConcreteComponent)  │         │ (Decorator, abstract)    │
   ├──────────────────────┤         ├──────────────────────────┤
   │ - itens: List<Item>  │         │ # decorado: ItemCobrável │
   │ + getDescricao()     │         │ + getDescricao()         │
   │ + getValor()         │         │ + getValor()             │
   └──────────────────────┘         └──────────────────────────┘
                                              △
                              ┌───────────────┼───────────────┐
                              │               │               │
                      EmbalagemPresente  EntregaExpressa   Seguro
                      (ConcreteDec.)    (ConcreteDec.)   (ConcreteDec.)
```

A chave conceitual: o decorador **implementa a mesma interface** que decora, e **mantém uma referência** ao objeto decorado. Suas operações tipicamente delegam ao decorado e somam algo extra.

### 4.4 Código

**Componente (interface):**

```java
public interface ItemCobravel {
    String getDescricao();
    double getValor();
}
```

**Componente concreto:**

```java
public class PedidoBase implements ItemCobravel {
    private final List<Produto> itens;

    public PedidoBase(List<Produto> itens) {
        this.itens = itens;
    }

    @Override
    public String getDescricao() {
        return "Pedido com " + itens.size() + " item(ns)";
    }

    @Override
    public double getValor() {
        return itens.stream().mapToDouble(Produto::getPreco).sum();
    }
}
```

**Decorador abstrato:**

```java
public abstract class DecoradorPedido implements ItemCobravel {
    protected final ItemCobravel decorado;

    public DecoradorPedido(ItemCobravel decorado) {
        this.decorado = decorado;
    }
}
```

**Decoradores concretos:**

```java
public class EmbalagemPresente extends DecoradorPedido {
    public EmbalagemPresente(ItemCobravel decorado) {
        super(decorado);
    }

    @Override
    public String getDescricao() {
        return decorado.getDescricao() + " + embalagem para presente";
    }

    @Override
    public double getValor() {
        return decorado.getValor() + 10.0;
    }
}

public class EntregaExpressa extends DecoradorPedido {
    public EntregaExpressa(ItemCobravel decorado) {
        super(decorado);
    }

    @Override
    public String getDescricao() {
        return decorado.getDescricao() + " + entrega expressa";
    }

    @Override
    public double getValor() {
        return decorado.getValor() + 25.0;
    }
}

public class Seguro extends DecoradorPedido {
    public Seguro(ItemCobravel decorado) {
        super(decorado);
    }

    @Override
    public String getDescricao() {
        return decorado.getDescricao() + " + seguro contra roubo";
    }

    @Override
    public double getValor() {
        return decorado.getValor() * 1.05;  // 5% sobre o total acumulado
    }
}
```

**Uso:**

```java
ItemCobravel pedido = new PedidoBase(itens);
pedido = new EmbalagemPresente(pedido);
pedido = new EntregaExpressa(pedido);
pedido = new Seguro(pedido);

System.out.println(pedido.getDescricao());
// → "Pedido com 3 item(ns) + embalagem para presente + entrega expressa + seguro contra roubo"

System.out.println(pedido.getValor());
// → ((subtotal + 10) + 25) * 1.05
```

> 💡 **Intuição:** Note como o decorador é uma "camada" envolvendo a anterior — exatamente como, em um pacote real, você embrulha o item, depois embrulha em papel de presente, depois em uma caixa, depois em uma sacola. Cada camada adiciona algo *e delega* ao que está por baixo. Esse é o coração mecânico do padrão.
> 

### 4.5 Cuidado com a ordem!

Observe atentamente o cálculo de `Seguro`: `decorado.getValor() * 1.05`. Se o `Seguro` for o decorador *mais externo*, ele cobra 5% sobre o subtotal **mais** os outros opcionais. Se for aplicado *primeiro* (mais interno), o cálculo é diferente.

Em padrões cuja semântica depende da ordem de aplicação, **a ordem é parte do contrato**. Decorator não impõe ordem; cabe ao designer documentar e validar. Esta é uma armadilha comum em iniciantes.

### 4.6 Consequências

**Benefícios:**

- **Composição em tempo de execução**: o cliente decide a combinação de opcionais.
- **Princípio Aberto/Fechado em ação**: novos opcionais → novas classes; nenhuma modificação em código existente.
- **Evita explosão de subclasses** que herança traria.
- Exemplo industrial canônico: o `java.io` — `BufferedReader(new InputStreamReader(new FileInputStream(arquivo)))`.

**Custos:**

- **Identidade de objeto pode confundir**: o objeto decorado *não é* o objeto base. Se algum código fizer `instanceof PedidoBase`, vai falhar. Decorator combina mal com lógica baseada em `instanceof`.
- **Muitas classes pequenas**: cada opcional é uma classe nova, e debugar a pilha de chamadas com cinco decoradores aninhados pode ser desafiador.

---

## 5. Composite (Composto)

### 5.1 Intenção

> Compor objetos em estruturas de árvore para representar hierarquias parte-todo. Composite permite que clientes tratem objetos individuais e composições de objetos uniformemente.
> 

### 5.2 Motivação

Lembre-se do exemplo de e-commerce na aula de Diagrama de Classes: havia uma `Categoria` que podia conter outras categorias (autoassociação `0..1` para o pai, `*` para filhas). Agora suponha que precisamos:

- Calcular o **número total de produtos** em uma categoria, *incluindo* recursivamente todas as suas subcategorias.
- Listar **todos os produtos** de uma categoria, recursivamente.
- Calcular o **valor médio** dos produtos em uma categoria.

Sem padrão, o código que percorre a árvore precisa diferenciar "categoria com subcategorias" de "categoria folha", "produto individual" — usando muitos `if`s e `instanceof`. O Composite elimina essa distinção.

### 5.3 Estrutura

```
              ┌─────────────────────────────┐
              │ <<interface>>               │
              │ ComponenteCatalogo          │
              │ (Component)                 │
              ├─────────────────────────────┤
              │ + getNome(): String         │
              │ + contarProdutos(): int     │
              │ + listarProdutos(): List    │
              └─────────────────────────────┘
                          △
              ┌───────────┴───────────┐
              │                       │
   ┌──────────────────┐    ┌────────────────────────┐
   │ Produto          │    │ Categoria              │
   │ (Leaf)           │    │ (Composite)            │
   ├──────────────────┤    ├────────────────────────┤
   │ - nome           │    │ - nome                 │
   │ - preco          │    │ - filhos: List<...>    │◇┐
   ├──────────────────┤    ├────────────────────────┤ │
   │ + contarProdutos │    │ + contarProdutos()     │ │
   │   () return 1    │    │ + listarProdutos()     │ │
   │ + listar() ret.  │    │ + adicionar(c)         │ │
   │   [this]         │    │ + remover(c)           │ │
   └──────────────────┘    └────────────────────────┘ │
                                       │              │
                                       └──────────────┘
                                       (auto-referência: Categoria contém ComponenteCatalogo)
```

### 5.4 Código

```java
public interface ComponenteCatalogo {
    String getNome();
    int contarProdutos();
    List<Produto> listarProdutos();
}
```

**Folha:**

```java
public class Produto implements ComponenteCatalogo {
    private final String nome;
    private final double preco;

    public Produto(String nome, double preco) {
        this.nome = nome;
        this.preco = preco;
    }

    @Override
    public String getNome() {
        return nome;
    }

    @Override
    public int contarProdutos() {
        return 1;  // Eu sou um produto, conto como 1
    }

    @Override
    public List<Produto> listarProdutos() {
        return List.of(this);
    }

    public double getPreco() {
        return preco;
    }
}
```

**Composto:**

```java
public class Categoria implements ComponenteCatalogo {
    private final String nome;
    private final List<ComponenteCatalogo> filhos = new ArrayList<>();

    public Categoria(String nome) {
        this.nome = nome;
    }

    public void adicionar(ComponenteCatalogo c) {
        filhos.add(c);
    }

    public void remover(ComponenteCatalogo c) {
        filhos.remove(c);
    }

    @Override
    public String getNome() {
        return nome;
    }

    @Override
    public int contarProdutos() {
        // Soma a contagem de cada filho — não importa se é folha ou composto
        return filhos.stream().mapToInt(ComponenteCatalogo::contarProdutos).sum();
    }

    @Override
    public List<Produto> listarProdutos() {
        return filhos.stream()
                     .flatMap(c -> c.listarProdutos().stream())
                     .collect(Collectors.toList());
    }
}
```

**Uso:**

```java
Categoria eletronicos = new Categoria("Eletrônicos");
Categoria celulares = new Categoria("Celulares");
Categoria notebooks = new Categoria("Notebooks");

celulares.adicionar(new Produto("iPhone 15", 7000));
celulares.adicionar(new Produto("Pixel 8", 5000));
notebooks.adicionar(new Produto("MacBook Pro", 18000));

eletronicos.adicionar(celulares);
eletronicos.adicionar(notebooks);

// Cliente trata categoria-raiz como qualquer outra
System.out.println(eletronicos.contarProdutos());  // 3
System.out.println(eletronicos.listarProdutos());  // [iPhone 15, Pixel 8, MacBook Pro]
```

### 5.5 A questão das operações específicas do composto

Há uma decisão de design clássica em Composite: métodos como `adicionar(filho)` e `remover(filho)` só fazem sentido em compostos, não em folhas. Há duas escolas:

- **(a) Transparência** (defendida pelo GoF): coloque `adicionar`/`remover` na interface `Component`. Folhas implementam lançando `UnsupportedOperationException`. **Vantagem:** clientes tratam tudo uniformemente. **Desvantagem:** quebra-se em tempo de execução; viola o Princípio de Substituição de Liskov (LSP).
- **(b) Segurança de tipos**: deixe `adicionar`/`remover` apenas em `Composite`. Clientes que precisam montar estruturas usam o tipo `Composite`; clientes que apenas leem usam o tipo `Component`. **Vantagem:** segurança em compilação. **Desvantagem:** clientes ocasionalmente precisam fazer `cast` ou `instanceof`.

Optei pela **opção (b)** no código acima, que é a recomendação moderna em código "tipado de verdade". A diferença é sutil mas relevante para discussões avançadas de design.

### 5.6 Consequências

**Benefícios:**

- **Uniformidade de tratamento**: clientes não precisam saber se estão lidando com uma folha ou um composto. Código de leitura fica drasticamente mais limpo.
- **Suporte natural a recursão**: operações que precisariam de `for` aninhado viram chamadas polimórficas elegantes.
- **Aderência ao OCP**: novos tipos de componentes podem ser introduzidos sem mexer em código existente.

**Custos:**

- **Pode tornar o design "permissivo demais"**: como qualquer `Component` pode estar em qualquer lugar, restrições semânticas (ex.: "uma categoria só pode conter produtos de uma mesma marca") precisam ser aplicadas em tempo de execução.
- **Quando aplicado com transparência (opção a)**, viola o LSP — questão filosófica que pode causar problemas em hierarquias maiores.

> 💡 **Intuição:** Composite é o padrão da árvore. Onde quer que você veja uma estrutura recursiva — sistema de arquivos, DOM HTML, organização hierárquica de uma empresa, menus aninhados de um sistema, expressões matemáticas (`(2 + (3 * 4))`) — tem chance de ser Composite. É um dos padrões mais "naturais" porque o mundo está cheio de hierarquias parte-todo.
> 

---

## 6. Os outros padrões estruturais (visão sumária)

A família estrutural do GoF tem três padrões adicionais que merecem ao menos menção:

| Padrão | Intenção em uma frase | Quando você o veria |
| --- | --- | --- |
| **Bridge** | Separar uma abstração de sua implementação, para que ambas variem independentemente. | Sistemas com duas dimensões de variação (ex.: `Forma` × `RendererGráfico`). |
| **Flyweight** | Compartilhar eficientemente um grande número de objetos de granularidade fina. | Editores de texto (cada caractere como objeto), engines de jogos (sprites), pools de conexões. |
| **Proxy** | Fornecer um substituto ou *placeholder* para outro objeto, controlando o acesso a ele. | Lazy loading de ORMs, RPC remoto (proxies de rede), proxies de segurança/permissão, *caching*. |

Em uma aula avançada, cada um destes merece tratamento próprio. **Proxy**, em particular, é onipresente em sistemas reais — se você usa Hibernate ou Spring, está rodeado de proxies dinâmicos sem perceber.

---

## 7. Quando aplicar cada um — guia rápido

| Situação típica | Padrão a considerar |
| --- | --- |
| "Tenho uma classe que faz o que preciso, mas a interface não bate com a esperada." | **Adapter** |
| "Meus clientes precisam coordenar 5 serviços para uma operação comum. Quero esconder isso." | **Facade** |
| "Quero adicionar opcionais combináveis a um objeto, sem criar 2^N subclasses." | **Decorator** |
| "Tenho uma estrutura em árvore e quero tratar nós e folhas uniformemente." | **Composite** |
| "Uma única abstração tem múltiplas implementações *e* a hierarquia inflou demais." | **Bridge** |
| "Tenho milhões de objetos pequenos e a memória está estourando." | **Flyweight** |
| "Quero controlar acesso/criação/transparência de rede sem mudar a interface vista pelo cliente." | **Proxy** |

---

## 8. Boas práticas e armadilhas comuns

1. **Não confunda Adapter com Facade.** O cheiro de "tenho uma API esquisita" pede Adapter; o cheiro de "tem um monte de coisa para chamar" pede Facade.
2. **Decorator e herança fazem mau casamento.** Se sua hierarquia já tem 3+ níveis de herança e você quer adicionar Decorator por cima, repense — provavelmente precisa de uma reformulação maior.
3. **Composite só vale se há operações realmente uniformes.** Se você sempre precisa diferenciar folha de composto na lógica de cliente, o padrão não está pagando seu custo.
4. **Cuidado com Decorators que mudam *a interface***. Se um decorador adiciona métodos novos ao componente, ele deixa de ser um decorador "puro" e se aproxima de outro padrão — pense bem antes.
5. **Facade não impede acesso ao subsistema** — em geral, programadores podem (e às vezes devem) ir direto. Trate-a como conveniência, não como prisão.
6. **Em todos eles, evite encadeamentos longos demais.** Cinco decoradores aninhados ou uma árvore Composite com 12 níveis tornam o código hostil para quem chega depois.

---

## 9. Exemplos resolvidos

### Exemplo Resolvido 1 — Decorator para opcionais de pedido (Introdutório) ⭐

**Enunciado:** Já vimos a estrutura geral do Decorator no e-commerce. Estenda o exemplo da Seção 4.4 com um quarto opcional: **mensagem personalizada** (R$ 5 fixo, e que adiciona "(com mensagem personalizada)" à descrição). Em seguida, aplique uma combinação específica e analise por que a ordem de aplicação importa.

**Solução:**

**Novo decorador:**

```java
public class MensagemPersonalizada extends DecoradorPedido {
    private final String mensagem;

    public MensagemPersonalizada(ItemCobravel decorado, String mensagem) {
        super(decorado);
        this.mensagem = mensagem;
    }

    @Override
    public String getDescricao() {
        return decorado.getDescricao() + " (com mensagem personalizada)";
    }

    @Override
    public double getValor() {
        return decorado.getValor() + 5.0;
    }

    public String getMensagem() {
        return mensagem;
    }
}
```

**Cenário 1 — Seguro como camada mais externa:**

```java
ItemCobravel p = new PedidoBase(itens);          // R$ 100
p = new EmbalagemPresente(p);                    // R$ 110
p = new MensagemPersonalizada(p, "Feliz Natal"); // R$ 115
p = new Seguro(p);                               // R$ 115 * 1.05 = R$ 120,75
```

**Cenário 2 — Seguro como camada mais interna:**

```java
ItemCobravel p = new PedidoBase(itens);          // R$ 100
p = new Seguro(p);                               // R$ 105
p = new EmbalagemPresente(p);                    // R$ 115
p = new MensagemPersonalizada(p, "Feliz Natal"); // R$ 120
```

**Análise:**

A diferença final é R$ 0,75. Pequena? Sim. Significativa? Para a auditoria fiscal, sim. Para a transparência ao cliente ("o seguro foi calculado *antes* ou *depois* do opcional de embalagem?"), também.

**Lições:**

- Decoradores **não são comutativos** quando algum deles aplica operações multiplicativas/percentuais sobre operações aditivas. A ordem importa — e essa é uma decisão de **regra de negócio**, não puramente técnica.
- Em uma implementação real, você documentaria explicitamente a ordem canônica (por exemplo: opcionais aditivos primeiro, depois percentuais) e/ou expressaria a regra através de uma fábrica que sempre monta os decoradores na mesma ordem.

### Exemplo Resolvido 2 — Composite + Adapter integrando catálogo legado (Intermediário) ⭐⭐

**Enunciado:** Nosso e-commerce comprou uma rede menor concorrente. Eles têm um sistema de catálogo legado escrito em outra estrutura — não usam classes `Categoria` ou `Produto`, mas uma estrutura em forma de XML hierárquico, exposta por uma API Java legada chamada `LegacyCatalogAPI`.

A `LegacyCatalogAPI` oferece os métodos:

```java
public class LegacyCatalogAPI {
    public LegacyNode fetchRoot();
    // LegacyNode tem getChildren(), getName(), getType() ("CATEGORY" ou "ITEM"),
    //                getItemPrice() (só faz sentido se type for "ITEM")
}
```

Queremos integrar esse catálogo legado ao nosso sistema, de modo que toda a interface Composite (`ComponenteCatalogo`) já existente continue funcionando — clientes que faziam `eletronicos.contarProdutos()` agora podem fazer `catalogoLegado.contarProdutos()` sem perceber a diferença.

**Solução:**

A combinação aqui é elegante: usamos **Adapter** para fazer um `LegacyNode` parecer um `ComponenteCatalogo`, aproveitando que a estrutura em árvore do legado naturalmente se mapeia para o nosso Composite.

**Adaptador:**

```java
public class LegacyNodeAdapter implements ComponenteCatalogo {
    private final LegacyNode node;

    public LegacyNodeAdapter(LegacyNode node) {
        this.node = node;
    }

    @Override
    public String getNome() {
        return node.getName();
    }

    @Override
    public int contarProdutos() {
        if ("ITEM".equals(node.getType())) {
            return 1;
        }
        // É uma categoria: soma recursivamente
        return node.getChildren().stream()
                   .mapToInt(filho -> new LegacyNodeAdapter(filho).contarProdutos())
                   .sum();
    }

    @Override
    public List<Produto> listarProdutos() {
        if ("ITEM".equals(node.getType())) {
            return List.of(new Produto(node.getName(), node.getItemPrice()));
        }
        return node.getChildren().stream()
                   .flatMap(filho -> new LegacyNodeAdapter(filho).listarProdutos().stream())
                   .collect(Collectors.toList());
    }
}
```

**Integrando os dois mundos no mesmo composto:**

```java
Categoria raizUnificada = new Categoria("Catálogo Completo");

// Catálogo nativo (sistema novo)
Categoria eletronicos = new Categoria("Eletrônicos");
eletronicos.adicionar(new Produto("iPhone 15", 7000));
raizUnificada.adicionar(eletronicos);

// Catálogo legado, adaptado
LegacyCatalogAPI api = new LegacyCatalogAPI();
ComponenteCatalogo legadoAdaptado = new LegacyNodeAdapter(api.fetchRoot());
raizUnificada.adicionar(legadoAdaptado);

// Cliente trata uniformemente — não sabe que parte é nativa e parte é legada
System.out.println("Total de produtos no catálogo: " + raizUnificada.contarProdutos());
```

**Discussão das decisões:**

- **Por que dois padrões?** Composite estava lá antes — é a estrutura da nossa hierarquia. Adapter é necessário porque o sistema legado expõe **uma estrutura conceitualmente análoga** mas com **interface diferente**. A genialidade da combinação está em que o Adapter, ao ajustar a interface, permite que o legado *participe* da árvore Composite como um galho qualquer.
- **Lazy adaptation**: note que o adaptador cria novos `LegacyNodeAdapter` recursivamente *à medida que é navegado*. Não convertemos o catálogo legado inteiro de uma vez (o que poderia ser custoso); apenas envolvemos cada nó conforme ele é tocado. Isso é uma forma de *Proxy* implícita, na verdade — outro padrão estrutural se imiscuindo.
- **Limites do Adapter**: nosso `LegacyNodeAdapter` cria `Produto` na hora, dentro de `listarProdutos`. Isso significa que cada chamada gera novas instâncias. Em sistemas com restrições de memória ou identidade, isso pode ser um problema — e seria a hora de aplicar **Flyweight** para reusar instâncias.

> 💡 **Intuição:** Esse exemplo ilustra uma verdade pouco discutida em livros didáticos: **padrões estruturais têm afinidade entre si**. Adapter, Composite, Decorator, Proxy — todos usam composição com a mesma interface base. Aprender a vê-los como variações do mesmo princípio (em vez de sete coisas distintas) é um marco da maturidade em design OO.
> 

---

## Exercícios Práticos

### Exercício 1 — Adapter para sistema de logging legado ⭐

Sua empresa tem uma classe de logging antiga, herdada de um sistema dos anos 2000:

```java
public class LegacyLogger {
    public void writeLog(int severity, String message) {
        // 0=DEBUG, 1=INFO, 2=WARN, 3=ERROR
        // Escreve em arquivo de texto plano
    }
}
```

Sua equipe agora padroniza o logging via uma interface moderna:

```java
public interface Logger {
    void debug(String msg);
    void info(String msg);
    void warn(String msg);
    void error(String msg);
}
```

Sua tarefa:

(a) Modele em **diagrama de classes UML** uma solução com Adapter. Identifique claramente os papéis: `Logger` (alvo), `LegacyLogger` (adaptee), e o adaptador que você criará.
(b) Implemente o adaptador em Java. Cada método de `Logger` deve traduzir corretamente para a chamada `writeLog(...)`.
(c) Mostre, em código de uso, como um cliente que recebe `Logger logger` (parâmetro) pode receber transparentemente uma instância adaptada do legado.
(d) Discuta em 3-5 linhas: você consegue imaginar uma situação em que esse mesmo problema seria melhor resolvido com **Facade** em vez de Adapter? Justifique.

### Exercício 2 — Sistema de menus de restaurante: Composite + Decorator ⭐⭐

Um aplicativo de restaurantes precisa exibir o cardápio com a seguinte estrutura:

> O cardápio organiza-se em **seções** (Entradas, Pratos Principais, Sobremesas), que podem conter **itens** (pratos individuais) ou **subseções** (ex.: dentro de Pratos Principais, há "Massas", "Carnes", "Vegetarianos"). Cada item tem nome, descrição e preço. Adicionalmente, o app exibe **selos** sobre os itens: "vegano", "sem glúten", "destaque do chef" e "promoção de hoje" (que adiciona texto e modifica o preço — 20% off). Um mesmo prato pode ter múltiplos selos combinados (ex.: vegano + sem glúten + promoção do dia).
> 

Sua tarefa:

(a) Modele em UML uma solução combinando **Composite** (para a hierarquia de seções/subseções/itens) e **Decorator** (para os selos). Indique claramente as interfaces, classes concretas e relacionamentos.
(b) Implemente o esqueleto em Java: a interface `ItemMenu`, a folha `Prato`, o composto `SecaoMenu`, e ao menos dois decoradores (`SeloVegano`, `SeloPromocaoDoDia`).
(c) Mostre um trecho de código de uso construindo um cardápio pequeno (uma seção, com uma subseção, com dois pratos — sendo um deles decorado com dois selos).
(d) Justifique, em pelo menos 5 linhas: **(i)** por que você escolheu Composite (e não, digamos, listas aninhadas explicitamente) para a hierarquia; **(ii)** por que você escolheu Decorator (e não atributos booleanos como `isVegano`, `emPromocao`) para os selos; **(iii)** identifique uma situação em que sua escolha de Decorator poderia se mostrar inadequada — por exemplo, se houvesse uma regra de negócio que dois selos específicos *não pudessem* coexistir.

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Gamma, E.; Helm, R.; Johnson, R.; Vlissides, J. *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley, 1994. — Capítulo 4.
- Freeman, E.; Robson, E.; Bates, B.; Sierra, K. *Head First Design Patterns*. 2ª ed., O'Reilly, 2020. — Capítulos 3 (Decorator), 7 (Adapter, Facade) e 9 (Composite).
- Fowler, M. *Patterns of Enterprise Application Architecture*. Addison-Wesley, 2002. (Discute aplicações industriais de Adapter, Facade e Proxy em arquiteturas corporativas.)
- Bloch, J. *Effective Java*. 3ª ed., Addison-Wesley, 2018. — Item 18 ("Favor composition over inheritance"): justifica filosoficamente vários dos padrões desta aula.
- Guerra, E. *Design Patterns com Java: Projeto orientado a objetos guiado por padrões*. Casa do Código, 2014. — Capítulos sobre padrões estruturais (em português).
- Martin, R. *Agile Software Development, Principles, Patterns, and Practices*. Pearson, 2002. (Discute padrões à luz dos princípios SOLID.)

---

*Próxima aula: Padrões de Projeto III — aprofundamento nos padrões Comportamentais restantes (Command, Template Method, State, Iterator) e o impacto de padrões em refatoração.*