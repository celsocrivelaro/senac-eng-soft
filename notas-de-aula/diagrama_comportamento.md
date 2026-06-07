# Diagrama de Comportamento

## 1. Estrutura versus Comportamento

Na aula anterior, vimos que o diagrama de classes responde à pergunta *"quais tipos de coisa existem no meu sistema e como elas se relacionam?"*. Essa é uma visão **estrutural**: estática, atemporal, sobre tipos.

Mas um sistema de software é, por essência, **dinâmico**. Objetos colaboram, trocam mensagens, mudam de estado ao longo do tempo. Para capturar essa dinâmica, a UML oferece os chamados **diagramas comportamentais**, dos quais os dois mais usados na prática são:

- **Diagrama de Sequência**: foco em *colaborações* — quem envia o que para quem, em que ordem.
- **Diagrama de Estados**: foco em *ciclos de vida* — como um objeto individual evolui ao longo do tempo em resposta a eventos.

> 💡 **Intuição:** Pense em um filme. O diagrama de classes é o *elenco* (os personagens existentes); o diagrama de sequência é uma *cena específica* (quem fala com quem, em que ordem); o diagrama de estados é a *jornada interna* de um único personagem (como ele muda de humor ao longo da história). Os três são vistas complementares do mesmo sistema.
> 

---

## 2. Diagrama de Sequência

### 2.1 Elementos básicos

Um diagrama de sequência é desenhado em duas dimensões:

- **Eixo horizontal (X)**: representa os participantes da interação — atores externos e objetos do sistema. Cada participante recebe uma **linha de vida** (lifeline).
- **Eixo vertical (Y)**: representa o **tempo**, fluindo de cima para baixo. Não importa a distância em pixels — o que importa é a *ordem*.

```
   Ator        :Sistema      :BD
    │             │           │
    │             │           │      ← topo = início
    │             │           │
    ▼             ▼           ▼      ← base = fim
```

Cada participante é representado por um **retângulo** no topo (com nome no formato `nomeInstância:NomeClasse`, sendo o nome da instância opcional) e uma **linha tracejada vertical** descendo dele — a linha de vida.

### 2.2 Mensagens

Mensagens são **setas horizontais** entre linhas de vida, anotadas com o nome da operação e seus parâmetros.

| Tipo de mensagem | Notação | Significado |
| --- | --- | --- |
| **Síncrona** | `─────▶` (seta cheia) | Chamada bloqueante. O remetente aguarda o retorno. |
| **Assíncrona** | `─────▷` (seta aberta) | Não bloqueante. Remetente continua imediatamente. |
| **Retorno** | `- - - ▶` (seta tracejada) | Resposta de uma chamada síncrona. Opcional, mas recomendado quando o valor importa. |
| **Auto-mensagem** | seta que sai e volta no mesmo participante | Um objeto chama um método de si mesmo. |
| **Criação** | `<<create>>` apontando para o topo de uma nova lifeline | Cria-se um novo objeto durante a interação. |
| **Destruição** | `<<destroy>>` ou um `X` no fim da lifeline | O objeto é destruído. |

### 2.3 Barras de ativação (foco de controle)

Sobre cada linha de vida, desenhamos **retângulos finos verticais** para indicar os intervalos em que aquele objeto está *ativo* — isto é, executando um método. Em chamadas síncronas, a barra de ativação do receptor coincide com o tempo em que o remetente está bloqueado.

```
   :A             :B
    │             │
    │ activate    │
    ┃─────msg()──▶│ activate
    ┃             ┃
    ┃             ┃ (B executando)
    ┃◀- - retorno ┃ deactivate
    │ deactivate  │
```

### 2.4 Fragmentos combinados

Mensagens lineares só nos levam até certo ponto. Cenários reais têm condicionais, laços, paralelismo. Para isso, a UML 2.x introduziu os **fragmentos combinados** — molduras retangulares com um *operador* no canto superior esquerdo:

| Operador | Significado |
| --- | --- |
| `alt` | Alternativa (if/else). Cada compartimento tem uma guarda; só um executa. |
| `opt` | Opcional (if sem else). Executa se a guarda for verdadeira. |
| `loop` | Repetição. Pode ter guarda e/ou número mínimo/máximo de iterações. |
| `par` | Paralelo. Os compartimentos executam em qualquer ordem entrelaçada. |
| `ref` | Referência. Aponta para outro diagrama de sequência (reuso). |
| `break` | Quebra. Se a guarda for verdadeira, encerra o cenário envolvente. |
| `critical` | Seção crítica (não pode ser intercalada). |

Exemplo de `alt`:

```
┌─ alt ──────────────────────────┐
│ [saldo >= valor]               │
│   conta - - debitar(valor) -▶  │
│ - - - - - - - - - - - - - - -  │
│ [else]                         │
│   conta - - registrarFalha() ▶ │
└────────────────────────────────┘
```

> 💡 **Intuição:** Diagramas de sequência sem fragmentos combinados são quase sempre simplistas demais para a realidade. Mas o oposto também é verdade: diagramas com fragmentos aninhados em três níveis viram um quebra-cabeça. Se você precisa de mais que dois níveis, considere quebrar em vários diagramas com `ref`.
> 

---

## 3. Diagrama de Estados

### 3.1 Origem e propósito

O diagrama de estados da UML é fundamentado nos **statecharts de David Harel** (1987), uma generalização das máquinas de estado finitas clássicas. Ele descreve a vida de **um único objeto** (ou sistema) em termos dos *estados* que ele pode ocupar e dos *eventos* que disparam transições entre eles.

**Quando usar?** Modele com diagrama de estados qualquer objeto cujo *comportamento depende fortemente de seu histórico*. Exemplos canônicos: um pedido de e-commerce (`pendente → pago → enviado → entregue`), uma porta automática, um protocolo de rede, um caixa eletrônico, um documento em fluxo de aprovação.

### 3.2 Elementos básicos

| Elemento | Notação | Descrição |
| --- | --- | --- |
| **Estado** | retângulo com cantos arredondados, contendo o nome | Situação na vida do objeto. |
| **Estado inicial** | círculo preto sólido (`●`) | Pseudo-estado: ponto de partida. |
| **Estado final** | círculo preto com anel (`◉`) | Pseudo-estado: fim do ciclo de vida. |
| **Transição** | seta entre estados, rotulada `evento[guarda]/ação` | Mudança de estado. |

### 3.3 Anatomia da rotulação de transições

A sintaxe completa de uma transição é:

```
evento(parâmetros) [guarda] / ação
```

- **Evento** (*trigger*): o que dispara a transição. Pode ser um sinal, uma chamada de método, uma expiração de tempo (`after(5s)`), ou uma mudança de condição (`when(temp > 100)`).
- **Guarda**: condição booleana. A transição só dispara se ela for verdadeira.
- **Ação** (*effect*): o que acontece *durante* a transição. Tipicamente uma chamada de método ou emissão de sinal.

Exemplo: `pagar(valor) [valor >= total] / emitirNota()`

### 3.4 Atividades internas a um estado

Um estado pode ter **três tipos de atividade interna**:

```
┌─────────────────────────────┐
│       Aquecendo             │
│─────────────────────────────│
│ entry / ligarResistencia()  │  ← executa ao entrar no estado
│ do / monitorarTemperatura() │  ← executa enquanto está no estado
│ exit / desligarResistencia()│  ← executa ao sair do estado
└─────────────────────────────┘
```

A grande vantagem das atividades `entry`/`exit` é que elas são **garantidas a qualquer transição** que entre/saia do estado — independentemente da rota. Isso evita duplicação de código nas transições.

### 3.5 Estados compostos (aninhados)

A grande inovação dos statecharts de Harel sobre as FSMs clássicas é permitir **estados que contêm outros estados**. Isso resolve o problema da explosão combinatória.

