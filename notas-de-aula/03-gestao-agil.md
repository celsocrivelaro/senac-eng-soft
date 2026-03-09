# Aula 03 – Metodologias Ágeis: Scrum e XP
---

## Objetivos de Aprendizagem

Ao final desta aula, o aluno será capaz de:
- Compreender os valores e princípios do Manifesto Ágil e seu contexto histórico
- Dominar papéis, cerimônias e artefatos do Scrum
- Identificar e aplicar as práticas técnicas do XP
- Diferenciar quando usar Scrum, XP ou uma combinação dos dois

---

## 1. Contexto Histórico e o Manifesto Ágil (20 min)

No final dos anos 1990, o mercado de software vivia uma contradição: os modelos pesados de processo (Cascata, RUP) geravam toneladas de documentação mas entregavam software tarde, caro e desalinhado com as necessidades reais dos clientes. Times de desenvolvimento buscavam alternativas mais leves.

Em **fevereiro de 2001**, 17 desenvolvedores e pensadores se reuniram em Snowbird, Utah, e assinaram o **Manifesto para o Desenvolvimento Ágil de Software**. Entre eles estavam Kent Beck (XP), Ken Schwaber (Scrum), Martin Fowler, Robert C. Martin e Ward Cunningham.

### Os 4 Valores

| Valorizamos mais | Do que |
|---|---|
| Indivíduos e interações | Processos e ferramentas |
| Software funcionando | Documentação abrangente |
| Colaboração com o cliente | Negociação de contratos |
| Resposta a mudanças | Seguir um plano |

> **Importante:** "Embora haja valor nos itens à direita, valorizamos mais os itens à esquerda." Agilidade não é ausência de processo ou documentação — é priorização consciente.

### Os 12 Princípios

Os princípios detalham os valores. Os mais relevantes para discussão:

1. Satisfação do cliente por meio de entrega contínua e antecipada de software com valor
2. Mudanças de requisitos são bem-vindas, mesmo tardiamente — agilidade como vantagem competitiva
3. Entregar software funcionando com frequência (semanas, não meses)
4. Colaboração diária entre desenvolvedores e pessoas de negócio
5. Construir projetos com pessoas motivadas — confiar e dar autonomia
6. A forma mais eficiente de comunicação é face a face
7. **Software funcionando é a medida primária de progresso**
8. Ritmo sustentável — agilidade não é sinônimo de burnout
9. Atenção contínua à excelência técnica e bom design
10. Simplicidade — maximizar o trabalho não realizado
11. Times auto-organizados geram as melhores arquiteturas e designs
12. Reflexão regular sobre como se tornar mais efetivo

---

## 2. Scrum

O Scrum é um framework leve para desenvolver e sustentar produtos complexos. Criado por **Ken Schwaber e Jeff Sutherland** no início dos anos 1990, foi formalizado no Scrum Guide (última versão: 2020). É deliberadamente incompleto — define apenas um conjunto mínimo de regras, deixando o restante para o time decidir.

### 2.1 Os Três Pilares do Scrum

- **Transparência:** todos os aspectos relevantes do processo devem ser visíveis aos responsáveis pelo resultado
- **Inspeção:** os artefatos e o progresso devem ser inspecionados regularmente
- **Adaptação:** se algo está fora dos limites aceitáveis, o processo deve ser ajustado

### 2.2 Papéis

**Product Owner (PO)**
Responsável por maximizar o valor do produto e do trabalho do Dev Team. Gerencia e prioriza o **Product Backlog** — é a única pessoa autorizada a ordenar os itens. Representa os stakeholders perante o time. Não é um gerente de projetos nem um proxy — deve ter autoridade real para tomar decisões de produto.

**Scrum Master (SM)**
Servidor-líder do time Scrum. Responsável por garantir que o Scrum seja compreendido e aplicado. Remove impedimentos, facilita as cerimônias, protege o time de interferências externas e promove práticas de melhoria contínua. Não é gerente — não tem autoridade sobre o Dev Team.

