# Qualidade e Refatoração

## 1. Por que qualidade importa?

Há uma lei empírica em engenharia de software: **a maior parte do esforço gasto em um sistema é em sua manutenção, não em seu desenvolvimento inicial**. Estimativas variam, mas frequentemente ficam entre 60% e 80% do custo total ao longo da vida do sistema.

Isso muda radicalmente a economia da decisão "vamos entregar rápido, ajeitamos depois":

- **Curto prazo**: código mal estruturado entrega na sexta-feira.
- **Médio prazo**: cada nova feature custa 20% mais por causa do código anterior.
- **Longo prazo**: o sistema entra em "morte por mil cortes" — bugs em cascata, desenvolvedores fugindo, reescrita inevitável.

A **dívida técnica** — termo cunhado por Ward Cunningham em 1992 — é a metáfora financeira que captura essa dinâmica: assim como uma dívida financeira cobra juros, código de baixa qualidade cobra juros na forma de tempo gasto em cada mudança subsequente.

> 💡 **Intuição:** Imagine duas oficinas mecânicas. A primeira mantém ferramentas organizadas, manuais atualizados, peças identificadas. A segunda joga tudo em uma pilha. Ambas começam abrindo a mesma quantidade de carros por dia. Mas no terceiro mês, a segunda oficina gasta uma hora procurando uma chave que a primeira encontra em 10 segundos. *Isso é dívida técnica*. Não é uma "qualidade extra para entusiastas" — é o que separa equipes que entregam rápido por anos das que entregam rápido por seis meses.
> 

### 1.1 Qualidade interna vs qualidade externa

Há duas dimensões complementares e frequentemente confundidas:

| Qualidade | O que é | Quem percebe |
| --- | --- | --- |
| **Externa** | Funciona? Responde rápido? É segura? Não tem bugs? | Usuários e *stakeholders* |
| **Interna** | É legível? É testável? É modificável? | Desenvolvedores |

A qualidade externa é a que paga o boleto — sem ela, ninguém usa o produto. A qualidade interna é a que **permite a qualidade externa ao longo do tempo**: software com qualidade interna ruim eventualmente perde qualidade externa, porque corrigir bugs e adicionar features fica progressivamente impossível.

A norma **ISO/IEC 25010** organiza qualidade em oito atributos (performance, segurança, manutenibilidade, etc.), mas para nossos propósitos a divisão interna/externa é a navegação prática.

---

## 2. Princípios de Código Limpo

Robert C. Martin, em *Clean Code* (2008), consolidou um conjunto de princípios para a qualidade interna do código. Não são regras mecânicas — são *heurísticas* desenvolvidas por uma comunidade ao longo de décadas. As mais importantes:

### 2.1 Nomes significativos

Nomes devem responder *três perguntas*: o que é? O que faz? Como é usado?

```java
// Ruim
int d;             // o que é d?
List<int[]> list1; // o que tem na lista?

// Melhor
int diasDesdeUltimaCompra;
List<int[]> coordenadasDeCelulasMarcadas;
```

Algumas regras:

- **Evite abreviações desnecessárias.** O autocomplete da IDE digita por você.
- **Nomes pronunciáveis.** Em *code review*, você precisa falar deles em voz alta.
- **Nomes pesquisáveis.** `MAX_TENTATIVAS = 5` é encontrável; `5` espalhado pelo código não.
- **Classes são substantivos** (`Pedido`, `CalculadoraFrete`). **Métodos são verbos** (`calcular`, `validar`, `enviar`).
- **Não tema nomes longos.** `calcularTotalComImpostosEDescontos()` é melhor que `calc()`.

### 2.2 Funções pequenas, fazendo uma coisa

A regra de Martin: **funções devem ser pequenas. Depois, devem ser ainda menores.** Idealmente, abaixo de 20 linhas.

Por quê? Porque uma função pequena:

- É lida de uma só vez, sem rolagem mental.
- Tem um único motivo para mudar (Princípio da Responsabilidade Única — SRP).
- É testável.
- É reutilizável.
- Tem nome significativo (justamente porque faz *uma coisa*).

A pergunta diagnóstica: *"posso dar a esta função um nome que descreva exatamente o que ela faz, sem usar 'e' nem 'ou'?"* Se não, ela faz coisas demais.

### 2.3 Comentários: quando, como — e quando não

Há uma escola que diz "código limpo não precisa de comentários". Há outra que diz "comente tudo". Ambas estão erradas.

**Comentários ruins (devem ser removidos):**

```java
// Incrementa i
i++;

// Verifica se a idade é maior que 18
if (idade > 18) { /*...*/ }

// Função que calcula o total
public double calcular() { /*...*/ }
```

São redundantes — o código já diz isso.

**Comentários ruins disfarçados de bons:**

```java
// Precisa ser maior que 18 porque é a idade legal
if (idade > 18) { /*...*/ }
```

Melhor: extraia para uma constante nomeada.

```java
private static final int IDADE_LEGAL = 18;
if (idade >= IDADE_LEGAL) { /*...*/ }
```

**Comentários bons (essenciais):**

- **Explicar *por quê*, quando o código não pode**: "este `Thread.sleep(100)` existe porque a API do gateway tem rate limit de 10 req/s e nem documentação a respeito" — código não revela isso.
- **Avisos sobre consequências**: `// CUIDADO: alterar a ordem dos campos quebra serialização do log de produção.`
- **Documentação pública** (Javadoc/Docstrings): contratos, parâmetros, exceções de uma API que outros desenvolvedores consumirão.
- **TODO/FIXME**: marca explícita de débito técnico, com responsável e prazo se possível.

