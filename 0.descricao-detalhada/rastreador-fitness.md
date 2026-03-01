## Rastreador de Fitness (FitTrack)

### 1. Escopo e Objetivos

Muitas pessoas querem acompanhar suas atividades físicas para melhorar a saúde e atingir metas de condicionamento. Porém, registrar exercícios manualmente é trabalhoso e difícil de manter ao longo do tempo.

A plataforma **FitTrack** é um rastreador de atividades físicas que permite aos usuários registrar exercícios, definir metas e acompanhar seu progresso. O objetivo principal é motivar os usuários a manterem uma rotina de exercícios através de estatísticas, desafios e compartilhamento com amigos.

### 2. Descrição do Sistema

O sistema é organizado por **Academias/Grupos** (academias, grupos de corrida, equipes de treino). Cada academia pode cadastrar seus alunos e acompanhar o progresso coletivo. Usuários independentes também podem usar o sistema sem vínculo com uma academia.

O sistema oferece:
- **Registro de exercícios:** Caminhada, corrida, ciclismo, musculação e outros
- **Metas pessoais:** Definir objetivos de distância, tempo ou calorias
- **Estatísticas:** Gráficos de progresso semanal e mensal
- **Desafios:** Competições entre amigos ou membros da academia

### 3. Atores e Fluxos Principais

#### Administrador da Academia
- Cadastrar a academia/grupo na plataforma
- Cadastrar e gerenciar alunos/membros
- Criar desafios e competições para o grupo
- Visualizar relatórios de atividades dos membros
- Enviar mensagens de motivação para os alunos

**Permissões:** Gerencia apenas sua própria academia e seus membros.

#### Atleta
- Registrar atividades físicas (tipo, duração, distância)
- Definir metas pessoais de fitness
- Visualizar estatísticas e gráficos de progresso
- Participar de desafios e competições
- Compartilhar resultados com amigos
- Informar dados pessoais (peso, altura) para cálculos

**Permissões:** Acessa apenas seus próprios dados e desafios em que participa.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir cadastrar academias/grupos
- **RF02:** O sistema deve permitir cadastrar atletas vinculados a uma academia ou independentes
- **RF03:** O sistema deve permitir registrar atividades físicas com tipo, duração e distância
- **RF04:** O sistema deve permitir definir metas pessoais (distância, calorias, tempo)
- **RF05:** O sistema deve calcular calorias queimadas com base na atividade e dados do usuário
- **RF06:** O sistema deve exibir gráficos de progresso (semanal, mensal)
- **RF07:** O sistema deve permitir criar e participar de desafios
- **RF08:** O sistema deve permitir compartilhar resultados com amigos
- **RF09:** O sistema deve manter histórico completo de atividades
- **RF10:** O sistema deve enviar lembretes e notificações de motivação

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 1.000-5.000 usuários ativos por mês
- **Volume de dados:** ~100.000 atividades registradas no primeiro ano
- **Segurança básica:** Login com usuário e senha; cada usuário acessa apenas seus próprios dados; administradores só acessam dados de sua academia
- **Disponibilidade esperada:** Alta (usuários registram atividades a qualquer momento)

### 6. Integrações Externas

- **Serviço de mapas:** Para rastrear rotas de corrida e ciclismo
- **Envio de notificações:** Para lembretes de exercícios e alertas de desafios
- **Dispositivos de fitness:** Para importar dados de relógios e pulseiras inteligentes

### 7. Riscos e Desafios Técnicos

1. **Precisão dos dados:** O usuário pode registrar dados incorretos ou exagerados. Validações básicas podem ajudar a identificar inconsistências.

2. **Engajamento:** Muitos usuários abandonam apps de fitness após algumas semanas. Desafios e notificações ajudam a manter a motivação.

3. **Integração com dispositivos:** Cada marca de relógio/pulseira tem seu próprio formato de dados. Padronizar a importação pode ser complexo.

4. **Cálculo de calorias:** Fórmulas de gasto calórico são aproximadas e variam por pessoa. O sistema deve deixar claro que são estimativas.

---