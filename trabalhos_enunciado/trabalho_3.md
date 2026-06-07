# Trabalho 3 - Modelagem

# Trabalho de Modelagem — Engenharia de Software

> **Pré-requisitos entregues:**
- Documento de Visão do Produto (Trabalho 1)
- Documento de Requisitos (Trabalho 2 — casos de uso, MoSCoW, requisitos não funcionais)
> 
> 
> **Filosofia deste trabalho:** profundidade sobre largura. Em vez de modelar superficialmente todo o sistema, vocês vão **selecionar um subconjunto representativo dos requisitos** e modelá-lo em profundidade. A escolha do que modelar é parte do trabalho — e deve ser justificada.
> 

---

## 0. Seleção de escopo (faça antes de começar)

Antes de produzir qualquer diagrama, o grupo deve definir e **justificar por escrito** o que será modelado neste trabalho. Esta seção (~1-2 páginas) abre o documento.

A ideia é simples: vocês não vão modelar o sistema inteiro. Vão escolher **3 fatias verticais** representativas e modelá-las em profundidade. Modelar 3 fatias bem feitas ensina mais do que modelar 30 superficialmente.

### 0.1 O que é uma “fatia vertical”?

Uma **fatia vertical** é um caso de uso completo, ponta a ponta, atravessando os componentes que ele toca. Não é “uma tela”, não é “uma classe”. É um fluxo coerente que tem início (alguém aciona), meio (o sistema processa) e fim (algo muda no estado do sistema, alguém recebe valor).

Exemplos do que **é** fatia vertical:
- “Cliente finaliza um pedido de livros”
- “Lojista cadastra um livro no catálogo”
- “Entregador aceita uma entrega disponível”

Exemplos do que **não é** fatia vertical:
- “Tela de login” (interface, não fluxo)
- “Classe Pedido” (artefato, não fluxo)
- “Banco de dados” (infraestrutura)

### 0.2 Critérios obrigatórios de seleção

As 3 fatias escolhidas devem cobrir, no conjunto, **os três critérios abaixo** (uma fatia pode atender a mais de um critério):

- **Pelo menos 1 fatia *Must Have*** da priorização MoSCoW do Trabalho 2 — o coração do produto, sem o qual ele não existe.
- **Pelo menos 1 fatia que envolva mais de um subsistema ou tipo de usuário** — para que o modelo capture interações reais entre atores, não só comportamento de um ator isolado.
- **Pelo menos 1 fatia com regras de negócio não-triviais** — cálculos, transições de estado, decisões compostas. Modelo de funcionalidade trivial (CRUD puro) não revela problemas de design.

### 0.3 Estrutura da justificativa

Para cada fatia escolhida, escrevam 2-4 linhas explicando:

- Qual casos de uso (ou conjunto de casos) ela cobre.
- Por que ela é representativa o suficiente para merecer modelagem aprofundada.
- O que o grupo espera **aprender** modelando essa fatia (regra de negócio? fluxo distribuído? estado complexo?).

E, ao final, listem explicitamente os casos de uso do Trabalho 2 que **não** serão modeladas. O objetivo é deixar transparente o recorte feito.

### 0.4 Exemplo trabalhado — Marketplace de Livrarias e Sebos

Para ajudar a entender o que se espera, segue um exemplo de como um grupo modelo justificaria a seleção de escopo, usando o estudo de caso do Marketplace de Livrarias e Sebos.

> **Atenção:** este é um exemplo. Vocês podem (e devem) escolher fatias diferentes, conforme o foco que o grupo quer dar. O que importa é a **qualidade da justificativa**.
> 

### Subsistemas e atores identificados (recapitulação do Trabalho 1 e 2)

A plataforma tem três subsistemas principais:

| Subsistema | Atores principais |
| --- | --- |
| **Catálogo & Compras** | Cliente (usuário final), Lojista (livraria/sebo) |
| **Logística** | Lojista, Entregador, Cliente |
| **Pagamentos & Avaliações** | Cliente, Lojista, gateway de pagamento (externo) |

### Fatias selecionadas

