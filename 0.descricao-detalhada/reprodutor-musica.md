## Reprodutor de Música (SoundWave)

### 1. Escopo e Objetivos

Artistas independentes têm dificuldade em disponibilizar suas músicas para o público sem depender de grandes gravadoras. Por outro lado, ouvintes buscam descobrir novos artistas e músicas de forma simples e organizada.

A plataforma **SoundWave** é um serviço de streaming de música que conecta artistas a ouvintes. O objetivo principal é permitir que artistas publiquem suas músicas e que ouvintes descubram, reproduzam e organizem suas músicas favoritas em playlists.

### 2. Descrição do Sistema

O sistema é organizado por **Artistas** (músicos, bandas, produtores). Cada artista pode criar seu perfil, fazer upload de suas músicas e álbuns, e acompanhar estatísticas de reprodução.

O sistema oferece:
- **Catálogo:** Músicas organizadas por artista, álbum e gênero
- **Playlists:** Listas de reprodução personalizadas criadas pelos ouvintes
- **Reprodução:** Controles de play, pause, próxima, anterior, shuffle e repeat
- **Busca:** Pesquisa por nome da música, artista ou álbum

### 3. Atores e Fluxos Principais

#### Artista
- Cadastrar perfil de artista na plataforma
- Fazer upload de músicas e álbuns
- Definir informações das músicas (nome, álbum, gênero, capa)
- Visualizar estatísticas de reprodução das suas músicas
- Gerenciar seu perfil e discografia

**Permissões:** Gerencia apenas seu próprio perfil e suas músicas.

#### Ouvinte
- Buscar e descobrir músicas e artistas
- Reproduzir músicas do catálogo
- Criar e gerenciar playlists pessoais
- Seguir artistas favoritos
- Curtir músicas e álbuns
- Visualizar histórico de reprodução

**Permissões:** Acessa todas as músicas públicas do catálogo e suas próprias playlists.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir cadastrar perfis de artistas
- **RF02:** O sistema deve permitir upload de músicas (MP3, FLAC, WAV)
- **RF03:** O sistema deve organizar músicas por artista, álbum e gênero
- **RF04:** O sistema deve permitir criar e editar playlists
- **RF05:** O sistema deve permitir reproduzir músicas com controles básicos (play, pause, próxima, anterior)
- **RF06:** O sistema deve permitir buscar músicas por nome, artista ou álbum
- **RF07:** O sistema deve permitir seguir artistas e curtir músicas
- **RF08:** O sistema deve exibir estatísticas de reprodução para os artistas
- **RF09:** O sistema deve manter histórico de músicas reproduzidas
- **RF10:** O sistema deve exibir informações da música atual (capa, artista, álbum, duração)

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 500-2.000 usuários ativos por mês
- **Volume de dados:** ~10.000 músicas cadastradas no primeiro ano
- **Segurança básica:** Login com usuário e senha; artistas gerenciam apenas suas próprias músicas; ouvintes acessam músicas públicas e suas playlists
- **Disponibilidade esperada:** Alta (a reprodução de música não deve ser interrompida)

### 6. Integrações Externas

- **Armazenamento de arquivos:** Para guardar os arquivos de música dos artistas
- **Serviço de streaming de áudio:** Para reproduzir músicas sem necessidade de download

### 7. Riscos e Desafios Técnicos

1. **Arquivos grandes:** Músicas em alta qualidade podem ocupar muito espaço. É importante gerenciar o armazenamento de forma eficiente.

2. **Direitos autorais:** Artistas podem tentar publicar músicas de terceiros. Validações e termos de uso são necessários.

3. **Streaming simultâneo:** Muitos ouvintes reproduzindo músicas ao mesmo tempo podem sobrecarregar o sistema.

4. **Concorrência:** Existem grandes plataformas de streaming no mercado. O diferencial deve ser o foco em artistas independentes.

---