> 💡 **Intuição:** Comentários são como uma admissão de derrota. Você só precisa deles quando *o código não conseguiu se explicar sozinho*. Antes de escrever um comentário, pergunte: "consigo renomear, extrair função ou reorganizar para tornar este comentário desnecessário?". Frequentemente, sim.
> 

### 2.4 DRY — Don't Repeat Yourself

Andy Hunt e Dave Thomas, em *The Pragmatic Programmer* (1999):

> Cada peça de conhecimento deve ter uma representação única, não-ambígua e autorizada dentro de um sistema.
> 

Note que DRY não é sobre "não copie linhas iguais". É sobre **conhecimento**. Se duas funções *parecem* iguais mas representam decisões diferentes que poderiam evoluir independentemente, deixá-las separadas é correto. DRY violado de fato é quando uma decisão de negócio (regra de cálculo, validação, fórmula) está expressa em três lugares — porque mudar a regra exige caçar todas as cópias, e algumas serão esquecidas.

### 2.5 Recapitulando SOLID

Vimos os princípios SOLID atravessando aulas anteriores; vale recapitular:

| Sigla | Princípio | Em uma frase |
| --- | --- | --- |
| **S**RP | *Single Responsibility* | Uma classe deve ter um único motivo para mudar. |
| **O**CP | *Open/Closed* | Aberta para extensão, fechada para modificação. |
| **L**SP | *Liskov Substitution* | Subtipos devem substituir seus supertipos sem surpresa. |
| **I**SP | *Interface Segregation* | Interfaces específicas são melhores que uma genérica e gigante. |
| **D**IP | *Dependency Inversion* | Dependa de abstrações, não de implementações concretas. |

SOLID não é dogma — é um conjunto de heurísticas. Um sistema "perfeitamente SOLID" pode ser tão sobre-engenhado quanto um sistema "anti-SOLID" pode ser frágil. Como sempre, o discernimento manda.

---

## 3. *Code Smells* — diagnóstico antes do tratamento

Em *Refactoring* (1999, 2ª ed. 2018), Martin Fowler popularizou a metáfora dos **cheiros de código**: sintomas observáveis na superfície que sugerem problemas mais profundos. *Smells* não são bugs — código *fede* mas funciona. O problema é que o sistema fica progressivamente mais difícil de modificar.

Apresentamos os mais importantes — não como uma lista enciclopédica para decorar, mas como vocabulário diagnóstico que você reconhecerá em código real (inclusive no seu próprio).

### 3.1 *Long Method* (Método Longo)

Um método com 100, 200, 500 linhas. Quase sempre faz coisas demais.

**Diagnóstico:** se você precisa rolar a tela para vê-lo todo, ou se ele tem comentários separando "blocos lógicos", o método é longo demais.

**Tratamento principal:** *Extract Method* (extrair os blocos em métodos com nomes significativos).

### 3.2 *Large Class* / *God Class* (Classe Grande)

Uma classe com 50 atributos, 100 métodos, 3.000 linhas. Tipicamente uma classe `Pedido` que sabe sobre clientes, pagamentos, frete, notificações, e ainda controla a impressão da nota fiscal.

**Diagnóstico:** ela mudou por motivos completamente diferentes nas últimas 10 *commits*.

**Tratamento principal:** *Extract Class* (separar responsabilidades em classes coesas).

### 3.3 *Long Parameter List* (Lista Longa de Parâmetros)

```java
calcularFrete(cep, peso, altura, largura, profundidade, valor, urgencia,
              tipoEmbalagem, ehFragil, codigoCliente, dataPedido)
```

**Diagnóstico:** o método pede informações demais. Quem chama precisa lembrar a ordem; quem mantém precisa atualizar 30 chamadas quando adiciona um parâmetro.

**Tratamento principal:** *Introduce Parameter Object* (`calcularFrete(Encomenda enc)`).

### 3.4 *Duplicated Code* (Código Duplicado)

A primeira vez é coincidência. A segunda é descuido. A terceira pede refatoração.

**Diagnóstico clássico:** dois trechos diferindo apenas em um nome de variável ou um valor literal.

**Tratamento:** *Extract Method* (e às vezes *Pull Up Method* se as cópias estão em subclasses irmãs).

### 3.5 *Switch Statements* / Cadeias de `if/else` por Tipo

```java
switch (pedido.getTipo()) {
    case "FISICO": /* lógica */ break;
    case "DIGITAL": /* lógica */ break;
    case "ASSINATURA": /* lógica */ break;
}
```

Quando você vê este *smell* em três lugares diferentes, eis o desastre: adicionar `"BRINDE"` exige tocar nos três.

**Tratamento principal:** *Replace Conditional with Polymorphism* — promova cada caso a uma classe (Strategy ou State, dependendo do contexto).

### 3.6 *Feature Envy* (Inveja de Funcionalidade)

Um método de uma classe que mexe muito mais com dados de *outra* classe do que com os próprios. Ele "queria ser" da outra classe.

```java
public class CalculadoraFrete {
    public double calcular(Pedido p) {
        double peso = p.getItens().stream().mapToDouble(Item::getPeso).sum();
        double dim = p.getItens().stream().mapToDouble(Item::getVolume).sum();
        boolean fragil = p.getItens().stream().anyMatch(Item::ehFragil);
        // ... usa pedido o tempo todo, mal usa atributos próprios ...
    }
}
```