### Fatia 1 — Cliente finaliza pedido de livro com pagamento

**Casos de uso cobertos:** US-CC-003 (“Como cliente, quero adicionar livros de diferentes lojistas ao carrinho para comparar antes de comprar”), US-CC-007 (“Como cliente, quero pagar meu pedido com cartão de crédito de forma segura”), US-PA-002 (“Como cliente, quero receber confirmação imediata após o pagamento aprovado”).

**Por que é representativa:**
- É **Must Have** absoluto — sem checkout, não há marketplace.
- Atravessa **três subsistemas**: Catálogo (validar disponibilidade), Pagamentos (autorizar transação), Logística (criar solicitação de entrega após pagamento aprovado).
- Envolve **gateway externo** (Stripe/Mercado Pago) — exercita modelagem de integração com sistema fora do controle do grupo.
- Tem **caminhos de erro críticos**: cartão recusado, item esgotado entre adicionar ao carrinho e finalizar, gateway fora do ar.

**O que esperamos aprender:**
- Como modelar um **fluxo síncrono distribuído** com retornos de gateway externo (provável diagrama de sequência).
- Como expressar **caminhos de exceção** (`alt`, `opt`) em UML.
- Como representar uma **transação que afeta entidades de subsistemas diferentes** (carrinho esvaziado, pedido criado, transação de pagamento registrada, solicitação de entrega aberta).

### Fatia 2 — Ciclo de vida da entrega, do aceite até a confirmação de recebimento

**Casos de uso cobertos:** US-LOG-004 (“Como entregador, quero visualizar entregas disponíveis perto de mim e aceitar a que eu quiser”), US-LOG-006 (“Como cliente, quero acompanhar em tempo real onde está meu pedido”), US-LOG-009 (“Como cliente, quero confirmar que recebi o pedido e avaliar o entregador”).

**Por que é representativa:**
- Envolve **três atores distintos**: Lojista (origina), Entregador (executa), Cliente (recebe).
- Tem **estado claro com transições**: `Aguardando Coleta → Aceita por Entregador → Em Coleta → Em Trânsito → Entregue → Avaliada`. Cada transição tem evento, regra e às vezes ação automática (notificação).
- Tem **regras de tempo e exceção**: o que acontece se o entregador não coletar em X horas? E se o pacote ficar parado?
- É um caso clássico onde **diagrama de estados é mais informativo que sequência** — exercita escolha consciente do tipo de diagrama.

**O que esperamos aprender:**
- Como modelar um **ciclo de vida com vários estados intermediários** e eventos de timeout.
- Como decompor um fluxo longo em fases sem perder a visão geral.
- A diferença prática entre “estado da entrega” (o que estamos modelando) e “estado do pedido” (que é diferente — pedido pode estar pago e ainda sem entrega aceita).

### Fatia 3 — Lojista cadastra livro com cálculo dinâmico de comissão

**Casos de uso cobertos:** US-CC-011 (“Como lojista, quero cadastrar um livro do meu acervo informando título, autor, edição, preço e estado de conservação”), US-CC-014 (“Como lojista, quero ver quanto vou receber líquido por venda, depois das taxas da plataforma”), US-CC-016 (“Como administrador da plataforma, quero definir comissões diferenciadas por categoria de livro”).

**Por que é representativa:**
- Tem **regras de negócio não-triviais**: a comissão da plataforma depende da categoria (novo / usado / raro), do volume de vendas do lojista (lojistas de alto volume pagam comissão menor) e de promoções vigentes.
- Envolve **decisão composta** ideal para tabela de decisão e tratamento polimórfico — exercita aplicação prática de Strategy ou State.
- É **Should Have** segundo nosso MoSCoW (não é a fatia mais crítica), mas foi escolhida porque expõe complexidade de domínio que as duas primeiras não capturam.
- Modela uma **entidade central** (`Livro`) com hierarquia (livro novo / usado / raro têm atributos e regras diferentes) — bom exercício de herança/composição.

