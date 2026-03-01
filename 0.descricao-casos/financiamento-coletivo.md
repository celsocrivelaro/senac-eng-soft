## Plataforma de Financiamento Coletivo (ImpactFund)

### 1. Escopo e Objetivos

A plataforma **ImpactFund** visa democratizar o acesso a capital para projetos criativos, sociais e tecnológicos. O sistema deve atuar como um ecossistema de confiança onde "Criadores" apresentam suas ideias e "Apoiadores" financiam essas iniciativas em troca de recompensas ou impacto social. O objetivo é gerenciar todo o ciclo de vida de uma campanha, desde o rascunho até a entrega das recompensas e prestação de contas.

### 2. Descrição do Sistema

O sistema permitirá que os usuários criem campanhas detalhadas, definindo metas financeiras (em diferentes moedas, se necessário), prazos de encerramento e descrições multimídia. A plataforma oferecerá suporte a múltiplos **Níveis de Recompensa**, onde cada valor de doação está atrelado a um benefício específico para o apoiador.

Para os apoiadores, a plataforma deve oferecer ferramentas robustas de descoberta, com filtros por categoria (Arte, Tecnologia, Educação, etc.), proximidade do prazo de encerramento e porcentagem de arrecadação atingida. A interação social é fundamental: o sistema deve permitir comentários públicos, atualizações de progresso enviadas por e-mail e um sistema de mensagens privadas para sanar dúvidas diretamente com o criador.

### 3. Gestão Financeira e Regras de Negócio

A plataforma deve implementar a lógica de **"Tudo ou Nada"** (onde o dinheiro só é liberado se a meta for atingida) ou **"Flexível"** (onde o criador recebe o que arrecadar). A integração com gateways de pagamento (Stripe, PayPal, Pix) deve garantir a retenção segura dos valores até o encerramento da campanha. O sistema também deve calcular automaticamente as taxas de administração da plataforma e as taxas de transação financeira antes de repassar o valor líquido ao criador.

### 4. Arquitetura e Requisitos Não Funcionais

- **Arquitetura:** Utilização do padrão **MVC**. A camada *Model* deve ser rigorosa na gestão de concorrência para evitar erros no cálculo do montante arrecadado durante picos de doações.
- **Escalabilidade:** O sistema deve estar preparado para o "efeito viral", suportando um aumento súbito de acessos e transações sem perda de desempenho.
- **Segurança:** Implementação de protocolos de segurança financeira (PCI DSS) e proteção contra fraudes de estorno (*chargeback*). A privacidade dos dados dos usuários deve seguir estritamente a LGPD.
- **Resiliência:** Mecanismos de backup em tempo real e planos de recuperação de desastres para garantir que o histórico de doações nunca seja corrompido ou perdido.

### 5. Riscos e Desafios Técnicos

1. **Fraude e Lavagem de Dinheiro:** O desafio de validar a identidade dos criadores e a legitimidade dos projetos para proteger os doadores.
2. **Gestão de Recompensas:** Controlar o estoque de recompensas limitadas durante o processo de checkout simultâneo.
3. **Complexidade Regulatória:** Cumprimento de normas específicas de financiamento coletivo e proteção ao consumidor, que variam por país.
4. **Retenção de Usuários:** O desafio de engenharia em criar uma interface altamente engajadora que facilite o compartilhamento orgânico em redes sociais.

---