**Tratamento:** *Move Method* — o cálculo provavelmente pertence a `Pedido`, ou a uma terceira classe que recebe o pedido como parâmetro com mais coesão.

### 3.7 *Data Clumps* (Agrupamento de Dados)

Os mesmos três ou quatro parâmetros aparecem juntos em vários métodos: `(rua, numero, cidade, cep)`. **Esses parâmetros estão pedindo para ser uma classe**.

**Tratamento:** *Introduce Parameter Object* ou *Extract Class* (`Endereco`).

### 3.8 *Primitive Obsession* (Obsessão por Primitivos)

Tudo é `String`, `int`, `double`. CPFs são `String` (e CEPs são `String`, e códigos de produto são `String`...). Você acaba comparando uma `String` que era CPF com uma `String` que era CEP — e o compilador não te avisa.

**Tratamento:** crie tipos próprios — `CPF`, `CEP`, `CodigoProduto`. Pequenos *value objects* com validação no construtor previnem categorias inteiras de bugs.

### 3.9 *Shotgun Surgery* (Cirurgia com Espingarda)

Cada vez que você quer mudar uma decisão (ex.: a alíquota de imposto), precisa modificar arquivos em dez lugares diferentes.

**Diagnóstico:** *single responsibility* foi violado — uma decisão está espalhada.

**Tratamento:** *Move Method* / *Move Field* para concentrar a decisão em uma única classe.

### 3.10 *Divergent Change* (Mudança Divergente)

O oposto de Shotgun Surgery: uma mesma classe muda por motivos completamente diferentes (ora porque mudou o cálculo de imposto, ora porque mudou o formato de e-mail, ora porque mudou a regra de cancelamento).

**Diagnóstico:** SRP violado — a classe tem múltiplas responsabilidades.

**Tratamento:** *Extract Class*.

### 3.11 *Comments* como cheiro

Polêmico, mas válido: comentários *podem* ser sintoma de código que não consegue se explicar. Antes de escrever um, tente refatorar para que o comentário se torne desnecessário.

> 💡 **Intuição:** *Smells* não são uma sentença. Sistemas reais convivem com vários deles indefinidamente — e tudo bem. O importante é **reconhecer quando um *smell* está bloqueando uma mudança que você precisa fazer agora**. Aí é hora de refatorar. Refatorar todos os *smells* preventivamente é tão problemático quanto ignorá-los.
> 

---

## 4. Refatoração — o tratamento

Fowler define refatoração assim:

> Uma alteração feita na estrutura interna do software para torná-lo mais fácil de entender e mais barato de modificar **sem alterar o seu comportamento observável**.
> 

A frase-chave é a final. Refatorar **não é**:

- "Reescrever do zero". Reescrever é abandonar o investimento existente — geralmente um desastre, como Joel Spolsky famoso defendeu em "Things You Should Never Do, Part I" (2000).
- "Adicionar feature". Se enquanto reorganiza você também muda o que o sistema faz, perde-se a rede de segurança — não há como saber se o problema veio da reorganização ou da nova lógica.

### 4.1 Os "dois chapéus" de Kent Beck

Kent Beck propõe uma metáfora poderosa: ao programar, você usa um de dois chapéus, **um de cada vez**:

- **Chapéu de "adicionar funcionalidade"**: você modifica o comportamento do sistema. Pode ficar feio temporariamente; o objetivo é fazer o teste novo passar.
- **Chapéu de "refatorar"**: você reorganiza o código. Os testes existentes continuam passando — nenhum novo é adicionado.

O fluxo profissional alterna constantemente entre os dois. Mas **nunca os dois ao mesmo tempo**: senão, quando um teste quebra, você não sabe se foi a feature ou o rearranjo.

### 4.2 Catálogo de refatorações fundamentais

Fowler catalogou ~70 refatorações; aqui apresentamos as 6-8 mais frequentes — as que cobrem ~80% dos casos.

### *Extract Method*

Sem dúvida a refatoração mais usada. Pegue um trecho de código com lógica coesa, extraia em um método separado com nome significativo.

**Antes:**

```java
public void imprimirNotaFiscal(Pedido p) {
    System.out.println("=== NOTA FISCAL ===");
    System.out.println("Pedido: " + p.getId());
    System.out.println("Cliente: " + p.getCliente().getNome());

    // Calcular total com impostos
    double subtotal = 0;
    for (Item i : p.getItens()) {
        subtotal += i.getPreco() * i.getQuantidade();
    }
    double imposto = subtotal * 0.18;
    double total = subtotal + imposto;

    System.out.println("Subtotal: " + subtotal);
    System.out.println("Imposto: " + imposto);
    System.out.println("Total: " + total);
}
```

**Depois:**

```java
public void imprimirNotaFiscal(Pedido p) {
    imprimirCabecalho(p);
    double subtotal = calcularSubtotal(p);
    double imposto = calcularImposto(subtotal);
    imprimirValores(subtotal, imposto);
}

private double calcularSubtotal(Pedido p) {
    return p.getItens().stream()
            .mapToDouble(i -> i.getPreco() * i.getQuantidade())
            .sum();
}

private double calcularImposto(double subtotal) {
    return subtotal * ALIQUOTA_IMPOSTO;
}
```

### *Rename* (renomear)

A refatoração mais subestimada. Renomeie variáveis, métodos, classes para nomes que comuniquem a intenção. IDEs modernas fazem isso com segurança em segundos.

### *Inline Method* (o oposto de Extract)

