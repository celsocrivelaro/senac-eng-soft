# Testes de Software

## 1. Recapitulação rápida: por que testar?

Vimos em aulas anteriores os argumentos amplos para testar — confiança para refatorar, documentação executável, detecção precoce de regressões, *driver* de design via TDD. Esta aula assume esses motivos como dados.

A pergunta operacional desta aula é: **como se escreve um teste bem-feito?** Porque escrever testes sofríveis é igual a escrever código sofrível: gera dívida em vez de retorno.

> 💡 **Intuição:** Há uma diferença qualitativa entre “ter testes” e “ter testes bons”. Times com testes ruins frequentemente têm pior produtividade que times sem teste algum — porque testes ruins consomem tempo de manutenção, falham aleatoriamente, não detectam regressões reais, e ainda dão falsa sensação de segurança. **A meta não é cobertura; é confiança fundamentada.**
> 

---

## 2. Estrutura de um bom teste

### 2.1 O padrão AAA: Arrange, Act, Assert

Um teste bem-estruturado tem três fases visualmente separadas:

- **Arrange** (preparar): monta o cenário — cria objetos, prepara mocks, define entrada.
- **Act** (executar): chama o método/comportamento sob teste. Idealmente uma única linha.
- **Assert** (verificar): confirma o resultado esperado.

```java
@Test
public void calcularFrete_pedidoSudesteAcimaDe100_retornaGratis() {
    // Arrange
    Pedido pedido = new Pedido();
    pedido.adicionar(new Item("Livro", 1, BigDecimal.valueOf(150)));
    pedido.setCepEntrega("01310-100"); // SP
    CalculadoraFrete calculadora = new CalculadoraFrete();

    // Act
    BigDecimal frete = calculadora.calcular(pedido);

    // Assert
    assertEquals(BigDecimal.ZERO, frete);
}
```

A separação visual (linhas em branco ou comentários) é mais importante do que parece — quando alguém olha o teste em três meses, a estrutura *grita* o que cada parte faz. Testes embaralhados (com asserções no meio do *arrange*, ou múltiplos *acts* misturados) levam dois minutos para ser entendidos; testes AAA, dois segundos.

### Variante BDD: Given-When-Then

A mesma ideia em vocabulário de negócio:

- **Given** (dado): estado pré-existente
- **When** (quando): ação ocorre
- **Then** (então): resultado esperado

Esses dois padrões são equivalentes — a diferença é apenas o vocabulário. AAA tende a ser mais usado em testes unitários puros; Given-When-Then domina em BDD e testes de aceitação.

### 2.2 Princípios FIRST (Robert Martin)

Bons testes são **F.I.R.S.T.**:

| Princípio | Significa |
| --- | --- |
| **Fast** | Rodam em milissegundos. Suíte de 1.000 testes deve completar em segundos. |
| **Independent** | Ordem não importa. Um teste não pode depender ou afetar outro. |
| **Repeatable** | Roda em qualquer ambiente: máquina do dev, CI, sem rede, sem horário específico. |
| **Self-Validating** | Passa ou falha. **Sem** interpretar logs ou inspecionar saída manualmente. |
| **Timely** | Escritos no momento adequado — idealmente *junto* com o código (TDD), não meses depois. |

Cada princípio violado custa caro. Um teste **lento** desencoraja a equipe de rodar a suíte; um teste **dependente** falha aleatoriamente quando a ordem muda; um teste **não-repetível** (“só funciona na minha máquina”) destrói credibilidade da suíte.

### 2.3 Convenções de nomeação

O nome do teste deve **comunicar três coisas**: o método/comportamento testado, o cenário, e o resultado esperado. Algumas convenções comuns:

```java
// Estilo "metodo_cenario_resultado"
@Test
public void calcularFrete_pedidoForaDoSudeste_retornaTaxaFixa() { ... }

// Estilo "should... when..."
@Test
public void shouldReturnZeroWhenPedidoSudesteAcimaDeCem() { ... }

// Estilo descritivo (mais comum em BDD/JS)
@Test
public void deveDarFreteGratisParaSudesteAcimaDeCemReais() { ... }
```

A regra: **o nome do teste é a documentação**. Se você precisa abrir o corpo do teste para entender o que ele testa, o nome falhou.

> 💡 **Intuição:** Testes com nomes vagos como `testeFrete()` ou `testCase01()` são equivalentes a comentários como `// faz uma coisa`: ocupam espaço e não comunicam. Bons nomes de teste, lidos em sequência, formam uma **especificação executável** do sistema.
> 

---

## 3. Test doubles — a taxonomia de Meszaros

Frequentemente o objeto sob teste depende de outros objetos: serviços, repositórios, integrações. Em testes unitários, queremos isolar o objeto sob teste — substituindo as dependências por **dublês** (test doubles).

Gerard Meszaros, em *xUnit Test Patterns* (2007), classificou cinco tipos de dublês. A confusão entre eles é fonte interminável de discussões — vale dominar a taxonomia.

### 3.1 Os cinco tipos

| Tipo | O que faz | Quando usar |
| --- | --- | --- |
| **Dummy** | Passado como argumento mas nunca usado | Preencher parâmetro irrelevante para o teste |
| **Stub** | Retorna valor pré-definido quando chamado | Controlar a entrada que o SUT recebe |
| **Spy** | Stub + registra chamadas que recebeu | Verificar que foi chamado, com quais argumentos |
| **Mock** | Pré-programa expectativas + falha se não cumpridas | *Interaction-based testing*: o teste é sobre a chamada |
| **Fake** | Implementação simplificada mas funcional | Substituir por algo “leve” mas real (ex.: BD em memória) |

### 3.2 Stub vs Mock — a confusão clássica

A diferença é sobre **o que o teste verifica**:

- **Stub** suporta *state-based testing*: configura entrada para o SUT; o teste verifica o **estado final** (retorno, mudança em outro objeto).
- **Mock** suporta *interaction-based testing*: o teste verifica que **certas chamadas aconteceram** com certos argumentos.

