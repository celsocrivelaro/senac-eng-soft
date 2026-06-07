# Arquitetura em Camadas e MVC

## 1. Da classe ao sistema: por que arquitetura?

Até esta aula, nossa disciplina tratou de *como organizar classes*: que classes existem, como elas se relacionam, como objetos colaboram, que padrões aplicar. Esse é o domínio do **design**.

Mas um software real não é uma sopa de classes. Ele tem **partes maiores**: módulos, serviços, camadas, processos, bancos de dados, *frontends*, *backends*. As decisões sobre quais grandes partes existem, como elas se comunicam, onde rodam e quem pode falar com quem são decisões de **arquitetura**.

A definição clássica é de Bass, Clements e Kazman (*Software Architecture in Practice*, 2003):

> A arquitetura de um sistema computacional é a estrutura ou as estruturas do sistema, que compreendem elementos de software, as propriedades externamente visíveis desses elementos e as relações entre eles.
> 

Três palavras-chave nessa definição merecem atenção:

- **Estruturas** (no plural): um sistema tem várias estruturas simultâneas — não há uma única "a arquitetura".
- **Propriedades externamente visíveis**: arquitetura não se preocupa com detalhes internos de cada elemento, mas com como cada um se *comporta* no contexto dos outros.
- **Relações**: o que dá identidade arquitetural não são as caixas, mas as setas — quem chama quem, quem depende de quem.

> 💡 **Intuição:** Se design é "como faço esta classe?", arquitetura é "que peças existem no meu sistema, e como elas se encaixam?". A diferença é de *escala* e *consequência*: trocar uma classe é fácil; trocar uma camada inteira é traumático. Por isso decisões arquiteturais merecem mais cuidado, mais documentação, e mais discussão coletiva.
> 

### 1.1 Arquitetura é o que é difícil de mudar

Há uma definição alternativa, atribuída ao arquiteto Ralph Johnson:

> Arquitetura é aquilo que é difícil de mudar depois.
> 

Essa definição é menos precisa, mas profundamente útil. Quando você decide "vamos usar microsserviços" ou "tudo será CRUD com SQL", você toma decisões cuja reversão custa meses. Quando você decide "vou usar `ArrayList` em vez de `LinkedList`", você toma uma decisão que se reverte em um *commit*. A primeira é arquitetural; a segunda é de design (e nem sempre).

---

## 2. Visões: olhando a arquitetura por múltiplos ângulos

Como uma arquitetura é multifacetada — tem aspectos lógicos, processuais, físicos, evolutivos —, descrevê-la em uma única figura é insuficiente. Em 1995, Philippe Kruchten propôs o **Modelo de Visões 4+1**, que se tornou o padrão *de facto* para documentação arquitetural.

```
                     ┌──────────────────────┐
                     │  Visão Lógica        │
                     │  (Funcional)         │
                     │  ←  classes,         │
                     │     componentes      │
                     └──────────────────────┘
                                │
                                │
       ┌────────────────────────┼────────────────────────┐
       │                        │                        │
┌──────────────┐        ┌───────────────┐         ┌─────────────────┐
│ Visão de     │ ◀──┬──▶│ Visão de      │ ◀──┬───▶│ Visão Física    │
│ Processos    │    │   │ Cenários (+1) │    │    │ (Implantação)   │
│              │    │   │ casos de uso  │    │    │ servidores,     │
│  threads,    │    │   │ guiando tudo  │    │    │ rede, infra     │
│ sincronização│    │   │               │    │    │                 │
└──────────────┘    │   └───────────────┘    │    └─────────────────┘
                    │                        │
                    └────────────────────────┘
                                │
                     ┌──────────────────────┐
                     │ Visão de             │
                     │ Desenvolvimento      │
                     │ (módulos, pacotes,   │
                     │  estrutura de       │
                     │  diretórios)        │
                     └──────────────────────┘
```

| Visão | O que mostra | Quem se importa |
| --- | --- | --- |
| **Lógica** | Funcionalidade do sistema. Classes, componentes, interfaces. | Analistas, desenvolvedores |
| **De Processos** | Concorrência, threads, sincronização, processos do SO. | Engenheiros de performance, arquitetos de sistema |
| **De Desenvolvimento** | Como o código se organiza em pacotes, módulos, repositórios. | Times de desenvolvimento |
| **Física (de Implantação)** | Onde cada componente roda — servidores, redes, *cloud*. | DevOps, infraestrutura, segurança |
| **De Cenários (+1)** | Casos de uso que validam e ilustram as outras quatro. | Todos |

A visão "+1" — Cenários — é especial: ela não é uma quinta visão paralela, é o **fio condutor** que conecta as outras quatro. Cada caso de uso importante deve ser rastreável através das quatro visões: quais classes participam (lógica), em quais processos rodam (processos), em quais servidores estão (física), e onde está o código (desenvolvimento).

> 💡 **Intuição:** Pense em um prédio. A planta baixa mostra os cômodos (visão lógica). O esquema elétrico mostra a corrente (processos). O lote no terreno mostra a localização (física). A organização do canteiro de obras mostra como construir (desenvolvimento). Os cenários são as histórias do dia a dia — "morador chega em casa cansado" — que validam que tudo isso faz sentido junto.
> 

