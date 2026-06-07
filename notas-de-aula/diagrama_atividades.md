# Diagrama de Atividades

## 1. Atividades: a UML olhando para os processos

Os diagramas que vimos até aqui têm focos bem definidos: classes capturam *estrutura*, sequência captura *colaborações*, estados capturam o *ciclo de vida de um objeto*. O **diagrama de atividades** preenche uma lacuna distinta: ele descreve **fluxos de trabalho** — sequências de tarefas que, juntas, realizam um objetivo.

Há dois usos principais, igualmente válidos:

- **Modelagem de processos de negócio**: a forma mais comum e politicamente importante. Diagramas de atividades são usados para mapear como uma empresa atende um pedido, processa uma reclamação, contrata um funcionário. Aqui o diagrama serve como linguagem comum entre analistas, desenvolvedores e *stakeholders* não-técnicos.
- **Modelagem de algoritmos e fluxos de software**: descrever a lógica interna de um caso de uso, de um método complexo, ou de um pipeline de processamento.

> 💡 **Intuição:** Se o diagrama de sequência é uma cena com vários personagens dialogando, o diagrama de atividades é uma *receita de bolo* — uma sequência de passos a executar, com possíveis bifurcações, paralelismos e voltas. A pergunta que ele responde é: *"Como esse trabalho é feito, do começo ao fim?"*
> 

### 1.1 Mas isso não é só um fluxograma?

Estudantes frequentemente perguntam — com razão — qual a diferença entre um diagrama de atividades e um fluxograma tradicional (com losangos, retângulos e setas). A resposta é **sim e não**: o diagrama de atividades é uma evolução do fluxograma clássico, mas com semântica formal e construções modernas que o fluxograma não tem:

| Recurso | Fluxograma clássico | Diagrama de Atividades |
| --- | --- | --- |
| Sequência e decisão | ✅ | ✅ |
| Paralelismo (`fork`/`join`) | ❌ | ✅ |
| Particionamento por responsável (raias) | ❌ | ✅ |
| Fluxo de objetos entre ações | ❌ | ✅ |
| Eventos de tempo e sinais | ❌ | ✅ |
| Semântica formal (tokens) | ❌ | ✅ |

Em resumo: todo fluxograma legível pode ser desenhado como diagrama de atividades, mas a recíproca não é verdadeira.

---

## 2. Elementos básicos

A semântica do diagrama de atividades é baseada em **tokens** (conceito emprestado das redes de Petri): um token "viaja" pelo diagrama, e cada nó dita as regras de como tokens são consumidos e produzidos. Isso pode soar abstrato, mas é o que dá rigor ao paralelismo.

### 2.1 Nós de controle

| Nó | Notação | Descrição |
| --- | --- | --- |
| **Inicial** | círculo preto sólido (`●`) | Ponto de partida do fluxo. |
| **Final de atividade** | círculo com anel (`◉`) | Encerra o diagrama inteiro. |
| **Final de fluxo** | círculo com `X` dentro (`⊗`) | Encerra apenas *aquele fluxo*; outros podem continuar. |
| **Ação** | retângulo de cantos arredondados | Uma unidade de trabalho. |
| **Decisão** | losango com 1 entrada, ≥2 saídas | Bifurcação condicional, com guardas em cada saída. |
| **Mesclagem** (*merge*) | losango com ≥2 entradas, 1 saída | Reúne fluxos alternativos. |
| **Bifurcação** (*fork*) | barra horizontal/vertical, 1 entrada, ≥2 saídas | Inicia execução paralela. |
| **Junção** (*join*) | barra horizontal/vertical, ≥2 entradas, 1 saída | Sincroniza fluxos paralelos. |

> 💡 **Intuição:** Decisão e mesclagem usam o **mesmo símbolo** (losango), assim como bifurcação e junção usam a mesma barra. O que os distingue é a *direção do fluxo*: se um nó tem mais setas saindo que entrando, ele está abrindo caminhos; se tem mais entrando que saindo, está reunindo. Essa simetria deliberada é elegante mas confunde iniciantes — sempre confira o sentido das setas.
> 

### 2.2 Decisão e mesclagem

