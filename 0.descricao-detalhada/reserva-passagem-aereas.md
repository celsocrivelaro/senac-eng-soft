## Plataforma de Reserva de Passagens Aéreas (FlyHigh)

### 1. Escopo e Objetivos

Viajantes têm dificuldade em comparar preços de passagens aéreas entre diferentes companhias. Cada companhia tem seu site, preços e regras diferentes. Encontrar a melhor opção de voo exige muito tempo pesquisando.

A plataforma **FlyHigh** facilita a busca e compra de passagens aéreas. O objetivo principal é permitir que o viajante pesquise voos de diferentes companhias, compare preços e horários, e faça a reserva de forma simples e segura.

### 2. Descrição do Sistema

O sistema funciona como um **agregador de voos**. Quando o viajante pesquisa um trecho (origem → destino), o sistema busca opções de diferentes companhias aéreas e apresenta todas juntas para comparação.

O sistema oferece:
- **Busca de voos:** Por origem, destino, datas e número de passageiros
- **Filtros:** Preço, horário, duração, número de escalas
- **Comparação:** Todas as opções em uma única tela
- **Reserva:** Seleção do voo e pagamento online

### 3. Atores e Fluxos Principais

#### Administrador da Plataforma
- Cadastrar companhias aéreas parceiras
- Configurar taxas de serviço da plataforma
- Visualizar relatórios de vendas
- Gerenciar disputas e cancelamentos
- Atualizar informações de voos (quando necessário)

**Permissões:** Acesso administrativo geral da plataforma.

#### Viajante
- Pesquisar voos por origem, destino e datas
- Filtrar resultados por preço, horário e escalas
- Comparar opções de diferentes companhias
- Selecionar voo e informar dados dos passageiros
- Escolher serviços adicionais (bagagem, assento)
- Fazer pagamento online
- Visualizar e gerenciar suas reservas
- Solicitar cancelamento ou alteração

**Permissões:** Acessa voos disponíveis e suas próprias reservas.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir pesquisar voos por origem, destino e datas
- **RF02:** O sistema deve mostrar voos de diferentes companhias aéreas
- **RF03:** O sistema deve permitir filtrar por preço, horário e escalas
- **RF04:** O sistema deve mostrar detalhes do voo (duração, bagagem incluída)
- **RF05:** O sistema deve permitir selecionar voo e informar dados dos passageiros
- **RF06:** O sistema deve permitir adicionar serviços (bagagem extra, escolha de assento)
- **RF07:** O sistema deve processar pagamento online
- **RF08:** O sistema deve enviar confirmação e código de reserva por e-mail
- **RF09:** O sistema deve permitir visualizar e gerenciar reservas
- **RF10:** O sistema deve permitir cancelamento conforme regras da tarifa

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 2.000-10.000 usuários ativos por mês
- **Volume de dados:** ~20.000 reservas no primeiro ano
- **Segurança básica:** Login com usuário e senha; dados de pagamento protegidos; dados de passageiros armazenados de forma segura
- **Disponibilidade esperada:** Alta (viajantes podem pesquisar e comprar a qualquer hora)

### 6. Integrações Externas

- **Companhias aéreas:** Para buscar voos e confirmar reservas
- **Sistema de pagamentos:** Para processar compras de passagens
- **Envio de e-mails:** Para confirmar reservas e enviar lembretes de viagem

### 7. Riscos e Desafios Técnicos

1. **Preço desatualizado:** O preço pode mudar entre a pesquisa e a confirmação do pagamento. O sistema deve verificar o preço antes de finalizar.

2. **Disponibilidade:** O assento pode ser vendido por outro canal enquanto o viajante está decidindo. O sistema deve verificar disponibilidade antes de confirmar.

3. **Dados de passageiros:** Nomes e documentos devem estar corretos. Erros podem impedir o embarque. O sistema deve validar os dados.

4. **Cancelamentos e reembolsos:** Cada companhia tem regras diferentes. O sistema precisa aplicar corretamente as políticas de cada uma.

---