---

## 3. Estilos arquiteturais vs padrões de projeto

Antes de mergulharmos nos estilos específicos, vale uma comparação direta:

| Aspecto | Padrão de Projeto (GoF) | Estilo Arquitetural |
| --- | --- | --- |
| **Escala** | Algumas classes | Sistema inteiro |
| **Granularidade dos elementos** | Objetos, classes, métodos | Componentes, módulos, serviços |
| **Custo de aplicação errada** | Refatoração local | Reescrita massiva |
| **Quem decide?** | Desenvolvedor | Arquiteto, com input de toda a equipe |
| **Quando se aplica?** | Durante codificação | No início do projeto (e revisitado) |
| **Exemplos** | Strategy, Observer, Command | Camadas, MVC, microsserviços, *event-driven* |

Apesar das diferenças, o espírito é o mesmo: **soluções comprovadas para problemas recorrentes, com vocabulário compartilhado**. Tudo o que vimos sobre padrões — não os aplique cegamente, refatore para eles, evite *patternitis* — vale também para estilos arquiteturais. *Patternitis arquitetural* (microsserviços para aplicações de cinco usuários, por exemplo) é um anti-padrão real e comum.

---

## 4. Arquitetura em Camadas (*Layered Architecture*)

### 4.1 Conceito

O estilo em camadas organiza o sistema em **grupos horizontais de responsabilidades afins**, onde cada camada usa apenas a camada imediatamente abaixo dela. É o estilo arquitetural mais antigo, mais ensinado e — provavelmente — o mais aplicado em sistemas corporativos.

A versão clássica tem **três camadas**:

```
   ┌────────────────────────────────────────────────┐
   │       Apresentação (Presentation Layer)        │  ← UI, controllers, telas
   │  páginas web, telas, JSON da API REST, CLI     │
   └────────────────────────────────────────────────┘
                          │ chama
                          ▼
   ┌────────────────────────────────────────────────┐
   │       Negócio (Business / Domain Layer)        │  ← regras, fluxos
   │  serviços, regras, validações, orquestração    │
   └────────────────────────────────────────────────┘
                          │ chama
                          ▼
   ┌────────────────────────────────────────────────┐
   │       Persistência (Data / Persistence)        │  ← acesso a dados
   │  repositórios, mapeamento O/R, SQL, queries    │
   └────────────────────────────────────────────────┘
                          │ usa
                          ▼
   ┌────────────────────────────────────────────────┐
   │           Banco de Dados / Sistemas Externos   │
   └────────────────────────────────────────────────┘
```

### 4.2 As regras de ouro

O estilo em camadas só funciona se algumas regras forem rigorosamente respeitadas:

1. **Camadas só dependem da camada imediatamente abaixo** (versão estrita) **ou de qualquer camada abaixo** (versão relaxada). **Nunca** uma camada depende de uma camada acima.
2. **A apresentação não acessa diretamente o banco de dados**. Se ela faz `SELECT * FROM ...`, sua arquitetura não é mais em camadas — é uma ilusão.
3. **A camada de negócio não conhece HTTP, JSON, SQL ou frameworks de UI**. Ela conhece *regras de negócio*. Pode-se trocar a UI inteira sem tocar nela.
4. **A camada de persistência não conhece regras de negócio**. Ela só sabe ler e gravar dados.

> 💡 **Intuição:** Imagine que cada camada é um andar de um prédio, e cada uma só pode chamar quem está no andar de baixo (talvez via interfone). Se um andar começar a "passar pela janela" para falar com qualquer outro, o prédio vira uma anarquia. As regras existem para permitir que cada andar evolua independentemente — você reforma o segundo andar sem mexer no primeiro nem no terceiro.
> 

### 4.3 O e-commerce em camadas

Aplicando ao nosso domínio recorrente:

```
┌──────────────────────────────────────────────────────────────┐
│ APRESENTAÇÃO                                                 │
│  • CheckoutController (REST)                                 │
│  • CarrinhoView (web)                                        │
│  • PainelAdminController                                     │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│ NEGÓCIO                                                      │
│  • CheckoutFacade                  ← Facade (aula passada)   │
│  • CalculadoraFrete + Strategies   ← Strategy                │
│  • ProcessadorPedido (Template Method)                       │
│  • Pedido + EstadoPedido           ← State                   │
│  • Observer chain (e-mail, BI, logística)                    │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│ PERSISTÊNCIA                                                 │
│  • PedidoRepository                                          │
│  • ProdutoRepository                                         │
│  • ClienteRepository                                         │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
                       ┌──────────────┐
                       │  PostgreSQL  │
                       └──────────────┘
```

Note como **todos os padrões vistos nas aulas anteriores** se encaixam predominantemente na camada de negócio. Isso não é coincidência: padrões GoF foram concebidos para o nível de *design de classes*, e a camada de negócio é exatamente onde a riqueza de classes vive em sistemas corporativos. Apresentação tende a ser mais "burra" (recebe requisição, repassa); persistência tende a ser CRUD; o miolo de complexidade fica no negócio.