**Dev Team**
Auto-organizado e cross-funcional. Responsável por entregar um Incremento potencialmente entregável ao final de cada Sprint. Tamanho ideal: 3 a 9 pessoas. Sem títulos internos — todos são "Developers", independentemente da especialidade.

### 2.3 Artefatos

**Product Backlog**
Lista ordenada de tudo que pode ser necessário no produto. É a única fonte de trabalho para o Dev Team. Nunca está "completo" — evolui enquanto o produto existe. Cada item (PBI — Product Backlog Item) tem: descrição, ordem, estimativa e valor. O refinamento do backlog é uma atividade contínua.

**Sprint Backlog**
Conjunto de itens do Product Backlog selecionados para a Sprint + o plano de como entregá-los. Pertence ao Dev Team. É atualizado diariamente durante a Sprint. Torna visível o trabalho necessário para atingir a Sprint Goal.

**Incremento**
Soma de todos os PBIs completados durante a Sprint mais o valor dos Incrementos anteriores. Deve estar em condição utilizável e atender à **Definition of Done**. A decisão de liberar ou não para o cliente é do PO, mas o Incremento deve estar pronto para ser liberado.

**Sprint Goal**
Objetivo único para a Sprint que guia o Dev Team. Dá coerência ao conjunto de itens selecionados. Se o trabalho se torna diferente do esperado, o time pode negociar o escopo com o PO sem cancelar a Sprint — desde que a Sprint Goal seja preservada.

### 2.4 Cerimônias (Events)

**Sprint**
Container de todas as demais cerimônias. Duração fixa (timebox) de até 4 semanas — normalmente 2 semanas. Durante a Sprint: nenhum objetivo é colocado em risco, o Dev Team não é substituído e a qualidade não é reduzida.

| Cerimônia | Quando | Timebox (Sprint 2 sem.) | Objetivo |
|---|---|---|---|
| Sprint Planning | Início da Sprint | até 4h | Definir o que e como será feito |
| Daily Scrum | Todo dia | 15 min | Sincronização e plano para as próximas 24h |
| Sprint Review | Final da Sprint | até 2h | Inspecionar o Incremento com stakeholders |
| Sprint Retrospective | Após a Review | até 1h30 | Inspecionar e adaptar o processo do time |

**Sprint Planning — detalhe**
Responde a duas perguntas: (1) *O que pode ser entregue nesta Sprint?* — PO apresenta itens prioritários, time seleciona com base na velocidade histórica; (2) *Como o trabalho será realizado?* — Dev Team decompõe os PBIs em tarefas menores.

**Daily Scrum — detalhe**
Não é uma reunião de status para o SM ou PO. É do Dev Team, para o Dev Team. As três perguntas clássicas ("o que fiz, o que farei, algum impedimento?") são apenas um padrão sugerido — o time pode adaptar o formato. O que importa é a sincronização e o plano para as próximas 24h.

**Sprint Retrospective — detalhe**
A cerimônia mais negligenciada e uma das mais valiosas. Responde: *Como podemos melhorar?* O resultado deve ser um plano de ação concreto com ao menos uma melhoria a ser implementada na próxima Sprint. Não é uma sessão de reclamações — é uma oportunidade de inspeção e adaptação do processo.

### 2.5 Definition of Done (DoD)

A DoD é um contrato de qualidade do time. Define o que significa "pronto" para qualquer item. Exemplos típicos:
- Código implementado e revisado via pull request por ao menos 1 dev
- Testes unitários escritos com cobertura mínima de 80%
- Build de CI passando sem falhas
- Documentação atualizada
- Deploy realizado em ambiente de staging
- Sem vulnerabilidades críticas detectadas por análise estática

---

## 3. Extreme Programming — XP

XP foi criado por **Kent Beck** durante o projeto C3 na Chrysler, em 1996, e formalizado no livro *Extreme Programming Explained* (1999). Enquanto o Scrum se concentra no processo de gestão, o XP se concentra nas **práticas técnicas de engenharia** que sustentam a agilidade.

