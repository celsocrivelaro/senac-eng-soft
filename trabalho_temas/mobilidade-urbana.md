## Plataforma de Mobilidade Urbana (CityGo)

### 1. Escopo e Objetivos

Muitas pessoas precisam se deslocar pela cidade de forma rápida e prática, mas nem sempre têm carro próprio ou acesso fácil a transporte público. Por outro lado, motoristas com veículos disponíveis buscam oportunidades de trabalho flexível.

A plataforma **CityGo** conecta passageiros a frotas de motoristas parceiros para viagens urbanas. O objetivo principal é permitir que o passageiro solicite uma viagem, acompanhe a chegada do motorista e chegue ao destino de forma segura e conveniente.

### 2. Descrição do Sistema

O sistema é organizado por **Frotas** (cooperativas de motoristas, empresas de transporte, locadoras). Cada frota cadastra seus próprios motoristas e veículos, define suas regiões de atuação e acompanha o desempenho da equipe.

O fluxo básico de uma viagem:
1. Passageiro informa origem e destino
2. Sistema calcula preço e tempo estimado
3. Passageiro confirma a solicitação
4. Sistema encontra motorista de uma frota próxima e disponível
5. Motorista aceita a viagem e vai buscar o passageiro
6. Passageiro acompanha a chegada do motorista pelo mapa
7. Viagem é realizada e pagamento processado automaticamente

### 3. Atores e Fluxos Principais

#### Administrador da Frota
- Cadastrar a frota na plataforma
- Cadastrar e gerenciar motoristas da frota
- Registrar veículos e documentação
- Definir regiões de atuação da frota
- Visualizar relatórios de viagens da frota
- Gerenciar pagamentos e repasses aos motoristas

**Permissões:** Gerencia apenas sua própria frota e seus motoristas.

#### Motorista
- Vincular-se a uma frota
- Registrar dados do veículo
- Definir disponibilidade (online/offline)
- Receber solicitações de viagem
- Aceitar ou recusar viagens
- Navegar até o passageiro e destino usando GPS
- Visualizar ganhos acumulados

**Permissões:** Vê apenas informações da viagem atual. Não vê histórico completo de passageiros.

#### Passageiro
- Cadastrar-se e salvar formas de pagamento
- Solicitar viagens informando origem e destino
- Visualizar preço estimado antes de confirmar
- Acompanhar localização do motorista em tempo real
- Avaliar motorista após a viagem
- Visualizar histórico de viagens e recibos

**Permissões:** Acessa apenas suas próprias viagens e informações.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir cadastrar frotas
- **RF02:** O sistema deve permitir cadastrar motoristas vinculados a uma frota
- **RF03:** O sistema deve permitir solicitar viagens com origem e destino
- **RF04:** O sistema deve calcular preço estimado baseado na distância
- **RF05:** O sistema deve encontrar motoristas disponíveis próximos ao passageiro
- **RF06:** O sistema deve mostrar localização do motorista em tempo real
- **RF07:** O sistema deve mostrar tempo estimado de chegada
- **RF08:** O sistema deve processar pagamento automaticamente ao fim da viagem
- **RF09:** O sistema deve permitir avaliação mútua (passageiro e motorista)
- **RF10:** O sistema deve gerar recibo digital de cada viagem

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 5.000-20.000 usuários ativos por mês
- **Volume de dados:** ~100.000 viagens no primeiro ano
- **Segurança básica:** Login com usuário e senha; administradores só acessam dados de sua frota; passageiros e motoristas não veem dados pessoais completos um do outro
- **Disponibilidade esperada:** Alta (viagens podem ser solicitadas a qualquer hora)

### 6. Integrações Externas

- **Sistema de mapas:** Para calcular rotas, distâncias e mostrar localização
- **Sistema de pagamentos:** Para processar pagamentos das viagens
- **Envio de notificações:** Para alertar motoristas sobre viagens e passageiros sobre chegada

### 7. Riscos e Desafios Técnicos

1. **Encontrar motorista:** Pode não haver motoristas disponíveis na região. O sistema deve informar o passageiro sobre a indisponibilidade.

2. **Localização imprecisa:** O GPS pode não funcionar bem em alguns lugares. Motorista e passageiro podem ter dificuldade em se encontrar.

3. **Preço variável:** O preço pode mudar entre a estimativa e o valor final (rota diferente, trânsito). É importante ser transparente sobre a precificação.

4. **Segurança:** Tanto passageiros quanto motoristas precisam se sentir seguros. Avaliações e histórico ajudam a construir confiança.

---