```java
// Stub — state-based
@Test
public void calcularDescontoVIP_clienteVIP_aplicaDezPorcento() {
    // Stub: ClienteService retorna cliente VIP
    ClienteService stub = mock(ClienteService.class);
    when(stub.buscar(123L)).thenReturn(new Cliente(true)); // VIP

    DescontoCalculator calc = new DescontoCalculator(stub);

    BigDecimal desconto = calc.calcular(123L, BigDecimal.valueOf(100));

    // Verificamos o RESULTADO, não a chamada
    assertEquals(BigDecimal.valueOf(10), desconto);
}

// Mock — interaction-based
@Test
public void notificarPedidoPago_envia_emailEsmS() {
    NotificadorEmail emailMock = mock(NotificadorEmail.class);
    NotificadorSMS smsMock = mock(NotificadorSMS.class);

    PedidoService service = new PedidoService(emailMock, smsMock);
    service.confirmarPagamento(new Pedido("p123"));

    // Verificamos as CHAMADAS, não o estado
    verify(emailMock).enviar("p123");
    verify(smsMock).enviar("p123");
}
```

Note que em ambos os casos usamos `mock()` do Mockito — o framework não distingue. A distinção é **conceitual**, não sintática. O **uso** define o tipo.

### 3.3 Quando preferir Fake?

Fakes são frequentemente subestimados. Quando a dependência tem uma interface complexa (BD, sistema de arquivos, broker), criar uma **implementação leve** (em memória) pode ser melhor que uma cascata de mocks:

```java
public class RepositorioPedidoEmMemoria implements RepositorioPedido {
    private final Map<String, Pedido> pedidos = new HashMap<>();

    @Override
    public void salvar(Pedido p) { pedidos.put(p.getId(), p); }

    @Override
    public Pedido buscar(String id) { return pedidos.get(id); }

    @Override
    public List<Pedido> buscarPorCliente(Long clienteId) {
        return pedidos.values().stream()
            .filter(p -> p.getClienteId().equals(clienteId))
            .toList();
    }
}
```

Vantagens do fake sobre mock:
- Testes mais legíveis (sem `when().thenReturn()` espalhados).
- Comportamento real da estrutura (lista de pedidos cresce, busca filtra).
- Menos acoplamento ao framework de mocking.

Desvantagens:
- Implementação a manter (a interface mudou? Atualize o fake).
- Pode esconder bugs do real (in-memory tem semântica diferente de BD em alguns casos).

### 3.4 Frameworks populares

| Linguagem | Framework |
| --- | --- |
| Java | **Mockito** (dominante), JMockit, EasyMock |
| Python | **unittest.mock** (built-in), pytest-mock |
| JavaScript | **Jest** (mocks built-in), Sinon |
| C# / .NET | **Moq**, NSubstitute, FakeItEasy |
| Ruby | **RSpec mocks**, Mocha |

> 💡 **Intuição:** Test doubles são como sublocação. Mock é morador-ator que cumpre script preciso (se sair do roteiro, derruba o teste). Stub é figurante que entrega só uma fala. Fake é colega de quarto temporário que vive normalmente, só que em apartamento menor. Use o tipo certo: contratar um ator caro para entregar uma falinha é desperdício; contratar um figurante para um papel central é problema.
> 

---

## 4. Test-Driven Development (TDD)

### 4.1 O ciclo Red-Green-Refactor

TDD é uma disciplina de desenvolvimento, não apenas uma técnica de teste. O ciclo, popularizado por Kent Beck (*Test-Driven Development by Example*, 2002):

1. **Red** — escreva um teste que falha. O código de produção ainda não existe ou é insuficiente.
2. **Green** — escreva o código **mais simples possível** que faça o teste passar. Sem elegância, sem premeditação.
3. **Refactor** — melhore o código (e os testes) preservando o comportamento. Os testes são a rede.

Repita. Cada ciclo é tipicamente curto — minutos, não horas.

### 4.2 Exemplo de ciclo completo

Vamos implementar a regra de cálculo de desconto VIP via TDD.

**Ciclo 1 — Red**: escrevo o teste mais básico possível.

```java
@Test
public void clienteNaoVIP_semDesconto() {
    DescontoCalculator calc = new DescontoCalculator();
    BigDecimal d = calc.calcular(new Cliente(false), BigDecimal.valueOf(100));
    assertEquals(BigDecimal.ZERO, d);
}
```

Não compila — `DescontoCalculator` não existe. Esse é o “red” mais cru.

**Ciclo 1 — Green**: faço o mínimo para passar.

```java
public class DescontoCalculator {
    public BigDecimal calcular(Cliente c, BigDecimal valor) {
        return BigDecimal.ZERO;
    }
}
```

Sim, “*hard-coded*” `BigDecimal.ZERO`. O teste passa. Não há lógica ainda.

**Ciclo 1 — Refactor**: nada para refatorar; código mínimo.

**Ciclo 2 — Red**: novo teste que força lógica.

```java
@Test
public void clienteVIP_dezPorcentoDeDesconto() {
    DescontoCalculator calc = new DescontoCalculator();
    BigDecimal d = calc.calcular(new Cliente(true), BigDecimal.valueOf(100));
    assertEquals(BigDecimal.valueOf(10), d);
}
```

Falha (sempre retorna zero).

**Ciclo 2 — Green**: implemento o suficiente.

```java
public BigDecimal calcular(Cliente c, BigDecimal valor) {
    if (c.isVip()) return valor.multiply(BigDecimal.valueOf(0.10));
    return BigDecimal.ZERO;
}
```

Ambos os testes passam.

**Ciclo 2 — Refactor**: extraio constante mágica.

```java
private static final BigDecimal DESCONTO_VIP = BigDecimal.valueOf(0.10);

public BigDecimal calcular(Cliente c, BigDecimal valor) {
    if (c.isVip()) return valor.multiply(DESCONTO_VIP);
    return BigDecimal.ZERO;
}
```