### 3.1 Valores do XP

**Comunicação:** problemas em projetos frequentemente surgem de falta de comunicação. XP valoriza comunicação contínua entre todos os membros do time.

**Simplicidade:** faça a coisa mais simples que pode funcionar. YAGNI — *You Ain't Gonna Need It*. Evite over-engineering.

**Feedback:** feedback rápido em todos os níveis: testes automatizados fornecem feedback em segundos, releases curtos fornecem feedback do cliente em dias.

**Coragem:** refatorar código difícil, jogar fora código ruim, dizer "não sei" ou "não consigo entregar isso no prazo" com honestidade.

**Respeito:** respeito mútuo entre membros do time e entre time e cliente.

### 3.2 Práticas Técnicas do XP

**Test-Driven Development (TDD)**
Ciclo: Red → Green → Refactor.
1. Escreva um teste que falha (Red)
2. Escreva o código mínimo para o teste passar (Green)
3. Refatore sem quebrar o teste (Refactor)

TDD não é apenas sobre testes — é uma prática de design. Ao escrever o teste primeiro, você é forçado a pensar na interface do código antes de na sua implementação. Código testável tende a ser mais coeso e menos acoplado.

**Pair Programming**
Dois desenvolvedores trabalham juntos em uma única máquina. Um é o **driver** (escreve o código), o outro é o **navigator** (revisa, pensa no design, antecipa problemas). Os papéis se alternam regularmente.

Benefícios comprovados: redução de defeitos, disseminação de conhecimento, solução mais rápida de problemas complexos, onboarding acelerado. O custo aparente (dois devs para uma tarefa) é compensado pela redução de retrabalho e pela qualidade resultante.

**Refatoração Contínua**
Melhorar o design interno do código sem alterar seu comportamento externo. Técnicas: extrair método, renomear variável, introduzir parâmetro, extrair classe, substituir condicional por polimorfismo. A suíte de testes é o que torna a refatoração segura.

**Integração Contínua (CI)**
O código é integrado e testado várias vezes ao dia. Regra: nunca deixar o build quebrado. Cada integração dispara uma suíte de testes automatizados. Conflitos de merge são resolvidos cedo, quando ainda são pequenos.

**Small Releases**
Entregue software funcionando em ciclos curtos — dias ou semanas, não meses. Isso maximiza o feedback e minimiza o risco de construir a coisa errada.

**Collective Code Ownership**
Qualquer desenvolvedor pode modificar qualquer parte do código a qualquer momento. Elimina silos de conhecimento e gargalos. Exige standards de código compartilhados e TDD para funcionar bem.

**Coding Standards**
O time define e segue convenções compartilhadas de nomenclatura, formatação e estilo. O código parece ter sido escrito por uma única pessoa. Ferramentas como linters e formatadores automatizam a verificação.

**Sustainable Pace**
Times ágeis não trabalham horas extras cronicamente. Ritmo sustentável = maior qualidade ao longo do tempo. Fadiga gera bugs. XP reconhece que software é um trabalho de longo prazo.

**Metáfora do Sistema**
Uma narrativa compartilhada que descreve como o sistema funciona — serve como linguagem comum entre devs e clientes. Precursor do conceito de **Ubiquitous Language** do DDD (Domain-Driven Design).

### 3.3 XP e Scrum: Complementaridade

Scrum e XP não competem — se complementam. Scrum provê o framework de gestão e processo; XP provê as práticas técnicas que tornam a agilidade sustentável. Times que adotam Scrum sem práticas técnicas de XP frequentemente se tornam "Scrum but" — fazem cerimônias mas acumulam débito técnico a cada Sprint até o processo implodir.

