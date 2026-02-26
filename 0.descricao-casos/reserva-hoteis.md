### 1. Escopo e Objetivos

A plataforma **StayHub** visa conectar viajantes a uma vasta rede de meios de hospedagem (hotéis, pousadas, resorts e apartamentos por temporada). O objetivo principal é oferecer uma experiência de busca simplificada, reserva garantida e gestão de estadias, funcionando de forma análoga ao Hotels.com. O sistema deve servir como um marketplace confiável que gerencia a disponibilidade de quartos em tempo real e processa transações financeiras seguras.

### 2. Descrição do Sistema

O sistema permitirá que os usuários pesquisem acomodações filtrando por destino, datas de check-in/check-out e número de hóspedes. A plataforma deve exibir uma lista de opções com fotos de alta resolução, descrições detalhadas das comodidades (Wi-Fi, café da manhã, piscina, pet-friendly) e a localização exata em um mapa interativo.

Os usuários poderão comparar diferentes tipos de quartos dentro de um mesmo estabelecimento (Standard, Luxo, Suíte) e visualizar políticas de cancelamento específicas para cada tarifa. Um componente essencial será o **Sistema de Avaliações**, onde hóspedes reais podem dar notas e deixar comentários detalhados sobre sua experiência, servindo como prova social para futuros clientes.

### 3. Reservas e Programas de Fidelidade

A plataforma deve gerenciar o ciclo de vida da reserva: desde a pré-reserva (bloqueio temporário do inventário) até a confirmação após o pagamento. Um diferencial pedagógico para este projeto é a inclusão de um **Programa de Fidelidade** (ex: "Reserve 10 noites, ganhe 1"), que exige o rastreamento histórico de estadias concluídas e a aplicação automática de benefícios e descontos para usuários recorrentes.

### 4. Arquitetura e Requisitos Não Funcionais

- **Arquitetura:** Aplicação do padrão **MVC** (Model-View-Controller). O "Model" deve gerenciar a lógica complexa de cálculo de diárias, que variam conforme a sazonalidade e ocupação.
- **Segurança:** Proteção rigorosa dos dados de pagamento e conformidade com a LGPD. O sistema deve garantir que os dados sensíveis dos hóspedes não sejam expostos a terceiros não autorizados.
- **Performance:** O carregamento dos resultados de busca deve ser otimizado via *caching* de resultados frequentes, garantindo respostas rápidas mesmo em destinos de alta demanda.
- **Disponibilidade:** O inventário deve ser atualizado instantaneamente para evitar o "double booking" (duas pessoas reservando o mesmo quarto no mesmo segundo).

### 5. Riscos e Desafios Técnicos

1. **Sincronização de Inventário:** O desafio de manter a disponibilidade atualizada quando o hotel também vende em outras plataformas (conflitos de API).
2. **Conversão de Moeda:** Lidar com reservas internacionais que exigem conversão de câmbio em tempo real e taxas de IOF.
3. **Fraude de Reservas:** Mecanismos para detectar e impedir reservas falsas ou cartões de crédito clonados.
4. **Escalabilidade em Sazonalidade:** Suportar aumentos massivos de acessos durante períodos de férias e feriados nacionais.

---

### Estrutura Pedagógica: Foco em Regras de Negócio e Casos de Uso

Para este sistema, é fundamental que o aluno saiba diferenciar o fluxo do **Hóspede** do fluxo do **Administrador do Hotel**:

| **Ator** | **Principais Casos de Uso** |
| --- | --- |
| **Hóspede** | Pesquisar hotéis, Aplicar filtros, Realizar reserva, Cancelar reserva, Avaliar estadia. |
| **Hoteleiro** | Cadastrar quartos, Atualizar preços de diárias, Confirmar check-in, Responder avaliações. |
| **Administrador StayHub** | Gerenciar comissões, Banir estabelecimentos fraudulentos, Gerar relatórios de vendas. |

---