Continua passando. **Pequenos passos, *commits* frequentes**.

**Ciclo 3 — Red**: regra mais complexa: cliente VIP + compra > R$ 500 = 15%.

```java
@Test
public void clienteVIP_compraAcima500_quinzePorcento() {
    DescontoCalculator calc = new DescontoCalculator();
    BigDecimal d = calc.calcular(new Cliente(true), BigDecimal.valueOf(600));
    assertEquals(BigDecimal.valueOf(90), d); // 15% de 600
}
```

Falha (retorna 60 — 10%).

E o ciclo continua.

### 4.3 Vantagens do TDD

- **Design emergente**: o teste *é* o cliente do código. Código sob TDD é naturalmente testável (interfaces claras, dependências injetadas, métodos pequenos).
- **Cobertura natural**: você só escreve código se tem teste; cobertura tende a ser alta sem esforço explícito.
- **Confiança para refatorar**: cada passo verde é um *checkpoint*.
- **Documentação**: a suíte de testes documenta o comportamento esperado.

### 4.4 Controvérsias

TDD não é unanimidade. Em 2014, David Heinemeier Hansson (criador do Rails) escreveu *“TDD is Dead. Long Live Testing”*, criticando o uso *cargo cult* de TDD. O argumento central: **TDD em todas as situações é dogma**; a prática deve ser dirigida a contexto.

Onde TDD funciona bem:
- Lógica pura (cálculos, regras de negócio, algoritmos)
- APIs com contratos claros
- Refatoração de código legado (após ter testes de caracterização)

Onde TDD é difícil:
- UI (a “verdade” é visual; assertion sobre pixels é frágil)
- Integração com sistemas externos (mocks proliferam)
- *Spike* exploratório (você não sabe o que vai escrever)
- Código altamente acoplado a frameworks (testar é difícil sem rodar tudo)

A posição equilibrada: **TDD é uma ferramenta de design**. Use quando o ganho de design compensa o overhead. Não use por dogma.

> 💡 **Intuição:** O grande valor do TDD não é o teste em si — é o *feedback rapidíssimo* sobre suas escolhas de design. Quando o teste fica difícil de escrever, é sinal de que o design está errado: dependência demais, escopo demais, acoplamento demais. TDD força você a tropeçar nesses problemas **enquanto eles são baratos de corrigir**.
> 

---

## 5. Behavior-Driven Development (BDD)

### 5.1 De TDD para BDD

Dan North, em 2006, observou que TDD respondia *como* testar mas não *o que* testar. Equipes seguiam o ciclo mecanicamente, escrevendo testes para “métodos” — mas não para **comportamentos** que o negócio se importa.

BDD propõe: o teste deve descrever **comportamento esperado em vocabulário de negócio**, compreensível por *product owners* e analistas.

### 5.2 Gherkin: Given-When-Then

A linguagem Gherkin estrutura cenários:

```gherkin
Funcionalidade: Cálculo de frete
  Como cliente do e-commerce
  Quero saber o valor do frete antes de finalizar a compra
  Para decidir conscientemente se vou prosseguir

Cenário: Frete grátis para Sudeste com pedido acima de R$ 100
  Dado um cliente com endereço de entrega em São Paulo
  E um carrinho com produtos no valor total de R$ 150,00
  Quando o cliente solicita o cálculo de frete
  Então o frete deve ser R$ 0,00
  E a mensagem "Frete grátis para sua região" deve ser exibida

Cenário: Frete fixo para Sudeste com pedido abaixo de R$ 100
  Dado um cliente com endereço de entrega no Rio de Janeiro
  E um carrinho com produtos no valor total de R$ 80,00
  Quando o cliente solicita o cálculo de frete
  Então o frete deve ser R$ 15,00
```

Ferramentas como **Cucumber** (Java), **SpecFlow** (.NET), **Behave** (Python), **Cucumber.js** (JS) leem esses arquivos `.feature` e os ligam a código de teste (chamado *step definitions*).

### 5.3 Quando BDD agrega valor (e quando é overkill)

**Faz sentido quando**:
- *Stakeholders* de negócio querem ler/escrever cenários
- Equipes multidisciplinares (dev + analista + QA) precisam alinhamento
- Funcionalidades têm regras de negócio complexas e variáveis
- A organização está disposta a manter o vocabulário ubíquo

**Costuma ser overkill quando**:
- Equipe é pequena, todos técnicos, comunicação direta funciona
- A tradução Gherkin → código adiciona overhead sem benefício
- Os cenários acabam escritos só por desenvolvedores (ninguém de negócio lê)
- O sistema é puramente técnico (infra, ferramentas internas)

A armadilha: muitas equipes adotam Cucumber pensando que isso *é* BDD. **BDD é uma cultura colaborativa**, não uma ferramenta. Cucumber sem cultura é Gherkin entre dois pares de aspas.

---

## 6. Property-Based Testing

### 6.1 Limites do teste por exemplo

Testes tradicionais funcionam por **exemplos**: “para entrada `X`, esperar saída `Y`”. O problema: você só testa o que pensou em testar. Bugs frequentemente vivem em casos que ninguém imaginou.

### 6.2 A ideia

**Property-based testing**: em vez de exemplos, defina **propriedades que devem valer para qualquer entrada válida**. A ferramenta gera entradas aleatórias automaticamente e tenta achar contraexemplo.

Exemplo conceitual: para qualquer lista de inteiros `xs`, deve valer:
- `length(reverse(xs)) == length(xs)`
- `reverse(reverse(xs)) == xs`
- `sum(xs) == sum(reverse(xs))`

A ferramenta gera centenas de listas (vazia, com um elemento, com 1000 elementos, com negativos…) e verifica se a propriedade vale em todas.

### 6.3 Exemplo prático com jqwik (Java)