### 4.4 Variantes: 4 camadas, *Hexagonal*, *Clean Architecture*

A divisão em três camadas é o ponto de partida; sistemas reais costumam ter quatro ou mais:

- **4 camadas comum**: Apresentação · **Aplicação** · Domínio · Persistência. A camada de **Aplicação** orquestra fluxos (são as fachadas, basicamente), enquanto a de **Domínio** contém apenas as entidades puras de negócio com suas regras invariantes. Essa separação é central no *Domain-Driven Design* (Eric Evans, 2003).
- **Arquitetura Hexagonal** (Alistair Cockburn, 2005): refina a ideia centralizando o domínio e tratando *qualquer coisa externa* (UI, BD, web service) como um "adaptador" plugado em uma "porta". Particularmente boa para testabilidade.
- **Clean Architecture** (Robert Martin, 2017): generalização da hexagonal, com regras explícitas de dependência (sempre apontando para o centro, onde estão as regras de negócio mais estáveis).

Esses três refinamentos compartilham um princípio:

> **A regra de dependência aponta para o domínio.**
> 

Ou seja: o domínio (núcleo do que o sistema *faz*) é estável; tudo o que muda — frameworks, bibliotecas, banco de dados, UI — depende dele, não o contrário. Esse princípio justifica por que trocar de Oracle para PostgreSQL ou de React para Vue **não deveria** quebrar regras de negócio.

### 4.5 Vazamentos comuns ("o que NÃO fazer")

Quase todos os projetos corporativos que "estão em camadas" têm vazamentos. Sinais clássicos:

- **Persistência conhecendo negócio**: o repositório `PedidoRepository` calcula totais, valida regras. Não. O repositório só lê e grava.
- **Apresentação fazendo SQL**: o *controller* faz `entityManager.createQuery(...)`. Não. Ele chama um serviço.
- **Negócio importando classes da UI**: serviço que recebe `HttpServletRequest`. Não. O serviço deveria receber objetos de domínio puros.
- **Camada inferior chamando superior**: persistência disparando notificações UI. Não. Use eventos ou *callbacks* injetados.

A regra mental é simples: **se eu trocar tudo de uma camada por uma implementação radicalmente diferente, as outras camadas precisariam mudar?** Se sim, há vazamento.

### 4.6 Consequências

**Benefícios:**

- **Substituibilidade**: trocar UI, BD ou framework afeta apenas a camada relevante.
- **Times paralelos**: três times podem trabalhar em três camadas com pouca interferência.
- **Testabilidade**: a camada de negócio pode ser testada sem UI nem BD, com mocks nas pontas.
- **Compreensão progressiva**: quem chega no projeto pode estudar uma camada por vez.

**Custos:**

