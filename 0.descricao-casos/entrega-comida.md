O mercado de entrega de alimentos tem vivenciado um aumento expressivo na demanda por serviços que proporcionem conveniência e rapidez. Diante disso, este projeto tem como objetivo desenvolver um aplicativo de entrega de alimentos que permita aos usuários escolher entre uma ampla seleção de restaurantes e tipos de comida para pedir, além de oferecer entregas rápidas e confiáveis. O aplicativo estará disponível tanto para desktop quanto para dispositivos móveis.

O aplicativo permitirá que os usuários pesquisem restaurantes e tipos de comida por nome, localização e outros filtros. Eles poderão visualizar menus de restaurantes, incluindo opções de comida, preços e avaliações de outros usuários. Além disso, os usuários poderão fazer pedidos, escolher itens do menu, selecionar opções de entrega e pagamento, e adicionar notas para o restaurante.

Os usuários terão a possibilidade de acompanhar o status de seus pedidos, incluindo a localização do entregador e o tempo de chegada estimado. O aplicativo também permitirá que os usuários avaliem a qualidade do serviço prestado pelo restaurante e pelo entregador. Os usuários poderão salvar endereços de entrega frequentes e histórico de pedidos para facilitar pedidos futuros.

Restaurantes terão a opção de gerenciar e atualizar seus menus, gerenciar pedidos e responder a comentários de clientes. Já os entregadores poderão receber e gerenciar pedidos, visualizar rotas de entrega e atualizar o status dos pedidos. O aplicativo contará com uma interface de usuário intuitiva e fácil de usar, garantindo compatibilidade com diversos sistemas operacionais, como Windows, Mac OS e iOS.

A escalabilidade será priorizada, assegurando que o aplicativo possa lidar com um grande número de restaurantes, entregadores e usuários simultaneamente. A segurança das informações pessoais e financeiras dos usuários também será fundamental, bem como o desempenho e a capacidade de resposta rápida aos comandos do usuário.

A integração com outros sistemas de pagamento online, como PayPal ou Stripe, também será possível. Por fim, o aplicativo contará com recursos de backup e recuperação de dados para proteger as informações dos usuários em caso de falha do sistema.

No entanto, é importante considerar os riscos associados a um projeto deste tipo, como a possibilidade de falhas de segurança, questões de privacidade, concorrência acirrada no mercado e desafios na aquisição e retenção de usuários.

Para este sexto cenário, abordaremos o domínio de **Logística de Last-Mile e Delivery de Alimentos**. Este é um caso de estudo riquíssimo para a Engenharia de Software, pois exige a orquestração de **três interfaces distintas** (Cliente, Restaurante e Entregador) operando em sincronia quase perfeita.

Abaixo, apresento o enunciado estruturado para a plataforma **FoodFlow**.

---

## Proposta de Trabalho Prático: Plataforma de Logística e Delivery de Alimentos (FoodFlow)

### 1. Escopo e Objetivos

A plataforma **FoodFlow** tem como missão conectar consumidores finais a uma rede diversificada de estabelecimentos gastronômicos, facilitando o processo de descoberta, pedido e entrega de refeições. O sistema deve gerenciar o fluxo completo da cadeia de suprimentos: do gerenciamento de cardápios pelos restaurantes à roteirização para entregadores, garantindo conveniência para o usuário e eficiência operacional para os parceiros.

### 2. Descrição do Sistema

O ecossistema FoodFlow será composto por três módulos integrados:

1. **Módulo do Cliente:** Pesquisa inteligente por geolocalização, filtros de culinária, visualização de cardápios dinâmicos e sistema de carrinho de compras.
2. **Módulo do Restaurante (Merchant):** Gestão de inventário em tempo real, controle de pedidos (Aceite/Preparação/Despacho) e análise de avaliações dos clientes.
3. **Módulo do Entregador:** Sistema de ofertas de frete baseada em proximidade, navegação GPS integrada e atualização de status da jornada (Coleta/Em trânsito/Entregue).

A experiência deve ser personalizada, permitindo que o usuário salve endereços frequentes, visualize o histórico de pedidos e acompanhe, via mapa, o deslocamento do entregador com o tempo estimado de chegada (ETA).

### 3. Integração e Transações Financeiras

O sistema deve integrar-se a gateways de pagamento (como Stripe ou PayPal) para processar transações via cartão de crédito e carteiras digitais. A lógica de negócio deve prever a **divisão de pagamentos (split)**: após a confirmação do pedido, o sistema deve calcular e distribuir automaticamente as partes devidas ao restaurante, ao entregador e a comissão da plataforma.

### 4. Arquitetura e Requisitos Não Funcionais

- **Arquitetura:** Implementação baseada em **MVC**. Devido à natureza tripartite do sistema, a camada de *Controller* deve ser capaz de lidar com eventos assíncronos (notificações push) para alertar restaurantes sobre novos pedidos e entregadores sobre novas rotas.
- **Escalabilidade:** O sistema deve suportar picos de demanda em horários sazonais (almoço/jantar), garantindo que a latência na busca por restaurantes não prejudique a conversão de vendas.
- **Disponibilidade:** A persistência de dados deve ser resiliente, com estratégias de backup e recuperação para evitar que pedidos ativos se percam em caso de falha de infraestrutura.
- **Segurança:** Proteção de dados sensíveis (LGPD) e protocolos rígidos para transações financeiras e autenticação de usuários.

### 5. Riscos e Desafios Técnicos

1. **Consistência de Inventário:** O risco de vender um item que acabou de esgotar no restaurante físico (sincronização de estoque).
2. **Roteirização e Logística:** Otimizar o encontro entre entregador e restaurante para minimizar o tempo de espera do cliente e manter a temperatura do alimento.
3. **Concorrência e Performance:** Gerenciar milhares de atualizações de GPS simultâneas sem sobrecarregar o banco de dados.
4. **Gestão de Conflitos:** Implementar fluxos de cancelamento e estorno automáticos quando o pedido não é coletado ou sofre atrasos críticos.

---

### Estrutura Pedagógica: Foco em Interoperabilidade

Para este projeto, é vital que o aluno compreenda a **interdependência** dos requisitos funcionais entre os diferentes perfis de usuário:

| **Requisito Funcional (RF)** | **Ator Relacionado** | **Dependência Técnica** |
| --- | --- | --- |
| Atualizar Status do Pedido | Restaurante | Dispara Notificação Push para o Cliente. |
| Aceitar Rota de Entrega | Entregador | Altera o status do pedido para "Em trânsito". |
| Aplicar Cupom de Desconto | Cliente | Valida contra as Regras de Negócio do Marketing. |
| Gerenciar Cardápio | Restaurante | Atualiza o Cache de busca do Cliente imediatamente. |