```java
@Property
void freteSempreNaoNegativo(@ForAll @Positive int valor,
                            @ForAll @AlphaChars String cep,
                            @ForAll @Positive double peso) {
    Pedido p = new Pedido();
    p.adicionar(new Item("X", 1, BigDecimal.valueOf(valor)));
    p.setCepEntrega(cep);
    p.setPesoKg(peso);

    BigDecimal frete = calculadora.calcular(p);

    // Propriedade: frete nunca pode ser negativo
    assertTrue(frete.compareTo(BigDecimal.ZERO) >= 0);
}
```

A ferramenta vai gerar centenas de combinações `(valor, cep, peso)` e verificar a propriedade. Se encontrar contraexemplo, faz **shrinking**: reduz a entrada para o caso mínimo que ainda quebra (ex.: “frete negativo com valor=1, cep=‘AAAAA’, peso=0.001”).

### 6.4 Onde funciona bem

- Algoritmos com propriedades matemáticas claras (ordenação, parsing, codificação)
- Sistemas com invariantes (nunca negativo, nunca duplicado, sempre crescente)
- Funções puras com muitos *edge cases*

### 6.5 Ferramentas

| Linguagem | Ferramenta |
| --- | --- |
| Haskell | QuickCheck (pioneira, 1999) |
| Java | jqwik, junit-quickcheck |
| Python | Hypothesis (excelente, muito popular) |
| JavaScript | fast-check |
| Scala | ScalaCheck |
| .NET | FsCheck |

> 💡 **Intuição:** Property-based testing complementa testes por exemplo — não substitui. Bons testes por exemplo capturam intenção e regras de negócio; property-based captura *invariantes* e encontra os *edge cases* que humanos esquecem. Ambos cabem na suíte.
> 

---

## 7. Mutation Testing — quem testa os testes?

### 7.1 O problema da cobertura

Já vimos: cobertura mede *execução*, não *qualidade dos testes*. Você pode ter 100% de cobertura com testes assim:

```java
@Test
public void testCalculadora() {
    Calculadora c = new Calculadora();
    c.somar(2, 3);  // Executa o método, mas NÃO verifica nada
}
```

Cobertura: 100%. Verificação: zero. Bug? Indetectável.

### 7.2 A ideia do mutation testing

**Introduza pequenas mutações no código de produção** e verifique se a suíte de testes detecta. Mutações típicas:

- Trocar `>` por `>=`
- Trocar `+` por
- Trocar `&&` por `||`
- Remover uma linha
- Trocar `true` por `false`
- Trocar literal numérico (100 por 99 ou 101)

Para cada mutação, roda a suíte. Resultados possíveis:
- **Killed (morta)**: algum teste falhou — bom, a suíte detectou.
- **Survived (sobreviveu)**: todos os testes passaram apesar da mutação — a suíte tem buraco.
- **No coverage**: a mutação está em código não exercitado — refletindo cobertura.

### 7.3 Mutation score

A métrica resultante:

```
mutation_score = mutações killed / mutações aplicáveis
```

Um *mutation score* alto (≥80%) indica testes que efetivamente detectam mudanças. Cobertura de 100% com mutation score de 30% é um sinal vermelho: testes executam mas não verificam.

### 7.4 Custo computacional

Mutation testing é **caro**: para cada mutação, a suíte roda inteira. Se o código tem 10.000 mutações possíveis e a suíte demora 1 minuto, são ~7 dias de execução pura.

Estratégias práticas:
- Rodar **pré-merge** apenas em arquivos alterados (incremental).
- Rodar **suíte completa em CI noturno** ou semanal.
- Focar em módulos críticos (financeiro, segurança), não no sistema todo.

### 7.5 Ferramentas

| Linguagem | Ferramenta |
| --- | --- |
| Java | **Pitest** (de longe o mais maduro) |
| Python | mutmut, MutPy |
| JavaScript / TS | **Stryker** |
| .NET | Stryker.NET |
| Ruby | Mutant |
| Go | go-mutesting |

---

## 8. Test smells — quando o teste é o problema

Assim como há *code smells* no código de produção, há *test smells* no código de teste. Vamos a alguns dos mais comuns.

### 8.1 Mystery Guest

Teste que depende de um arquivo, banco, ou recurso externo cujo conteúdo não é visível no teste.

```java
// 🚫 Smell: o que tem em customers.csv?
@Test
public void importacaoCadastroClientes() {
    CSVImporter imp = new CSVImporter();
    int total = imp.importar("customers.csv");
    assertEquals(150, total);
}
```

Tratamento: trazer os dados para o teste, ou pelo menos documentar:

```java
// ✅
@Test
public void importacaoCadastroClientes_arquivoCom150Linhas() {
    String csv = """
        nome,email
        Alice,alice@x.com
        Bob,bob@y.com
        ...""";
    CSVImporter imp = new CSVImporter();
    int total = imp.importar(new StringReader(csv));
    assertEquals(150, total);
}
```

### 8.2 Eager Test

Um único teste verifica muitas coisas. Falha em qualquer asserção mascara as demais.

```java
// 🚫 Smell: três coisas em um teste
@Test
public void testePedidoCompleto() {
    Pedido p = service.criar(...);
    assertNotNull(p.getId());
    assertEquals("PENDENTE", p.getStatus());
    assertTrue(p.getValor().compareTo(BigDecimal.ZERO) > 0);
    p = service.confirmar(p.getId());
    assertEquals("CONFIRMADO", p.getStatus());
    p = service.cancelar(p.getId());
    assertEquals("CANCELADO", p.getStatus());
}
```

Tratamento: **um conceito por teste**.

```java
// ✅
@Test public void criarPedido_geraIdValido() { ... }
@Test public void criarPedido_estadoInicialEPendente() { ... }
@Test public void criarPedido_valorTotalCalculadoCorretamente() { ... }
@Test public void confirmarPedido_mudaEstadoParaConfirmado() { ... }
@Test public void cancelarPedido_mudaEstadoParaCancelado() { ... }
```

