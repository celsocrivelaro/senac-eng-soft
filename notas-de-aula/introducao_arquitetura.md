# Introdução a Arquitetura

## 1. O que é arquitetura de software?

A pergunta “o que é arquitetura de software?” parece simples, mas resiste a respostas únicas. A literatura oferece pelo menos três definições canônicas, cada uma destacando um aspecto importante:

### 1.1 Definição “estrutural” (Bass, Clements, Kazman, 2003)

> A arquitetura de um sistema computacional é a estrutura ou as estruturas do sistema, que compreendem elementos de software, as propriedades externamente visíveis desses elementos e as relações entre eles.
> 

Três palavras-chave dessa definição:

- **Estruturas** (no plural): um sistema tem várias estruturas simultâneas — não há uma única “a arquitetura”.
- **Propriedades externamente visíveis**: arquitetura não se preocupa com detalhes internos de cada elemento, mas com como cada um se *comporta* diante dos outros.
- **Relações**: o que dá identidade arquitetural não são as caixas, mas as setas.

### 1.2 Definição “decisional” (Ralph Johnson, atribuída)

> Arquitetura é o conjunto de decisões importantes sobre o software — onde “importante” é medido pelo custo de mudança.
> 

Essa definição muda o foco: arquitetura não é o que existe no sistema, é o que **foi decidido** que existiria. As “caixas e setas” são consequências; a essência é o conjunto de escolhas conscientes (ou não) que as produziram.

### 1.3 Definição “significativa” (Grady Booch)

> Arquitetura representa as decisões de design *significativas* que dão forma a um sistema, onde a significância é medida pelo custo de mudança.
> 

Booch combina os dois aspectos: arquitetura é design (no sentido amplo), mas apenas o **design significativo** — o que estrutura o sistema de forma duradoura.

### 1.4 Síntese

As três definições convergem em três ideias:

1. **Arquitetura é um conjunto de decisões**, não um diagrama.
2. **As decisões importantes** são as caras de reverter.
3. **As decisões abrangem estruturas**, propriedades observáveis e relações entre componentes.

> 💡 **Intuição:** Imagine duas casas com a mesma planta baixa. A primeira foi construída com fundação para mais um andar futuro; a segunda, não. Os moradores não percebem essa diferença no dia a dia — a planta é igual. Mas, quando o filho cresce e a família precisa de um quarto a mais, uma família reforma em três meses, a outra precisa derrubar a casa. A “fundação para o segundo andar” é uma decisão arquitetural. Você só vê quando precisa.
> 

### 1.5 O que arquitetura **não é**

Esclarecer o que arquitetura **não é** ajuda a delimitar o conceito:

- **Não é uma linguagem de programação ou framework.** “Vamos usar Spring Boot” não é uma decisão arquitetural — é uma decisão de tecnologia. Frequentemente confundidas.
- **Não é o diagrama de classes.** Diagramas são *artefatos* que comunicam aspectos da arquitetura. A arquitetura existe nas decisões, não no PowerPoint.
- **Não é micromanagement do código.** Definir que toda variável de instância deve ser privada não é arquitetura — é convenção de codificação.
- **Não é separada do design.** Arquitetura *é* design, no nível mais alto. Não há uma fronteira concreta separando “fase de arquitetura” de “fase de design”.

---

## 2. Arquitetura vs Design: onde está a fronteira?

Já estabelecemos que arquitetura é “o design importante”. Mas o que torna um design *importante*? A literatura oferece três critérios práticos:

### 2.1 Critério do custo de mudança

Decisões cuja reversão custa **dias ou semanas** são prováveis decisões arquiteturais. Decisões cuja reversão custa **minutos** são geralmente decisões de design.

| Decisão | Custo de reverter | Tipo |
| --- | --- | --- |
| Trocar `ArrayList` por `LinkedList` | Minutos (um *commit*) | Design |
| Renomear um método | Minutos (com IDE) | Design |
| Aplicar o padrão Strategy a um *switch* | Horas (uma refatoração) | Design |
| Trocar SQL por NoSQL | Meses | Arquitetura |
| Trocar monólito por microsserviços | 1-3 anos | Arquitetura |
| Trocar Java por Python | Reescrita total | Arquitetura |

### 2.2 Critério do escopo

Decisões que afetam **muitos componentes do sistema** tendem a ser arquiteturais; decisões que afetam **um componente** tendem a ser de design.

### 2.3 Critério dos atributos de qualidade

Decisões que afetam **performance, escalabilidade, segurança, manutenibilidade** do sistema como um todo são arquiteturais. Decisões que afetam apenas a clareza local do código são design.

### 2.4 A fronteira é borrada (e tudo bem)

Há decisões em zona cinzenta: “vamos usar uma fila de mensagens entre estes dois módulos” — é arquitetura ou design? **Depende do escopo.** Se afeta o sistema todo, é arquitetura. Se é localizada, é design.

> 💡 **Intuição:** Não existe uma fronteira nítida — existe um *gradiente*. As decisões mais arquiteturais ficam num extremo (estilo geral, escolha de bancos de dados, decomposição em serviços); as mais de design no outro (nomes de variáveis, estruturas de dados internas). Bons engenheiros desenvolvem intuição para reconhecer onde uma decisão se encaixa nesse gradiente. A intuição vem da experiência — e do estudo de catálogos como veremos nas próximas aulas.
> 

---

## 3. Por que arquitetura importa?

### 3.1 O custo desigual das decisões

Há uma observação empírica clássica em engenharia de software, frequentemente atribuída a Barry Boehm (1981): **o custo de corrigir um defeito cresce exponencialmente com o tempo entre sua introdução e sua descoberta**. Decisões erradas durante requisitos custam cêntimos para corrigir; durante design, custam unidades; durante implementação, dezenas; em produção, centenas ou milhares.