**O que esperamos aprender:**
- Como representar **hierarquia de classes** (Livro → LivroNovo, LivroUsado, LivroRaro) com atributos específicos por subclasse.
- Como modelar **regras parametrizáveis** (tabela de comissões) sem hard-codar valores.
- A relação entre o **diagrama de classes** (estrutura) e o **diagrama de atividades** (fluxo de cálculo da comissão).

### Cobertura dos critérios

| Critério | Fatia 1 | Fatia 2 | Fatia 3 |
| --- | --- | --- | --- |
| Must Have do MoSCoW | ✅ Sim | ✅ Sim | ⚪ Should Have |
| Múltiplos subsistemas/atores | ✅ 3 subsistemas | ✅ 3 atores | ⚪ Foco em 1 subsistema |
| Regras de negócio não-triviais | ✅ Caminhos de exceção | ✅ Estados + timeouts | ✅ Comissão dinâmica |

Os três critérios estão cobertos. Cada fatia traz uma dimensão diferente do sistema.

### O que fica de fora (e por quê)

Os seguintes casos de uso do Trabalho 2 **não serão modeladas** neste trabalho:

- **Cadastro e autenticação de usuários** (US-CC-001, US-CC-002): fluxo padrão de login/cadastro, sem regra de negócio específica do domínio. Modelar não traria insight novo.
- **Sistema de recomendação** (US-CC-019, US-CC-020): mencionado no estudo de caso como diferencial, mas é uma funcionalidade que depende fortemente de algoritmos de ML/heurísticas, fora do escopo de modelagem orientada a domínio. Trataremos como uma “caixa preta” que consome dados do histórico.
- **Painel administrativo da plataforma** (US-PA-005 a US-PA-010): conjunto de telas operacionais relevantes em produção, mas predominantemente CRUD. Modelar tudo isso ocuparia espaço sem agregar aprendizado.
- **Avaliação do lojista pelo cliente** (US-PA-012): isolada, simples, e parcialmente coberta pela Fatia 2 (avaliação do entregador segue padrão similar).

A Fatia 2 modela o entregador; o lojista seguirá o **mesmo padrão** com pequenas adaptações — implícito na rastreabilidade.

### 0.5 Como usar este exemplo

Este é **um** exemplo de seleção. Outro grupo, mesmo com o mesmo estudo de caso, poderia ter feito escolhas diferentes — por exemplo:

- **Foco em catálogo e busca**: privilegiar a fatia “cliente busca livro com filtros e compara entre lojistas” em vez do checkout completo.
- **Foco em logística**: aprofundar mais na otimização de rotas e atribuição automática de entregadores.
- **Foco em moderação**: explorar o fluxo de moderação de cadastros (lojista novo vira lojista aprovado), que tem ciclo de vida interessante.

O critério não é “qual é a escolha certa?” — é **“a escolha está justificada e cobre a variedade exigida?”**.

O que **não** é uma boa seleção:

- Três fatias todas dentro do mesmo subsistema (não exercita interação).
- Três fatias todas CRUD simples (não exercita regra de negócio).
- “Vamos modelar ‘pedido’ inteiro” — pedido inteiro tem dezenas de fluxos; precisa recortar.
- “Escolhemos os três primeiros casos de uso do backlog” — sem critério, sem aprendizado deliberado.

---

## 1. Diagrama de classes

Modelem as classes envolvidas nas **3 fatias selecionadas** — não o sistema inteiro. Classes que aparecem em mais de uma fatia entram uma única vez.

Para cada classe, identifiquem:

- **Atributos** essenciais (nome, tipo, e visibilidade quando relevante).
- **Métodos** que representam responsabilidades — não apenas getters/setters triviais.
- **Relações** com outras classes do diagrama, com cardinalidade explícita: associação, agregação, composição, herança, dependência ou realização.

### 1.1 Conceitos de UML que vocês podem precisar

**Atributos** são características ou propriedades que definem uma classe. Para a classe `Pedido`, atributos podem ser `id`, `dataAbertura`, `valorTotal`, `status`. Para `Cliente`: `nome`, `cpf`, `email`.