Se um método não acrescenta nada (`int dobro(int x) { return x * 2; }` chamado uma única vez), substitua a chamada pelo conteúdo. Não tenha medo de desfazer extrações.

### *Extract Variable*

Pegue uma expressão complicada, atribua a uma variável com nome significativo.

```java
// Antes
if ((cliente.getDataNascimento().isBefore(LocalDate.now().minusYears(60)))
    && pedido.getValor() > 500) { /* ... */ }

// Depois
boolean clienteIdoso = cliente.getDataNascimento()
                              .isBefore(LocalDate.now().minusYears(60));
boolean pedidoAcimaDoMinimo = pedido.getValor() > 500;
if (clienteIdoso && pedidoAcimaDoMinimo) { /* ... */ }
```

### *Replace Magic Number with Symbolic Constant*

```java
// Antes
if (idade >= 18) { /* ... */ }
double imposto = subtotal * 0.18;

// Depois
private static final int IDADE_LEGAL = 18;
private static final double ALIQUOTA_ICMS = 0.18;

if (idade >= IDADE_LEGAL) { /* ... */ }
double imposto = subtotal * ALIQUOTA_ICMS;
```

### *Introduce Parameter Object*

Vimos no *smell* de *Data Clumps*. Quatro parâmetros que sempre andam juntos viram uma classe.

### *Move Method* / *Move Field*

Um método (ou campo) está na classe errada. Mova-o para onde pertence — frequentemente para a classe cujos dados ele mais consome (combatendo *Feature Envy*).

### *Replace Conditional with Polymorphism*

A refatoração que produz, naturalmente, os padrões **Strategy** ou **State**.

**Antes:**

```java
public double calcularFrete(Pedido p) {
    switch (p.getTipoFrete()) {
        case "CORREIOS":
            return 10.0 + p.getPesoTotal() * 1.5;
        case "TRANSPORTADORA":
            return p.getValorTotal() * 0.05;
        case "RETIRADA":
            return 0.0;
        default:
            throw new IllegalStateException();
    }
}
```

**Depois (Strategy emerge):**

```java
public interface EstrategiaFrete {
    double calcular(Pedido p);
}

public class FreteCorreios implements EstrategiaFrete {
    public double calcular(Pedido p) { return 10.0 + p.getPesoTotal() * 1.5; }
}
// ... outras estratégias ...

public double calcularFrete(Pedido p, EstrategiaFrete e) {
    return e.calcular(p);
}
```

> 💡 **Intuição:** Esta é a observação que fecha o ciclo da disciplina: **muitos padrões de projeto são *destinos naturais* de refatoração, não pontos de partida**. Um desenvolvedor experiente não decide "vou aplicar Strategy aqui"; ele percebe um *switch* feio crescendo, sente o desconforto, e refatora — chegando a Strategy quase sem perceber. Foi assim que Joshua Kerievsky escreveu *Refactoring to Patterns* (2004): catalogando as *trilhas* de refatoração que conduzem a cada padrão.
> 

### 4.3 Refatoração em pequenos passos

A regra de ouro de Fowler: **passos pequenos, testes entre cada passo**. Nunca: "refatorar 4 horas, ver se compila". Sempre: "extrair um método, rodar testes; renomear, rodar testes; mover, rodar testes". Cada passo, em segundos.

Por quê? Porque se algo quebra, você sabe exatamente o quê: foi a última mudança. Se você fez 200 mudanças, descobrir o erro consome o dia.

---

## 5. Testes — a rede de segurança

Refatorar **sem testes automatizados é imprudente**. É como trapezistas trabalhando sem rede: pode dar certo, mas o custo do erro é catastrófico.

### 5.1 A pirâmide de testes

```
                        ╱╲
                       ╱  ╲
                      ╱ E2E╲      ← Poucos: lentos, frágeis, caros
                     ╱──────╲
                    ╱        ╲
                   ╱Integração╲   ← Alguns: validam interações reais
                  ╱────────────╲
                 ╱              ╲
                ╱   Unitários    ╲ ← Muitos: rápidos, focados, baratos
               ╱──────────────────╲
```

- **Unitários**: testam uma classe ou função isoladamente, com mocks. Milhões deles, rodando em segundos. Base da pirâmide.
- **Integração**: testam módulos juntos (ex.: serviço + repositório + BD em memória). Centenas, rodando em minutos.
- **End-to-End (E2E)**: simulam o usuário final atravessando o sistema inteiro (UI → API → BD → fora). Dezenas, rodando em dezenas de minutos.

A inversão da pirâmide (poucos unitários, muitos E2E) é um anti-padrão clássico em equipes inexperientes — testes ficam lentos demais para rodar a cada mudança, e eventualmente são desligados.

### 5.2 Testes de caracterização para código legado

Você herdou um sistema de 300.000 linhas sem **um teste**. Não há tempo nem orçamento para testar tudo. Como começar a refatorar com segurança?

**Resposta**: testes de caracterização (Michael Feathers, *Working Effectively with Legacy Code*, 2004).

A ideia é simples: você **não sabe** o comportamento correto do código — mas pode **observar o comportamento atual** e fixá-lo em testes. Esses testes não validam que o código está certo; validam que **a refatoração não mudou o comportamento**. Que era, afinal, a definição de refatoração.

Procedimento:

1. Escolha um trecho que precisa refatorar.
2. Escreva testes que invocam o trecho com várias entradas e capturam o que ele retorna *hoje*.
3. Rode — todos passam (afinal, o código *é* o que faz).
4. Refatore. Se algum teste quebrar, ou (a) você mudou comportamento sem querer (ruim), ou (b) o teste capturava um bug que você corrigiu (bom).

