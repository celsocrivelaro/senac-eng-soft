## Plataforma de Gestão de Aprendizagem (EduFlow LMS)

### 1. Escopo e Objetivos

Muitas instituições de ensino e empresas precisam de uma forma organizada de oferecer cursos online para seus alunos ou funcionários. A gestão manual de conteúdos, avaliações e acompanhamento de progresso é trabalhosa e propensa a erros.

A plataforma **EduFlow** é um Sistema de Gestão de Aprendizagem (LMS) que permite criar e gerenciar cursos online. O objetivo principal é oferecer um ambiente onde professores publicam conteúdos e alunos estudam, realizam avaliações e acompanham seu progresso.

### 2. Descrição do Sistema

O sistema é organizado por **Instituições** (escolas, empresas ou organizações). Cada instituição possui seus próprios cursos, professores e alunos. Uma instituição pode criar múltiplos cursos, e cada curso é dividido em módulos e aulas.

Os cursos são compostos por:
- **Módulos:** Agrupam aulas relacionadas a um tema
- **Aulas:** Contêm o conteúdo (vídeos, textos, arquivos)
- **Avaliações:** Testes para verificar o aprendizado

Os alunos se matriculam em cursos e podem acompanhar seu progresso (aulas concluídas, notas das avaliações).

### 3. Atores e Fluxos Principais

#### Administrador da Instituição
- Criar e configurar a instituição
- Cadastrar professores e alunos
- Criar cursos e atribuir professores responsáveis
- Visualizar relatórios de uso da plataforma
- Gerenciar matrículas de alunos nos cursos

**Permissões:** Gerencia toda a instituição, mas não cria conteúdo dos cursos.

#### Professor
- Criar e editar módulos e aulas dentro dos cursos atribuídos
- Fazer upload de materiais (vídeos, PDFs, textos)
- Criar avaliações (testes de múltipla escolha)
- Visualizar notas e progresso dos alunos
- Responder dúvidas dos alunos no fórum do curso

**Permissões:** Acesso apenas aos cursos em que é professor. Pode ver o progresso dos alunos matriculados.

#### Aluno
- Visualizar cursos disponíveis e se matricular
- Assistir aulas e acessar materiais do curso
- Realizar avaliações e ver suas notas
- Acompanhar seu progresso no curso
- Fazer perguntas no fórum do curso

**Permissões:** Acesso apenas aos cursos em que está matriculado.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir criar e gerenciar instituições
- **RF02:** O sistema deve permitir cadastrar professores e alunos
- **RF03:** O sistema deve permitir criar cursos com módulos e aulas
- **RF04:** O sistema deve permitir upload de materiais (vídeos, PDFs)
- **RF05:** O sistema deve permitir criar avaliações de múltipla escolha
- **RF06:** O sistema deve registrar o progresso do aluno (aulas assistidas)
- **RF07:** O sistema deve calcular e exibir notas das avaliações
- **RF08:** O sistema deve permitir matrícula de alunos em cursos
- **RF09:** O sistema deve ter um fórum de discussão por curso
- **RF10:** O sistema deve emitir certificado de conclusão do curso

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 1.000-5.000 usuários ativos por mês
- **Volume de dados:** ~500 cursos e ~10.000 matrículas no primeiro ano
- **Segurança básica:** Login com usuário e senha; alunos só acessam cursos em que estão matriculados; professores só acessam seus cursos
- **Disponibilidade esperada:** Alta (sistema deve estar disponível a maior parte do tempo, especialmente em horários de aula)

### 6. Integrações Externas

- **Armazenamento de vídeos:** Para hospedar os vídeos das aulas
- **Envio de e-mails:** Para notificar alunos sobre novos conteúdos, prazos e resultados de avaliações

### 7. Riscos e Desafios Técnicos

1. **Vídeos pesados:** O sistema pode ficar lento se muitos alunos acessarem o mesmo vídeo ao mesmo tempo. É importante usar um serviço adequado para streaming de vídeos.

2. **Perda de progresso:** Risco de o aluno perder o progresso em uma avaliação se a conexão cair. É importante salvar as respostas automaticamente.

3. **Organização do conteúdo:** Professores podem ter dificuldade em organizar o conteúdo de forma clara. A interface deve ser simples e intuitiva.

4. **Engajamento dos alunos:** Risco de alunos abandonarem os cursos. Notificações e lembretes podem ajudar a manter o engajamento.

---