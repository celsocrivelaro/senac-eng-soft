## Plataforma de Gerenciamento de Arquivos na Nuvem (CloudVault)

### 1. Escopo e Objetivos

Muitas pessoas e pequenas empresas têm dificuldade em manter seus arquivos organizados e acessíveis em diferentes dispositivos. A perda de documentos importantes por falha de equipamentos ou falta de backup é um problema comum.

A plataforma **CloudVault** oferece uma solução simples para armazenar, organizar e compartilhar arquivos na nuvem. O objetivo principal é permitir que o usuário acesse seus documentos de qualquer lugar (computador ou celular), mantendo seus arquivos seguros e organizados.

### 2. Descrição do Sistema

O sistema é organizado por **Organizações** (empresas, equipes ou grupos). Cada organização possui seus próprios usuários, arquivos e configurações. Um usuário pertence a uma organização e pode acessar os arquivos compartilhados dentro dela.

Os usuários podem fazer upload de arquivos (documentos, imagens, vídeos) e organizá-los em pastas. O usuário pode criar, renomear, mover e excluir pastas conforme sua necessidade.

O sistema oferece uma busca simples por nome de arquivo e tipo. Também permite visualizar documentos PDF e imagens diretamente no navegador, sem precisar baixar o arquivo.

O compartilhamento de arquivos pode ser feito dentro da organização (com outros membros) ou externamente através de links públicos.

### 3. Atores e Fluxos Principais

#### Administrador da Organização
- Criar e gerenciar a organização
- Convidar e remover membros da organização
- Definir limite de armazenamento para cada membro
- Visualizar estatísticas de uso da organização (espaço total utilizado, número de arquivos)
- Gerenciar pastas compartilhadas da organização

**Permissões:** Gerencia os membros e configurações da organização. Pode acessar as pastas compartilhadas da organização, mas não os arquivos pessoais dos membros.

#### Membro da Organização
- Fazer upload e download de arquivos
- Criar e organizar pastas pessoais
- Buscar arquivos por nome ou tipo
- Compartilhar arquivos com outros membros da organização
- Compartilhar arquivos externamente via link
- Acessar pastas compartilhadas da organização

**Permissões:** Acesso aos seus próprios arquivos, aos arquivos compartilhados com ele e às pastas compartilhadas da organização.

#### Usuário Externo (Convidado)
- Visualizar arquivos compartilhados via link
- Fazer download de arquivos (se permitido pelo dono)
- Editar arquivos (se permitido pelo dono)

**Permissões:** Acesso limitado apenas aos arquivos específicos compartilhados com ele. Não precisa ter conta no sistema.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir criar e gerenciar organizações
- **RF02:** O sistema deve permitir convidar e remover membros de uma organização
- **RF03:** O sistema deve permitir upload de arquivos de diversos formatos
- **RF04:** O sistema deve permitir organizar arquivos em pastas e subpastas
- **RF05:** O sistema deve permitir buscar arquivos por nome e tipo
- **RF06:** O sistema deve permitir compartilhar arquivos com membros da organização
- **RF07:** O sistema deve permitir compartilhar arquivos externamente através de links
- **RF08:** O sistema deve permitir visualizar PDFs e imagens sem fazer download
- **RF09:** O sistema deve mostrar quanto espaço de armazenamento está sendo usado (individual e da organização)
- **RF10:** O sistema deve permitir excluir arquivos e pastas

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 500-2.000 usuários ativos por mês
- **Volume de dados:** ~50.000 arquivos no primeiro ano
- **Segurança básica:** Login com usuário e senha; cada usuário só acessa seus próprios arquivos; administradores não acessam conteúdo de arquivos
- **Disponibilidade esperada:** Alta (sistema deve estar disponível a maior parte do tempo)

### 6. Integrações Externas

- **Armazenamento em nuvem:** Para guardar os arquivos dos usuários de forma segura
- **Envio de e-mails:** Para notificar usuários sobre compartilhamentos e recuperação de senha

### 7. Riscos e Desafios Técnicos

1. **Perda de arquivos:** Risco de usuários perderem arquivos por falhas no sistema. É importante fazer backups regulares.

2. **Arquivos muito grandes:** Usuários podem tentar enviar arquivos muito grandes, causando lentidão. Pode ser necessário limitar o tamanho máximo de upload.

3. **Espaço de armazenamento:** O sistema pode ficar sem espaço se muitos usuários armazenarem muitos arquivos. É preciso monitorar o uso de espaço.

4. **Acesso indevido:** Risco de pessoas acessarem arquivos que não deveriam. É importante garantir que cada usuário só veja seus próprios arquivos.

---