Decisões expressam *escolhas exclusivas*: apenas um caminho é tomado. Cada saída deve ter uma **guarda** (condição booleana) entre colchetes; é boa prática usar `[else]` para o caso default.

```
                    ┌─[idade >= 18]──▶ acessoLiberado
   verificarIdade ─▶ ◇
                    └─[else]─────────▶ negarAcesso
```

Mesclagem é o oposto: junta caminhos alternativos sem esperar — basta um token chegar para o fluxo continuar.

```
   acessoLiberado ─▶ ◇ ─▶ registrarLog
   negarAcesso ─────▶ ◇
```

### 2.3 Bifurcação e junção

A bifurcação dispara *múltiplos fluxos simultaneamente*. A junção espera **todos** os fluxos paralelos chegarem antes de prosseguir — é uma **sincronização por barreira**.

```
                                 ┌──▶ enviarConfirmacao ──┐
   processarPedido ─▶ ═════════ ─┤                        ├─▶ ═════════ ─▶ encerrar
                     (fork)     │                         │     (join)
                                 └──▶ debitarEstoque ─────┘
```

Esta é a construção que **fluxogramas tradicionais não conseguem expressar**. Sem ela, somos forçados a serializar artificialmente atividades que, no mundo real, podem (e frequentemente devem) acontecer em paralelo.

### 2.4 Cuidado com "fork sem join"

Um erro comum é abrir um `fork` e nunca sincronizar os ramos. A semântica formal permite isso (cada ramo pode terminar em seu próprio final de fluxo), mas costuma ser sintoma de modelo confuso. Se duas tarefas paralelas precisam ter resultados combinados, **sempre use `join`**.

---

## 3. Partições (Raias / *Swimlanes*)

Em processos de negócio, é raro que tudo aconteça "no mesmo lugar" — diferentes ações são executadas por diferentes atores, departamentos ou sistemas. As **partições** (popularmente chamadas de **raias** ou *swimlanes*) tornam isso explícito ao dividir o diagrama em faixas, cada uma representando um responsável.

```
┌─────────────┬─────────────────┬─────────────────┐
│  Cliente    │     Sistema     │   Logística     │
├─────────────┼─────────────────┼─────────────────┤
│             │                 │                 │
│ ● ──fazer──▶│ validarPedido   │                 │
│   pedido    │       │         │                 │
│             │       ▼         │                 │
│             │ confirmarPedido │                 │
│             │       │         │                 │
│             │       └────────▶│ separarItens    │
│             │                 │       │         │
│             │                 │       ▼         │
│             │                 │   despachar     │
│             │                 │       │         │
│ receber  ◀──┼─────────────────┼───────┘         │
│ entrega     │                 │                 │
│   │         │                 │                 │
│   ▼         │                 │                 │
│   ◉         │                 │                 │
└─────────────┴─────────────────┴─────────────────┘
```

Quando uma ação migra de uma raia para outra, o cruzamento da seta sobre a borda da raia é, semanticamente, uma **transferência de responsabilidade**.

> 💡 **Intuição:** Raias são uma das construções mais didáticas da UML. Mostre um diagrama com raias para um analista de negócio e ele *imediatamente* identifica o que pertence ao seu time. Raias também ajudam a detectar dois cheiros (smells) clássicos: (1) *raias muito carregadas* — um único ator está fazendo trabalho demais, possível candidato a refatoração de processo; (2) *idas e voltas excessivas* entre raias — um mesmo trabalho sendo "jogado por cima do muro" várias vezes, indicando processo mal projetado.
> 

### 3.1 Partições bidimensionais

A UML 2.x permite **partições bidimensionais** — combinando, por exemplo, "departamento" no eixo horizontal e "localização" no vertical. São raras na prática (ficam visualmente carregadas), mas existem.

---

## 4. Fluxo de objetos

Até aqui falamos de **fluxo de controle** (quem executa depois de quem). Mas frequentemente precisamos modelar também o **fluxo de objetos** — *que dado* sai de uma ação e entra em outra.

A UML oferece duas notações equivalentes:

**(a) Nó de objeto** entre duas ações:

```
   preencherFormulário ─▶ [ Solicitação ] ─▶ revisarSolicitação
```