> 💡 **Intuição:** Testes de caracterização são uma fotografia do comportamento atual. Eles não dizem "está certo"; dizem "está assim". Refatoração honesta preserva o "assim". Quando você descobre um bug durante a refatoração, há decisão consciente: corrijo agora (e atualizo o teste) ou deixo para depois (mantendo o teste fixando o comportamento esquisito)?
> 

### 5.3 TDD — uma palavra

*Test-Driven Development* (Beck, 2002) propõe escrever o teste **antes** do código de produção, em ciclos curtos: *Red* (escreva teste que falha) → *Green* (faça-o passar com o mínimo possível) → *Refactor* (limpe).

Não é unanimidade — há equipes excelentes que praticam TDD religiosamente, e outras que escrevem testes depois e produzem código de igual qualidade. Mas há consenso em uma coisa: **escreva o teste em algum momento, antes do código entrar em produção**. Sistemas sem testes acabam com refatoração impossível, e arquitetura aprisionada nas más decisões de cinco anos atrás.

---

## 6. Dívida técnica revisitada

Voltamos à metáfora de Cunningham com uma ferramenta de classificação. Martin Fowler propôs em 2009 um **quadrante de dívida técnica** baseado em duas dimensões:

|  | **Prudente** (sabíamos os trade-offs) | **Imprudente** (descuido) |
| --- | --- | --- |
| **Deliberada** | "Vamos enviar agora e tratar as consequências." | "Não temos tempo para design." |
| **Inadvertida** | "Agora sabemos como deveríamos ter feito." | "O quê é design em camadas?" |
- **Deliberada-Prudente** é dívida saudável: você conscientemente troca tempo agora por trabalho futuro, sabendo o que está fazendo. Ex.: lançar uma versão com hardcode para validar produto-mercado, com plano explícito de refatoração se decolar.
- **Deliberada-Imprudente** é a "pressa que mata": você sabe que está fazendo errado, mas faz mesmo assim — sem plano, sem registro, sem responsável. Geralmente vira dívida sem fim.
- **Inadvertida-Prudente** é o aprendizado natural: você fez o melhor que sabia, e em retrospecto descobriu uma forma melhor. Não é falha; é evolução.
- **Inadvertida-Imprudente** é a dívida do desconhecimento: a equipe não sabe que existe um problema. Sai cara: descoberta tardia, frequentemente quando o sistema já está em crise.

A lição: **dívida não é necessariamente ruim** (deliberada-prudente é decisão estratégica), mas **toda dívida deve ser visível**. Use TODOs, ADRs (vimos na aula passada), *issue trackers* — não deixe a dívida invisível.

### 6.1 Quando pagar?

Dívida invisível ou esquecida não é paga. Dívida documentada precisa de critério. Heurística prática:

- **Pague quando estiver na vizinhança.** Se você precisa modificar a classe `Pedido` para uma feature nova, e há dívida documentada nela — aproveite e refatore. Nunca há momento melhor que "já estou aqui mexendo".
- **Pague quando bloqueia.** Se a próxima feature é difícil *por causa* de um *smell* específico, a refatoração não é luxo — é parte do trabalho.
- **Pague quando o juro fica caro demais.** Se cada *bugfix* nessa área leva o triplo do tempo, é hora de uma refatoração maior.
- **Não pague tudo de uma vez.** "Sprint de refatoração" raramente é bem-vista por *stakeholders* não-técnicos, e raramente termina onde se planejou.

### 6.2 A "Boy Scout Rule"

Robert Martin popularizou uma diretriz simples atribuída ao código de honra dos escoteiros:

> Sempre deixe o código mais limpo do que você o encontrou.
> 

Cada *commit* em uma área pode incluir uma melhoria pequena: um nome melhor, um método extraído, uma constante nomeada. Aplicada por toda a equipe, ao longo de meses, essa prática reverte degradação sem nunca exigir uma "sprint de refatoração" formal.

---

## 7. Boas práticas

1. **Leia o código mais do que escreve.** Profissionais experientes gastam muito mais tempo lendo código do que escrevendo. A qualidade interna é otimização para o leitor.
2. **Code review é refatoração colaborativa.** Use *pull requests* não só para aprovar mudanças, mas para sugerir extrações, renomeações, simplificações.
3. **Configurações de IDE são parte do projeto.** Linters, formatadores, regras de import — tudo isso versionado no repositório, garantindo consistência.
4. **Métricas são úteis, não absolutas.** Cobertura de testes, complexidade ciclomática, duplicação — são bons termômetros, péssimos chefes. Equipes que perseguem 100% de cobertura cega frequentemente entregam testes inúteis.
5. **Combata o medo de refatorar.** O maior obstáculo à refatoração contínua não é técnico — é cultural. Se desenvolvedores têm medo de mexer em código por medo de quebrar, sua equipe tem um problema de testes ou de cultura, ou ambos.

---

## 8. Exemplos resolvidos

### Exemplo Resolvido 1 — Refatorando um método "fedido" passo a passo (Introdutório) ⭐

**Enunciado:** O método abaixo, do nosso e-commerce, está cheio de *smells*. Identifique-os e refatore em pequenos passos, nomeando cada refatoração aplicada.

**Código original:**