| Aspecto | Scrum | XP |
|---|---|---|
| Foco | Gestão do processo | Práticas de engenharia |
| Iterações | Sprints (1–4 semanas) | Iterações (1–2 semanas) |
| Papéis | PO, SM, Dev Team | Coach, Cliente, Programador |
| Mudança mid-sprint | Não recomendado | Permitido se necessário |
| Práticas técnicas | Não prescreve | TDD, Pair, CI, Refatoração |

---

## 4. Outros Frameworks Ágeis

### Kanban

Originado no sistema de produção da Toyota nos anos 1940, formalizado para software por **David J. Anderson** em 2010. Enquanto o Scrum organiza o trabalho em iterações com timebox fixo, o Kanban organiza o trabalho como um **fluxo contínuo** — sem sprints, sem papéis prescritos, sem cerimônias obrigatórias.

#### O Quadro Kanban

O artefato central é o **quadro Kanban** — uma representação visual do fluxo de trabalho dividida em colunas que representam os estágios pelo quais um item passa.

```
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│   BACKLOG    │  EM ANÁLISE  │ EM PROGRESSO │  EM REVISÃO  │    PRONTO    │
│              │   (WIP: 2)   │   (WIP: 3)   │   (WIP: 2)   │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│ [Tarefa E]   │ [Tarefa C]   │ [Tarefa A]   │ [Tarefa X]   │ [Tarefa Z]   │
│ [Tarefa F]   │ [Tarefa D]   │ [Tarefa B]   │              │ [Tarefa W]   │
│ [Tarefa G]   │              │ [Tarefa Y]   │              │ [Tarefa V]   │
│ [Tarefa H]   │              │              │              │              │
└──────────────┴──────────────┴──────────────┴──────────────┴──────────────┘
```

Cada cartão (card) representa um item de trabalho. O cartão se move da esquerda para a direita conforme avança no fluxo. O quadro torna o estado de todo o trabalho visível para todos — gargalos, bloqueios e distribuição de carga ficam imediatamente aparentes.

#### WIP Limits — O Coração do Kanban

O **limite de WIP (Work In Progress)** é o número máximo de itens que podem estar simultaneamente em um estágio. É o mecanismo mais importante do Kanban e o que o diferencia de simplesmente "ter um quadro de tarefas".

**Por que limitar WIP?**

A **Lei de Little** (John Little, 1961) estabelece a relação fundamental:

```
Lead Time = WIP / Throughput
```

Se o throughput (capacidade de entrega) é constante, reduzir WIP reduz lead time diretamente. Menos trabalho em progresso = itens chegando ao final mais rápido.

Além da matemática, há uma consequência prática: quando um estágio atinge seu limite de WIP, **ninguém pode puxar novo trabalho** — são forçados a ajudar a desbloquear o gargalo. O sistema se auto-regula em direção ao fluxo.

```
Sem WIP limit (multitarefa excessiva):
Dev A: [Tarefa 1 — 30%] [Tarefa 2 — 40%] [Tarefa 3 — 20%] → nada entregue
Dev B: [Tarefa 4 — 50%] [Tarefa 5 — 10%]                  → nada entregue

Com WIP limit = 1 por pessoa:
Dev A: [Tarefa 1 — 100%] → entregue → [Tarefa 2]
Dev B: [Tarefa 4 — 100%] → entregue → [Tarefa 5]
```

#### Métricas Fundamentais do Kanban

**Cycle Time:** tempo desde que um item começa a ser trabalhado até ser concluído. É o que o time controla diretamente.

**Lead Time:** tempo desde que o item é solicitado (entra no backlog) até ser entregue. É o que o cliente percebe.

**Throughput:** número de itens entregues por unidade de tempo (ex.: 8 itens por semana). É a medida de capacidade do sistema.

**CFD (Cumulative Flow Diagram):** gráfico que mostra a quantidade acumulada de itens em cada estágio ao longo do tempo. Permite visualizar gargalos, previsibilidade e estabilidade do fluxo.

```
Itens
▲
40│         ████████████ Pronto
  │       ██████████
30│     ████████ Em Revisão
  │   ██████
20│  ████ Em Progresso
  │ ██
10│█ Em Análise
  └─────────────────── Tempo
```