```
┌── Conectado ──────────────────────┐
│                                   │
│   ●──▶ Ocioso ──msg─▶ Recebendo   │
│         ▲                │        │
│         └────fim─────────┘        │
│                                   │
└───────────────────────────────────┘
       │
       │ desconectar
       ▼
   Desconectado
```

Uma transição que sai do estado composto (`desconectar`) é disparada **independentemente do subestado interno em que se está**. Isso evita ter que desenhar setas saindo de cada subestado.

### 3.6 Regiões ortogonais (estados concorrentes)

Um estado composto pode ter **múltiplas regiões paralelas**, separadas por linha tracejada. O objeto está simultaneamente em um subestado de *cada* região:

```
┌── Em uso ───────────────────────────┐
│  Som:                               │
│  ●──▶ Mudo  ──tecla─▶ ComSom        │
│        ▲                │           │
│        └─tecla──────────┘           │
│ - - - - - - - - - - - - - - - - - - │
│  Tela:                              │
│  ●──▶ Clara  ──noite─▶ Escura       │
│         ▲                │          │
│         └─dia────────────┘          │
└─────────────────────────────────────┘
```

### 3.7 Pseudo-estado de história

O **pseudo-estado de história** (`H` em um círculo) "lembra" qual era o último subestado de um estado composto, permitindo retornar a ele quando o objeto reentrar no composto. Existe em duas variantes: **rasa** (`H`) — lembra apenas um nível — e **profunda** (`H*`) — lembra recursivamente. Útil para modelar pausas/retomadas (ex.: vídeo pausado, aplicação minimizada).

---

## 4. Sequência ou Estados? Como escolher

Estes diagramas **respondem perguntas diferentes** e raramente competem. A escolha decorre da pergunta que você quer responder:

| Pergunta | Diagrama |
| --- | --- |
| "Como vários objetos colaboram para realizar este caso de uso?" | Sequência |
| "Como o objeto X reage a eventos ao longo de sua vida?" | Estados |
| "Em que ordem essas chamadas acontecem?" | Sequência |
| "Que comportamentos são válidos no estado Y? E no estado Z?" | Estados |
| "Como modelar este protocolo de comunicação?" | Estados (e às vezes Sequência) |

Bons projetos costumam usar **ambos**: sequência para os principais cenários de caso de uso; estados para classes ricas em comportamento (tipicamente entidades de domínio com ciclo de vida explícito).

---

## 5. Boas práticas

1. **Não modele tudo.** Modele os cenários *interessantes*: o caminho feliz mais 1–2 alternativas relevantes. Diagramas exaustivos não são lidos.
2. **Mantenha coerência com o diagrama de classes.** Toda mensagem em um diagrama de sequência deve corresponder a um método existente na classe receptora. Toda transição em um diagrama de estados deve corresponder a um evento ou método público da classe.
3. **Nomeie eventos no domínio, não na implementação.** `pagamentoConfirmado` é melhor que `setStatus(2)`.
4. **Prefira `entry`/`exit` a duplicar ações em transições.** Reduz erros e ambiguidades.
5. **Modele os erros explicitamente em sequência.** Use `alt` para o caminho feliz e o de erro; *não esconda* falhas.
6. **Em estados, cada estado deve ter pelo menos uma transição de saída.** Estados sem saída (que não sejam o final) são quase sempre um erro de modelagem.

---

## 6. Exemplos resolvidos

### Exemplo Resolvido 1 — Autenticação de Usuário (Introdutório) ⭐

**Enunciado:** Modele em diagrama de sequência o cenário em que um usuário tenta fazer login em um sistema. O sistema valida as credenciais consultando o banco de dados. Inclua o caso de sucesso e o caso de senha inválida.

**Solução:**

Identificamos quatro participantes: `Usuario` (ator), `:TelaLogin`, `:ServicoAutenticacao`, `:RepositorioUsuario`.