A versão arquitetural dessa lei é ainda mais brutal: decisões arquiteturais erradas frequentemente **não são corrigidas** — são contornadas indefinidamente, a custo crescente de cada nova *feature*. O sistema entra em “morte por mil cortes”: cada mudança fica progressivamente mais difícil até reescrever do zero parecer mais barato que continuar evoluindo.

### 3.2 Dívida técnica arquitetural

Vimos a metáfora da dívida técnica em aulas anteriores. No nível arquitetural, ela é particularmente custosa:

- **Dívida de classes**: uma classe mal estruturada se refatora em um dia.
- **Dívida arquitetural**: um sistema mal estruturado se refatora em meses ou anos — quando se refatora.

Exemplos clássicos de dívida arquitetural:

- Acoplamento direto entre módulos que deveriam ser independentes.
- Banco de dados único compartilhado por sistemas que deveriam ser autônomos.
- Lógica de negócio espalhada por *controllers* da UI, *triggers* do BD, *jobs* assíncronos — sem dono.
- Dependência de versões antigas de bibliotecas que travam upgrade do resto.

### 3.3 Complexidade essencial vs acidental (Brooks)

Em “No Silver Bullet” (1987), Fred Brooks distinguiu duas formas de complexidade em software:

- **Essencial**: inerente ao problema. Modelar um sistema bancário é inerentemente complexo; nenhuma tecnologia mágica simplifica isso.
- **Acidental**: introduzida pelas ferramentas, escolhas técnicas, processos. *Pode ser reduzida*.

Boa arquitetura **não elimina complexidade essencial** — ela é inegociável. Mas **minimiza complexidade acidental**: cada acoplamento desnecessário, cada indireção sem propósito, cada framework adotado sem necessidade adiciona complexidade que poderia não existir.

A pergunta arquitetural fundamental é: *“Que complexidade meu sistema **deve** ter para resolver o problema, e que complexidade ele **adquiriu** sem necessidade?”*

> 💡 **Intuição:** Complexidade essencial é como a gravidade — você lida com ela. Complexidade acidental é como caminhar com pedras nos sapatos — você as carrega porque ninguém as tirou. Arquitetura tem dois trabalhos: *organizar* a complexidade essencial, e *eliminar* a acidental. Quando uma equipe diz “esse sistema é complicado demais”, a pergunta é: o domínio é complexo, ou as escolhas foram?
> 

---

## 4. O arquiteto de software: papel e responsabilidades

### 4.1 Quem é o arquiteto?

Há diversos modelos, com nomenclatura variada entre empresas:

- **Arquiteto de software** (técnico, foco em uma aplicação ou produto)
- **Arquiteto de soluções** (foco em integração entre sistemas)
- **Arquiteto corporativo** (foco no portfólio inteiro de TI da organização)
- **Tech Lead** com responsabilidades arquiteturais (modelo comum em equipes ágeis)

Em sistemas pequenos e times reduzidos, frequentemente **não há “um arquiteto”** — as decisões são coletivas, e isso é saudável.

### 4.2 Responsabilidades típicas

Independentemente do título, o trabalho arquitetural inclui:

1. **Identificar drivers**: requisitos funcionais críticos, atributos de qualidade, restrições.
2. **Tomar e documentar decisões**: estilo geral, decomposição, tecnologias-chave.
3. **Comunicar**: traduzir decisões para *stakeholders* técnicos e não-técnicos.
4. **Avaliar**: revisar decisões em luz de novos contextos; conduzir avaliações arquiteturais (ATAM, *risk-driven*).
5. **Mentorar**: difundir o entendimento arquitetural na equipe — para que decisões diárias sejam coerentes com o todo.
6. **Codificar (sim, codificar)**: arquitetos que perderam contato com código tendem a propor arquiteturas inviáveis.

### 4.3 Anti-padrões do papel de arquiteto

Há padrões disfuncionais bem catalogados:

- ***Architecture Astronaut*** (Joel Spolsky): o arquiteto que abstrai tudo em camadas tão genéricas que perdem contato com qualquer problema concreto. Diagramas com “Manager”, “Handler”, “Processor” sem semântica de domínio são sintoma.
- ***Ivory Tower Architecture***: o arquiteto que dita decisões de cima, sem entender o dia a dia da equipe. Resultado: arquitetura impraticável, ignorada na prática, e ressentimento crescente.
- ***Resume-Driven Architecture***: escolhas tecnológicas guiadas pelo desejo do arquiteto de aprender algo novo (ou enfeitar o currículo), não pelas necessidades do sistema.
- ***Cabal***: um pequeno grupo “arquitetural” que toma decisões em segredo. O time precisa adivinhar o porquê de cada restrição imposta.

### 4.4 Arquitetura como atividade participativa

A literatura contemporânea (Mark Richards, Neal Ford, Gregor Hohpe) é consistente: **boa arquitetura é colaborativa**. O arquiteto tem voz ativa, mas decisões importantes são tomadas com participação da equipe que vai construir e manter o sistema. Os motivos são:

- **Conhecimento distribuído**: ninguém sabe tudo. Quem implementa frequentemente sabe da viabilidade melhor que quem decide à distância.
- **Compromisso**: decisões impostas tendem a ser sabotadas (consciente ou inconscientemente). Decisões coletivas tendem a ser defendidas.
- **Aprendizado**: a equipe aprende junto, e o “fator ônibus” diminui (se o arquiteto sair, o conhecimento fica).

> 💡 **Intuição:** O arquiteto ideal não é um “decisor”; é um *facilitador* de boas decisões. Ele estrutura o problema, traz vocabulário, mostra alternativas, alerta sobre riscos — e deixa a equipe decidir junto. Na prática, isso é mais difícil do que ditar — exige paciência e humildade técnica.
> 