**Métodos** são ações que a classe pode executar, baseados em suas responsabilidades. Para `Pedido`, métodos podem ser `adicionarItem(produto, qtd)`, `calcularTotal()`, `confirmarPagamento()`. Evitem listar apenas getters/setters — eles são óbvios em qualquer linguagem orientada a objetos.

**Tipos de relação:**

- **Associação**: relação entre duas classes. `Cliente` “faz” `Pedido` (linha simples ligando as duas).
- **Agregação**: uma classe é parte de outra, mas existe independentemente. Um `Catálogo` é uma agregação de `Produtos` — produtos existem mesmo sem catálogo (losango vazio do lado do todo).
- **Composição**: similar à agregação, mas a parte não existe sem o todo. `Pedido` é composto de `ItemPedido` — itens não existem fora de um pedido (losango cheio do lado do todo).
- **Herança**: subclasse de outra. `LivroUsado` herda de `Livro` (seta vazia apontando para a classe pai).
- **Realização** (implementação de interface): `CalculadoraComissaoPadrao` realiza a interface `CalculadoraComissao` (seta tracejada vazia).
- **Dependência**: uma classe usa outra em um método mas não a guarda como atributo. Tipicamente representada por linha tracejada com seta.

### 1.2 Critérios de qualidade

- Pelo menos uma classe abstrata ou interface, quando o domínio comportar.
- Multiplicidades em todas as associações (`1`, `0..1`, `0..*`, `1..*`).
- Sem “classes God” com 20+ atributos. Se aparecer, é sinal de que o domínio não foi decomposto.
- Coerência com o caso de uso: cada classe deve ter rastreabilidade clara para uma necessidade do Trabalho 2.

> 💡 **Dica do exemplo do marketplace:** se vocês forem modelar a Fatia 3 (cadastro com comissão), pensem se faz sentido ter `Livro` abstrato com subclasses `LivroNovo`, `LivroUsado`, `LivroRaro`, ou se uma única classe `Livro` com atributo `categoria` resolve. Há vantagens e desvantagens em cada escolha — discutam.
> 

---

## 2. Modelo Entidade-Relacionamento (MER)

A partir do diagrama de classes, elaborem o MER apenas para as **classes persistentes** das fatias selecionadas — ou seja, aquelas cujos dados precisam sobreviver ao final da execução do sistema.

### 2.1 O que precisa ser modelado

Para cada entidade:

- **Atributos com tipos** (ex.: `nome: VARCHAR(100)`, `data_pedido: TIMESTAMP`).
- **Chave primária** identificada — geralmente um `id` único.
- **Chaves estrangeiras** explícitas, ligando às entidades relacionadas.
- **Cardinalidades** nos relacionamentos.

### 2.2 Conceitos importantes

**Entidades** são objetos do mundo real sobre os quais o sistema precisa armazenar informações. Para o marketplace: `Livro`, `Cliente`, `Pedido`, `Entregador`, `Avaliacao`.

**Relacionamentos** indicam como entidades se associam. Ex.: “Cliente faz Pedido”, “Entregador realiza Entrega”.

**Cardinalidade** especifica quantas instâncias podem se relacionar:

- **Um para um (1:1)**: cada `Cliente` tem um `Endereço Principal`.
- **Um para muitos (1:N)**: um `Cliente` faz vários `Pedidos`, mas cada `Pedido` é de um único `Cliente`.
- **Muitos para muitos (N:N)**: `Pedidos` contêm vários `Livros`, e cada `Livro` aparece em vários `Pedidos`. Isso normalmente vira uma tabela associativa (`item_pedido`) na implementação.

### 2.3 Coerência com o diagrama de classes

Divergências entre o diagrama de classes e o MER são **esperadas e devem ser justificadas em ~1 parágrafo**. Casos comuns:

- **Relacionamento N:N no diagrama de classes vira tabela associativa** no MER.
- **Herança no diagrama de classes** pode virar tabela única com discriminador, tabela por subclasse, ou tabela por classe concreta — explicitem qual estratégia escolheram e por quê.
- **Atributos calculados** existem no diagrama de classes (ex.: `valorTotal()` como método) mas podem **não existir** no MER (são derivados, não persistidos) — ou existir como cache, dependendo da decisão.

