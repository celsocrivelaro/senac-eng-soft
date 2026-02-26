### 1. Escopo e Objetivos

A plataforma **CityGo** tem como objetivo conectar passageiros a motoristas parceiros para a realização de viagens urbanas sob demanda. O sistema deve operar como um ecossistema bilateral, atendendo a duas interfaces distintas: uma para o passageiro (solicitação e acompanhamento) e outra para o motorista (gestão de ofertas e navegação), garantindo uma experiência fluida, segura e de baixo custo.

### 2. Descrição do Sistema

O sistema permitirá que o passageiro solicite viagens informando o ponto de embarque e o destino. A plataforma deve realizar o cálculo de estimativa de preço e tempo de chegada com base na distância e condições de tráfego. Uma vez solicitada a viagem, o algoritmo de *matching* deve identificar e notificar os motoristas mais próximos que estejam disponíveis.

Os motoristas poderão aceitar ou rejeitar as solicitações. Ao aceitar, o sistema inicia o rastreamento em tempo real via GPS, permitindo que ambas as partes visualizem o deslocamento no mapa. A plataforma também deve oferecer suporte a diferentes categorias de veículos (ex: Econômico, Conforto, Entrega) e um sistema de avaliação mútua (estrelas e comentários) para garantir a qualidade e segurança da comunidade.

### 3. Integração e Pagamentos

A plataforma deve integrar-se a serviços de mapas (Google Maps API ou Mapbox) para roteirização e geocodificação. O processamento de pagamentos deve ser automatizado, permitindo o cadastro de cartões de crédito/débito, uso de saldo em carteira digital ou pagamento direto ao motorista via Pix/Dinheiro. Ao final de cada corrida, o sistema deve gerar um recibo digital detalhando o trajeto, impostos e taxas da plataforma.

### 4. Arquitetura e Requisitos Não Funcionais

- **Arquitetura:** Recomenda-se a adoção do padrão **MVC** para a estruturação do servidor, garantindo que a lógica de precificação (Model) esteja separada da visualização do mapa (View). Para turmas avançadas, pode-se discutir a transição para **Microserviços** devido à necessidade de processamento assíncrono.
- **Disponibilidade e Resiliência:** O sistema deve operar com disponibilidade de $99,9\%$. Estratégias de **Chaos Engineering** (Engenharia de Caos) devem ser consideradas para simular falhas na rede móvel ou perda de sinal de GPS.
- **Performance:** O tempo de resposta para o pareamento entre motorista e passageiro deve ser inferior a 5 segundos.
- **Segurança:** Implementação de protocolos de autenticação robustos (OAuth2/JWT) e recursos de segurança em tempo real, como o botão de emergência e compartilhamento de rota.

### 5. Riscos e Desafios Técnicos

1. **Consistência Geográfica:** Manter a precisão da localização dos veículos em áreas de sombra de sinal ou alta densidade urbana.
2. **Algoritmo de Precificação Dinâmica:** O risco de instabilidade no sistema ao calcular aumentos de preço baseados na relação oferta vs. demanda em tempo real.
3. **Concorrência de Dados:** Gerenciar múltiplas requisições simultâneas para o mesmo motorista, evitando o "race condition" (dois passageiros aceitos pelo mesmo veículo).
4. **Privacidade:** Garantir que o histórico de rotas dos usuários seja armazenado de forma criptografada e em conformidade com a LGPD.