---

## 5. Drivers arquiteturais

**Drivers** são os fatores que **motivam e restringem** as decisões arquiteturais. Há quatro categorias principais.

### 5.1 *Stakeholders* e seus interesses

*Stakeholders* são todos os afetados pelo sistema: usuários finais, *product owners*, gerentes, equipes operacionais, auditores, clientes, parceiros. Cada grupo tem **interesses diferentes**, frequentemente conflitantes:

- Usuários: usabilidade, performance percebida.
- *Product owners*: time-to-market, capacidade de mudança rápida.
- Operações: monitoramento, *deploy* sem dor, recuperação de falhas.
- Segurança: conformidade, controle de acesso, *audit trail*.
- Finanças: custo de infraestrutura, custo de licenciamento.

A arquitetura **não pode satisfazer todos plenamente** — ela é o resultado de uma negociação consciente entre interesses. Identificar quem são os *stakeholders* e o que cada um valoriza é o primeiro passo de qualquer trabalho arquitetural sério.

### 5.2 Requisitos funcionais (o “o quê”)

São as funcionalidades que o sistema deve oferecer: “permitir cadastro de produtos”, “calcular frete”, “gerar nota fiscal eletrônica”. Em si, requisitos funcionais raramente determinam arquitetura — qualquer estilo razoável os comporta. Mas **alguns requisitos funcionais são “arquiteturalmente significativos”**: aqueles que, por sua escala ou natureza, **forçam** decisões estruturais (ex.: “o sistema deve operar offline e sincronizar quando voltar online” — isso tem implicações arquiteturais profundas).

### 5.3 Atributos de qualidade (o “quão bem”)

Os atributos de qualidade — também chamados *requisitos não-funcionais* — são as propriedades **observáveis em tempo de execução** ou **durante a vida do sistema** que determinam sua qualidade. Voltaremos a eles em detalhe na próxima seção.

### 5.4 Restrições

Restrições são fatores **não-negociáveis** que limitam o espaço de soluções:

- **Técnicas**: “deve rodar no SO da empresa (Linux antigo)”; “deve integrar com o ERP legado X”.
- **Organizacionais**: “deve usar a *stack* já adotada pela empresa”; “time tem 3 desenvolvedores Java”.
- **Regulatórias**: “deve atender LGPD”; “deve estar em conformidade PCI-DSS para tratar cartões”.
- **Econômicas**: “orçamento de R$ 50 mil para infra no primeiro ano”.
- **Temporais**: “go-live em 6 meses não-negociáveis”.

Restrições não são “obstáculos do mau”; são parte do problema. Uma boa arquitetura é a que **respeita as restrições e ainda atende aos atributos de qualidade prioritários**.

---

## 6. Atributos de qualidade

Os atributos de qualidade são, talvez, o conceito mais subestimado em cursos introdutórios de arquitetura. Estudantes saem da graduação acreditando que arquitetura é sobre “escolher entre microsserviços e monólito”, quando na verdade é sobre **escolher quais qualidades o sistema vai privilegiar**, sabendo que algumas serão necessariamente sacrificadas.

### 6.1 Os principais atributos

| Atributo | Pergunta que faz |
| --- | --- |
| **Performance** | Quão rápido o sistema responde? Qual é a latência? E a vazão? |
| **Escalabilidade** | O sistema aguenta crescer? 10x usuários? 100x dados? |
| **Disponibilidade** | Que % do tempo está no ar? Como reage a falhas? |
| **Confiabilidade** | Os resultados são corretos? Sob carga? Sob falhas? |
| **Segurança** | Resiste a ataques? Protege dados sensíveis? |
| **Manutenibilidade** | Quão fácil é evoluir? Adicionar features? Corrigir bugs? |
| **Modificabilidade** | Mudar requisitos custa caro ou barato? |
| **Testabilidade** | Quão fácil é validar? Que partes podem ser testadas isoladamente? |
| **Usabilidade** | Os usuários conseguem fazer o que precisam? |
| **Implantabilidade** | Quão fácil é colocar uma nova versão em produção? |
| **Interoperabilidade** | Conversa bem com outros sistemas? |
| **Portabilidade** | Funciona em diferentes plataformas? |
| **Custo** (operacional) | Quanto custa para rodar mensalmente? |

A norma **ISO/IEC 25010** organiza essas propriedades em um modelo formal de oito categorias. Para nossos propósitos, a lista acima é suficiente.

### 6.2 Trade-offs entre atributos

A grande verdade desconfortável: **atributos de qualidade conflitam entre si**. Não existe arquitetura que maximize todos. Cada decisão privilegia alguns e sacrifica outros.

Exemplos de conflitos clássicos:

- **Segurança vs Performance**: criptografia, autenticação, autorização — todos custam latência.
- **Disponibilidade vs Consistência** (Teorema CAP): sistemas distribuídos forçam essa escolha.
- **Modificabilidade vs Performance**: indireções e abstrações que facilitam mudança custam ciclos.
- **Escalabilidade vs Simplicidade**: arquiteturas escaláveis (microsserviços, *event sourcing*) são mais complexas.
- **Time-to-market vs Manutenibilidade**: entregar rápido frequentemente compromete qualidade interna.

### 6.3 Cenários de atributos de qualidade

O *Software Engineering Institute* (SEI) propôs um formato útil para tornar atributos de qualidade **mensuráveis**: o **cenário de qualidade**. Um cenário tem seis partes:

| Parte | Descrição | Exemplo (performance) |
| --- | --- | --- |
| **Fonte** | Quem ou o que dispara o estímulo? | Cliente externo |
| **Estímulo** | O evento que ocorre | Faz uma busca de produtos |
| **Artefato** | Parte afetada | API de catálogo |
| **Ambiente** | Em que condições? | Sob carga normal (1.000 req/s) |
| **Resposta** | O que o sistema deve fazer? | Retornar resultados |
| **Medida da resposta** | Critério mensurável | Em até 200ms no percentil 95 |