Notação a critério do grupo (Chen, Crow’s Foot ou UML restrita a entidades), desde que consistente ao longo do trabalho.

---

## 3. Modelagem comportamental (escolham 2 entre 3)

Para as 3 fatias selecionadas, escolham **dois tipos de diagrama comportamental** entre os três abaixo, e produzam diagramas que cubram coletivamente todas as 3 fatias:

### 3.1 Opção A — Diagrama de Sequência

Adequado quando a fatia envolve **interação entre múltiplos componentes** (ator + sistema + serviços externos, ou múltiplos serviços conversando). Mostre lifelines, mensagens síncronas/assíncronas, retornos, e use fragmentos (`alt`, `opt`, `loop`) quando houver lógica condicional ou repetição.

**Elementos esperados:**

- **Participantes** (lifelines) na parte superior, cada um com sua linha vertical.
- **Mensagens** representadas por setas entre lifelines, com nome no formato `metodoChamado(parametros)`.
- **Retornos** como setas tracejadas (quando relevantes).
- **Ordem temporal**: o tempo flui de cima para baixo.
- **Fragmentos combinados** (`alt` para alternativas, `opt` para opcional, `loop` para repetição) quando o fluxo tem ramificações.

Exemplo de quando **escolher sequência**: “Cliente finaliza pedido com pagamento” — porque envolve cliente → frontend → backend → gateway de pagamento → broker de eventos, e cada um responde ao próximo.

### 3.2 Opção B — Diagrama de Estados

Adequado quando a fatia envolve uma **entidade com ciclo de vida bem definido**, com transições disparadas por eventos. Mostre estados, eventos, guardas e ações `entry/exit/do` quando relevante.

**Elementos esperados:**

- **Estados** representados como retângulos arredondados.
- **Transições** como setas rotuladas com `evento [guarda] / ação`.
- **Estado inicial** (círculo cheio) e **estados finais** (círculo com borda).
- **Ações** dentro de estados: `entry/`, `exit/`, `do/`.
- **Estados compostos** ou **regiões ortogonais** quando houver hierarquia ou paralelismo.

Exemplo de quando **escolher estados**: “Ciclo de vida da entrega” — porque há um claro `Aguardando Coleta → Aceita → Em Coleta → Em Trânsito → Entregue → Avaliada`, com regras de timeout e cancelamento em vários pontos.

### 3.3 Opção C — Diagrama de Atividades

Adequado quando a fatia envolve um **fluxo de trabalho com decisões, paralelismo, ou múltiplos atores em raias** (swimlanes). Mostre nós de decisão, *fork/join* para concorrência, e raias para deixar visível quem faz o quê.

**Elementos esperados:**

- **Atividades** como retângulos arredondados.
- **Nós de decisão** (losangos) e **merge** para reconvergência.
- ***Fork* e *join*** para representar paralelismo.
- **Raias (swimlanes)** quando há múltiplos atores ou subsistemas executando partes do fluxo.
- **Fluxo de objetos** (opcional) quando relevante.

Exemplo de quando **escolher atividades**: “Lojista cadastra livro com cálculo dinâmico de comissão” — porque envolve decisões em sequência (categoria? volume do lojista? promoção vigente?), e o fluxo é melhor visualizado como árvore de decisão e cálculo.

### 3.4 Cobertura mínima exigida

- Cada uma das 3 fatias deve ser representada por pelo menos um diagrama comportamental.
- O grupo deve usar **pelo menos 2 dos 3 tipos** ao longo do trabalho — cada tipo expõe aspectos diferentes do sistema.
- Para cada diagrama, abram com 2-3 linhas justificando a escolha do tipo: *“escolhemos sequência aqui porque a fatia envolve coordenação entre cliente, livraria e gateway de pagamento”*; *“escolhemos estados para o ciclo de vida do Pedido porque há transições críticas com regras claras”*.

Essa escolha consciente vale tanto quanto o diagrama em si — é prática de **engenharia de modelagem**, não de “fazer figurinha”.