**(b) Pinos** (*pins*) — pequenos retângulos nos cantos das ações:

```
   ┌───────────────────┐  ┌─┐    ┌─┐  ┌──────────────────┐
   │ preencherFormul.  │──┤ ├────┤ ├──│ revisarSolicit.  │
   └───────────────────┘  └─┘    └─┘  └──────────────────┘
                       output    input
```

Pinos são mais econômicos visualmente; nós de objeto são mais explícitos. Use o que tornar o diagrama mais legível para o público.

Nós de objeto podem carregar **estado** entre colchetes — útil para mostrar que o objeto muda à medida que percorre o fluxo:

```
   processar ─▶ [ Pedido [pago] ] ─▶ separar ─▶ [ Pedido [separado] ] ─▶ enviar
```

Note a coerência com diagramas de estados: aqueles `[pago]`, `[separado]` são literalmente estados de uma máquina associada à classe `Pedido`.

---

## 5. Construções avançadas

### 5.1 Eventos de tempo

Um nó com a forma de **ampulheta** (`⌛`, ou um triângulo bipartido) representa o **decorrer do tempo** — uma "ação" que apenas espera. É muito útil para modelar prazos:

```
   solicitarConfirmacao ─▶ ⌛ after(48h) ─▶ cancelarPorInatividade
```

### 5.2 Recepção e envio de sinais

Processos de negócio frequentemente envolvem **comunicação assíncrona** — uma atividade dispara um sinal que outra recebe (talvez em outro processo, em outro sistema, em outro momento).

| Nó | Forma | Uso |
| --- | --- | --- |
| **Enviar sinal** | retângulo com ponta convexa (▶) | Emite um sinal e prossegue. |
| **Receber sinal** | retângulo com entalhe côncavo (◀) | Aguarda receber um sinal antes de prosseguir. |

```
   enviarFatura ─▶ [▶ NotificacaoEmitida ] ──── (cliente recebe e age)
                                                       │
   ... outro processo ... ─▶ [◀ PagamentoRecebido] ──▶ baixarFatura
```

### 5.3 Regiões interruptíveis

Uma **região interruptível** é uma área tracejada do diagrama dentro da qual um evento (sinal/exceção) pode interromper a execução normal e desviar para outro fluxo. É a forma de modelar tratamento de exceções e cancelamentos.

```
   ╭──────────────────────────────────────╮
   │  preencherFormulario ─▶ enviar ─▶ ⊗  │
   ╰─────────────────────┬────────────────╯
                          │ (interrupção)
                          ▼
                    [◀ TimeoutSessao]
                          │
                          ▼
                       cancelarTudo
```

### 5.4 Pré e pós-condições locais

Cada ação pode ter **pré-condições** (`«precondition»`) e **pós-condições** (`«postcondition»`) anotadas, que descrevem o que deve ser verdade antes e depois da execução. São úteis em modelagem rigorosa, especialmente em domínios regulamentados (saúde, financeiro).

---

## 6. Boas práticas

1. **Comece pelo "caminho feliz".** Modele primeiro o fluxo principal sem exceções. Adicione decisões e regiões interruptíveis depois.
2. **Um diagrama, um propósito.** Não tente capturar todo o processo em um único diagrama. Decomponha em sub-atividades quando ele crescer demais.
3. **Use raias quando há mais de um ator.** Se o diagrama envolve apenas o sistema interno, raias são opcionais; se envolve pessoas e/ou sistemas externos, raias são quase obrigatórias.
4. **Nomeie ações com verbo no infinitivo.** `enviarConfirmacao`, não `confirmacao` ou `envia confirmação`. Consistência facilita leitura.
5. **Toda decisão precisa de guardas mutuamente exclusivas e exaustivas.** Inclua sempre `[else]` ou demonstre que as guardas explícitas cobrem todos os casos.
6. **Não confunda decisão com bifurcação.** Decisão (losango) é *ou-exclusivo*; bifurcação (barra) é *e-paralelo*. Errar isso gera ambiguidade séria.
7. **Casamento entre `fork` e `join` é boa higiene.** Embora a UML não exija, manter cada `fork` com seu `join` correspondente torna o diagrama drasticamente mais legível.