Cenário completo: *“Um cliente externo faz uma busca de produtos na API de catálogo, sob carga normal de 1.000 req/s; o sistema retorna resultados em até 200ms no percentil 95.”*

A grande vantagem: **isso é testável**. Você pode rodar testes de carga e verificar se o cenário foi atendido. Atributos de qualidade vagos (“o sistema deve ser rápido”) não são acionáveis; cenários são.

### 6.4 Priorização

Como você não pode atender a todos os atributos plenamente, **priorize**. Em workshops formais (Quality Attribute Workshops), a equipe e os *stakeholders* listam os atributos relevantes, escrevem cenários, e os **classificam em uma matriz de prioridade x dificuldade**:

|  | Baixa dificuldade | Alta dificuldade |
| --- | --- | --- |
| **Alta prioridade** | “Faça e celebre” | **Foco arquitetural principal** |
| **Baixa prioridade** | “Faça se sobrar tempo” | “Não se prenda” |

Os cenários no quadrante “alta prioridade + alta dificuldade” são os **drivers arquiteturais críticos** — os que mais influenciam decisões.

> 💡 **Intuição:** Não existe “arquitetura boa em abstrato”. Existe arquitetura *adequada* aos atributos prioritários. Bancos privilegiam segurança e consistência; jogos online privilegiam latência e escalabilidade; *startups* iniciais privilegiam time-to-market e modificabilidade. As mesmas decisões arquiteturais que são certas para um são erradas para o outro.
> 

---

## 7. O Modelo de Visões 4+1 de Kruchten

Em 1995, Philippe Kruchten propôs um modelo para descrever arquiteturas de forma multifacetada — porque uma única figura não captura a complexidade. O modelo se tornou o padrão *de facto* para documentação arquitetural.

```
                     ┌──────────────────────┐
                     │ Visão Lógica         │
                     │ (Funcional)          │
                     │ classes, componentes │
                     └──────────────────────┘
                                │
       ┌────────────────────────┼────────────────────────┐
       │                        │                        │
┌──────────────┐        ┌───────────────┐         ┌─────────────────┐
│ Visão de     │ ◀──┬──▶│ Visão de      │ ◀──┬───▶│ Visão Física    │
│ Processos    │    │   │ Cenários (+1) │    │    │ (Implantação)   │
│ threads,     │    │   │ casos de uso  │    │    │ servidores,     │
│ sincronização│    │   │               │    │    │ rede, infra     │
└──────────────┘    │   └───────────────┘    │    └─────────────────┘
                    │                        │
                                │
                     ┌──────────────────────┐
                     │ Visão de             │
                     │ Desenvolvimento      │
                     │ módulos, pacotes     │
                     └──────────────────────┘
```

### 7.1 Visão Lógica

**O que mostra**: a estrutura *funcional* do sistema. Componentes, classes, interfaces, e como se relacionam.

**Diagramas típicos**: diagrama de classes, diagrama de componentes, diagrama de pacotes.

**Audiência principal**: analistas, desenvolvedores, arquitetos.

**Exemplo no e-commerce**: o diagrama de classes que vimos com `Pedido`, `Cliente`, `Produto`, `Categoria` e seus relacionamentos.

### 7.2 Visão de Processos

