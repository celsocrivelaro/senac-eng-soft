## Plataforma de Financiamento Coletivo (ImpactFund)

### 1. Escopo e Objetivos

Muitas pessoas têm projetos criativos, sociais ou tecnológicos, mas não conseguem capital para tirá-los do papel. Por outro lado, há pessoas dispostas a apoiar boas iniciativas se souberem como encontrá-las.

A plataforma **ImpactFund** conecta criadores de projetos a apoiadores. O objetivo principal é permitir que criadores apresentem suas ideias, definam metas de arrecadação e recebam contribuições de pessoas interessadas em apoiar.

### 2. Descrição do Sistema

O sistema funciona como um **marketplace de projetos**. Criadores cadastram campanhas com descrição, meta financeira, prazo e recompensas. Apoiadores podem descobrir projetos, contribuir com valores e receber recompensas em troca.

O sistema oferece dois modelos de arrecadação:
- **Tudo ou Nada:** O criador só recebe o dinheiro se atingir a meta
- **Flexível:** O criador recebe o que arrecadar, mesmo sem atingir a meta

### 3. Atores e Fluxos Principais

#### Administrador da Plataforma
- Aprovar ou reprovar campanhas antes da publicação
- Gerenciar categorias de projetos (Arte, Tecnologia, Social, etc.)
- Visualizar relatórios de arrecadação
- Resolver disputas entre criadores e apoiadores
- Definir taxas de serviço da plataforma

**Permissões:** Acesso administrativo geral da plataforma.

#### Criador
- Criar e editar campanhas de arrecadação
- Definir meta financeira, prazo e modelo (Tudo ou Nada / Flexível)
- Criar níveis de recompensas para diferentes valores de contribuição
- Publicar atualizações sobre o andamento do projeto
- Responder perguntas dos apoiadores
- Gerenciar entrega de recompensas

**Permissões:** Gerencia apenas suas próprias campanhas. Vê dados dos apoiadores de suas campanhas.

#### Apoiador
- Descobrir projetos por categoria, popularidade ou prazo
- Visualizar detalhes da campanha e recompensas
- Contribuir com valores e escolher recompensa
- Acompanhar atualizações dos projetos apoiados
- Enviar perguntas para o criador
- Avaliar projetos após entrega das recompensas

**Permissões:** Acessa campanhas públicas e seus próprios históricos de contribuições.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir criar campanhas com meta, prazo e recompensas
- **RF02:** O sistema deve permitir escolher entre modelo "Tudo ou Nada" ou "Flexível"
- **RF03:** O sistema deve permitir buscar projetos por categoria e popularidade
- **RF04:** O sistema deve permitir contribuir para campanhas
- **RF05:** O sistema deve mostrar quanto foi arrecadado e quantos apoiadores contribuíram
- **RF06:** O sistema deve permitir que criadores publiquem atualizações
- **RF07:** O sistema deve enviar notificações sobre prazo e metas atingidas
- **RF08:** O sistema deve calcular e descontar a taxa da plataforma
- **RF09:** O sistema deve liberar o dinheiro ao criador conforme as regras do modelo
- **RF10:** O sistema deve manter histórico de contribuições do apoiador

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 1.000-5.000 usuários ativos por mês
- **Volume de dados:** ~200 campanhas e ~10.000 contribuições no primeiro ano
- **Segurança básica:** Login com usuário e senha; dados de pagamento protegidos; criadores não veem dados de cartão dos apoiadores
- **Disponibilidade esperada:** Alta (especialmente próximo ao fim do prazo das campanhas)

### 6. Integrações Externas

- **Sistema de pagamentos:** Para processar contribuições e repassar valores aos criadores
- **Envio de e-mails:** Para notificar sobre atualizações, prazo e status das campanhas

### 7. Riscos e Desafios Técnicos

1. **Campanhas fraudulentas:** Risco de pessoas criarem campanhas falsas para arrecadar dinheiro. É importante validar os criadores antes de publicar.

2. **Recompensas não entregues:** O criador pode arrecadar o dinheiro e não entregar as recompensas. É preciso ter um processo de acompanhamento.

3. **Pico no fim do prazo:** Muitas contribuições acontecem nos últimos dias da campanha. O sistema precisa aguentar a demanda.

4. **Estorno de pagamentos:** Apoiadores podem pedir estorno do cartão. O sistema precisa lidar com essas situações.

---