# Trabalho 4 - Casos de Teste

> **Pré-requisito entregue:**
- Trabalho 3 — Modelagem (seleção das **3 fatias verticais** e seus modelos)
>
> **Filosofia deste trabalho:** testar com **método**, não por intuição. Em vez de escrever testes aleatórios, vocês vão derivar casos de teste das fatias já modeladas no Trabalho 3, exercitando caminho feliz **e** fronteiras — onde os bugs realmente moram.

---

## 1. O que entregar

Para cada uma das **3 fatias selecionadas** no Trabalho 3, elaborem **2 casos de teste** seguindo o padrão IEEE — totalizando **6 casos de teste**.

Os casos devem ser rastreáveis: cada um aponta para a fatia e a história de usuário (Trabalho 2) que exercita.

---

## 2. Template do caso de teste

Cada caso deve usar o seguinte template:

| Campo | Conteúdo |
| --- | --- |
| **ID** | Identificador (ex.: TC-FATIA1-01) |
| **Fatia / História de usuário** | Qual fatia e qual história de usuário do Trabalho 2 |
| **Pré-condições** | Estado do sistema antes do teste |
| **Dados de entrada** | Valores específicos a serem usados |
| **Passos** | Sequência de ações |
| **Resultado esperado** | O que o sistema deve fazer |
| **Critério de aprovação** | Condição mensurável para passar |
| **Severidade em caso de falha** | Crítica / Alta / Média / Baixa |

---

## 3. Critério de seleção dos 6 casos

Dos 2 casos por fatia, **pelo menos 1 deve ser um caso de fronteira** — não apenas “caminho feliz”. Casos de fronteira incluem:

- **Valor limítrofe** (BVA — boundary value analysis): testar exatamente no limite de uma regra. Ex.: pedido de R$ 99,99 (frete cobrado) vs R$ 100,00 (frete grátis).
- **Transição crítica de estado**: testar uma transição que tem regra específica. Ex.: tentar cancelar uma entrega que já está `Em Trânsito` (não deve permitir).
- **Caminho de erro**: testar comportamento quando algo falha. Ex.: cartão recusado pelo gateway, ou timeout de coleta pelo entregador.

A intenção é exercitar a noção de que casos de teste se desenham com **método** — não escolhendo aleatoriamente.

---

## 4. Exemplo de caso de teste preenchido

| Campo | Conteúdo |
| --- | --- |
| **ID** | TC-FATIA1-02 |
| **Fatia / História de usuário** | Fatia 1 (checkout) — US-CC-007 (pagamento seguro) |
| **Pré-condições** | Cliente autenticado; carrinho com 1 livro de R$ 50,00 + frete R$ 15,00; cartão de crédito cadastrado no perfil. |
| **Dados de entrada** | Cartão com saldo insuficiente (mock do gateway configurado para retornar `INSUFFICIENT_FUNDS`). |
| **Passos** | 1) Cliente clica em “Finalizar Compra”; 2) Cliente confirma forma de pagamento; 3) Cliente clica em “Pagar”. |
| **Resultado esperado** | Sistema exibe mensagem “Pagamento recusado: saldo insuficiente”; pedido **não é** criado; estoque **não é** baixado; carrinho permanece intacto. |
| **Critério de aprovação** | (a) Mensagem de erro clara e específica é exibida; (b) Nenhum registro de pedido é criado no banco; (c) Quantidade do livro no estoque permanece igual. |
| **Severidade em caso de falha** | **Crítica** — bug aqui significa pedido criado sem pagamento confirmado, dor financeira direta. |

> Nota: este trabalho **não é um plano de testes completo** — é uma amostra de casos de teste rastreável às fatias modeladas. Plano de testes formal pode ser tema de trabalho futuro.
>

---

## 5. Rastreabilidade

Inclua uma **tabela de rastreabilidade** ligando cada fatia (Trabalho 3) aos seus casos de teste:

| Fatia | Histórias de usuário (T2) | Diagrama comportamental (T3) | Casos de teste |
| --- | --- | --- | --- |
| Fatia 1 | US-CC-003, US-CC-007, US-PA-002 | Sequência | TC-FATIA1-01, TC-FATIA1-02 |
| Fatia 2 | US-LOG-004, US-LOG-006, US-LOG-009 | Estados | TC-FATIA2-01, TC-FATIA2-02 |
| Fatia 3 | US-CC-011, US-CC-014, US-CC-016 | Atividades | TC-FATIA3-01, TC-FATIA3-02 |

Essa tabela mostra que os casos de teste **não nasceram do nada** — derivam diretamente das fatias modeladas, fechando o ciclo requisito → modelo → teste.

---

## 6. Entrega

A entrega deve ser feita em **repositório no GitHub** com documentação em **Markdown** — **não serão aceitos Word ou PDF**. Pode ser adicionada ao mesmo repositório do Trabalho 3 (ex.: `docs/06-casos-de-teste.md`).

### 6.1 Padrões a seguir

- **IEEE-829** / **IEEE-830** na documentação dos casos de teste (template fornecido na Seção 2).
- **ABNT** para citações e referências.
- **Markdown** padrão CommonMark/GFM.

### 6.2 Políticas que se aplicam

- [Política antiplágio](https://www.notion.so/Pol-tica-antipl-gio-5187d7b1ab514bfb8424ac0fcfb59dba)
- [Política de uso de ferramentas generativas de IA](https://www.notion.so/Pol-tica-de-uso-de-ferramentas-generativas-de-IA-2-353a6ec0abcd807285f9c609011f7c3e)

---

## Referências

- IEEE Std 829-2008. *IEEE Standard for Software and System Test Documentation*.
- IEEE Std 830-1998. *IEEE Recommended Practice for Software Requirements Specifications*.
- MYERS, G. J.; SANDLER, C.; BADGETT, T. *The Art of Software Testing*. 3rd ed. Wiley, 2011.
- Markdown Guide: https://www.markdownguide.org/getting-started/