```java
public class Pedido {
    public double calcular() {
        double t = 0;
        for (Item i : itens) {
            t += i.getPreco() * i.getQuantidade();
        }

        // Aplica desconto se cliente é VIP
        if (cliente.getTipoCliente().equals("VIP")) {
            t = t * 0.9;
        }

        // Aplica imposto
        t = t + (t * 0.18);

        // Frete
        if (cep.startsWith("01") || cep.startsWith("02") || cep.startsWith("03")
            || cep.startsWith("04") || cep.startsWith("05")) {
            t = t + 15.00;
        } else {
            t = t + 35.00;
        }

        return t;
    }
}
```

**Diagnóstico (smells encontrados):**

1. *Long Method* — vários blocos lógicos diferentes em um método.
2. *Magic Numbers* — `0.9`, `0.18`, `15.00`, `35.00` sem nome.
3. *Magic Strings* — `"VIP"`, prefixos de CEP soltos.
4. *Comments as smell* — cada bloco precisa de comentário porque não tem nome.
5. *Switch Statement* implícito sobre tipo de cliente (vai crescer).
6. *Primitive Obsession* — CEP é `String` solta; tipo de cliente é `String` solta.
7. *Variable name `t`* — incompreensível.

**Refatoração — passo 1: renomear (`Rename`).**

```java
public double calcular() {
    double total = 0;
    for (Item i : itens) {
        total += i.getPreco() * i.getQuantidade();
    }
    // (resto igual, com 't' substituído por 'total')
}
```

**Passo 2: extrair constantes (`Replace Magic Number with Symbolic Constant`).**

```java
private static final double DESCONTO_VIP = 0.10;
private static final double ALIQUOTA_IMPOSTO = 0.18;
private static final double FRETE_LOCAL = 15.00;
private static final double FRETE_OUTRAS_REGIOES = 35.00;
```

**Passo 3: extrair métodos (`Extract Method`) — um para cada responsabilidade do código original:**

```java
public double calcular() {
    double subtotal = calcularSubtotal();
    double comDesconto = aplicarDesconto(subtotal);
    double comImposto = aplicarImposto(comDesconto);
    double frete = calcularFrete();
    return comImposto + frete;
}

private double calcularSubtotal() {
    return itens.stream()
                .mapToDouble(i -> i.getPreco() * i.getQuantidade())
                .sum();
}

private double aplicarDesconto(double valor) {
    if (cliente.ehVIP()) {           // << usa método do Cliente, não compara String
        return valor * (1 - DESCONTO_VIP);
    }
    return valor;
}

private double aplicarImposto(double valor) {
    return valor * (1 + ALIQUOTA_IMPOSTO);
}

private double calcularFrete() {
    return ehCepRegiaoLocal() ? FRETE_LOCAL : FRETE_OUTRAS_REGIOES;
}

private boolean ehCepRegiaoLocal() {
    return cep.startsWith("01") || cep.startsWith("02") || cep.startsWith("03")
        || cep.startsWith("04") || cep.startsWith("05");
}
```

**Passo 4 (futuro): conforme `tipoCliente` cresça (Premium, Diamond, etc.), aplicar `Replace Conditional with Polymorphism` em `aplicarDesconto` — chegando ao padrão Strategy. Conforme regiões de frete cresçam, fazer o mesmo. O código está pronto para essas evoluções.**

**Lições:**

- O método final tem 5 linhas e diz exatamente o que faz: `subtotal → desconto → imposto → frete`. É autoexplicativo.
- Os comentários originais ("Aplica desconto se cliente é VIP") sumiram porque os nomes os tornaram redundantes.
- Cada método extraído pode agora ser **testado isoladamente** — `aplicarImposto(100)` deve dar `118`. Antes não havia como testar isoladamente.
- Note que o **comportamento observável não mudou**: dada a mesma entrada, a saída é a mesma. Isso é refatoração honesta.

### Exemplo Resolvido 2 — *Replace Conditional with Polymorphism* aplicado a estados (Intermediário) ⭐⭐

**Enunciado:** O código abaixo é uma versão sem padrões da máquina de estados do `Pedido` que vimos. Refatore-o aplicando *Replace Conditional with Polymorphism*, chegando ao padrão State. Use testes de caracterização para garantir segurança.

**Código original (com cheiros graves):**

```java
public class Pedido {
    private String estado = "PENDENTE";  // PENDENTE, PAGO, ENVIADO, ENTREGUE, CANCELADO

    public void pagar() {
        if (estado.equals("PENDENTE")) {
            estado = "PAGO";
            log("pago em " + LocalDateTime.now());
        } else {
            throw new IllegalStateException("Não pode pagar em estado " + estado);
        }
    }

    public void enviar() {
        if (estado.equals("PAGO")) {
            estado = "ENVIADO";
            log("enviado em " + LocalDateTime.now());
        } else {
            throw new IllegalStateException("Não pode enviar em estado " + estado);
        }
    }

    public void cancelar() {
        if (estado.equals("PENDENTE") || estado.equals("PAGO")) {
            estado = "CANCELADO";
            log("cancelado em " + LocalDateTime.now());
        } else {
            throw new IllegalStateException("Não pode cancelar em estado " + estado);
        }
    }

    public void confirmar() {
        if (estado.equals("ENVIADO")) {
            estado = "ENTREGUE";
            log("entregue em " + LocalDateTime.now());
        } else {
            throw new IllegalStateException("Não pode confirmar em estado " + estado);
        }
    }
}
```

**Diagnóstico:**

- *Switch on type* (sobre `estado`) repetido em quatro métodos.
- *Magic strings* (`"PENDENTE"`, `"PAGO"`...) — qualquer typo passa pelo compilador.
- Adicionar um quinto estado (ex.: `EM_DEVOLUCAO`) exige modificar **os quatro métodos**. Anti-OCP gritante.
- Adicionar uma quinta operação (ex.: `solicitarReembolso`) exige escrever o switch *de novo*.