```
  Usuario     :TelaLogin    :ServicoAutenticacao    :RepositorioUsuario
    │             │                  │                     │
    │─submit─────▶│                  │                     │
    │  (login,    │                  │                     │
    │   senha)    │                  │                     │
    │             │─autenticar──────▶│                     │
    │             │  (login, senha)  │                     │
    │             │                  │─buscarPorLogin─────▶│
    │             │                  │  (login)            │
    │             │                  │◀- - usuario - - - - │
    │             │                  │                     │
    │             │                  │ ┌── alt ─────────────────────────┐
    │             │                  │ │ [usuario.senhaConfere(senha)]  │
    │             │                  │ │   gerarToken()                 │
    │             │◀- - token - - - -│ │                                │
    │◀--mostrarHome│                 │ │- - - - - - - - - - - - - - - - │
    │             │                  │ │ [else]                         │
    │             │◀- erro: 401 - - -│ │                                │
    │◀-mostrarErro│                  │ │                                │
    │             │                  │ └────────────────────────────────┘
```

Comentários:

- A primeira mensagem (`submit`) é assíncrona do ponto de vista do usuário: ele preenche o formulário e clica.
- As demais são síncronas: o serviço aguarda o BD; a tela aguarda o serviço.
- O fragmento `alt` separa elegantemente os dois desfechos. Note que cada compartimento termina retornando algo *distinto* à tela (token vs. erro).
- A senha não é armazenada em texto plano — `senhaConfere` encapsula a comparação de hashes. Modelagem honesta sobre segurança importa, mesmo em um diagrama.

### Exemplo Resolvido 2 — Ciclo de Vida de um Pedido (Intermediário) ⭐⭐

**Enunciado:** Continuando o sistema de e-commerce visto na aula anterior, modele em diagrama de estados o ciclo de vida de um `Pedido`. Cada pedido começa pendente. Quando o cliente paga, ele passa a pago. O time logístico então o envia, e por fim ele é entregue. Em qualquer momento antes do envio, o cliente pode cancelar. Após o envio, o cliente só pode solicitar devolução, que abre uma nova subjornada de devolução.

**Solução:**

```
                   ┌──── Ativo ─────────────────────────────────────────┐
                   │                                                    │
                   │  ●──▶ Pendente ──pagar()───▶ Pago                  │
                   │           │                    │                   │
                   │           │                    │ enviar()          │
                   │           │                    │                   │
                   │           │                    ▼                   │
                   │           │                  Enviado               │
                   │           │                    │                   │
                   │           │                    │ confirmarEntrega()│
                   │           │                    ▼                   │
                   │           │                  Entregue              │
                   │           │                    │                   │
                   └───────────┼────────────────────┼───────────────────┘
                               │ cancelar()         │ solicitarDevolucao()
                               │ [estado != Enviado │
                               │  AND estado != Entregue]               │
                               ▼                    ▼
                          Cancelado     ┌── EmDevolucao ────────────┐
                              │         │                           │
                              ▼         │  ●─▶ AguardandoColeta     │
                              ◉         │         │                 │
                                        │         │ coletarItem()   │
                                        │         ▼                 │
                                        │     ItemRecebido          │
                                        │         │                 │
                                        │         │ aprovarReembolso│
                                        │         ▼                 │
                                        │     Reembolsado           │
                                        │         │                 │
                                        │         ▼                 │
                                        │         ◉                 │
                                        └───────────────────────────┘
```

Decisões-chave:

- **Estado composto `Ativo`**: agrupa `Pendente`, `Pago`, `Enviado`, `Entregue`. A transição `cancelar()` saindo do composto evita ter que desenhar uma seta a partir de cada subestado individual.
- **Guarda na transição de cancelamento**: `[estado != Enviado AND estado != Entregue]`. Isso é uma forma textual; a forma mais elegante em UML seria definir a transição `cancelar()` saindo apenas dos subestados `Pendente` e `Pago`. Ambas estratégias são aceitáveis — escolhi a primeira para ilustrar guardas explícitas.
- **`EmDevolucao` como estado composto** com seu próprio fluxo interno: traduz a observação de domínio de que devolução é uma "subjornada" com vida própria.
- **Atividades de `entry` (não desenhadas para não poluir):** poderíamos enriquecer cada estado com `entry / notificarCliente()` ou `entry / registrarLogTransicao()`.

