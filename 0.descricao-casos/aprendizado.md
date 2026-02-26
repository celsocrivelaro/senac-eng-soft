## Proposta de Trabalho Prático: Plataforma de Gestão de Aprendizagem (EduFlow LMS)

### 1. Escopo e Objetivos

O projeto **EduFlow** visa desenvolver um Sistema de Gestão de Aprendizagem (LMS - *Learning Management System*) robusto e escalável. O objetivo é criar um ambiente virtual de aprendizagem que suporte o ciclo completo da educação a distância: desde a curadoria e publicação de conteúdos por docentes até a avaliação e certificação de discentes, promovendo a interatividade e o acompanhamento analítico do progresso educacional.

### 2. Descrição do Sistema

A plataforma deve gerenciar três perfis de acesso com permissões rigorosamente definidas:

1. **Módulo do Professor:** Ferramentas para autoria de cursos, upload de videoaulas, criação de bancos de questões para testes e gestão de fóruns de discussão.
2. **Módulo do Estudante:** Interface de consumo de conteúdo (player de vídeo, leitor de e-books), painel de entrega de tarefas com controle de prazos e simulados com cronômetro integrado.
3. **Módulo Administrativo:** Dashboard para controle de licenças, moderação de conteúdos, gestão de usuários e extração de relatórios de auditoria e uso da plataforma.

### 3. Recursos e Interatividade

A plataforma deve oferecer uma experiência síncrona e assíncrona. Isso inclui fóruns de discussão hierarquizados e sistemas de mensagens diretas. Um componente crítico é o **Motor de Avaliação**, que deve permitir configurações flexíveis para testes: randomização de perguntas, limite de tempo por tentativa e diferentes tipos de entrada (múltipla escolha, dissertativa, associação).

### 4. Arquitetura e Requisitos Não Funcionais

* **Arquitetura:** Padrão **MVC**. O "Model" deve ser capaz de gerenciar objetos complexos de aprendizagem e o estado de progresso de cada aluno em cada granularidade do curso (aula, módulo, curso).
* **Escalabilidade:** O sistema deve utilizar **Content Delivery Networks (CDN)** para a entrega de vídeos e materiais pesados, garantindo baixa latência global.
* **Segurança:** Implementação de proteção contra hotlinking de vídeos e criptografia de dados sensíveis dos usuários. Garantia de conformidade com a LGPD no tratamento de dados de menores de idade (se aplicável).
* **Disponibilidade:** O sistema de testes deve possuir mecanismos de **salvamento automático** para que o aluno não perca o progresso em caso de queda de conexão.

### 5. Riscos e Desafios Técnicos

1. **Integridade das Avaliações:** O desafio técnico de impedir fraudes em testes online (ex: detecção de troca de abas ou perda de foco da janela).
2. **Gestão de Armazenamento:** O alto custo e a complexidade de gerenciar petabytes de vídeos e arquivos educacionais com alta disponibilidade.
3. **Interoperabilidade:** O desafio de integrar com outras plataformas (como Coursera ou Udemy) via protocolos como **LTI (Learning Tools Interoperability)** ou **SCORM**.
4. **Engajamento e Retenção:** Implementar algoritmos de recomendação de conteúdo e notificações inteligentes para reduzir a taxa de evasão escolar.

---

### Estrutura Pedagógica: Foco em Modelagem de Domínio

Para este sistema, é vital que os alunos compreendam a hierarquia de objetos de aprendizagem:

| Entidade | Responsabilidade | Relacionamento |
| --- | --- | --- |
| **Curso** | Unidade principal de ensino. | Contém múltiplos Módulos. |
| **Módulo** | Agrupador lógico de aulas. | Contém múltiplas Aulas e um Teste Final. |
| **Aula** | Unidade mínima de conteúdo (Vídeo/Texto). | Vinculada a um Módulo. |
| **Matrícula** | Instância que liga Estudante ao Curso. | Armazena o Progresso e a Nota Final. |

---

### Exercício de Engenharia de Software: Escalabilidade

> **Desafio para o Aluno:** "Durante o lançamento de um curso gratuito de alta demanda, 50.000 alunos tentam acessar o mesmo vídeo simultaneamente. Como você utilizaria estratégias de **Caching** e **Load Balancing** (Balanceamento de Carga) na sua arquitetura para evitar que o servidor de aplicação caia?"