### 8.3 Flaky Test

Teste que ora passa, ora falha — sem mudança no código. Causas comuns:
- Dependência de tempo (`Thread.sleep`, `LocalDateTime.now()`)
- Concorrência (race conditions)
- Ordem dependente de outro teste
- Recurso compartilhado (banco, arquivo, porta de rede)
- Geração aleatória sem semente fixa

Testes *flaky* são piores que testes ausentes — destroem a credibilidade da suíte. Política saudável: **trate todo teste flaky como bug crítico**. Quarentena ou correção imediata, não tolerância.

### 8.4 Slow Test

Suíte de testes unitários deve rodar em **segundos**, não minutos. Suítes lentas são executadas raramente, e suítes raramente executadas pegam regressões tarde.

Causas: I/O real (BD, rede), `Thread.sleep`, *fixtures* gigantes desnecessárias, frameworks pesados sendo carregados em testes unitários.

Tratamento: testes com I/O real são testes de **integração**, não unitários. Mantenha a separação.

### 8.5 Conditional Test Logic

`if`/`else`/`switch` dentro do teste.

```java
// 🚫 Smell: lógica no teste
@Test
public void testCalcularFrete() {
    Pedido p = ...;
    BigDecimal frete = calc.calcular(p);
    if (p.getValor().compareTo(BigDecimal.valueOf(100)) >= 0) {
        assertEquals(BigDecimal.ZERO, frete);
    } else {
        assertEquals(BigDecimal.valueOf(15), frete);
    }
}
```

Esse teste não testa nada de definitivo — depende do que `Pedido` traz. Tratamento: testes parametrizados, ou testes separados por cenário.

```java
// ✅ Parameterized test (JUnit 5)
@ParameterizedTest
@CsvSource({
    "150, 0",   // acima de 100 → grátis
    "99,  15",  // abaixo de 100 → R$ 15
    "100, 0"    // exatamente 100 → grátis (BVA)
})
public void calcularFrete(int valor, int freteEsperado) {
    Pedido p = new Pedido();
    p.adicionar(new Item("X", 1, BigDecimal.valueOf(valor)));
    p.setCepEntrega("01310-100");

    BigDecimal frete = calculadora.calcular(p);

    assertEquals(BigDecimal.valueOf(freteEsperado), frete);
}
```

### 8.6 Test Code Duplication

*Copy-paste* entre testes (mesmo *setup* repetido em 20 testes). Tratamento: extraia para `@BeforeEach`, *builders*, *fixtures*. Trate código de teste com a mesma disciplina do código de produção.

### 8.7 Magic Numbers

Literais como `100`, `0.18`, `5` aparecendo em testes sem nome. Igual a code smell — refatore para constantes nomeadas.

> 💡 **Intuição:** A regra de ouro do código de teste: ele tem o **mesmo padrão de qualidade** do código de produção. “É só teste” é a frase que precede dívida técnica em testes, que precede testes ignorados, que precede regressões em produção. Trate seus testes como cidadãos de primeira classe.
> 

---

## 9. Testes em CI/CD

### 9.1 Pipeline típico

Um pipeline de CI/CD bem-estruturado integra testes em múltiplos pontos:

```
Commit → trigger
    ↓
Lint + format
    ↓
Build
    ↓
Unit tests (segundos)        ← FALHA AQUI: para tudo
    ↓
Integration tests (minutos)
    ↓
Static analysis (SonarQube)
    ↓
Build artifact
    ↓
Deploy → ambiente de staging
    ↓
E2E tests + smoke tests
    ↓
[Manual approval]
    ↓
Deploy → produção
    ↓
Smoke tests pós-deploy
```

### 9.2 Princípios

- **Fail fast**: testes mais rápidos primeiro. Se o build quebra, o resto não roda.
- **Cobertura como gate** (com cuidado): “*build* falha se cobertura < 80%”. Útil mas frágil — equipe pode otimizar para a métrica.
- **Mutation score como gate qualitativo** (em CI noturno): “alerta se mutation score < 70%”.
- **Política para flaky tests**: zero tolerância. Quarentena automática + ticket bloqueante.
- ***Test impact analysis***: rodar apenas testes afetados por mudanças de código (otimização para projetos grandes).

### 9.3 Ferramentas comuns

- **CI**: GitHub Actions, GitLab CI, Jenkins, CircleCI, Buildkite
- **Cobertura**: JaCoCo, Codecov, Coveralls
- **Quality gates**: SonarQube, CodeClimate
- **Mutation**: Pitest (Java), Stryker (multi-linguagem)
- ***Test impact***: Bazel, Buck, Nx, ferramentas comerciais

---

## 10. Anti-padrões e culturas tóxicas

Encerrando, alguns anti-padrões repetidos:

- **“Testes são responsabilidade do QA”**: dev escreve código, joga por cima do muro, QA testa. Isso atrasa feedback, esconde bugs até depois, e gera ressentimento. Testes unitários e de integração são responsabilidade do dev.
- **“Não temos tempo para testes agora”**: sempre há tempo para corrigir bugs em produção; testes seriam mais baratos. Falsa economia.
- **“Cobertura como meta única”**: pessoas otimizam para a métrica. Testes que sobem cobertura sem detectar bugs surgem em massa. **Lei de Goodhart**: quando uma métrica vira meta, deixa de ser bom indicador.
- **Mockar tudo**: testes que mockam todas as dependências testam apenas a interação com mocks — não o comportamento real. Sintoma: refatoração quebra todos os testes mesmo sem mudança de comportamento.
- **Tolerar testes flaky**: equipe se acostuma a “rodar de novo” quando falha. A suíte vira inútil — você não confia em vermelho nem em verde.
- **Suítes monolíticas**: 5.000 testes em uma única classe. Manutenção agoniza, isolamento desaparece.

---

## 11. Exemplos resolvidos

### Exemplo Resolvido 1 — Refatorando um teste fedido (Introdutório) ⭐