> 💡 **Intuição:** Note como o diagrama de estados *complementa* — não substitui — o diagrama de classes da aula passada. Lá, `Pedido` tinha um atributo `status`. Aqui, descobrimos que esse atributo é, na verdade, o nome de um estado dentro de uma máquina rica. Modelagem é iterativa: cada nova vista pode forçar a revisar as anteriores.
> 

---

## Exercícios Práticos

### Exercício 1 — Caixa Eletrônico ⭐

Modele em **diagrama de sequência** o cenário de saque em um caixa eletrônico (ATM):

> O cliente insere o cartão. O ATM lê os dados e solicita a senha. O cliente digita a senha. O ATM consulta o sistema do banco para validar. Se válido, o cliente seleciona "saque" e digita o valor. O ATM verifica saldo no sistema do banco; se houver saldo, debita, dispensa o dinheiro e ejeta o cartão. Se a senha for inválida ou o saldo for insuficiente, o ATM exibe a mensagem apropriada e ejeta o cartão.
> 

Sua solução deve usar pelo menos um fragmento `alt` e identificar todos os participantes (atores e objetos do sistema). Inclua mensagens de retorno onde forem semanticamente importantes.

### Exercício 2 — Máquina de Café ⭐⭐

Modele em **diagrama de estados** o comportamento de uma máquina de café automática, atendendo aos seguintes requisitos:

> Em estado de repouso, a máquina mostra "selecione uma bebida". Ao selecionar (café, capuccino ou chá), a máquina pede pagamento. Após pagamento confirmado, ela aquece a água e prepara a bebida (essas duas etapas têm comportamentos próprios — aquecer leva até 10s, preparar varia conforme bebida). Após preparar, ela serve e volta ao repouso. Em qualquer momento durante o pagamento, o cliente pode cancelar e ser reembolsado. Se acabar a água ou faltar grãos durante a preparação, a máquina vai para um estado de manutenção, do qual só sai com intervenção do operador.
> 

Sua solução deve:

(a) Usar pelo menos um **estado composto** que agrupe estados afins.
(b) Usar atividades **`entry`**, **`exit`** e/ou **`do`** em pelo menos dois estados.
(c) Usar pelo menos uma **guarda** em uma transição.
(d) Modelar transições disparadas por **tempo** (`after(...)`) onde apropriado.
(e) Justificar em prosa, ao final, por que você modelou "manutenção" como estado regular ou como estado composto — e o que se ganharia/perderia na escolha oposta.

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Fowler, M. *UML Distilled: A Brief Guide to the Standard Object Modeling Language*. 3ª ed., Addison-Wesley, 2003. — Capítulos 4 (Sequência) e 10 (Estados).
- Booch, G.; Rumbaugh, J.; Jacobson, I. *The Unified Modeling Language User Guide*. 2ª ed., Addison-Wesley, 2005. — Partes IV e V.
- Harel, D. "Statecharts: A Visual Formalism for Complex Systems". *Science of Computer Programming*, vol. 8, n. 3, 1987. (Artigo seminal sobre statecharts; leitura recomendada para alunos avançados.)
- Larman, C. *Applying UML and Patterns*. 3ª ed., Prentice Hall, 2004. — Capítulos 15 e 29.
- Bezerra, E. *Princípios de Análise e Projeto de Sistemas com UML*. 3ª ed., Elsevier, 2015. — Capítulos 7 e 9.
- OMG. *OMG Unified Modeling Language Specification*, versão 2.5.1, 2017. — Cláusulas 17 e 14.

---