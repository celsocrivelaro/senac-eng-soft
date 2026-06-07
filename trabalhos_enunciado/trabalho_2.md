# Requisitos do projeto

Melhorado: Yes
Number: 2

# Trabalho: Documento de Requisitos

A partir da visão de projeto, devemos construir os documentos de requisitos das startups de cada estudo de caso.

---

## 1. Levantamento dos Requisitos Funcionais

Requisitos funcionais são especificações detalhadas que descrevem o que um sistema, aplicativo ou produto de software deve fazer. Eles definem as funcionalidades ou características esperadas do sistema para atender às necessidades e expectativas dos usuários e dos stakeholders. Os requisitos funcionais são focados nas operações e processos que o sistema deve realizar, e servem como base para o design, desenvolvimento e teste do software.

### 1.1 Histórias de Usuário

Cada estudo de caso possui de 2 a 3 subsistemas, cada um atendendo a grupos de usuários distintos. Os alunos deverão:

- **Identificar os subsistemas e seus respectivos grupos de usuários** presentes no estudo de caso.
- **Elaborar 10 histórias de usuário para cada subsistema**, cobrindo os principais fluxos de uso. As histórias devem seguir o formato padrão:

> **Como** [tipo de usuário], **eu quero** [ação/funcionalidade], **para que** [benefício/valor obtido].
> 

Cada história de usuário deve conter:

| Campo | Descrição |
| --- | --- |
| ID | Identificador único (ex.: US-SUB1-001) |
| História | Texto no formato "Como... eu quero... para que..." |
| Critérios de Aceitação | Condições objetivas que comprovam que a história foi atendida |
| Dependências | Pré-requisitos ou outras histórias das quais esta depende |
| Fonte | Stakeholder ou persona de origem da demanda |

### 1.2 Estimativa de Tamanho

Os alunos devem **estimar o tamanho de cada história de usuário** utilizando uma metodologia de estimativa reconhecida. Exemplos de metodologias aceitas:

- Story Points (escala Fibonacci: 1, 2, 3, 5, 8, 13, 21)
- T-Shirt Sizing (PP, P, M, G, GG)
- Pontos de Função

**É obrigatório justificar a escolha da metodologia de estimativa**, explicando por que ela é adequada para o contexto do projeto e como foi aplicada pelo grupo.

### 1.3 Priorização com MoSCoW

Todas as histórias de usuário devem ser classificadas utilizando o framework de priorização **MoSCoW**:

| Categoria | Significado | Descrição |
| --- | --- | --- |
| **M** — Must Have | Deve ter | Funcionalidades essenciais, sem as quais o sistema não funciona |
| **S** — Should Have | Deveria ter | Funcionalidades importantes, mas não impedem o funcionamento básico |
| **C** — Could Have | Poderia ter | Funcionalidades desejáveis que agregam valor, mas podem ser adiadas |
| **W** — Won't Have (por agora) | Não terá agora | Funcionalidades reconhecidas, mas fora do escopo da versão atual |

Os alunos devem apresentar uma **tabela consolidada** com todas as histórias organizadas por prioridade MoSCoW, incluindo o subsistema, a estimativa de tamanho e a justificativa da priorização.

---

## 2. Levantamento de Requisitos Não Funcionais

Requisitos não funcionais são especificações que descrevem **como** um sistema deve ser, em vez do que ele deve fazer. Eles estão relacionados às características de qualidade e desempenho do sistema, bem como às restrições sob as quais o sistema deve operar. Os requisitos não funcionais ajudam a garantir que o produto final seja eficiente, confiável, seguro e fácil de usar.

### 2.1 Mapeamento de Requisitos Não Funcionais

Os alunos devem **mapear quais requisitos não funcionais são importantes** para o sistema do estudo de caso, considerando categorias como:

- **Desempenho**: tempo de resposta, throughput, latência
- **Escalabilidade**: estimativa de acessos simultâneos, horários de pico de uso, estratégia de crescimento
- **Segurança**: autenticação, autorização, proteção de dados, conformidade com LGPD
- **Disponibilidade**: uptime esperado (ex.: 99,9%), tolerância a falhas
- **Usabilidade**: acessibilidade, curva de aprendizado, padrões de interface
- **Portabilidade/Plataforma**: critérios para plataformas web e mobile
- **Manutenibilidade**: facilidade de atualização, documentação técnica
- **Critérios de qualidade de desenvolvimento**: padrões de código, cobertura de testes, integração contínua