---

## 4. Casos de teste das fatias modeladas

Para cada uma das **3 fatias selecionadas**, elaborem **2 casos de teste** seguindo o padrão IEEE — totalizando **6 casos de teste**.

### 4.1 Template do caso de teste

Cada caso deve usar o seguinte template:

| Campo | Conteúdo |
| --- | --- |
| **ID** | Identificador (ex.: TC-FATIA1-01) |
| **Fatia / Caso de uso** | Qual fatia e qual caso de uso de usuário do Trabalho 2 |
| **Pré-condições** | Estado do sistema antes do teste |
| **Dados de entrada** | Valores específicos a serem usados |
| **Passos** | Sequência de ações |
| **Resultado esperado** | O que o sistema deve fazer |
| **Critério de aprovação** | Condição mensurável para passar |
| **Severidade em caso de falha** | Crítica / Alta / Média / Baixa |

### 4.2 Critério de seleção dos 6 casos

Dos 2 casos por fatia, **pelo menos 1 deve ser um caso de fronteira** — não apenas “caminho feliz”. Casos de fronteira incluem:

- **Valor limítrofe** (BVA — boundary value analysis): testar exatamente no limite de uma regra. Ex.: pedido de R$ 99,99 (frete cobrado) vs R$ 100,00 (frete grátis).
- **Transição crítica de estado**: testar uma transição que tem regra específica. Ex.: tentar cancelar uma entrega que já está `Em Trânsito` (não deve permitir).
- **Caminho de erro**: testar comportamento quando algo falha. Ex.: cartão recusado pelo gateway, ou timeout de coleta pelo entregador.

A intenção é exercitar a noção de que casos de teste se desenham com **método** — não escolhendo aleatoriamente.

### 4.3 Exemplo de caso de teste preenchido

| Campo | Conteúdo |
| --- | --- |
| **ID** | TC-FATIA1-02 |
| **Fatia / Caso de uso** | Fatia 1 (checkout) — US-CC-007 (pagamento seguro) |
| **Pré-condições** | Cliente autenticado; carrinho com 1 livro de R$ 50,00 + frete R$ 15,00; cartão de crédito cadastrado no perfil. |
| **Dados de entrada** | Cartão com saldo insuficiente (mock do gateway configurado para retornar `INSUFFICIENT_FUNDS`). |
| **Passos** | 1) Cliente clica em “Finalizar Compra”; 2) Cliente confirma forma de pagamento; 3) Cliente clica em “Pagar”. |
| **Resultado esperado** | Sistema exibe mensagem “Pagamento recusado: saldo insuficiente”; pedido **não é** criado; estoque **não é** baixado; carrinho permanece intacto. |
| **Critério de aprovação** | (a) Mensagem de erro clara e específica é exibida; (b) Nenhum registro de pedido é criado no banco; (c) Quantidade do livro no estoque permanece igual. |
| **Severidade em caso de falha** | **Crítica** — bug aqui significa pedido criado sem pagamento confirmado, dor financeira direta. |

> Nota: este trabalho **não é um plano de testes completo** — é uma amostra de casos de teste rastreável às fatias modeladas. Plano de testes formal pode ser tema de trabalho futuro.
> 

---

## 5. Rastreabilidade

Inclua uma **tabela de rastreabilidade** ao final do documento, mostrando como os artefatos se conectam:

| Fatia | Casos de Uso(s) (T2) | Classes envolvidas | Entidades MER | Diagrama comportamental | Casos de teste |
| --- | --- | --- | --- | --- | --- |
| Fatia 1 | US-CC-003, US-CC-007, US-PA-002 | `Cliente`, `Carrinho`, `Pedido`, `ItemPedido`, `Pagamento` | `cliente`, `pedido`, `item_pedido`, `pagamento` | Sequência (Seção 3.1) | TC-FATIA1-01, TC-FATIA1-02 |
| Fatia 2 | US-LOG-004, US-LOG-006, US-LOG-009 | `Entrega`, `Entregador`, `Avaliacao` | `entrega`, `entregador`, `avaliacao` | Estados (Seção 3.2) | TC-FATIA2-01, TC-FATIA2-02 |
| Fatia 3 | US-CC-011, US-CC-014, US-CC-016 | `Livro`, `LivroNovo`, `LivroUsado`, `LivroRaro`, `Comissao` | `livro`, `categoria_comissao` | Atividades (Seção 3.3) | TC-FATIA3-01, TC-FATIA3-02 |