**Passo 0 — Testes de caracterização:**

Antes de qualquer refatoração, escrevemos testes que fixam o comportamento atual:

```java
@Test
void pedidoPendentePodeSerPago() {
    Pedido p = new Pedido();
    p.pagar();
    assertEquals("PAGO", p.getEstado());
}

@Test
void pedidoEntregueNaoPodeSerCancelado() {
    Pedido p = new Pedido();
    p.pagar();
    p.enviar();
    p.confirmar();
    assertThrows(IllegalStateException.class, () -> p.cancelar());
}

@Test
void pedidoCanceladoNaoPodeSerPago() {
    Pedido p = new Pedido();
    p.cancelar();
    assertThrows(IllegalStateException.class, () -> p.pagar());
}

// ... ~12 testes cobrindo todas as transições válidas e inválidas
```

**Passo 1 — Introduzir interface `EstadoPedido`:**

```java
public interface EstadoPedido {
    void pagar(Pedido p);
    void enviar(Pedido p);
    void cancelar(Pedido p);
    void confirmar(Pedido p);
    String getNome();
}
```

**Passo 2 — Criar uma classe por estado:**

```java
public class Pendente implements EstadoPedido {
    @Override
    public void pagar(Pedido p) {
        p.setEstado(new Pago());
        p.log("pago em " + LocalDateTime.now());
    }

    @Override
    public void enviar(Pedido p) {
        throw new IllegalStateException("Não pode enviar em estado Pendente");
    }

    @Override
    public void cancelar(Pedido p) {
        p.setEstado(new Cancelado());
        p.log("cancelado em " + LocalDateTime.now());
    }

    @Override
    public void confirmar(Pedido p) {
        throw new IllegalStateException("Não pode confirmar em estado Pendente");
    }

    @Override
    public String getNome() { return "PENDENTE"; }
}

// ... análogos para Pago, Enviado, Entregue, Cancelado ...
```

**Passo 3 — Refatorar `Pedido` para delegar:**

```java
public class Pedido {
    private EstadoPedido estado = new Pendente();

    public void pagar()     { estado.pagar(this); }
    public void enviar()    { estado.enviar(this); }
    public void cancelar()  { estado.cancelar(this); }
    public void confirmar() { estado.confirmar(this); }

    public void setEstado(EstadoPedido novo) { this.estado = novo; }
    public String getEstado() { return estado.getNome(); }

    public void log(String msg) { /* ... */ }
}
```

**Passo 4 — Rodar os testes de caracterização:**

Todos passam. Comportamento preservado, estrutura transformada.

**Lições:**

- O `Pedido` ficou com **três linhas por método** — todas idênticas em estrutura. Em uma refatoração avançada, poderíamos até extrair isso em um único `delegar(operação)`, mas o ganho é marginal.
- Adicionar `EmDevolucao` agora exige **uma única classe nova** — sem tocar em `Pendente`, `Pago`, etc. (apenas no estado de origem da nova transição). OCP em ação.
- Adicionar `solicitarReembolso()` exige adicionar o método à interface e implementá-lo em cada estado, mas o compilador reclama em todos os pontos onde falta — diferente do cenário inicial, em que um *case* esquecido passaria silenciosamente.
- **Os testes de caracterização foram a rede de segurança.** Sem eles, qualquer pequeno deslize na transcrição introduziria bug indetectável.
- **Esta refatoração é exatamente o State pattern.** O padrão emerge da refatoração — não foi imposto. Foi *destino*, não *partida*.

> 💡 **Intuição:** O ciclo "código ingênuo → smell percebido → refatoração disciplinada → padrão emerge → testes garantem segurança" é a essência do trabalho profissional em código mantido. Aprenda esse ciclo, e os 23 padrões do GoF se tornam consequências naturais — em vez de 23 nomes a decorar.
> 

---

## Exercícios Práticos

### Exercício 1 — Diagnóstico e refatoração de método "fedido" ⭐

Considere o seguinte método de um sistema de biblioteca:

```java
public class Emprestimo {
    private LocalDate dataDevolucaoPrevista;
    private LocalDate dataDevolucaoEfetiva;
    private String tipoUsuario;  // "GRADUACAO", "POSGRADUACAO", "PROFESSOR"
    private double valorMulta;

    public void calcular() {
        if (dataDevolucaoEfetiva == null) return;
        long d = dataDevolucaoEfetiva.toEpochDay() - dataDevolucaoPrevista.toEpochDay();
        if (d <= 0) {
            valorMulta = 0;
        } else {
            if (tipoUsuario.equals("GRADUACAO")) {
                valorMulta = d * 0.5;
                if (d > 30) valorMulta = valorMulta + 10;
            } else if (tipoUsuario.equals("POSGRADUACAO")) {
                valorMulta = d * 0.3;
                if (d > 30) valorMulta = valorMulta + 5;
            } else if (tipoUsuario.equals("PROFESSOR")) {
                valorMulta = 0;
            }
        }
    }
}
```

Sua tarefa:

(a) Liste **pelo menos cinco** *code smells* presentes, nomeando cada um corretamente segundo o catálogo de Fowler.
(b) Aplique sequencialmente: *Rename*, *Extract Method*, *Replace Magic Number with Symbolic Constant*, e *Extract Variable* onde apropriado. Mostre o código após cada passo (ao menos 3 versões intermediárias).
(c) Identifique **um** *smell* que persiste mesmo após esses passos e proponha (em texto, sem implementar) qual padrão de projeto seria o destino natural de uma próxima refatoração — justificando.
(d) Discuta em 3-5 linhas: para garantir que a refatoração não muda o comportamento, que testes de caracterização você escreveria *antes* de começar?

### Exercício 2 — Refatoração de sistema legado com testes de caracterização ⭐⭐

Você herdou o código abaixo, sem testes, em um sistema de processamento de pedidos de uma empresa de comida congelada:

```java
public class ProcessadorPedido {

    public String processar(String dados) {
        // dados = "id|cliente|cep|item1:qtd1:preco1,item2:qtd2:preco2|tipoEntrega"
        String[] partes = dados.split("\\|");
        String id = partes[0];
        String cliente = partes[1];
        String cep = partes[2];
        String[] itens = partes[3].split(",");
        String tipoEntrega = partes[4];

        double total = 0;
        for (String item : itens) {
            String[] x = item.split(":");
            int q = Integer.parseInt(x[1]);
            double p = Double.parseDouble(x[2]);
            total = total + (q * p);
        }

        // congelados: refrigeração obrigatória, +R$ 8 por kg
        // (assumimos 0.5 kg por unidade)
        double pesoEstimado = 0;
        for (String item : itens) {
            String[] x = item.split(":");
            int q = Integer.parseInt(x[1]);
            pesoEstimado = pesoEstimado + q * 0.5;
        }
        double frete = 0;
        if (tipoEntrega.equals("EXPRESSA")) {
            frete = 25 + pesoEstimado * 8;
        } else if (tipoEntrega.equals("PADRAO")) {
            frete = 12 + pesoEstimado * 8;
        }

        // gera relatório
        StringBuilder sb = new StringBuilder();
        sb.append("Pedido #").append(id).append("\n");
        sb.append("Cliente: ").append(cliente).append("\n");
        sb.append("CEP: ").append(cep).append("\n");
        sb.append("Subtotal: R$").append(total).append("\n");
        sb.append("Frete: R$").append(frete).append("\n");
        sb.append("Total: R$").append(total + frete).append("\n");
        return sb.toString();
    }
}
```

Sua tarefa:

(a) Identifique **pelo menos sete** *code smells*, nomeando cada um. Espera-se que apareçam: *Long Method, Primitive Obsession, Duplicated Code, Magic Numbers, Magic Strings, Long Parameter (oculto na string), Feature Envy* — entre outros.

(b) **Antes** de refatorar, escreva pelo menos 4 testes de caracterização cobrindo: caso normal expressa, caso normal padrão, pedido com um único item, pedido com múltiplos itens.

(c) Refatore o código aplicando as seguintes refatorações (não necessariamente nessa ordem):

- *Extract Class* para criar `ItemPedido` (eliminando o parsing repetido).
- *Extract Class* para criar `DadosPedido` (resultado do parsing inicial).
- *Extract Method* várias vezes.
- *Replace Magic Number with Symbolic Constant*.
- *Replace Conditional with Polymorphism* para o `tipoEntrega` (chegando à Strategy).
- *Move Method* — o cálculo de peso não pertence ao processador, e sim ao item ou pedido.

Apresente o código final completo, com todas as classes envolvidas, e rode mentalmente os testes de caracterização para garantir que ainda passam.

(d) Discuta em pelo menos 6 linhas: **(i)** qual *smell* foi o "fio condutor" que motivou a maior parte das refatorações? **(ii)** que *categoria* da dívida técnica (use o quadrante de Fowler) você diria que esse código original representa, e por quê? **(iii)** se o autor original tivesse uma semana para reescrever isso, com tempo para pensar — você acha que ele teria chegado em uma estrutura próxima da refatorada, ou ainda assim teria caído em alguns dos mesmos vícios? Justifique com base no que você acha que vê em iniciantes.

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Martin, R. *Clean Code: A Handbook of Agile Software Craftsmanship*. Prentice Hall, 2008. (Bíblia da qualidade interna.)
- Fowler, M. *Refactoring: Improving the Design of Existing Code*. 2ª ed., Addison-Wesley, 2018. (Catálogo definitivo de smells e refatorações; a 2ª ed. usa JavaScript em vez de Java da 1ª.)
- Feathers, M. *Working Effectively with Legacy Code*. Prentice Hall, 2004. (Sobre refatoração de código sem testes; testes de caracterização vêm daqui.)
- Beck, K. *Test-Driven Development: By Example*. Addison-Wesley, 2002. (Texto fundador do TDD.)
- Kerievsky, J. *Refactoring to Patterns*. Addison-Wesley, 2004. (Trilhas de refatoração que conduzem a padrões — síntese explícita das duas tradições.)
- Hunt, A.; Thomas, D. *The Pragmatic Programmer: Your Journey to Mastery*. 20th Anniv. ed., Addison-Wesley, 2019. (Origem do DRY e de muitas heurísticas práticas.)
- Cunningham, W. "The WyCash Portfolio Management System". *OOPSLA '92 Experience Report*. (Texto curto de duas páginas onde a metáfora da dívida técnica é cunhada.)
- Fowler, M. "Technical Debt Quadrant". 2009. Disponível em martinfowler.com. (O quadrante apresentado na aula.)
- ISO/IEC 25010:2011. *Systems and software engineering — Systems and software Quality Requirements and Evaluation (SQuaRE)*. (Modelo formal de qualidade — referência normativa.)

---