**Enunciado:** O teste abaixo apresenta múltiplos *test smells*. Identifique-os e refatore aplicando AAA, FIRST, e bons nomes.

```java
@Test
public void test1() throws Exception {
    Pedido p = new Pedido();
    p.adicionar(new Item("Livro", 1, new BigDecimal("99")));
    p.setCepEntrega("01310-100");
    BigDecimal f1 = service.calcular(p);
    if (f1.compareTo(BigDecimal.ZERO) > 0) {
        assertEquals(new BigDecimal("15"), f1);
    } else {
        fail();
    }
    p.adicionar(new Item("Caneta", 1, new BigDecimal("2")));
    BigDecimal f2 = service.calcular(p);
    assertEquals(BigDecimal.ZERO, f2);

    Thread.sleep(100);
    assertNotNull(service.getUltimoCalculoTimestamp());
}
```

**Solução:**

**Smells identificados:**

1. **Nome inútil** (`test1`): não comunica o que testa.
2. **Eager test**: três coisas testadas — frete abaixo de 100, frete acima de 100, e timestamp.
3. **Conditional test logic**: `if`/`else` dentro do teste.
4. **Slow test**: `Thread.sleep(100)` — não-determinístico e desnecessário.
5. **Sem AAA visual**: arrange, act e assert misturados.
6. **Magic numbers**: `99`, `15`, `100`, `2`, `100` (sleep) sem nome.
7. **`fail()` desnecessário**: `assertEquals` já falha sozinho.

**Refatoração:**

```java
public class CalculadoraFreteTest {

    private static final String CEP_SUDESTE = "01310-100";
    private static final BigDecimal LIMITE_FRETE_GRATIS = new BigDecimal("100");
    private static final BigDecimal FRETE_FIXO_SUDESTE = new BigDecimal("15");

    private CalculadoraFrete calculadora;

    @BeforeEach
    public void setup() {
        calculadora = new CalculadoraFrete();
    }

    @Test
    public void calcular_pedidoSudesteAbaixoDoLimite_retornaFreteFixo() {
        // Arrange
        Pedido p = pedidoSudesteComValor(new BigDecimal("99"));

        // Act
        BigDecimal frete = calculadora.calcular(p);

        // Assert
        assertEquals(FRETE_FIXO_SUDESTE, frete);
    }

    @Test
    public void calcular_pedidoSudesteAcimaDoLimite_retornaFreteGratis() {
        // Arrange
        Pedido p = pedidoSudesteComValor(new BigDecimal("101"));

        // Act
        BigDecimal frete = calculadora.calcular(p);

        // Assert
        assertEquals(BigDecimal.ZERO, frete);
    }

    @Test
    public void calcular_atualizaTimestampDoUltimoCalculo() {
        // Arrange
        Pedido p = pedidoSudesteComValor(new BigDecimal("50"));
        Instant antes = Instant.now();

        // Act
        calculadora.calcular(p);

        // Assert
        assertNotNull(calculadora.getUltimoCalculoTimestamp());
        assertTrue(calculadora.getUltimoCalculoTimestamp().isAfter(antes.minusMillis(1)));
    }

    private Pedido pedidoSudesteComValor(BigDecimal valor) {
        Pedido p = new Pedido();
        p.adicionar(new Item("Item teste", 1, valor));
        p.setCepEntrega(CEP_SUDESTE);
        return p;
    }
}
```

**Discussão:**

- **Três testes** em vez de um — cada um com nome descritivo e responsabilidade única.
- **AAA visualmente claro** — comentários separam as fases (em times maduros, linhas em branco bastam).
- **Magic numbers nomeados** como constantes.
- ***Builder helper* (`pedidoSudesteComValor`)** evita duplicação. Quando o setup ficar mais complexo, vira uma classe `PedidoBuilder`.
- ***Fail-safe* no timestamp**: comparei com `antes` em vez de `Thread.sleep` — agora o teste é determinístico e rápido.
- ***BeforeEach*** isola estado entre testes.

> 💡 **Intuição:** Veja o tamanho: o “antes” tinha ~13 linhas e três asserções; o “depois” tem ~50 linhas e três testes. Testes bem-feitos são mais longos por *teste* — mas mais curtos por *ideia testada*. O ganho é em legibilidade e diagnóstico: quando um deles falha, você sabe imediatamente o que quebrou.
> 

### Exemplo Resolvido 2 — TDD: implementando uma funcionalidade do zero (Intermediário) ⭐⭐

**Enunciado:** Implemente, via TDD em ciclos completos *red-green-refactor*, a classe `CarrinhoDeCompras` com as seguintes responsabilidades:

- Adicionar item (produto + quantidade)
- Remover item completamente
- Calcular total
- Aplicar cupom de desconto percentual (cupom só pode ser aplicado uma vez por carrinho)

Mostre **pelo menos 4 ciclos** completos. Ao final, apresente o código de produção e a suíte de testes.

**Solução:**

**Ciclo 1 — Carrinho vazio**

*Red*:

```java
@Test
public void carrinhoVazio_totalEZero() {
    CarrinhoDeCompras c = new CarrinhoDeCompras();
    assertEquals(BigDecimal.ZERO, c.total());
}
```

Não compila (classe não existe).

*Green* (mínimo):

```java
public class CarrinhoDeCompras {
    public BigDecimal total() { return BigDecimal.ZERO; }
}
```

*Refactor*: nada para refatorar.

**Ciclo 2 — Adicionar um item**

*Red*:

```java
@Test
public void adicionarUmItem_totalEhValorDoItem() {
    CarrinhoDeCompras c = new CarrinhoDeCompras();
    c.adicionar(new Produto("Livro", new BigDecimal("50")), 1);
    assertEquals(new BigDecimal("50"), c.total());
}
```

Falha — método `adicionar` não existe.

*Green*:

```java
public class CarrinhoDeCompras {
    private final List<ItemCarrinho> itens = new ArrayList<>();

    public void adicionar(Produto p, int qtd) {
        itens.add(new ItemCarrinho(p, qtd));
    }

    public BigDecimal total() {
        return itens.stream()
            .map(i -> i.produto.preco.multiply(BigDecimal.valueOf(i.quantidade)))
            .reduce(BigDecimal.ZERO, BigDecimal::add);
    }
}
```

(Classes `Produto` e `ItemCarrinho` introduzidas como mínimo necessário.)

*Refactor*: extrair `subtotal()` no `ItemCarrinho`:

```java
class ItemCarrinho {
    Produto produto;
    int quantidade;
    BigDecimal subtotal() {
        return produto.preco.multiply(BigDecimal.valueOf(quantidade));
    }
}
```

E o `total()` fica mais limpo:

```java
public BigDecimal total() {
    return itens.stream()
        .map(ItemCarrinho::subtotal)
        .reduce(BigDecimal.ZERO, BigDecimal::add);
}
```

Ambos os testes passam.

**Ciclo 3 — Múltiplos itens e remoção**

*Red*:

```java
@Test
public void adicionarTresItens_totalSomaCorretamente() {
    CarrinhoDeCompras c = new CarrinhoDeCompras();
    c.adicionar(new Produto("Livro", new BigDecimal("50")), 1);
    c.adicionar(new Produto("Caneta", new BigDecimal("3")), 5); // 15
    c.adicionar(new Produto("Caderno", new BigDecimal("20")), 2); // 40
    assertEquals(new BigDecimal("105"), c.total());
}

@Test
public void remover_itemDeixaDeContarNoTotal() {
    CarrinhoDeCompras c = new CarrinhoDeCompras();
    Produto livro = new Produto("Livro", new BigDecimal("50"));
    c.adicionar(livro, 1);
    c.adicionar(new Produto("Caneta", new BigDecimal("3")), 1);

    c.remover(livro);

    assertEquals(new BigDecimal("3"), c.total());
}
```

Primeiro passa; segundo falha (sem `remover`).

*Green*:

```java
public void remover(Produto p) {
    itens.removeIf(i -> i.produto.equals(p));
}
```

(Implícito: `Produto` precisa de `equals/hashCode` adequado — pode entrar em ciclo de refatoração.)

*Refactor*: nada novo significativo.

**Ciclo 4 — Cupom de desconto**

*Red*:

```java
@Test
public void aplicarCupom_dezPorcento_descontaDoTotal() {
    CarrinhoDeCompras c = new CarrinhoDeCompras();
    c.adicionar(new Produto("Livro", new BigDecimal("100")), 1);

    c.aplicarCupom(new Cupom("DESCONTO10", BigDecimal.valueOf(0.10)));

    assertEquals(new BigDecimal("90.00"), c.total());
}

@Test
public void aplicarCupom_segundaVez_lancaExcecao() {
    CarrinhoDeCompras c = new CarrinhoDeCompras();
    c.adicionar(new Produto("Livro", new BigDecimal("100")), 1);
    c.aplicarCupom(new Cupom("DESCONTO10", BigDecimal.valueOf(0.10)));

    assertThrows(IllegalStateException.class,
        () -> c.aplicarCupom(new Cupom("OUTROCUPOM", BigDecimal.valueOf(0.05))));
}
```

Falham (sem `Cupom` nem `aplicarCupom`).

*Green*:

```java
private Cupom cupomAplicado;

public void aplicarCupom(Cupom c) {
    if (cupomAplicado != null) {
        throw new IllegalStateException("Cupom já aplicado a este carrinho");
    }
    this.cupomAplicado = c;
}

public BigDecimal total() {
    BigDecimal subtotal = itens.stream()
        .map(ItemCarrinho::subtotal)
        .reduce(BigDecimal.ZERO, BigDecimal::add);

    if (cupomAplicado != null) {
        BigDecimal desconto = subtotal.multiply(cupomAplicado.percentual);
        return subtotal.subtract(desconto);
    }
    return subtotal;
}
```

*Refactor*: a complexidade de `total()` aumentou. Extraio:

```java
public BigDecimal total() {
    BigDecimal subtotal = calcularSubtotal();
    return subtotal.subtract(calcularDesconto(subtotal));
}

private BigDecimal calcularSubtotal() {
    return itens.stream()
        .map(ItemCarrinho::subtotal)
        .reduce(BigDecimal.ZERO, BigDecimal::add);
}

private BigDecimal calcularDesconto(BigDecimal subtotal) {
    if (cupomAplicado == null) return BigDecimal.ZERO;
    return subtotal.multiply(cupomAplicado.percentual);
}
```

Todos os testes continuam passando.

**Reflexão sobre o processo:**

- **Quatro ciclos**, cada um adicionando comportamento bem-delimitado.
- **A cada *green*, *commit*** — backup de progresso. Se o próximo *red* der errado, você volta.
- ***Refactor* só com testes verdes**: a rede de segurança protege.
- **Design emergiu**: `Cupom`, `ItemCarrinho`, `calcularSubtotal/calcularDesconto` não foram decididos *upfront* — apareceram conforme necessários.
- **Cobertura natural**: cada linha de produção foi escrita para passar um teste — cobertura é alta sem perseguir o número.

**Discussão sobre TDD**:

- O ciclo “ridiculamente pequeno” no início (`return BigDecimal.ZERO` *hard-coded*) parece tolice mas é **a disciplina**: força você a verificar que o teste falha antes do código existir; força você a não escrever código que não tem teste.
- **TDD não substitui pensar em design**. Ainda é necessário decidir nomes, responsabilidades, padrões. TDD apenas dá *feedback* rapidíssimo sobre essas decisões.

> 💡 **Intuição:** Quem nunca praticou TDD costuma achar que “escrever teste primeiro” é só uma inversão de ordem. Não é. É uma **inversão de papel**: o teste deixa de ser verificação a posteriori e passa a ser **especificação executável** — e o código de produção responde a essa especificação. A diferença é qualitativa, e leva semanas de prática para internalizar.
> 