Essa tabela é o **ativo mais importante do trabalho** — ela mostra que o grupo entende a relação entre os modelos, e não apenas que “fez os diagramas”. Ela é também o que torna defensável, na avaliação, o recorte feito na Seção 0.

---

## 6. Forma de entrega

- A entrega deve ser feita **exclusivamente** em um **repositório no GitHub**, com documentação em **Markdown** — **não serão aceitos Word ou PDF**, nem entrega por qualquer outro canal que não o repositório no GitHub.
- Pode-se utilizar o **mesmo repositório dos trabalhos anteriores** (Visão e Requisitos), apenas organizando este trabalho em uma nova pasta (ex.: `docs/`) dentro dele. Não é necessário criar um repositório novo.

### 6.1 Estrutura sugerida do repositório

```
README.md                        — visão geral + link para o documento principal
docs/
  00-selecao-de-escopo.md        — Seção 0 (justificativa das 3 fatias)
  01-diagrama-de-classes.md      — Seção 1 (com o diagrama Mermaid embutido)
  02-mer.md                      — Seção 2
  03-comportamental-fatia1.md    — Seção 3, fatia 1
  03-comportamental-fatia2.md    — Seção 3, fatia 2
  03-comportamental-fatia3.md    — Seção 3, fatia 3
  04-casos-de-teste.md           — Seção 4
  05-rastreabilidade.md          — Seção 5
references.md                    — bibliografia (ABNT)
```

### 6.2 Diagramas

**Todos os diagramas devem ser feitos com Mermaid** (https://mermaid.js.org), inseridos diretamente no Markdown. O Mermaid renderiza nativamente no GitHub, é o caminho mais leve para integrar com Markdown e mantém o diagrama versionado como texto.

- **Diagramas devem ser legíveis** — **não serão aceitos** *screenshots* de quadro-branco, de papel ou imagens exportadas de outras ferramentas.
- O uso de Mermaid é **obrigatório**: diagramas de classes, MER e comportamentais (sequência, estados, atividades) devem todos ser escritos em sintaxe Mermaid no próprio Markdown.

### 6.3 Padrões a seguir

- **ABNT** para citações e referências.
- **IEEE-830** nos casos de teste (template fornecido na Seção 4).
- **Markdown** padrão CommonMark/GFM.

### 6.4 Políticas que se aplicam

- [Política antiplágio](https://www.notion.so/Pol-tica-antipl-gio-5187d7b1ab514bfb8424ac0fcfb59dba)
- [Política de uso de ferramentas generativas de IA](https://www.notion.so/Pol-tica-de-uso-de-ferramentas-generativas-de-IA-2-353a6ec0abcd807285f9c609011f7c3e)

---

## Referências sobre as técnicas de modelagem

- BOOCH, G.; RUMBAUGH, J.; JACOBSON, I. *UML — Guia do Usuário*. 2ª ed. Rio de Janeiro: Campus, 2006.
- FOWLER, M. *UML Distilled: A Brief Guide to the Standard Object Modeling Language*. 3rd ed. Addison-Wesley, 2003.
- GAMMA, E.; HELM, R.; JOHNSON, R.; VLISSIDES, J. *Padrões de Projeto: Soluções Reutilizáveis de Software Orientado a Objetos*. Bookman, 2000.
- HEUSER, C. A. *Projeto de Banco de Dados*. 6ª ed. Bookman, 2008. (Para o MER.)
- IEEE Std 829-2008. *IEEE Standard for Software and System Test Documentation*.
- Markdown Guide: https://www.markdownguide.org/getting-started/
- Mermaid Diagram Syntax: https://mermaid.js.org/intro/