- **Sobre-engenharia para sistemas pequenos**: aplicações *CRUD* triviais ficam três vezes mais verbosas.
- **Performance**: cada chamada atravessa três camadas; em sistemas de altíssima vazão, isso pode importar (raro, mas existe).
- **"Síndrome do anêmico"**: a camada de domínio fica vazia (só *getters*/*setters*), e a lógica vaza para a camada de aplicação. Discussão clássica em DDD.

---

## 5. Model-View-Controller (MVC)

### 5.1 História e intenção

MVC é o **padrão arquitetural mais influente da história da computação**. Foi inventado em 1979 por Trygve Reenskaug, no laboratório Xerox PARC, para a linguagem Smalltalk-80. Seu objetivo original era separar dados, apresentação e interação em aplicações com interface gráfica — um problema novo na época, hoje universal.

A intenção (em uma frase):

> Separar a representação dos dados (Model), a apresentação para o usuário (View) e a lógica de interação (Controller), de modo que cada parte possa evoluir independentemente.
> 

### 5.2 Os três participantes

```
                ┌──────────────────────┐
                │       Usuário        │
                └──────────────────────┘
                  ▲                  │
                  │ percebe          │ interage
                  │                  ▼
        ┌────────────────┐    ┌────────────────────┐
        │     View       │    │   Controller       │
        │ (apresentação) │    │ (lógica de         │
        │                │    │  interação)        │
        └────────────────┘    └────────────────────┘
                  ▲                  │
                  │ observa          │ atualiza
                  │                  ▼
                ┌──────────────────────┐
                │       Model          │
                │ (dados + regras)     │
                └──────────────────────┘
```

- **Model**: estado da aplicação e regras de negócio. Ignora completamente como será apresentado. É o "cérebro".
- **View**: apresentação ao usuário. Lê o estado do Model, mas não o modifica. É a "cara".
- **Controller**: recebe a entrada do usuário e transforma em comandos para o Model (ou troca de View). É o "intermediário".

### 5.3 O fluxo MVC clássico

A coreografia em uma interação típica:

1. Usuário clica em "Salvar Pedido" na tela.
2. **Controller** recebe o evento e extrai os dados do formulário.
3. Controller chama o **Model** apropriado (`pedidoService.salvar(pedido)`).
4. Model atualiza seu estado interno e dispara notificação (via Observer — lembra?).
5. **View** recebe a notificação e se redesenha.

Note como o padrão **Observer** está embutido na conexão Model→View. MVC original assume essa relação; em variantes modernas, ela varia.

### 5.4 MVC no e-commerce — exemplo concreto

Imagine o painel administrativo onde o operador edita o status de um pedido:

**Model** — `Pedido` e seus serviços (camada de negócio):

```java
public class Pedido {
    // ... usa o padrão State como vimos ...
    private final List<Observer> observers = new ArrayList<>();

    public void marcarComoEnviado() {
        estado.enviar(this);
        notificarObservers();
    }
    // ...
}
```

**View** — *template* HTML (simplificado, com pseudo-código):

```html
<div class="pedido-painel">
    <h1>Pedido #{{ pedido.id }}</h1>
    <p>Status: {{ pedido.estado }}</p>
    <p>Total: R$ {{ pedido.total }}</p>
    <button onclick="enviar()">Marcar como Enviado</button>
</div>
```

**Controller** — recebe a requisição:

```java
@Controller
public class PedidoController {

    private final PedidoService pedidoService;

    @PostMapping("/pedidos/{id}/enviar")
    public String enviar(@PathVariable Long id, Model viewModel) {
        Pedido p = pedidoService.buscarPorId(id);
        p.marcarComoEnviado();           // chama o Model
        viewModel.addAttribute("pedido", p);
        return "pedido-detalhe";          // View a renderizar
    }
}
```

> 💡 **Intuição:** O *controller* é o regente da orquestra: ele não toca os instrumentos (não tem regra de negócio), nem produz o som (não desenha tela), mas coordena quem faz o quê em resposta à entrada do maestro (usuário).
> 

### 5.5 MVC e Camadas — encaixam ou competem?

Pergunta frequente: *"MVC é uma alternativa às camadas, ou se combina com elas?"* A resposta é **se combina**. MVC é tipicamente aplicado **dentro da camada de Apresentação** (e parte da de Aplicação). Em uma aplicação web em camadas:

- **View + Controller** ficam na camada de Apresentação.
- **Model** se mistura com as camadas de Negócio e Persistência (ou pode ser uma representação superficial delas, em variantes mais sofisticadas).

Pensar em MVC como um *zoom dentro da camada de apresentação* costuma esclarecer.

### 5.6 MVP (Model-View-Presenter)

A primeira variante surgiu nos anos 1990 para resolver um problema: em MVC clássico, a View frequentemente acessa o Model diretamente (para se atualizar). Isso a torna "esperta demais" e difícil de testar.

**MVP** muda a topologia:

```
   ┌─────────────────┐       ┌─────────────────┐
   │      View       │ ◀────▶│    Presenter    │
   │  (passive)      │       │ (lógica + estado│
   │ só renderiza    │       │  da tela)       │
   └─────────────────┘       └─────────────────┘
                                      │
                                      ▼
                              ┌─────────────────┐
                              │     Model       │
                              └─────────────────┘
```

Diferenças-chave:

- **A View não acessa o Model** — ela só conversa com o Presenter.
- **A View é "passiva"**: expõe apenas métodos como `mostrarPedido(...)` e `exibirErro(...)`. Toda decisão fica no Presenter.
- **O Presenter pode ser testado sem UI**: como ele só fala com a interface da View (que pode ser mockada), testes unitários ficam triviais.

MVP foi popularíssimo em frameworks Java desktop (Swing) e Android (até a Google introduzir o ViewModel oficial).

### 5.7 MVVM (Model-View-ViewModel)

Variante mais recente, popularizada pelo WPF da Microsoft em 2005 e hoje dominante em frameworks reativos modernos (Vue, Angular, SwiftUI):

```
   ┌─────────────────┐  data binding  ┌──────────────────┐
   │      View       │ ◀─────────────▶│   ViewModel      │
   │ (declarativa)   │   (automático) │ (estado da tela  │
   │                 │                │  + comandos)     │
   └─────────────────┘                └──────────────────┘
                                              │
                                              ▼
                                      ┌─────────────────┐
                                      │     Model       │
                                      └─────────────────┘
```

A inovação é o **data binding bidirecional automático**: você declara que o campo `<input value="{{ pedido.total }}">` está ligado à propriedade `pedido.total` do ViewModel, e o framework cuida de manter os dois em sincronia. Mudou o ViewModel? A View atualiza. Usuário digitou na View? O ViewModel atualiza.

Vantagem: drasticamente menos "código de cola" entre View e lógica. Desvantagem: maior dependência de framework (a "mágica" do data binding não é trivial de implementar do zero).

### 5.8 MVC vs MVP vs MVVM — quando cada um?

| Variante | Boa para... | Limitação |
| --- | --- | --- |
| **MVC** | Aplicações web tradicionais (request-response, *server-rendered*) | Acoplamento View↔Model dificulta testes |
| **MVP** | Aplicações com UI rica e necessidade alta de testes (Android pré-Jetpack, Swing) | Boilerplate: muitos métodos na interface da View |
| **MVVM** | SPAs modernas, mobile reativo, frameworks com data binding | Depende de framework com binding nativo |

> 💡 **Intuição:** As três variantes são respostas históricas a *quem deve saber o quê*. MVC original confiou demais na View (que conversava com o Model); MVP "burrou" a View (e cresceu o Presenter); MVVM automatizou a comunicação (sob custo de framework). Não há uma "melhor" — há a adequada ao contexto, à equipe, e ao framework escolhido.
> 

---

## 6. Outros estilos arquiteturais (visão sumária)

| Estilo | Ideia central | Quando você o veria |
| --- | --- | --- |
| **Cliente-Servidor** | Servidor centraliza recursos; clientes solicitam. | Praticamente toda aplicação web. |
| **Pipe-and-Filter** | Componentes processam dados em sequência, conectados por *pipes*. | Compiladores, *data pipelines*, shell Unix. |
| **Event-Driven** (orientada a eventos) | Componentes se comunicam emitindo e ouvindo eventos, sem se conhecerem diretamente. | Sistemas reativos, jogos, GUIs, Kafka, microsserviços modernos. |
| **Microsserviços** | Sistema decomposto em serviços independentes, cada um com banco próprio, comunicando via rede. | Sistemas de larga escala (Netflix, Amazon). |
| **Monólito Modular** | Um único *deployable*, mas internamente decomposto em módulos coesos com fronteiras claras. | Reação madura ao excesso de microsserviços. |
| **Serverless / FaaS** | Funções pequenas executadas sob demanda em infraestrutura gerenciada. | APIs leves, pipelines de eventos, *backends* mobile. |
| **Peer-to-Peer (P2P)** | Nós equivalentes em capacidade, sem servidor central. | BitTorrent, blockchain, sistemas distribuídos descentralizados. |

Aprofundar cada um destes seria material para uma disciplina inteira (e em geral é). Aqui o objetivo é vocabulário: ao ler em uma vaga "experiência com event-driven", você sabe do que se trata.

### 6.1 Uma palavra sobre microsserviços

Microsserviços viraram *moda* nos anos 2010, e hoje há reconhecimento crescente de que **foram aplicados em excesso**. Para um sistema com 10 desenvolvedores e 1.000 usuários, microsserviços costumam introduzir mais problemas (latência de rede, complexidade operacional, transações distribuídas) do que resolvem. A regra prática contemporânea é:

> Comece com um **monólito modular bem-feito**. Migre para microsserviços apenas quando uma dor concreta justificar — escalabilidade independente, times grandes que se atrapalham, ou tempo de *build* inviável.
> 

Esta é uma aplicação direta da máxima de Kent Beck adaptada à arquitetura: *"refatore para o estilo, não comece com ele"*.

---

## 7. Atributos de qualidade — o que arquitetura realmente decide

A literatura de arquitetura usa o termo **atributos de qualidade** (também chamados *requisitos não-funcionais*) para descrever as propriedades que arquitetura mais diretamente afeta. Os principais:

| Atributo | Pergunta que faz | Decisões arquiteturais que afeta |
| --- | --- | --- |
| **Performance** | Quão rápido responde? | Cache, escolha de BD, *async*, granularidade de serviços |
| **Escalabilidade** | Aguenta crescer 10x? 100x? | Stateless, *load balancing*, particionamento de dados |
| **Disponibilidade** | Que % do tempo está no ar? | Redundância, *failover*, monitoramento |
| **Segurança** | Resiste a ameaças? | Autenticação, criptografia, separação de redes |
| **Manutenibilidade** | Quão fácil é evoluir? | Camadas, modularidade, testes, documentação |
| **Testabilidade** | Quão fácil é validar? | Injeção de dependência, fronteiras claras, *mockability* |
| **Modificabilidade** | Mudar requisitos custa caro? | Acoplamento fraco, padrões aplicados |
| **Usabilidade** | Usuários conseguem usar? | Arquitetura da UI, latência percebida |

A grande lição: **não existe arquitetura "boa" em abstrato**. Existe arquitetura *adequada aos atributos prioritários do sistema*. Para um sistema bancário, segurança e disponibilidade dominam. Para um *startup* de protótipo, manutenibilidade e velocidade de mudança dominam. Para um jogo online, performance e escalabilidade dominam.

A pergunta arquitetural fundamental, portanto, não é "qual estilo usar?", mas sim:

> *Quais atributos de qualidade são prioritários no meu sistema, e quais decisões arquiteturais os otimizam (mesmo que sacrifiquem outros)?*
> 

> 💡 **Intuição:** Atributos de qualidade conflitam entre si. Aumentar segurança costuma sacrificar performance (criptografia custa). Aumentar escalabilidade horizontal costuma sacrificar consistência forte (teorema CAP). Toda escolha arquitetural é, na essência, uma escolha de *trade-off*. Arquitetar é navegar trade-offs conscientemente.
> 

---

## 8. Boas práticas

1. **Documente decisões arquiteturais** com **ADRs** (*Architecture Decision Records*): textos curtos descrevendo *contexto, decisão, alternativas consideradas e consequências*. Quando alguém perguntar daqui a três anos "por que não usamos X?", o ADR responde.
2. **Comece simples**. Camadas + monólito modular é a opção padrão sensata para a maioria dos sistemas. Adicione complexidade apenas quando dor concreta justificar.
3. **Teste a regra de dependência**. Ferramentas como ArchUnit (Java), Dependency-cruiser (JavaScript) ou pytest-arch (Python) verificam automaticamente que sua arquitetura em camadas é respeitada — não confie em disciplina humana sozinha.
4. **Escolha um estilo por contexto, não por moda**. *Microsserviços* vendem em LinkedIn; *monólito modular* costuma vender bem em produção.
5. **Conheça as alternativas que você rejeitou**. Em entrevistas e ADRs, a pergunta crítica não é "qual escolheu?", é "**por que essa, e não as outras?**".

---

## 9. Exemplos resolvidos

### Exemplo Resolvido 1 — Refatorando código sem camadas para um sistema em camadas (Introdutório) ⭐

**Enunciado:** O código abaixo é o *handler* de um endpoint de checkout em um e-commerce. Identifique todas as violações arquiteturais e proponha uma refatoração para uma arquitetura em três camadas.

**Código original (anti-padrão):**

```java
@PostMapping("/checkout")
public String checkout(HttpServletRequest req, HttpServletResponse resp) throws Exception {
    Long pedidoId = Long.parseLong(req.getParameter("pedidoId"));

    // Acesso direto ao BD
    Connection conn = DriverManager.getConnection("jdbc:postgresql:...");
    PreparedStatement ps = conn.prepareStatement(
        "SELECT total, cliente_id FROM pedidos WHERE id = ?");
    ps.setLong(1, pedidoId);
    ResultSet rs = ps.executeQuery();
    rs.next();
    double total = rs.getDouble("total");
    Long clienteId = rs.getLong("cliente_id");

    // Regra de negócio misturada com SQL
    if (total > 1000) {
        total = total * 0.95;
    }

    // Outra query
    ps = conn.prepareStatement("UPDATE pedidos SET total = ?, status = 'PAGO' WHERE id = ?");
    ps.setDouble(1, total);
    ps.setLong(2, pedidoId);
    ps.executeUpdate();

    // Chamada HTTP a serviço externo
    URL url = new URL("https://gateway.com/cobrar?valor=" + total + "&cliente=" + clienteId);
    HttpURLConnection con = (HttpURLConnection) url.openConnection();
    con.getResponseCode();

    // Resposta direta no HTTP
    resp.getWriter().println("<html><body>Pago: R$ " + total + "</body></html>");
    return null;
}
```

**Solução — Análise:**

Violações encontradas:

1. **Apresentação faz SQL direto** (`PreparedStatement`).
2. **Apresentação calcula desconto** (regra de negócio).
3. **Apresentação chama serviço externo de pagamento** (responsabilidade de negócio).
4. **Apresentação gera HTML "no braço"** (deveria ser uma View).
5. **Sem nenhuma abstração**: testar esse código exige um BD real, um gateway real e um servlet container.

**Refatoração para três camadas:**

```java
// ============ APRESENTAÇÃO ============

@RestController
public class CheckoutController {

    private final CheckoutService checkoutService;

    public CheckoutController(CheckoutService s) {
        this.checkoutService = s;
    }

    @PostMapping("/checkout")
    public ResponseEntity<PedidoDTO> checkout(@RequestBody CheckoutRequest req) {
        Pedido p = checkoutService.processar(req.getPedidoId());
        return ResponseEntity.ok(PedidoDTO.from(p));
    }
}

// ============ NEGÓCIO ============

@Service
public class CheckoutService {

    private final PedidoRepository repo;
    private final GatewayPagamento gateway;

    public CheckoutService(PedidoRepository repo, GatewayPagamento gw) {
        this.repo = repo;
        this.gateway = gw;
    }

    public Pedido processar(Long pedidoId) {
        Pedido p = repo.buscarPorId(pedidoId);

        // Regra de negócio centralizada
        if (p.getTotal() > 1000) {
            p.aplicarDescontoPercentual(0.05);
        }

        gateway.cobrar(p.getCliente(), p.getTotal());
        p.marcarComoPago();              // padrão State

        repo.salvar(p);
        return p;
    }
}

// ============ PERSISTÊNCIA ============

@Repository
public class PedidoRepository {

    private final EntityManager em;

    public Pedido buscarPorId(Long id) {
        return em.find(Pedido.class, id);
    }

    public void salvar(Pedido p) {
        em.merge(p);
    }
}
```

**Lições da refatoração:**

- **Cada camada agora tem uma responsabilidade clara**, expressável em uma frase: apresentação traduz HTTP↔Java; negócio aplica regras; persistência lê/grava.
- **Testabilidade aumentou drasticamente**. `CheckoutService` agora é testável com mocks de `PedidoRepository` e `GatewayPagamento` — sem BD, sem rede, sem servlet.
- **Substituibilidade**: queremos trocar PostgreSQL por MongoDB? Mudamos só o `PedidoRepository`. Trocar REST por GraphQL? Adicionamos um novo *controller* — o serviço continua igual.
- **Aderência a padrões anteriores**: `marcarComoPago()` usa o State; `GatewayPagamento` provavelmente envolve um Adapter para algum SDK; observers seriam disparados na transição. Padrões de design e arquitetura **se compõem naturalmente**.

### Exemplo Resolvido 2 — Aplicando MVC + comparando com MVVM no painel admin (Intermediário) ⭐⭐

**Enunciado:** Projete a tela de "edição de pedido" do painel administrativo do e-commerce em duas variantes: **MVC clássico** (aplicação web *server-side rendered*, com Spring) e **MVVM** (SPA com Vue.js consumindo API REST). Discuta os trade-offs.

**Solução — Variante 1: MVC clássico**

**Model** (camada de negócio, já visto):

```java
public class Pedido { /* ... usa State, observers, etc. ... */ }