**O que mostra**: aspectos de *concorrência* e *sincronização*. Quais processos/*threads* existem, como se comunicam, como dividem trabalho.

**Diagramas típicos**: diagramas de atividades com raias, diagramas de sequência ressaltando concorrência.

**Audiência principal**: engenheiros de performance, arquitetos de sistema.

**Exemplo no e-commerce**: como uma chamada à API de checkout dispara processos paralelos (validação de estoque, cálculo de impostos, autorização de pagamento) e os sincroniza.

### 7.3 Visão de Desenvolvimento

**O que mostra**: como o **código** se organiza. Pacotes, módulos, repositórios, dependências entre eles.

**Diagramas típicos**: diagrama de pacotes, diagrama de dependências de módulos, estrutura de diretórios.

**Audiência principal**: times de desenvolvimento, gerentes de configuração.

**Exemplo no e-commerce**: a divisão do *codebase* em pacotes (`com.empresa.ecommerce.pedidos`, `com.empresa.ecommerce.catalogo`) e quais pacotes podem importar de quais.

### 7.4 Visão Física (de Implantação)

**O que mostra**: onde cada componente *roda* — máquinas, redes, *cloud*, contêineres.

**Diagramas típicos**: diagramas de implantação UML, topologias de rede, mapas de infraestrutura.

**Audiência principal**: DevOps, infraestrutura, segurança.

**Exemplo no e-commerce**: dois servidores web atrás de um *load balancer*, um servidor de aplicação, um cluster de banco de dados primary/replica, um Redis para cache, um broker Kafka.

### 7.5 Visão de Cenários (+1)

**O que mostra**: **casos de uso concretos** que validam e ilustram as outras quatro visões.

**Por que “+1”**: porque ela não é uma quinta visão paralela; é o **fio condutor** que conecta as outras quatro.

Cada caso de uso importante deve ser rastreável através das quatro visões: quais classes participam (lógica), em quais processos rodam (processos), em quais servidores estão (física), e onde está o código (desenvolvimento).

**Exemplo no e-commerce**: o caso de uso “cliente finaliza pedido” deve ser explicável em cada visão — quais classes colaboram, em quais *threads*, em quais servidores, em quais módulos.

### 7.6 Quem precisa de qual visão?

Nem toda audiência precisa de todas as visões. Em geral:

- **Desenvolvedor júnior**: visão lógica + visão de desenvolvimento.
- **Tech Lead**: as quatro visões.
- ***Product Owner***: visão de cenários (sob forma de casos de uso).
- **Arquiteto**: as cinco, com ênfase variável conforme o projeto.
- **Operações**: visão física + visão de processos.
- **Segurança**: visão física (pontos de exposição) + visão lógica (fluxos de dados sensíveis).

> 💡 **Intuição:** O modelo 4+1 é um lembrete de que arquitetura é **multidimensional**. Quando alguém pede “me mostre a arquitetura do sistema”, a pergunta é incompleta — incompleta como “me mostre a planta do prédio” sem dizer se quer planta baixa, esquema elétrico, hidráulica ou estrutural. Bons documentos arquiteturais são organizados por visão, e cada leitor encontra o que precisa.
> 

---

## 8. Panorama dos estilos arquiteturais

Em uma aula introdutória, não aprofundamos estilos — fazemos isso em aulas seguintes. Mas vale uma vista panorâmica para que o aluno saiba o que está por vir.

| Estilo | Ideia central | Quando aparece |
| --- | --- | --- |
| **Camadas** | Organização horizontal: apresentação → negócio → persistência. | A grande maioria das aplicações corporativas. |
| **Cliente-Servidor** | Servidor centraliza recursos; clientes solicitam. | Web, APIs, qualquer sistema com *backend*. |
| **MVC** (e variantes MVP, MVVM) | Separação Model/View/Controller na camada de apresentação. | Aplicações com interface de usuário. |
| **Pipe-and-Filter** | Cadeia de filtros conectados por tubos. | Pipelines de dados, compiladores, ETL. |
| **Event-Driven** | Componentes se comunicam emitindo e ouvindo eventos. | Sistemas reativos, integração assíncrona. |
| **Microsserviços** | Sistema decomposto em serviços autônomos. | Sistemas grandes, equipes grandes. |
| **Hexagonal** | Núcleo de domínio puro com adaptadores plugáveis. | Sistemas com vida longa e foco em testes. |
| **Microkernel / Plugin** | Núcleo mínimo + plugins que estendem funcionalidade. | IDEs, navegadores, plataformas extensíveis. |
| **Serverless** | Funções pequenas executadas sob demanda. | APIs leves, backends mobile, integração. |
| **Peer-to-Peer** | Nós equivalentes, sem servidor central. | BitTorrent, blockchain, sistemas descentralizados. |

Esses estilos serão tratados em aulas dedicadas. Por ora, basta saber que **existem múltiplas formas de organizar um sistema**, cada uma com seus prós e contras.

### 8.1 Estilos não são exclusivos

Anote uma observação que será desenvolvida adiante: **sistemas reais combinam estilos**. Um sistema pode ser microsserviços (no nível do sistema) + event-driven (entre serviços) + hexagonal (dentro de cada serviço) + camadas (no código de cada componente). Não é “qual escolher?” — é “que estilo cabe em cada nível?”.

---

## 9. Decisões arquiteturais e ADRs

### 9.1 Arquitetura é um conjunto de decisões

Voltamos à definição de Ralph Johnson: arquitetura **é o conjunto de decisões importantes**. Como decisões importantes são caras de reverter, faz sentido **registrá-las**. E aqui chegamos à prática mais valiosa que você pode adotar imediatamente em qualquer projeto.

### 9.2 ADR — *Architecture Decision Record*

ADRs (Michael Nygard, 2011) são **textos curtos** que documentam decisões arquiteturais. Não são manuais técnicos extensos — são registros de **por que** algo foi decidido, em determinado momento, em determinado contexto.

Um ADR típico tem cinco campos:

| Campo | Conteúdo |
| --- | --- |
| **Título** | Ex.: “Adotar PostgreSQL como banco de dados principal” |
| **Status** | Proposta / Aceita / Substituída / Obsoleta |
| **Contexto** | Qual problema/decisão estamos enfrentando? Que restrições? |
| **Decisão** | O que foi decidido. |
| **Consequências** | Implicações positivas e negativas da decisão. |

ADRs são tipicamente armazenados como arquivos Markdown junto ao código (no próprio repositório), versionados, numerados sequencialmente, e tornam-se **o histórico arquitetural** do projeto.

### 9.3 Por que ADRs são poderosos?

- **Memória organizacional**: dois anos depois, quando alguém perguntar “por que estamos usando PostgreSQL?”, a resposta está documentada — incluindo as alternativas consideradas e descartadas.
- **Onboarding**: novos desenvolvedores entendem rapidamente *por que* o sistema é como é.
- **Evolução consciente**: ao revisitar uma decisão, você revisita também o contexto. Se o contexto mudou, a decisão pode ser revisada conscientemente.
- **Disciplina coletiva**: o ato de escrever um ADR força a equipe a articular o raciocínio. Decisões que “parecem óbvias” frequentemente são desafiadas quando se tenta escrevê-las.

### 9.4 ADRs típicos em um projeto

- “Escolha do banco de dados principal”
- “Estilo arquitetural geral (monólito modular vs microsserviços)”
- “Adoção de framework X”
- “Estratégia de autenticação (sessão vs JWT)”
- “Política de versionamento de API”
- “Estratégia de cache”
- “Decomposição em *bounded contexts*”

Não documente tudo — documente o que tem **custo significativo de reverter**.

> 💡 **Intuição:** ADRs são para arquitetura o que *commits* descritivos são para código. Você pode programar sem mensagens de *commit*, mas vai sofrer em seis meses. Você pode arquitetar sem ADRs, mas vai sofrer em dois anos. A diferença é só de escala temporal.
> 

---

## 10. Quando a arquitetura acontece?

### 10.1 *Big Design Up Front* (BDUF) — paradigma “tradicional”

A visão clássica da engenharia de software (anos 1970-1990) era: **fase de arquitetura → fase de design → fase de implementação**. Decida tudo antes; depois construa.

**Vantagens**: clareza inicial, menos retrabalho se as decisões forem corretas.
**Desvantagens**: as decisões frequentemente **não são** corretas — porque o entendimento do problema evolui ao longo do projeto. BDUF tende a produzir arquiteturas elegantes para problemas que se demonstram errados depois.

### 10.2 Arquitetura evolutiva (*evolutionary architecture*)

A visão contemporânea (Neal Ford, *Building Evolutionary Architectures*, 2017): **arquitetura evolui ao longo do projeto**. Decisões iniciais são tomadas com o que se sabe agora; revisitadas conforme o entendimento amadurece; refatoradas quando necessário.

**Princípio central**: a arquitetura deve **suportar mudança** como um atributo de qualidade de primeira classe. Sistemas que são fáceis de mudar absorvem revisões arquiteturais; sistemas rígidos travam.

### 10.3 Arquitetura em métodos ágeis

A pergunta “como ágil lida com arquitetura?” é frequente. A resposta sintética:

- **Ágil não rejeita arquitetura.** Rejeita BDUF dogmática e documentação excessiva sem propósito.
- **Arquitetura emerge incrementalmente**, mas **não acidentalmente** — ela é guiada por princípios e revisitada conscientemente em iterações.
- **Decisões arquiteturais são adiadas até o último momento responsável**: quando você tem informação suficiente para decidir bem, mas não esperou tanto que a decisão se tornou cara de implementar.
- ***Walking skeletons***: construir cedo um sistema esqueléticamente funcional ponta a ponta, antes de adicionar features — isso *valida* a arquitetura precocemente.

### 10.4 Refatoração arquitetural

Sim, é possível refatorar arquitetura — embora seja muito mais caro que refatorar classes. Casos típicos:

- Decompor monólito em módulos antes de pensar em microsserviços.
- Introduzir uma camada de abstração para permitir trocar tecnologia depois.
- Migrar de banco compartilhado para *database per service*.
- Substituir comunicação síncrona por assíncrona em pontos críticos.

A regra é a mesma da refatoração de código: **passos pequenos, testes entre eles**. No nível arquitetural, “testes” inclui também testes de carga, testes de integração ponta a ponta, e métricas de qualidade em produção.

---

## 11. Boas práticas

1. **Identifique e priorize atributos de qualidade explicitamente.** Sem isso, decisões arquiteturais são chutes.
2. **Documente as decisões importantes em ADRs.** Não confie em memória ou em “todo mundo sabe”.
3. **Use múltiplas visões.** Um único diagrama nunca explica a arquitetura inteira.
4. **Comece simples.** Adicione complexidade arquitetural quando dor concreta justificar — nunca preventivamente.
5. **Conheça as alternativas que rejeitou.** Em ADRs e revisões, “por que essa, e não outra?” é a pergunta crítica.
6. **Não deixe arquitetura ser hobby de uma pessoa.** Distribua o entendimento.
7. **Reveja a arquitetura periodicamente.** O contexto muda; a arquitetura também precisa.
8. **Trate documentação como código.** Versionada, revisada em PRs, próxima ao código.

---

## 12. Exemplos resolvidos

### Exemplo Resolvido 1 — Identificando atributos de qualidade prioritários (Introdutório) ⭐

**Enunciado:** Para o nosso e-commerce, identifique os 3-4 atributos de qualidade mais prioritários. Justifique cada escolha em 2-3 linhas, e elabore **um cenário de qualidade** completo (com as seis partes vistas) para o atributo principal.

**Solução:**

**Análise do contexto:** o e-commerce atende clientes finais (pressão de UX), processa transações financeiras (pressão regulatória e de confiabilidade), e enfrenta sazonalidade (Black Friday, Natal — picos de carga). Equipe média; operação 24/7; integração com gateways de pagamento, transportadoras, ERPs.

**Atributos prioritários identificados:**

1. **Disponibilidade** — comércio fora do ar perde dinheiro a cada minuto e mancha a marca. Plataformas concorrentes estão a um clique.
2. **Performance** — usuários abandonam *checkouts* lentos. Estudos da Amazon indicam que cada 100ms a mais reduz vendas perceptivelmente.
3. **Escalabilidade** — picos sazonais podem chegar a 10x ou 20x da carga normal. O sistema precisa absorver sem comprar 10x mais hardware permanente.
4. **Segurança** — dados de cartão (PCI-DSS), dados pessoais (LGPD), histórico de compras. Vazamento aqui é catastrófico.

**Atributos secundários:**

- *Manutenibilidade*: importante (o sistema vai evoluir), mas não no nível dos quatro acima.
- *Usabilidade*: tratada principalmente pela equipe de UX/UI; arquitetura participa indiretamente (latência percebida, tempo de carga).

**Cenário de qualidade para Disponibilidade:**

| Parte | Conteúdo |
| --- | --- |
| **Fonte** | Falha de hardware do servidor primário de aplicação |
| **Estímulo** | Servidor desliga abruptamente (queda de energia, falha de hardware) |
| **Artefato** | Cluster de aplicação que serve as APIs do *checkout* |
| **Ambiente** | Operação normal (sem *deploy* em andamento; carga média) |
| **Resposta** | O sistema continua atendendo requisições por meio de outros nós do cluster |
| **Medida** | Disponibilidade efetiva ≥ 99,9% mensal (≤ 43 minutos de indisponibilidade/mês); falha do servidor é detectada e contornada em ≤ 30 segundos |

**Discussão das implicações arquiteturais:**

- **Disponibilidade ≥ 99,9%** elimina arquitetura “single-server”. É necessário cluster, *load balancer*, *failover*.
- **Escalabilidade 10-20x** sugere componentes *stateless* (que podem rodar em N instâncias paralelas) e separação clara entre componentes de carga alta (catálogo, checkout) e baixa (admin).
- **Performance** + **Escalabilidade** apontam para uso intenso de **cache** e possivelmente CDN para conteúdo estático.
- **Segurança** + **PCI-DSS** sugere isolar o componente de pagamento — possivelmente delegando armazenamento de cartão a gateway certificado, evitando armazenar dados sensíveis no próprio sistema.

> 💡 **Intuição:** Note como a análise de atributos de qualidade já **antecipa decisões arquiteturais** sem ter mencionado nenhum estilo. Microsserviços, cache, CDN, isolamento PCI — todos surgem como consequências dos atributos prioritários, não como escolhas a priori. Esse é o caminho saudável: atributos antes de estilos.
> 

### Exemplo Resolvido 2 — Escrevendo um ADR completo (Intermediário) ⭐⭐

**Enunciado:** Um time precisa decidir o banco de dados principal para o serviço de *Pedidos* do e-commerce. Considerações em jogo: o time conhece bem PostgreSQL e MongoDB; ambos atendem aos requisitos básicos. Há pressão de um arquiteto sênior para usar Cassandra “porque escala”. Escreva o ADR completo dessa decisão.

**Solução:**

```markdown
# ADR-007: Banco de Dados Principal para o Serviço de Pedidos

## Status
Aceita — 2026-04-15

## Contexto

O serviço de Pedidos é responsável por persistir o ciclo de vida completo
de cada pedido, desde sua criação até entrega ou cancelamento. Os requisitos:

-Volume estimado: 50 mil pedidos/dia em produção atual,
  crescendo para ~500 mil/dia em 18 meses (estimativa otimista).
-Consultas dominantes: leitura de pedidos por ID (operacional),
  busca por cliente (suporte), agregações por período (BI).
-Necessidade de transações ACID: um pedido envolve atualizar status,
  baixar estoque (via outro serviço), e registrar histórico — em uma
  operação atômica do ponto de vista do serviço de Pedidos.
-Equipe: 8 desenvolvedores, dos quais 6 têm experiência sólida em
  PostgreSQL e 2 em MongoDB; ninguém tem experiência operacional
  com Cassandra em produção.
-Compliance: precisamos de capacidade de auditoria e backup
  pontual (PITR), exigências para conformidade fiscal.

## Decisão

Adotar **PostgreSQL 16** como banco principal do serviço de Pedidos.

## Alternativas consideradas

### MongoDB
-✅ Boa familiaridade parcial da equipe
-✅ Schema flexível facilita evolução do modelo
-❌ Transações distribuídas mais frágeis que em PostgreSQL
-❌ Auditoria fiscal mais trabalhosa de implementar
-❌ Custo operacional maior em nossa escala (não justifica)

### Cassandra
-✅ Escala horizontal natural; suporta cargas muito superiores ao previsto
-❌ Equipe não tem experiência operacional; risco de incidentes graves
-❌ Modelo de consultas restrito (consultas ad-hoc são difíceis);
     limitaria muito a equipe de BI e suporte
-❌ Não suporta transações multi-row do tipo que precisamos
-❌ Volume previsto (500 mil/dia) está muito abaixo do ponto onde
     Cassandra começa a justificar seu custo de complexidade

### PostgreSQL (decisão)
-✅ Forte familiaridade da equipe — risco operacional mínimo
-✅ Transações ACID nativas atendem aos requisitos
-✅ Recursos de auditoria e PITR maduros (`pgBadger`, `pg_audit`,
     replicação física)
-✅ Capacidade comprovada para volumes muito superiores ao previsto
     (com particionamento e índices adequados)
-⚠️ Escala horizontal de escrita é mais limitada que NoSQL —
     mitigada por: arquitetura de microsserviços (Pedidos é apenas
     um serviço), particionamento por período, e plano de revisão
     em 12 meses.

## Consequências

### Positivas
-Time produtivo desde o dia 1; sem curva de aprendizado de tecnologia.
-Aproveitamos *expertise* operacional já existente na equipe de DBA.
-Compliance fiscal sem sobressaltos.
-Custo de infra dentro do orçamento previsto para o ano.

### Negativas
-Caso o crescimento supere significativamente as projeções
  (10x do estimado), pode ser necessário particionar mais agressivamente
  ou migrar partes para outro banco. Esse risco está mapeado.

### Reavaliação
Esta decisão será revisitada em **abril/2027** ou se o volume de pedidos
ultrapassar **300 mil/dia** sustentadamente — o que ocorrer primeiro.
```

**Discussão:**

- **Note o que o ADR não tem**: jargão arquitetural sofisticado, diagramas elaborados, múltiplas páginas. É um texto curto, focado, acionável.
- **A seção “Alternativas consideradas” é a mais valiosa**: ela mostra que a decisão **não foi tomada por ignorância** das outras opções. Em dois anos, quando alguém perguntar “por que não Cassandra?”, a resposta está documentada.
- **A reavaliação tem critério objetivo**: “300 mil pedidos/dia ou abril/2027”. Sem isso, a reavaliação nunca acontece.
- **Note a pressão social tratada profissionalmente**: o ADR não diz “rejeitamos Cassandra porque o arquiteto sênior estava pressionando sem fundamento”. Ele apresenta os fatos técnicos e deixa que falem por si. Isso é diplomacia arquitetural — fundamental em organizações reais.

> 💡 **Intuição:** ADRs profissionais lidam não só com tecnologia, mas com **dinâmicas organizacionais**: pressões de moda, *resume-driven architecture*, vieses de “novidade vende”. A resposta não é confronto direto, é tornar o raciocínio explícito e replicável. Quem discordar, discorda dos critérios — não da pessoa.
> 

---

## Exercícios Práticos

### Exercício 1 — Análise de drivers e atributos de qualidade ⭐

Considere o seguinte sistema:

> **Sistema de monitoramento cardíaco remoto**: pacientes em recuperação pós-cirúrgica usam um dispositivo vestível (*wearable*) que envia continuamente dados cardíacos ao sistema. O sistema deve: (1) receber e armazenar os dados; (2) detectar anomalias em tempo real e alertar médicos plantonistas; (3) gerar relatórios diários para os médicos responsáveis; (4) permitir consulta histórica via aplicativo do paciente. Os dispositivos são distribuídos para até 5.000 pacientes por hospital. O sistema atende inicialmente 3 hospitais, com plano de chegar a 30 em 5 anos.
> 

Sua tarefa:

1. Identifique pelo menos **5 *stakeholders*** distintos e descreva, em uma frase cada, qual é o principal interesse de cada um.
2. Liste pelo menos **2 restrições** que você consegue inferir do contexto (técnicas, regulatórias, organizacionais ou econômicas), justificando cada uma.
3. Identifique e priorize os **4 atributos de qualidade mais críticos** para este sistema. Para cada um, escreva uma frase justificando por que é prioritário neste contexto específico — não em abstrato.
4. Para o atributo que você considera **o mais crítico**, escreva um **cenário de qualidade completo** com as seis partes (fonte, estímulo, artefato, ambiente, resposta, medida).
5. Discuta em 4-6 linhas: que **trade-off** entre dois dos seus atributos prioritários você antecipa que será a tensão mais difícil neste sistema?

### Exercício 2 — Escrita de ADR para decisão arquitetural não-trivial ⭐⭐

Considere o seguinte cenário:

> Você é tech lead de um aplicativo de **delivery de comida** (estilo iFood). O sistema atual é um monólito Java/Spring que serve clientes finais (app), restaurantes (painel web) e entregadores (app). Está em produção há 4 anos, com 200 mil pedidos/dia e crescimento de 30% ao ano. Equipe: 25 desenvolvedores em 5 squads (cada squad por área: clientes, restaurantes, entregadores, logística, financeiro). Ciclo de release: 1 semana, mas com frequentes conflitos entre squads (cada deploy carrega mudanças de todos). Tempo de testes do monólito: 90 minutos. Há crescente discussão na equipe sobre migrar para microsserviços.
> 

Sua tarefa:

1. Escreva um **ADR completo** sobre a decisão “Migrar gradualmente de monólito para microsserviços, começando pelo subsistema de Logística”. O ADR deve ter os cinco campos: título, status, contexto, decisão, consequências. Inclua **pelo menos três alternativas consideradas** (com prós e contras de cada).
2. Justifique brevemente, em até 5 linhas, por que escolheu **Logística** como ponto de partida — e não os outros subsistemas. Use os conceitos de *bounded context* (mencionado em aulas anteriores) e atributos de qualidade.
3. Identifique, em pelo menos 3 itens, **quais riscos arquiteturais** você antecipa nessa migração — incluindo o risco de *distributed monolith*. Para cada risco, sugira uma mitigação concreta.
4. Defina **dois critérios objetivos** para reavaliação do ADR: que evidências (positivas ou negativas) levariam a equipe a revisitar a decisão e possivelmente reverter (ou acelerar) a migração?
5. Discuta em pelo menos 4 linhas: como você comunicaria essa decisão a *stakeholders* não-técnicos (CEO, gerentes de produto, líderes de squads)? O que você incluiria, o que omitiria, e por quê?

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Bass, L.; Clements, P.; Kazman, R. *Software Architecture in Practice*. 4ª ed., Addison-Wesley, 2021. (A referência canônica; trata atributos de qualidade e cenários em profundidade.)
- Kruchten, P. “The 4+1 View Model of Architecture”. *IEEE Software*, vol. 12, n. 6, 1995. (Artigo seminal; leitura obrigatória para qualquer aluno do tema.)
- Booch, G. *Handbook of Software Architecture*. (Discussão sobre o papel e responsabilidades do arquiteto.)
- Richards, M.; Ford, N. *Fundamentals of Software Architecture: An Engineering Approach*. O’Reilly, 2020. (Tratamento moderno e prático; equilibra teoria e prática.)
- Brooks, F. “No Silver Bullet — Essence and Accident in Software Engineering”. *Computer*, vol. 20, n. 4, 1987. (Ensaio fundador sobre complexidade essencial vs acidental.)
- Nygard, M. “Documenting Architecture Decisions”. 2011. (Texto curto que popularizou os ADRs; disponível em cognitect.com.)
- Ford, N.; Parsons, R.; Kua, P. *Building Evolutionary Architectures*. O’Reilly, 2017. (Sobre arquiteturas que suportam mudança.)
- Hohpe, G. *The Software Architect Elevator*. O’Reilly, 2020. (Sobre o papel do arquiteto como conector entre níveis técnico e organizacional.)
- Spolsky, J. “Don’t Let Architecture Astronauts Scare You”. 2001. Disponível em joelonsoftware.com. (Texto curto, contundente, sobre o anti-padrão.)
- Fairbanks, G. *Just Enough Software Architecture: A Risk-Driven Approach*. Marshall & Brainerd, 2010. (Aborda a quantidade certa de arquitetura — nem demais, nem de menos.)
- ISO/IEC 25010:2011. *Systems and software engineering — Systems and software Quality Requirements and Evaluation (SQuaRE)*. (Modelo formal de qualidade.)

---

*Próxima aula: Arquitetura de Software (Camadas e MVC) — o primeiro mergulho em estilos específicos, com a arquitetura em camadas e a família MVC/MVP/MVVM.*