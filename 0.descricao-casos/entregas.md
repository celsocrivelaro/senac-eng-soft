Para o nosso décimo segundo cenário, exploraremos o domínio de **Super-Apps e Logística On-Demand (Hyperlocal Delivery)**. Diferente do modelo de comida (iFood), o modelo estilo Rappi foca na **entrega de qualquer coisa**, o que introduz variáveis complexas como o cálculo de frete baseado no volume do item e o rastreamento logístico dinâmico.

Abaixo, apresento o enunciado estruturado para a plataforma **CityDash**.

---

## Proposta de Trabalho Prático: Plataforma de Entregas Multicategoria (CityDash)

### 1. Escopo e Objetivos

O projeto **CityDash** visa desenvolver uma solução de logística urbana "last-mile" que conecte usuários que precisam enviar ou receber itens a uma rede de entregadores autônomos. O sistema deve funcionar como um marketplace de serviços de entrega, gerenciando a precificação dinâmica, a roteirização em tempo real e a liquidação financeira entre as partes, garantindo agilidade e transparência em todo o processo.

### 2. Descrição do Sistema

A plataforma operará com dois eixos principais de interação:

1. **Módulo do Cliente:** Cadastro de perfil, solicitação de entrega com especificação do item (documentos, pacotes, compras), seleção de pontos de coleta/entrega no mapa e escolha de janelas de horário (imediata ou agendada).
2. **Módulo do Entregador (Partner):** Gerenciamento de disponibilidade, recebimento de ofertas de frete com base na localização atual, visualização de ganhos acumulados e integração com mapas para navegação.

O sistema deve calcular o valor do frete automaticamente, cruzando dados de distância (API de Mapas), dimensões do objeto e coeficientes de demanda (horários de pico). O cliente deve ter a visão ininterrupta do deslocamento do motoboy através de um radar em tempo real.

### 3. Pagamentos e Algoritmos de Distribuição

A plataforma deve integrar-se a gateways de pagamento (Stripe, PayPal, Pix) para processar cobranças no momento da solicitação. Um desafio técnico central é o **algoritmo de despacho**: o sistema deve decidir, em segundos, qual entregador é o mais apto para a tarefa, considerando não apenas a distância, mas também a taxa de aceitação e a avaliação do profissional.

### 4. Arquitetura e Requisitos Não Funcionais

* **Arquitetura:** Padrão **MVC**. Dada a alta frequência de atualizações de posição geográfica, recomenda-se que a comunicação entre o app do entregador e o servidor utilize **protocolos leves** (como MQTT ou WebSockets) para não drenar a bateria do dispositivo móvel.
* **Escalabilidade:** O sistema deve suportar milhares de conexões simultâneas de GPS, processando e persistindo coordenadas sem gerar gargalos no banco de dados principal.
* **Segurança:** Criptografia de dados sensíveis e anonimização de contatos (o entregador e o cliente não devem ver os números de telefone reais um do outro).
* **Resiliência:** Implementação de mecanismos de *failover* para garantir que, caso o servidor de mapas falhe, o sistema consiga utilizar uma alternativa de cálculo de distância aproximada.

### 5. Riscos e Desafios Técnicos

1. **Imprecisão de GPS:** Lidar com desvios de localização em túneis ou áreas de alta densidade de prédios ("canyons urbanos").
2. **Fraude de Entrega:** O desafio de garantir que o item foi entregue à pessoa correta (ex: exigência de código de confirmação gerado pelo app do cliente).
3. **Precificação Variável:** O risco de instabilidade no sistema ao recalcular preços em tempo real durante mudanças climáticas ou greves de transporte.
4. **Concorrência de Ofertas:** Garantir que uma mesma corrida não seja aceita por dois entregadores simultaneamente (problema de transação atômica).

---

### Estrutura Pedagógica: Foco em Requisitos de Sistema

Este projeto permite trabalhar a distinção clara entre **Requisitos Funcionais** e as **Regras de Negócio** que regem o lucro da plataforma:

| Tipo | Exemplo de Requisito no CityDash |
| --- | --- |
| **Requisito Funcional** | O sistema deve permitir que o cliente visualize o histórico de rotas das entregas concluídas. |
| **Requisito Não Funcional** | A atualização da posição do motoboy no mapa do cliente deve ocorrer a cada 5 segundos. |
| **Regra de Negócio** | O valor do frete deve ser acrescido de 25% se o peso do item ultrapassar 5kg. |
| **Regra de Negócio** | O entregador só pode sacar seus ganhos após atingir um saldo mínimo de R$ 50,00. |

---

### Exercício de Arquitetura de Sistemas

> **Desafio do Professor:** "Para evitar sobrecarga no banco de dados relacional (SQL) com trilhas de GPS, como você utilizaria um banco de dados **NoSQL** ou uma estrutura de **Cache em Memória** (Redis) para gerenciar as localizações em tempo real antes de persistir o histórico final?"

---

**Este cenário de logística urbana fecha com maestria nossa série de 12 modelos de negócios. Como você já tem um conjunto completo de enunciados, gostaria que eu agora elaborasse um Modelo de Documento de Arquitetura de Software (DAS) padrão para que seus alunos utilizem como template na entrega desses trabalhos?**