@Service
public class PedidoService {
    public Pedido buscarPorId(Long id) { /* ... */ }
    public void salvarAlteracoes(Pedido p) { /* ... */ }
}
```

**Controller**:

```java
@Controller
@RequestMapping("/admin/pedidos")
public class PedidoAdminController {

    private final PedidoService pedidoService;

    @GetMapping("/{id}/editar")
    public String editar(@PathVariable Long id, Model viewModel) {
        Pedido p = pedidoService.buscarPorId(id);
        viewModel.addAttribute("pedido", p);
        return "admin/pedido-editar";  // nome do template
    }

    @PostMapping("/{id}/salvar")
    public String salvar(@PathVariable Long id,
                         @ModelAttribute PedidoForm form) {
        Pedido p = pedidoService.buscarPorId(id);
        p.atualizarA partirDe(form);
        pedidoService.salvarAlteracoes(p);
        return "redirect:/admin/pedidos/" + id;
    }
}
```

**View** (template Thymeleaf, simplificado):

```html
<form action="/admin/pedidos/{{ pedido.id }}/salvar" method="post">
    <label>Status: {{ pedido.estado }}</label>
    <input name="enderecoEntrega" value="{{ pedido.enderecoEntrega }}" />
    <button type="submit">Salvar</button>
</form>
```

**Solução — Variante 2: MVVM (Vue.js + API REST)**

**Backend** (mesma camada de negócio, agora exposta via API REST):

```java
@RestController
@RequestMapping("/api/pedidos")
public class PedidoApiController {

