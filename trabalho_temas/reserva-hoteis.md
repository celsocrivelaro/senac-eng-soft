## Plataforma de Reserva de Hotéis (StayHub)

### 1. Escopo e Objetivos

Viajantes têm dificuldade em encontrar e comparar hospedagens. São muitas opções, preços que variam e informações espalhadas em diferentes sites. Hotéis e pousadas, por sua vez, precisam de canais para divulgar seus quartos e gerenciar reservas.

A plataforma **StayHub** conecta viajantes a hospedagens (hotéis, pousadas, resorts). O objetivo principal é permitir que o viajante encontre acomodações, compare opções, faça reservas e gerencie sua estadia de forma simples.

### 2. Descrição do Sistema

O sistema é organizado por **Estabelecimentos** (hotéis, pousadas, resorts). Cada estabelecimento cadastra seus tipos de quarto, preços, fotos e comodidades. Os viajantes podem buscar hospedagens, comparar opções e fazer reservas online.

O sistema oferece:
- **Busca de hospedagens:** Por destino, datas e número de hóspedes
- **Comparação:** Preços, fotos, comodidades e avaliações
- **Reserva online:** Seleção de quarto e pagamento
- **Avaliações:** Comentários de hóspedes que já ficaram

### 3. Atores e Fluxos Principais

#### Administrador do Estabelecimento
- Cadastrar o estabelecimento na plataforma
- Cadastrar tipos de quartos com fotos e descrições
- Definir preços e disponibilidade de quartos
- Gerenciar reservas (confirmar, cancelar)
- Responder avaliações dos hóspedes
- Visualizar relatórios de ocupação

**Permissões:** Gerencia apenas seu próprio estabelecimento.

#### Hóspede
- Buscar hospedagens por destino, datas e quantidade de pessoas
- Visualizar fotos, comodidades e localização no mapa
- Comparar preços e tipos de quartos
- Fazer reservas online com pagamento
- Visualizar e gerenciar suas reservas
- Avaliar o estabelecimento após a estadia
- Cancelar reservas (conforme política)

**Permissões:** Acessa estabelecimentos disponíveis e suas próprias reservas.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir cadastrar estabelecimentos com informações e fotos
- **RF02:** O sistema deve permitir cadastrar tipos de quartos com preços
- **RF03:** O sistema deve permitir buscar hospedagens por destino e datas
- **RF04:** O sistema deve mostrar disponibilidade de quartos para as datas selecionadas
- **RF05:** O sistema deve permitir fazer reservas com pagamento online
- **RF06:** O sistema deve enviar confirmação de reserva por e-mail
- **RF07:** O sistema deve permitir cancelar reservas conforme política
- **RF08:** O sistema deve permitir que hóspedes avaliem estabelecimentos
- **RF09:** O sistema deve mostrar avaliações e nota média do estabelecimento
- **RF10:** O sistema deve manter histórico de reservas do hóspede

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 2.000-10.000 usuários ativos por mês
- **Volume de dados:** ~500 estabelecimentos e ~30.000 reservas no primeiro ano
- **Segurança básica:** Login com usuário e senha; dados de pagamento protegidos; hóspedes não veem dados de outros hóspedes
- **Disponibilidade esperada:** Alta (viajantes podem buscar e reservar a qualquer hora)

### 6. Integrações Externas

- **Sistema de pagamentos:** Para processar reservas e garantir o quarto
- **Envio de e-mails:** Para confirmar reservas e enviar lembretes da viagem
- **Sistema de mapas:** Para mostrar localização dos estabelecimentos

### 7. Riscos e Desafios Técnicos

1. **Reserva duplicada:** Dois hóspedes podem tentar reservar o mesmo quarto ao mesmo tempo. O sistema deve garantir que cada quarto só seja reservado uma vez por data.

2. **Preços variáveis:** Preços podem mudar conforme época do ano e demanda. O sistema deve mostrar o preço correto no momento da reserva.

3. **Política de cancelamento:** Diferentes estabelecimentos têm regras diferentes. O sistema precisa aplicar corretamente a política de cada um.

4. **Avaliações falsas:** Pessoas podem tentar postar avaliações sem ter ficado hospedadas. Apenas hóspedes com reserva confirmada devem poder avaliar.

---