---

## 7. Exemplos resolvidos

### Exemplo Resolvido 1 — Atendimento em Cafeteria (Introdutório) ⭐

**Enunciado:** Modele em diagrama de atividades o atendimento de um pedido em uma cafeteria, com a seguinte dinâmica:

> O cliente faz o pedido no caixa. O atendente registra e cobra. Após o pagamento, duas tarefas acontecem em paralelo: o barista prepara a bebida, e o auxiliar prepara o acompanhamento (sanduíche, bolo etc.). Quando ambos terminam, o cliente é chamado para retirar.
> 

**Solução:**

Usaremos três raias: `Cliente`, `Atendente`, `Cozinha`. (Optei por unificar barista e auxiliar na raia "Cozinha" para simplificar, já que do ponto de vista externo eles são um time único.)

```
┌──────────────┬──────────────────┬──────────────────────┐
│   Cliente    │    Atendente     │      Cozinha         │
├──────────────┼──────────────────┼──────────────────────┤
│              │                  │                      │
│  ● ─fazer───▶│ registrarPedido  │                      │
│    pedido    │       │          │                      │
│              │       ▼          │                      │
│              │   cobrar         │                      │
│              │       │          │                      │
│  pagar  ◀────┼───────┘          │                      │
│    │         │                  │                      │
│    └────────▶│ confirmarPgto    │                      │
│              │       │          │                      │
│              │       └─────────▶│  ═══════ (fork)      │
│              │                  │      │     │         │
│              │                  │      ▼     ▼         │
│              │                  │  prepar.  prepar.    │
│              │                  │  bebida   acomp.     │
│              │                  │      │     │         │
│              │                  │      └──┬──┘         │
│              │                  │         ▼            │
│              │                  │   ═══════ (join)     │
│              │                  │         │            │
│              │   chamarCliente ◀┼─────────┘            │
│              │       │          │                      │
│  retirar  ◀──┼───────┘          │                      │
│  pedido      │                  │                      │
│    │         │                  │                      │
│    ▼         │                  │                      │
│    ◉         │                  │                      │
└──────────────┴──────────────────┴──────────────────────┘
```

Comentários sobre as decisões:

- **Bifurcação após `confirmarPgto`**: as duas preparações são *genuinamente* paralelas — não há ordem natural entre elas, e fazê-las em série seria desperdício.
- **Junção antes de `chamarCliente`**: precisamos de **ambas** as preparações prontas antes de chamar; é o caso clássico de sincronização.
- **Atendente atravessa duas raias**: registrar e cobrar acontecem na raia do atendente; ele "serve de ponte" entre o cliente e a cozinha.
- **Mantive simples deliberadamente**: não modelei o caso de cliente desistir, pagamento recusado ou item indisponível. Isso seria material para uma versão expandida.

### Exemplo Resolvido 2 — Processo de Reembolso de Pedido (Intermediário) ⭐⭐

**Enunciado:** Continuando o sistema de e-commerce das aulas anteriores, modele em diagrama de atividades o processo completo de reembolso de um pedido, considerando:

> O cliente solicita reembolso pelo aplicativo. O sistema verifica automaticamente se o pedido está dentro do prazo de 30 dias. Se sim, gera-se uma etiqueta de devolução e o cliente envia o produto. Se a inspeção (feita pelo time de logística) aprovar, o reembolso é processado e o cliente é notificado. Se a inspeção reprovar (produto danificado pelo cliente, p. ex.), o gerente revisa manualmente; ele pode aprovar (reembolso parcial) ou negar. Em qualquer ponto antes do reembolso ser processado, o cliente pode cancelar a solicitação. Se o cliente não enviar o produto em 7 dias após receber a etiqueta, o pedido de reembolso é cancelado automaticamente.
> 

**Solução:**

Este cenário tem todos os elementos avançados: paralelismo, decisões aninhadas, evento de tempo, e interrupção. Vamos usar quatro raias: `Cliente`, `Sistema`, `Logística`, `Gerente`.