Bandas largas no CFD indicam acúmulo de trabalho — gargalo naquele estágio.

#### Princípios e Práticas do Kanban (Anderson)

**Comece com o que você tem:** Kanban não exige uma transformação radical. Você mapeia o processo atual, torna-o visível e começa a melhorá-lo gradualmente. Não há papéis novos, cerimônias obrigatórias ou sprints.

**Evolução incremental:** mudanças são introduzidas de forma experimental e incremental, com base em dados. Não há "big bang" de transformação de processo.

**Respeite papéis, responsabilidades e títulos atuais:** diferente do Scrum, que define papéis específicos, Kanban preserva a estrutura existente da organização e trabalha dentro dela.

**Encorajar liderança em todos os níveis:** qualquer membro do time pode sugerir melhorias no processo — não apenas gerentes ou facilitadores.

#### Cerimônias Típicas (não obrigatórias)

Embora o Kanban não prescreva cerimônias, times maduros frequentemente adotam:

| Cerimônia | Frequência | Objetivo |
|---|---|---|
| **Daily Standup** | Diária | Sincronização — foco no quadro, não nas pessoas ("o que está bloqueado?") |
| **Replenishment Meeting** | Semanal | Priorizar e puxar novos itens do backlog para o fluxo |
| **Throughput Review** | Quinzenal | Analisar métricas de fluxo e identificar melhorias |
| **Service Delivery Review** | Mensal | Avaliar a entrega de valor para stakeholders |

#### Kanban vs. Scrum: Quando usar cada um

| Dimensão | Scrum | Kanban |
|---|---|---|
| Natureza do trabalho | Iterativo, com entregáveis por sprint | Fluxo contínuo, demanda variável |
| Previsibilidade de escopo | Média/alta (sprint planning) | Baixa (trabalho chega continuamente) |
| Tipo de time ideal | Desenvolvimento de produto | Suporte, operações, manutenção |
| Comprometimento | Sprint Goal por iteração | Fluxo e SLAs de entrega |
| Mudanças mid-ciclo | Evitadas durante a sprint | Aceitas a qualquer momento |
| Métricas primárias | Velocity, burndown | Cycle time, throughput, CFD |
| Cerimônias | Obrigatórias e timeboxed | Opcionais, adaptadas ao contexto |

Na prática, muitos times combinam elementos dos dois — o chamado **Scrumban**: usam o quadro e os WIP limits do Kanban dentro da cadência de sprints do Scrum, obtendo visibilidade de fluxo sem abrir mão da previsibilidade das iterações.

### SAFe (Scaled Agile Framework)
Framework para escalar agilidade em grandes organizações. Opera em múltiplos níveis: Time, Programa, Portfólio. Muito adotado em empresas grandes, mas criticado por ser pesado e burocrático — alguns o chamam de "Waterfall disfarçado de Ágil".

### LeSS (Large-Scale Scrum)
Alternativa mais simples ao SAFe para escalar Scrum. Um único Product Backlog, múltiplos times Scrum trabalhando no mesmo produto. Mantém a simplicidade do Scrum original.

### Shape Up (Basecamp)
Abordagem criada pela Basecamp. Ciclos de 6 semanas com "apostas" em features específicas. Sem backlog infinito — o que não entra em um ciclo pode nunca entrar. Foco em escopo fixo e time fixo, com prazo fixo.

---

## Recursos e Referências

- **Schwaber, K. & Sutherland, J.** — *The Scrum Guide* (2020) — scrumguides.org — leitura obrigatória
- **Beck, K.** — *Extreme Programming Explained* (2ª ed., 2004)
- **Cohn, M.** — *Succeeding with Agile*
- **Larman, C. & Vodde, B.** — *Large-Scale Scrum (LeSS)*
- [agilemanifesto.org](https://agilemanifesto.org)
- [martinfowler.com/agile.html](https://martinfowler.com/agile.html)