### 2.2 Requisitos Diferenciados por Tipo de Usuário

Os requisitos não funcionais **podem e devem ser diferentes para cada subsistema e tipo de grupo de usuário**. Por exemplo:

- O painel administrativo pode exigir requisitos de segurança mais rigorosos do que a interface do cliente final.
- O subsistema mobile pode ter requisitos de desempenho offline que não se aplicam ao subsistema web.
- Diferentes perfis de usuário podem demandar diferentes níveis de disponibilidade ou tempo de resposta.

Os alunos devem explicitar essas diferenças na documentação.

### 2.3 Justificativa Baseada nos Aprendizados

Cada requisito não funcional mapeado deve ser **justificado com base nos aprendizados da disciplina**, referenciando conceitos, frameworks ou boas práticas estudados em aula (ex.: normas ISO/IEC 25010, padrão IEEE-830, princípios de arquitetura de software, etc.).

### 2.4 Formato de Documentação (Padrão IEEE-830)

Cada requisito não funcional deve ser documentado no seguinte formato:

| Campo | Descrição |
| --- | --- |
| Requisito ID | Identificador único (ex.: NF-SUB1-001) |
| Título | Nome descritivo do requisito |
| Descrição | Especificação clara e mensurável do requisito |
| Entrada | O que dispara ou ativa este requisito |
| Processamento | Como o sistema deve se comportar para atender ao requisito |
| Saída | Resultado esperado ou comportamento observável |
| Restrições | Condições ou limitações aplicáveis |
| Critérios de Aceitação | Condições objetivas e mensuráveis para validação |

**Exemplo:**

| Campo | Descrição |
| --- | --- |
| Requisito ID | NF-BIBL-001 |
| Título | Tempo de Resposta |
| Descrição | O sistema deve responder a todas as solicitações dos usuários em até 2 segundos. |
| Entrada | Solicitação do usuário (ex.: pesquisa de livro, reserva, empréstimo) |
| Processamento | O sistema processa a solicitação do usuário e retorna o resultado. |
| Saída | Resultado da solicitação (ex.: lista de livros, confirmação de reserva) |
| Restrições | Aplica-se a todas as solicitações feitas pelos usuários no sistema. |
| Critérios de Aceitação | (a) Tempo de resposta não excede 2 segundos. (b) A experiência do usuário não é prejudicada por lentidão. |

---

## 3. Forma de entrega

- A entrega deve ser feita **exclusivamente** em um repositório no **GitHub**, em formato de documento escrito (**Markdown**), seguindo os padrões requisitados.
- **NÃO SERÁ ACEITO WORD OU PDF**, nem entrega por qualquer outro canal que não o repositório no GitHub.
- Pode-se utilizar o **mesmo repositório do Trabalho 1** (Documento de Visão), apenas organizando este trabalho em uma nova pasta ou arquivo dentro dele. Não é necessário criar um repositório novo.
- Quaisquer **diagramas** devem ser feitos com **Mermaid** (https://mermaid.js.org), que renderiza nativamente no GitHub e mantém o diagrama versionado como texto. Não serão aceitos *screenshots* de quadro-branco ou de papel.

O documento deve seguir os padrões requisitados:

- Seguir padrões da **ABNT** e **IEEE** conforme indicado em cada seção.
- O documento deve conter todas as seções descritas acima de forma organizada e coesa.
- Incluir uma introdução com a descrição do estudo de caso e a identificação dos subsistemas e grupos de usuários.

Deverá ter referências externas sobre as definições do problema.

Este trabalho deve seguir:

[Política antiplágio](https://app.notion.com/p/Pol-tica-antipl-gio-5187d7b1ab514bfb8424ac0fcfb59dba?pvs=21)

[Política de uso de ferramentas generativas de IA (1)](https://app.notion.com/p/Pol-tica-de-uso-de-ferramentas-generativas-de-IA-1-334a6ec0abcd80828c86c3fe028f6d58?pvs=21)

Referências:

Markdown: [https://www.markdownguide.org/getting-started/](https://www.markdownguide.org/getting-started/)