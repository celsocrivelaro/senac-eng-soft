## Plataforma de Eventos Esportivos (ArenaStream)

### 1. Escopo e Objetivos

Torcedores têm dificuldade em acompanhar os jogos de seus times favoritos, comprar ingressos e ver resultados em tempo real. Organizadores de eventos precisam de uma forma prática de gerenciar partidas e vender ingressos.

A plataforma **ArenaStream** conecta torcedores a eventos esportivos. O objetivo principal é permitir que o torcedor encontre eventos, compre ingressos, acompanhe placares em tempo real e assista transmissões ao vivo.

### 2. Descrição do Sistema

O sistema é organizado por **Eventos** (clubes, federações, empresas criam eventos). Cada evento tem seu administrador que pode gerenciar partidas, definir preços de ingressos e atualizar informações.

O sistema oferece:
- **Busca de eventos:** Por modalidade esportiva, data e localização
- **Venda de ingressos:** Compra online com ingresso digital (QR Code)
- **Placar ao vivo:** Atualização em tempo real durante os jogos
- **Transmissão:** Assistir jogos ao vivo pela plataforma (quando disponível)

### 3. Atores e Fluxos Principais

#### Administrador do Evento
- Cadastrar o evento esportivo na plataforma
- Criar e configurar partidas
- Definir local, data e horário das partidas
- Configurar tipos de ingressos e preços
- Atualizar placar durante o jogo
- Gerenciar transmissão ao vivo (quando houver)
- Visualizar vendas de ingressos

**Permissões:** Gerencia apenas seus próprios eventos.

#### Torcedor
- Buscar eventos por modalidade, time ou data
- Comprar ingressos online
- Acessar ingresso digital via QR Code
- Acompanhar placar em tempo real
- Assistir transmissões ao vivo
- Configurar alertas para times favoritos
- Avaliar eventos após participação

**Permissões:** Acessa eventos públicos e seus próprios ingressos.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir cadastrar eventos esportivos
- **RF02:** O sistema deve permitir criar e gerenciar partidas dentro de um evento
- **RF03:** O sistema deve permitir definir tipos de ingressos com preços diferentes
- **RF04:** O sistema deve permitir buscar eventos por modalidade e data
- **RF05:** O sistema deve permitir comprar ingressos online
- **RF06:** O sistema deve gerar ingresso digital com QR Code
- **RF07:** O sistema deve permitir atualizar placar em tempo real
- **RF08:** O sistema deve mostrar o placar atualizado para os torcedores
- **RF09:** O sistema deve permitir transmissões ao vivo de eventos
- **RF10:** O sistema deve manter histórico de eventos e ingressos do torcedor

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 5.000-20.000 usuários ativos por mês
- **Volume de dados:** ~200 eventos e ~50.000 ingressos vendidos no primeiro ano
- **Segurança básica:** Login com usuário e senha; cada ingresso tem código único; organizadores só acessam seus próprios eventos
- **Disponibilidade esperada:** Alta (especialmente durante eventos ao vivo)

### 6. Integrações Externas

- **Sistema de pagamentos:** Para processar compras de ingressos
- **Serviço de streaming de vídeo:** Para transmitir eventos ao vivo
- **Envio de notificações:** Para alertar sobre jogos, placares e novidades

### 7. Riscos e Desafios Técnicos

1. **Pico de acessos:** Quando um jogo importante começa, muitas pessoas acessam ao mesmo tempo. O sistema precisa aguentar a demanda.

2. **Venda duplicada:** Dois torcedores podem tentar comprar o mesmo ingresso ao mesmo tempo. O sistema deve garantir que cada ingresso seja vendido apenas uma vez.

3. **Atualização do placar:** O placar precisa ser atualizado rapidamente para todos os usuários. Atraso pode frustrar os torcedores.

4. **Qualidade do vídeo:** A transmissão ao vivo pode travar se muitas pessoas assistirem ao mesmo tempo. É importante usar um serviço de streaming adequado.

---