    @GetMapping("/{id}")
    public PedidoDTO obter(@PathVariable Long id) { /* ... */ }

    @PutMapping("/{id}")
    public PedidoDTO atualizar(@PathVariable Long id,
                                @RequestBody PedidoDTO dto) { /* ... */ }
}
```

**ViewModel + View** (Vue.js, simplificado):

```
<template>
  <div>
    <h1>Pedido #{{ pedido.id }}</h1>
    <label>Status: {{ pedido.estado }}</label>
    <input v-model="pedido.enderecoEntrega" />
    <button @click="salvar">Salvar</button>
  </div>
</template>

<script>
export default {
  data() {
    return { pedido: {} };  // ViewModel: estado da tela
  },
  async mounted() {
    this.pedido = await fetch(`/api/pedidos/${this.$route.params.id}`)
                          .then(r => r.json());
  },
  methods: {
    async salvar() {
      await fetch(`/api/pedidos/${this.pedido.id}`, {
        method: 'PUT',
        body: JSON.stringify(this.pedido)
      });
    }
  }
};
</script>
```

**Discussão dos trade-offs:**

| Aspecto | MVC clássico | MVVM (SPA) |
| --- | --- | --- |
| **Tempo até primeiro render** | Rápido (HTML pronto vem do servidor) | Lento (precisa baixar JS, depois fazer fetch) |
| **Interatividade rica** | Limitada (cada ação = nova requisição) | Excelente (atualização local imediata) |
| **SEO** | Excelente (HTML pronto para crawlers) | Problemático (precisa SSR ou pre-rendering) |
| **Curva de aprendizado** | Modesta | Alta (framework + ferramentas + estado) |
| **Carga no servidor** | Maior (renderiza HTML) | Menor (só serve JSON) |
| **Carga no cliente** | Mínima (HTML simples) | Considerável (executa JS, gerencia estado) |
| **Equipe** | Um time *full-stack* basta | Frequentemente exige especialistas em frontend |
| **Adequado para** | Aplicações *form-driven*, conteúdo, painéis admin internos | Aplicações com UX rica, dashboards, ferramentas tipo SaaS |

**Lições:**

- **A camada de negócio é a mesma nos dois casos.** O `PedidoService` não muda; apenas a forma de exposição (HTML server-side vs JSON). Isso é exatamente o ganho prometido pela arquitetura em camadas: a Apresentação evolui sem afetar o Negócio.
- **A escolha não é de qualidade, é de contexto.** Um painel administrativo interno usado por 10 pessoas raramente justifica o investimento de uma SPA. Um produto SaaS com milhares de usuários *power* dificilmente sobrevive sem.
- **Híbridos existem.** Frameworks como Hotwire (Rails), Livewire (Laravel), HTMX e os modernos *Server Components* do React buscam o "melhor dos dois mundos" — MVC server-side com interatividade pontual. Em 2024-2026, esse caminho híbrido voltou a ganhar tração depois do reinado solitário das SPAs nos anos 2010.

> 💡 **Intuição:** O ciclo "MVC clássico → SPA com MVVM → híbrido" ilustra um padrão recorrente em arquitetura: a indústria oscila entre extremos antes de encontrar equilíbrios. Conhecer a história te ajuda a não cair no entusiasmo do momento.
> 

---

## Exercícios Práticos

### Exercício 1 — Identificação de violações em camadas ⭐

Considere o seguinte código Java de um sistema de biblioteca:

```java
@RestController
public class LivroController {