```
┌──────────────┬─────────────────────┬──────────────────┬──────────────────┐
│   Cliente    │      Sistema        │    Logística     │    Gerente       │
├──────────────┼─────────────────────┼──────────────────┼──────────────────┤
│              │                     │                  │                  │
│ ● ─solicitar▶│ verificarPrazo      │                  │                  │
│   reembolso  │       │             │                  │                  │
│              │       ▼             │                  │                  │
│              │       ◇             │                  │                  │
│              │      / \            │                  │                  │
│              │  [>30d]  [<=30d]    │                  │                  │
│              │   /         \       │                  │                  │
│              │  ▼           ▼      │                  │                  │
│              │ negar    gerarEtiq  │                  │                  │
│              │   │           │     │                  │                  │
│              │   ▼           ▼     │                  │                  │
│              │   ⊗     ╭────────────────────────────╮ │                  │
│              │         │ ⌛ after(7d) ─▶ cancelarAut │ │                  │
│              │         │      │                ⊗    │ │                  │
│              │         │      │ (interrupção)       │ │                  │
│              │         │  enviarProduto             │ │                  │
│ receberEtiq ◀┼─────────┤     │                      │ │                  │
│      │       │         │     ▼                      │ │                  │
│ enviar  ─────┼─────────┼────▶│ inspecionar          │ │                  │
│ produto      │         │     │     │                │ │                  │
│              │         │     │     ▼                │ │                  │
│              │         │     │     ◇                │ │                  │
│              │         │     │   /   \              │ │                  │
│              │         │     │ [ok] [reprov]        │ │                  │
│              │         │     │  /      \            │ │                  │
│              │         │     │  │       └───────────┼─┼─▶ revisarManual  │
│              │         │     │  │                   │ │       │          │
│              │         │     │  │                   │ │       ▼          │
│              │         │     │  │                   │ │       ◇          │
│              │         │     │  │                   │ │     /   \        │
│              │         │     │  │                   │ │ [aprov] [neg]    │
│              │         │     │  │                   │ │   │       │      │
│              │         │     │  │     ◇◀────────────┼─┼───┘       ▼      │
│              │         │     │  └────▶│             │ │           ⊗      │
│              │ proces. ◀────────────── │ (merge)    │ │                  │
│              │ reemb.  │     │                      │ │                  │
│              │   │     │     │                      │ │                  │
│              │   ▼     │     │                      │ │                  │
│ notificar  ◀─┼─ notif. │     │                      │ │                  │
│  recebida    │   │     │                            │ │                  │
│   │          │   ▼     ╰────────────────────────────╯ │                  │
│   ▼          │   ◉                                    │                  │
└──────────────┴─────────────────────┴──────────────────┴──────────────────┘
```

> Nota: a representação ASCII acima é necessariamente comprometida — em ferramentas como PlantUML ou Lucidchart, esse diagrama fica bem mais limpo. Aqui o objetivo é fixar a estrutura lógica.
> 

Decisões-chave:

- **Região interruptível** (área tracejada): engloba todo o trecho desde a geração da etiqueta até a inspeção. O evento de tempo `after(7d)` interrompe esse trecho se o cliente não enviar o produto, levando ao cancelamento automático.
- **Decisão dupla**: a primeira decisão filtra por prazo (regra automática); a segunda filtra por inspeção (regra física); a terceira (na raia do gerente) é uma decisão manual. Cada uma representa um *ponto de controle* diferente do processo.
- **Mesclagem antes de "processarReembolso"**: dois caminhos podem chegar ao processamento — aprovação automática pela logística *ou* aprovação manual pelo gerente. O nó de mesclagem deixa isso explícito.
- **Final de fluxo (`⊗`) versus final de atividade (`◉`)**: usei `⊗` para os caminhos de negação (o processo termina ali, mas em paralelo outros fluxos do sistema continuam normalmente) e `◉` apenas no caminho de sucesso, onde toda a atividade do reembolso se encerra.
- **Não usei `fork`/`join` neste exemplo**: este processo é majoritariamente sequencial. Forçar paralelismo onde ele não existe seria estilizar o diagrama em prejuízo da fidelidade.