---

## Exercícios Práticos

### Exercício 1 — Identificar e tratar test smells ⭐

Considere o seguinte código de teste:

```java
@Test
public void testaServicoCompleto() throws Exception {
    UserService service = new UserService();
    File f = new File("/tmp/users.json");
    service.carregar(f);

    User u = service.buscar(1);
    assertNotNull(u);
    if (u.getEmail().contains("@")) {
        assertTrue(true);
    }

    service.adicionar(new User("Alice", "alice@x.com"));
    service.adicionar(new User("Alice", "alice@x.com"));
    Thread.sleep(500);

    List<User> all = service.listar();
    assertEquals(3, all.size());

    service.remover(1);
    assertEquals(2, service.listar().size());

    User u2 = service.buscar(2);
    assertEquals("Alice", u2.getNome());
}
```

Sua tarefa:

1. Liste **pelo menos 6 test smells** distintos presentes neste código. Para cada um, cite o nome do *smell* e indique a(s) linha(s)/trecho(s) onde aparece.
2. Reescreva este código de teste em uma forma refatorada — quebrando em testes menores, aplicando AAA, eliminando lógica condicional e dependências não-determinísticas. Use `@BeforeEach` se fizer sentido.
3. Identifique pelo menos **um aspecto que NÃO consegue ser testado bem** sem usar test doubles (mock, stub, fake). Diga qual *double* você usaria e por quê.
4. Discuta em 4-6 linhas: o `assertEquals(3, all.size())` no original assumia o estado do arquivo. Por que isso é problemático mesmo se o teste passar? Como sua refatoração resolve?

### Exercício 2 — TDD: implementar funcionalidade nova ⭐⭐

Implemente, **via TDD em ciclos red-green-refactor explícitos**, a seguinte funcionalidade:

> **Sistema de pontuação de fidelidade do e-commerce**: clientes acumulam pontos a cada compra (1 ponto a cada R$ 10 gastos, arredondado para baixo). Cada pedido paga adiciona pontos. Cada pedido cancelado *após pago* subtrai os pontos correspondentes. Pontos podem ser resgatados em descontos (cada 100 pontos = R$ 5 de desconto, em múltiplos de 100). Pontos têm validade de 12 meses a partir do ganho — pontos expirados não podem ser resgatados, mas continuam contando para nível de fidelidade (Bronze, Prata, Ouro: até 500/1500/3000 pontos cumulativos no ano).
> 

Sua tarefa:

1. **Comece pelo teste mais simples possível** (cliente novo, sem compras, tem 0 pontos). Implemente o ciclo completo (red, green, refactor).
2. Mostre **pelo menos 5 ciclos completos**, cada um com:
- Teste novo que falha (red)
- Implementação mínima que faz passar (green)
- Refatoração aplicada (ou justificativa de “nada a refatorar nesse ciclo”)
1. No ciclo que envolver **expiração de pontos**, discuta como você lidaria com a dependência de `LocalDate.now()` — quais test doubles ou estratégias você usaria para tornar o teste determinístico.
2. Após os 5+ ciclos, apresente **o código de produção final** e **a suíte de testes completa**.
3. Reflita em pelo menos 5 linhas: que aspectos **do design final emergiram** ao longo do TDD que você provavelmente não teria escolhido se tivesse desenhado a classe inteira *upfront*? Que decisões você só percebeu serem necessárias quando um teste exigiu?

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Beck, K. *Test-Driven Development: By Example*. Addison-Wesley, 2002. (O livro fundador da prática de TDD; leitura obrigatória para quem quer levar TDD a sério.)
- Meszaros, G. *xUnit Test Patterns: Refactoring Test Code*. Addison-Wesley, 2007. (Catálogo definitivo de padrões e *smells* de testes; a taxonomia de test doubles vem daqui.)
- Khorikov, V. *Unit Testing: Principles, Practices, and Patterns*. Manning, 2020. (Tratamento contemporâneo, com discussão valiosa sobre o que faz um teste ser de qualidade — vai além de “ter teste”.)
- Martin, R. C. *Clean Code: A Handbook of Agile Software Craftsmanship*. Prentice Hall, 2008. (Capítulo “Clean Tests” trata os princípios FIRST e qualidade de código de teste.)
- Feathers, M. *Working Effectively with Legacy Code*. Prentice Hall, 2004. (Indispensável quando você herda código sem testes; testes de caracterização e técnicas de quebrar dependências.)
- North, D. “Introducing BDD”. *Better Software Magazine*, 2006. (Artigo seminal que cunhou o termo BDD.)
- Wlaschin, S. *Domain Modeling Made Functional*. Pragmatic Bookshelf, 2018. (Excelente tratamento de property-based testing aplicado a domínios reais.)
- Petrović, G.; Ivanković, M.; Fraser, G.; Just, R. “Practical Mutation Testing at Scale”. *IEEE Transactions on Software Engineering*, 2022. (Estado da arte em mutation testing.)
- Hansson, D. H. “TDD is Dead. Long Live Testing”. 2014. (Provocação valiosa sobre dogma em TDD — vale para equilibrar a leitura de Beck.)
- Fowler, M. “Mocks Aren’t Stubs”. 2007. (Artigo curto e definitivo sobre a distinção stub vs mock — disponível em martinfowler.com.)
- Fowler, M. “Test Pyramid”. 2012. (Origem prática da pirâmide de testes que vimos na aula de qualidade.)
- Crispin, L.; Gregory, J. *Agile Testing*. Addison-Wesley, 2009. (Perspectiva ágil sobre as práticas vistas aqui; tratamento prático do papel do QA em times ágeis.)

---

*Próxima aula: Engenharia de Requisitos — captura, modelagem e gestão de requisitos funcionais e não-funcionais; rastreabilidade requisito-teste; ligação entre o que se documenta, o que se modela, e o que se valida.*