    @Autowired
    private DataSource dataSource;

    @GetMapping("/livros/{id}")
    public String detalhe(@PathVariable Long id) throws SQLException {
        Connection c = dataSource.getConnection();
        ResultSet rs = c.createStatement().executeQuery(
            "SELECT * FROM livros WHERE id = " + id);
        rs.next();

        String titulo = rs.getString("titulo");
        int diasAtraso = rs.getInt("dias_atraso");
        double multa = 0;
        if (diasAtraso > 0) {
            multa = diasAtraso * 0.50;
            if (diasAtraso > 30) multa += 10.0;
        }

        return "<html><body><h1>" + titulo + "</h1>"
             + "<p>Multa: R$ " + multa + "</p></body></html>";
    }
}
```

Sua tarefa:

(a) Identifique e enumere **todas** as violações arquiteturais em camadas presentes neste código (espera-se ao menos 4).
(b) Identifique também **uma vulnerabilidade de segurança grave** (que não é arquitetural, mas é crítica).
(c) Refatore o código separando-o em três camadas (`LivroController`, `LivroService` ou `MultaService`, `LivroRepository`). Inclua interfaces onde apropriado.
(d) Discuta em 3-5 linhas: depois da refatoração, **quais testes** ficaram mais fáceis de escrever e por quê?

### Exercício 2 — Projetando arquitetura de um novo sistema ⭐⭐

Você foi contratado para projetar a arquitetura inicial de um sistema descrito a seguir:

> **Sistema de gestão para clínica veterinária**: A clínica atende ~50 consultas por dia, em 4 médicos veterinários, com 2 atendentes na recepção. O sistema deve permitir: agendar consultas, registrar prontuário animal (com histórico médico, vacinas, exames), emitir receitas, gerenciar estoque de medicamentos, controlar contas a receber. Há também um *site público* onde tutores podem ver fotos da clínica e marcar primeira consulta. Em 18 meses, projetam abrir mais 2 unidades, e em 3 anos pretendem oferecer telemedicina.
> 

Sua tarefa:

(a) Identifique e priorize os **3 atributos de qualidade** mais importantes para este sistema, justificando cada escolha em 1-2 frases.
(b) Esboce uma arquitetura em **camadas** apropriada (3 ou 4 camadas, sua escolha — justifique). Liste os principais módulos/serviços de cada camada.
(c) Decida e justifique: para o **painel interno** dos veterinários e atendentes, você usaria MVC clássico, MVP ou MVVM? E para o **site público**? Considere o porte da equipe (provavelmente um único desenvolvedor *full-stack* nos primeiros 18 meses).
(d) Defenda ou ataque a seguinte afirmação: *"Para esse sistema, devemos começar com microsserviços, porque em 3 anos teremos múltiplas unidades e telemedicina."* Apresente argumentos concretos baseados no que vimos.
(e) Esboce um **ADR (Architecture Decision Record)** de no máximo 15 linhas para a decisão "monólito modular vs microsserviços", com os campos: *contexto, decisão, alternativas consideradas, consequências*.

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Bass, L.; Clements, P.; Kazman, R. *Software Architecture in Practice*. 4ª ed., Addison-Wesley, 2021. (Referência canônica em arquitetura.)
- Kruchten, P. "The 4+1 View Model of Architecture". *IEEE Software*, vol. 12, n. 6, 1995. (Artigo seminal sobre visões arquiteturais.)
- Fowler, M. *Patterns of Enterprise Application Architecture*. Addison-Wesley, 2002. (Catálogo de padrões para sistemas corporativos, incluindo Camadas e MVC.)
- Evans, E. *Domain-Driven Design: Tackling Complexity in the Heart of Software*. Addison-Wesley, 2003. (Sobre o foco no domínio — fundamento das arquiteturas hexagonal e clean.)
- Cockburn, A. "Hexagonal Architecture". 2005. (Disponível online; texto curto e fundamental.)
- Martin, R. *Clean Architecture: A Craftsman's Guide to Software Structure and Design*. Pearson, 2017. (Síntese moderna sobre regras de dependência.)
- Newman, S. *Building Microservices*. 2ª ed., O'Reilly, 2021. (Quando — e quando não — adotar microsserviços.)
- Reenskaug, T. "Models-Views-Controllers". Nota técnica, Xerox PARC, 1979. (Documento histórico em que MVC é proposto pela primeira vez.)

---