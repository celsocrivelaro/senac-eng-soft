# Documento de visão do Produto

Melhorado: Yes
Number: 1

Cada grupo recebeu um estudo de caso para a disciplina de Engenharia de Software. Com ele, deve-se definir o documento de visão do projeto com detalhes especificados abaixo.

Casos de estudo dos trabalhos:

[https://github.com/celsocrivelaro/senac-eng-soft/tree/main/trabalho_temas](https://github.com/celsocrivelaro/senac-eng-soft/tree/main/trabalho_temas)

[https://youtu.be/eh0-2MQiklg](https://youtu.be/eh0-2MQiklg)

## Documento de visão do Produto

Exemplo de estrutura do documento:

1. Visão Geral do Produto
    
    1.1 Declaração do Problema e Oportunidade de Negócio
    1.2 Perspectiva do Produto
    1.3 Capacidades do Produto
    
2. Descrição dos Usuários 
3. Restrições do Projeto e do Produto
4.1 Tecnologia e Padrões
4.2 Legislação e Regulamentações
4. Análise de Riscos e Mitigação

## 1) Visão Geral do Produto

Visão Geral do Produto é uma seção essencial em um documento de visão de projeto, pois fornece uma descrição concisa das características, funcionalidades e objetivos do software a ser desenvolvido. 

Aqui estão algumas dicas para explicar aos alunos como elaborar as subseções

**1.1 Oportunidade de Negócio e Declaração do Problema**

Nesta subseção, os alunos devem identificar e descrever claramente o problema que o produto ou solução visa resolver, bem como as oportunidades de negócios que surgem a partir desse problema. Para isso, os alunos devem:

1.	**Definir o problema principal**: Explicar o desafio ou a lacuna existente no mercado ou na sociedade que justifica a criação do produto ou serviço. Isso pode envolver questões de eficiência, acessibilidade, custo ou qualquer outra necessidade não atendida.

2.	**Analisar as causas do problema**: Detalhar os fatores ou condições que contribuem para a existência do problema, considerando o contexto atual do mercado, a tecnologia disponível ou as tendências emergentes.

3.	**Apontar a oportunidade de negócio**: Identificar como o produto ou serviço pode resolver o problema e gerar valor para os stakeholders. Explique a viabilidade comercial da solução, considerando o potencial de mercado, a rentabilidade e os benefícios a longo prazo para os investidores, clientes e a empresa.

4.	**Destacar o impacto potencial**: Apresentar as vantagens econômicas e sociais que a solução pode trazer, não apenas para os usuários finais, mas também para o mercado como um todo, destacando a inovação e a diferenciação da solução em relação a alternativas existentes.

Procurar estas informações em fontes externas.

1.2 Perspectiva do Produto

Esta subseção descreve a relação do produto com outros sistemas e produtos relacionados, seu posicionamento no mercado e o público-alvo. Aqui, os alunos devem:

1. Contextualizar o produto no ambiente atual: Explicar como o software se encaixa no ecossistema tecnológico, em termos de sistemas relacionados ou produtos similares existentes no mercado.
2. Público-alvo: Descrever os usuários finais, bem como as partes interessadas que podem ser afetadas pelo produto.
3.  Proposta de valor: Resumir os principais benefícios que o software trará aos usuários e como ele se diferencia de outras soluções existentes.

**1.3 Capacidades do Produto**

Esta subseção apresenta as principais funcionalidades e características do produto. Para abordá-la, os alunos devem:

1. Principais funcionalidades: Descrever as funções que o software realizará, destacando os benefícios que cada função proporciona aos usuários.
2. Wireframes baixíssima resolução: Fazer esboços de fluxos principais dos usuários
3. Características de qualidade: Abordar aspectos como usabilidade, segurança, confiabilidade e manutenibilidade, que são essenciais para garantir a qualidade do produto.

## 2) Definição de usuários

Um usuário do sistema é uma pessoa que interage diretamente com um sistema, software ou aplicativo para realizar tarefas, acessar informações ou executar processos específicos. Os usuários do sistema são o público-alvo para o qual o produto de software é projetado e desenvolvido. Eles podem ter diferentes níveis de conhecimento técnico, experiência e habilidades, dependendo de seus papéis e responsabilidades.

Exemplo de tabela que usa o padrão **ISO/IEC/IEEE 29148:2011**

| **Atributo** | **Descrição** |
| --- | --- |
| Usuário ID | USER-001 |
| **Nome do Perfil** | Administrador do Sistema |
| **Descrição** | Profissional de TI responsável pela manutenção, segurança e controle do sistema. |
| **Experiência Técnica** | Alta; conhecimento avançado em TI e banco de dados. |
| **Frequência de Uso** | Regular, acessando funcionalidades avançadas e logs do sistema. |
| **Principais Objetivos** | Gerenciar permissões de usuários, monitorar logs e garantir a integridade do sistema. |
| **Desafios** | Necessidade de acesso rápido a informações críticas e ferramentas de análise. |
| **Restrições** | Responsável pela segurança do sistema, exige autenticação reforçada. |
| **Requisitos Principais** | Painel de administração detalhado, relatórios avançados e logs de auditoria. |

## 3) Restrições do projeto

Uma restrição é uma limitação ou condição imposta ao projeto que afeta sua execução, desenvolvimento ou resultado. Restrições podem ser de origem interna ou externa, e geralmente são fatores que a equipe do projeto deve considerar e respeitar ao longo do ciclo de vida do projeto.

As restrições podem ser:

1. Tecnológicas: Limitações ou exigências específicas sobre as tecnologias, plataformas, linguagens de programação ou padrões a serem usados no projeto.
2. Prazos: Prazos estabelecidos para a entrega do projeto ou de marcos intermediários.
3. Recursos: Limitações na quantidade de recursos disponíveis para o projeto, como equipe, orçamento, equipamentos ou infraestrutura.
4. Legais ou regulamentares: Leis, regulamentos ou normas que devem ser seguidos durante o desenvolvimento e implantação do projeto.
5. Qualidade: Níveis de qualidade ou padrões específicos que devem ser atingidos pelo projeto ou produto final.

Exemplo usando padrão IEEE-830:

| **Campo** | **Descrição** |
| --- | --- |
| Restrição ID | NF-CONST-001 |
| Título | Restrições Tecnológicas |
| Descrição | O projeto deve ser desenvolvido utilizando as tecnologias, linguagens de programação e padrões especificados pela empresa. |
| Origem | Equipe de arquitetura |
| Critérios de verificação e validação | a. A equipe do projeto deve seguir as diretrizes e padrões estabelecidos pela empresa |
| Relacionamento com outros requisitos | a. O projeto é desenvolvido utilizando as tecnologias e padrões especificados (b) A revisão do código e da arquitetura confirma a conformidade com os padrões estabelecidos |

## 4) Riscos do projeto

Riscos do projeto são eventos, condições ou circunstâncias incertas que, se ocorrerem, podem afetar positiva ou negativamente os objetivos do projeto. Os riscos do projeto podem surgir de várias fontes e podem impactar diferentes aspectos do projeto, como prazos, custos, qualidade, escopo e satisfação do cliente.

Os riscos do projeto podem ser categorizados de várias maneiras, como:

1. Riscos internos: Riscos que surgem de fatores dentro da organização ou equipe de projeto, como mudanças no escopo, falta de recursos, comunicação inadequada, problemas de pessoal ou falhas técnicas.
2. Riscos externos: Riscos que surgem de fatores fora da organização ou equipe de projeto, como mudanças no mercado, condições econômicas, desastres naturais, alterações regulatórias ou concorrência.
3. Riscos técnicos: Riscos associados à tecnologia, arquitetura, design ou implementação do projeto, como a escolha de uma tecnologia inadequada, falhas de integração ou problemas de desempenho.

Vamos criar um exemplo de análise de risco para um projeto de desenvolvimento de software, utilizando a estrutura do padrão IEEE-830. 

| **Campo** | **Descrição** |
| --- | --- |
| ID do Risco | RISCO-001 |
| Descrição | Usuários não conseguirem cadastrar livros em lote |
| Categoria | Parceiro |
| Probabilidade | Alta |
| Impacto | Alto |
| Ação de Mitigação | Conversar com os usuários como eles armazenam os dados em seus computadores |
| Plano de Contingência | Fazer um cadastro manual 1 a 1 |

## Forma de entrega

- A entrega deve ser feita **exclusivamente** em um repositório no **GitHub**, em formato de documento escrito (**Markdown**), seguindo os padrões requisitados.
- **NÃO SERÁ ACEITO WORD OU PDF**, nem entrega por qualquer outro canal que não o repositório no GitHub.
- Este é o **primeiro trabalho** da disciplina: o repositório criado aqui deverá ser **reutilizado nos trabalhos seguintes** (Requisitos e Modelagem). Mantenham a organização do projeto pensando nessa continuidade.
- Quaisquer **diagramas** (incluindo os wireframes de baixíssima resolução) devem ser feitos com **Mermaid** (https://mermaid.js.org), que renderiza nativamente no GitHub e mantém o diagrama versionado como texto. Não serão aceitos *screenshots* de quadro-branco ou de papel.

Deverá ter referências externas sobre as definições do problema.

Este trabalho deve seguir:

[Política antiplágio](https://app.notion.com/p/Pol-tica-antipl-gio-5187d7b1ab514bfb8424ac0fcfb59dba?pvs=21)

[Política de uso de ferramentas generativas de IA ](https://app.notion.com/p/Pol-tica-de-uso-de-ferramentas-generativas-de-IA-1b53bb4e12a54b4aa06eaa02e62192f4?pvs=21)

Referências:

Markdown: [https://www.markdownguide.org/getting-started/](https://www.markdownguide.org/getting-started/)