> 💡 **Intuição:** Repare como o diagrama de atividades torna *visíveis* regras de negócio que ficariam escondidas em código: o prazo de 30 dias, o limite de 7 dias para envio, a possibilidade de revisão manual. Esse é exatamente o tipo de artefato que se discute com o cliente — não com o programador. Por isso ele é tão valorado em análise de requisitos.
> 

---

## Exercícios Práticos

### Exercício 1 — Empréstimo em Biblioteca ⭐

Modele em **diagrama de atividades** o processo de empréstimo de livros em uma biblioteca universitária:

> O aluno chega ao balcão com o livro escolhido. O atendente verifica no sistema se o aluno tem pendências (multas ou livros atrasados). Se houver pendência, o empréstimo é negado e o aluno é orientado a regularizar. Se não houver, o atendente verifica se o aluno já está com o limite de empréstimos simultâneos atingido — em caso positivo, também nega. Caso contrário, registra o empréstimo, define a data de devolução (15 dias para alunos de graduação, 30 para pós) e entrega o livro ao aluno.
> 

Sua solução deve:

(a) Usar pelo menos **duas raias** (sugestão: `Aluno` e `Sistema/Atendente`, ou três se preferir separar atendente humano de sistema).
(b) Usar pelo menos **duas decisões** com guardas explícitas e mutuamente exclusivas.
(c) Indicar claramente o(s) ponto(s) de **final de fluxo** (negações) e o **final de atividade** (sucesso).

### Exercício 2 — Processo de Onboarding de Funcionário ⭐⭐

Considere o seguinte processo:

> Um novo funcionário aceita a oferta. A partir desse aceite, três coisas acontecem em paralelo: (1) o RH prepara a documentação trabalhista; (2) o TI cria as contas (e-mail, sistemas internos, VPN); (3) o gestor direto monta o plano de integração dos primeiros 30 dias. Quando todas as três tarefas terminam, o funcionário é chamado para o primeiro dia. No primeiro dia, ele assina os documentos com o RH e recebe os equipamentos do TI — essas duas atividades também são paralelas. Em seguida, conhece o time e participa do treinamento inicial (sequenciais). Após o treinamento, o gestor dá *feedback* da primeira semana ao final do quinto dia útil. Se o funcionário não comparecer no primeiro dia em até 3 dias após a data combinada, o processo é cancelado e a vaga reaberta.
> 

Modele este processo atendendo aos seguintes requisitos:

(a) Use pelo menos **três raias** (`RH`, `TI`, `Gestor`, opcionalmente `Funcionário`).
(b) Use pelo menos **dois pares de `fork`/`join`**, um para a fase pré-primeiro-dia e outro para a fase do primeiro dia.
(c) Use pelo menos **um evento de tempo** (`after(...)`) e uma **região interruptível** para modelar o cancelamento por não comparecimento.
(d) Indique pelo menos **um nó de objeto** (ex.: `[ Documentação [pronta] ]`, `[ Acessos [provisionados] ]`) para deixar explícito o fluxo de informação entre ações.
(e) Justifique em prosa, ao final, **três decisões** de modelagem que você tomou — por exemplo: "Por que decidi colocar o funcionário como raia ou não?" "Por que escolhi `fork`/`join` aqui em vez de uma sequência?".

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Fowler, M. *UML Distilled: A Brief Guide to the Standard Object Modeling Language*. 3ª ed., Addison-Wesley, 2003. — Capítulo 11.
- Booch, G.; Rumbaugh, J.; Jacobson, I. *The Unified Modeling Language User Guide*. 2ª ed., Addison-Wesley, 2005. — Capítulo 20.
- Larman, C. *Applying UML and Patterns*. 3ª ed., Prentice Hall, 2004. — Capítulo 28.
- Bezerra, E. *Princípios de Análise e Projeto de Sistemas com UML*. 3ª ed., Elsevier, 2015. — Capítulo 8.
- White, S. A.; Miers, D. *BPMN Modeling and Reference Guide*. Future Strategies, 2008. (Para o aluno interessado em modelagem de processos de negócio em escala industrial — BPMN é uma alternativa especializada e amplamente adotada.)
- OMG. *OMG Unified Modeling Language Specification*, versão 2.5.1, 2017. — Cláusula 15.

---