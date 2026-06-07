# Diagrama de Classes

## 1. Por que modelar?

Antes de mergulharmos na sintaxe, é importante entendermos *por que* gastamos tempo desenhando diagramas em vez de simplesmente programar.

Modelar é o ato de construir uma **representação simplificada** de um sistema com o objetivo de raciocinar sobre ele antes (ou durante) sua construção. Um modelo deixa de fora detalhes irrelevantes para o problema em questão e destaca aquilo que importa. Engenheiros civis fazem plantas baixas; engenheiros de software fazem diagramas.

Entre as várias linguagens de modelagem propostas ao longo da história, a **UML (Unified Modeling Language)** se consolidou como padrão de fato na indústria e na academia. A UML não é uma metodologia nem uma linguagem de programação — é um conjunto de notações gráficas padronizadas pela OMG (*Object Management Group*). Dentre os ~14 tipos de diagramas previstos pela UML 2.x, o **diagrama de classes** é, sem dúvida, o mais utilizado.

> 💡 **Intuição:** O diagrama de classes é o "esqueleto" estrutural do sistema orientado a objetos. Ele responde à pergunta: *"Quais são os tipos de coisa que existem no meu sistema, e como elas se relacionam?"* — em contraste com diagramas comportamentais, que respondem *"o que essas coisas fazem ao longo do tempo?"*.
> 

---

## 2. Anatomia de uma classe

Na UML, uma classe é representada graficamente por um **retângulo dividido em três compartimentos**:

```
┌─────────────────────────┐
│      NomeDaClasse       │   ← compartimento 1: nome
├─────────────────────────┤
│ atributo1: Tipo         │   ← compartimento 2: atributos
│ atributo2: Tipo = valor │
├─────────────────────────┤
│ operacao1(): TipoRetorno│   ← compartimento 3: operações
│ operacao2(p: Tipo)      │
└─────────────────────────┘
```

### 2.1 Nome da classe

Por convenção, escreve-se em **PascalCase** (`ContaCorrente`, não `conta_corrente`). Classes abstratas têm o nome em *itálico* — voltaremos a isso na Seção 4.

### 2.2 Atributos

A sintaxe completa de um atributo é:

```
visibilidade nome: Tipo [multiplicidade] = valorDefault {restrições}
```

Por exemplo:

```
- saldo: double = 0.0
+ titular: Pessoa
# tentativasLogin: int [0..3]
```

### 2.3 Operações (métodos)

A sintaxe é análoga:

```
visibilidade nome(parâmetro: Tipo, ...): TipoRetorno
```

Por exemplo:

```
+ depositar(valor: double): void
+ calcularJuros(taxa: double): double
- validarSenha(s: String): boolean
```

### 2.4 Visibilidade

Os modificadores de visibilidade são herdados (em parte) das linguagens orientadas a objetos:

| Símbolo | Significado | Equivalente em Java |
| --- | --- | --- |
| `+` | público | `public` |
| `-` | privado | `private` |
| `#` | protegido | `protected` |
| `~` | pacote | (default) |

> 💡 **Intuição:** Visibilidade não é uma decoração — ela documenta *contratos*. Um atributo `+` está dizendo ao mundo "podem mexer aqui livremente"; um `-` está dizendo "este é assunto interno meu". Mais tarde, na disciplina, veremos que excessos de `+` são quase sempre um *code smell*.
> 

---

## 3. Relacionamentos entre classes

Classes raramente vivem isoladas. A riqueza do diagrama de classes está justamente em capturar os **vínculos** entre elas. A UML define seis tipos principais de relacionamento, que apresentaremos do mais "fraco" (mais transitório) ao mais "forte" (mais estrutural).

### 3.1 Dependência (`- - >`)

Notação: linha **tracejada** com seta aberta.

Significa: "a classe A *usa* a classe B de forma transitória" — tipicamente porque B aparece como parâmetro de um método de A, ou como tipo de uma variável local.

```
ClasseA - - - - - > ClasseB
```

### 3.2 Associação (`──────`)

Notação: linha **contínua** entre as duas classes.

Significa: "objetos de A *conhecem* objetos de B de forma persistente" — tipicamente porque A possui um atributo cujo tipo é B.

A associação pode ser anotada com:

- **Nome da associação** (verbo no centro): `Professor ──leciona── Disciplina`
- **Papéis** (substantivos nas pontas): qual papel cada classe desempenha na relação.
- **Multiplicidade** (números nas pontas): quantos objetos participam.
- **Navegabilidade** (seta opcional na ponta): indica em que direção é possível "navegar" do código.

### Multiplicidades comuns

| Notação | Significado |
| --- | --- |
| `1` | exatamente um |
| `0..1` | zero ou um (opcional) |
| `*` ou `0..*` | zero ou mais |
| `1..*` | um ou mais |
| `2..5` | entre 2 e 5 |

### 3.3 Agregação (`◇──────`)

Notação: linha contínua com **losango vazio** na ponta do "todo".

Significa: "A é composta por B, mas B *pode existir independentemente* de A". É uma relação *todo-parte fraca*.

Exemplo clássico: `Departamento ◇── Professor`. Se o departamento for extinto, os professores continuam existindo (podem ser realocados).

### 3.4 Composição (`◆──────`)

Notação: linha contínua com **losango preenchido** na ponta do "todo".

Significa: "A é composta por B, e B *não pode existir* sem A". É uma relação *todo-parte forte* — o ciclo de vida das partes está atrelado ao do todo.

Exemplo clássico: `Livro ◆── Capítulo`. Se o livro deixa de existir, seus capítulos também — eles não fazem sentido isolados.

> 💡 **Intuição:** A diferença entre agregação e composição é uma das fontes mais comuns de confusão. A pergunta-chave é: *"Se eu destruir o objeto-todo, faz sentido as partes continuarem existindo?"*. Se sim, agregação. Se não, composição. Na prática, muitos modeladores experientes usam apenas **associação** e **composição**, evitando a agregação por sua semântica frouxa — um conselho que segue [Fowler, 2003].
> 

### 3.5 Generalização / Herança (`────▷`)

Notação: linha contínua com **triângulo vazio** apontando para a superclasse.

Significa: "A *é-um-tipo-de* B". A subclasse herda atributos e operações da superclasse e pode estendê-los ou redefini-los.

```
   Animal
     △
     │
  ┌──┴──┐
  │     │
Cachorro Gato
```

### 3.6 Realização (`- - ▷`)

Notação: linha **tracejada** com **triângulo vazio** apontando para a interface.

Significa: "A *implementa* a interface B". Interfaces são representadas com o estereótipo `<<interface>>` ou com nome em itálico.

---

## 4. Classes abstratas, interfaces e estereótipos

### 4.1 Classes abstratas

Uma classe abstrata é uma classe que **não pode ser instanciada diretamente** — ela existe apenas para ser herdada. Em UML, indicamos abstração escrevendo o nome da classe (e/ou de seus métodos abstratos) em *itálico*, ou anotando `{abstract}`.

```
   FormaGeometrica  {abstract}
   ─────────────────────────
   + área(): double  {abstract}
```

### 4.2 Interfaces

Uma interface define um *contrato* — um conjunto de operações que classes podem implementar. Em UML moderna, usamos o estereótipo `<<interface>>` acima do nome:

```
   <<interface>>
    Comparável
   ─────────────
   + comparar(o: Object): int
```

### 4.3 Estereótipos

Estereótipos são "rótulos" entre `<<...>>` que estendem a semântica de elementos da UML. Os mais comuns são `<<interface>>`, `<<abstract>>`, `<<enumeration>>`, `<<entity>>`, `<<service>>`, e — em modelagem de domínio — `<<value object>>`.

---

## 5. Boas práticas de modelagem

Antes dos exemplos, registre estas heurísticas — elas separam um diagrama amador de um profissional:

1. **Modele o domínio, não a implementação.** Atributos como `idCliente: int` em uma classe `Cliente` quase sempre são uma intrusão do banco de dados no modelo. Modele a semântica do problema.
2. **Não polua com getters e setters.** Em UML, atributos privados *implicitamente* têm acessores; só explicite operações que tenham comportamento real.
3. **Cuide das multiplicidades.** A multiplicidade default em UML é `1`, mas explicitar evita ambiguidade. `0..1` versus `1` é a diferença entre opcional e obrigatório.
4. **Prefira composição a herança.** Hierarquias profundas envelhecem mal; relações de composição costumam ser mais flexíveis.
5. **Um diagrama por propósito.** Não tente caber o sistema inteiro em um único diagrama. Faça vistas focadas (módulo de pagamento, módulo de catálogo, etc.).

---

## 6. Exemplos resolvidos

### Exemplo Resolvido 1 — Sistema de Biblioteca (Introdutório) ⭐

**Enunciado:** Modele um sistema simples de biblioteca em que *Usuários* podem emprestar *Livros*. Cada empréstimo tem uma data de retirada e uma data prevista de devolução. Um livro só pode estar emprestado para um usuário por vez, mas um usuário pode ter vários livros emprestados simultaneamente.

**Solução:**

Identificamos três classes naturais: `Usuario`, `Livro` e `Emprestimo`. A entidade `Emprestimo` é uma **classe associativa** — ela existe para reificar a relação entre `Usuario` e `Livro`, carregando atributos próprios (datas).

```
┌─────────────────┐         ┌──────────────────┐         ┌─────────────┐
│    Usuario      │         │    Emprestimo    │         │   Livro     │
├─────────────────┤  1   *  ├──────────────────┤  *   1  ├─────────────┤
│ - nome: String  │─────────│ - dataRetirada   │─────────│ - isbn      │
│ - matricula     │ realiza │ - dataDevolucao  │ refere-se│ - titulo    │
├─────────────────┤         ├──────────────────┤         ├─────────────┤
│ + emprestar()   │         │ + estaAtrasado() │         │ + reservar()│
└─────────────────┘         └──────────────────┘         └─────────────┘
```

Comentários sobre as decisões:

- **Multiplicidade `1` no lado do `Usuario`**: cada empréstimo pertence a exatamente um usuário.
- **Multiplicidade  (ou `0..*`) no lado do `Emprestimo`**: um usuário pode ter zero ou muitos empréstimos.
- **Por que não modelar `Emprestimo` como uma associação simples com atributos?** Porque ele tem comportamento próprio (`estaAtrasado()`) e identidade — promovê-lo a classe é a escolha correta.
- **Restrição implícita** (que poderíamos formalizar com OCL): para um mesmo livro, no máximo um empréstimo pode estar ativo.

### Exemplo Resolvido 2 — Sistema de E-commerce (Intermediário) ⭐⭐

**Enunciado:** Modele o subdomínio de *catálogo e pedidos* de um e-commerce. Produtos são organizados em categorias (uma categoria pode ter subcategorias). Clientes fazem pedidos contendo itens; cada item refere-se a um produto e a uma quantidade. Existem clientes pessoa física e clientes pessoa jurídica, cada um com regras próprias de cálculo de imposto.

**Solução:**

Aqui combinamos várias construções: hierarquia recursiva de categorias (autoassociação), composição forte para itens de pedido, e generalização para os tipos de cliente.

```
                        <<abstract>>
                          Cliente
                       ─────────────
                       + nome: String
                       + endereço
                       ─────────────
                       + calcularImposto() {abstract}
                              △
                              │
                ┌─────────────┴─────────────┐
                │                           │
        ClientePF                    ClientePJ
        ─────────                    ─────────
        + cpf                        + cnpj
        + dataNasc                   + razaoSocial
        ─────────                    ─────────
        + calcularImposto()          + calcularImposto()

    Cliente  ──1──── faz ────*──▶  Pedido
                                    │
                                    │ 1
                                    ◆ (composição)
                                    │ 1..*
                                    ▼
                                 ItemPedido
                                 ──────────
                                 - quantidade: int
                                 - precoUnit: double
                                    │
                                    │ * (associação)
                                    ▼
                                  Produto ──*──── pertence ────1..*──▶ Categoria
                                                                            │
                                                                            ▼ 0..1 (autoassociação:
                                                                                    "categoria pai")
```

Decisões-chave:

- **`Cliente` é abstrata** porque o sistema sempre lida com PF ou PJ — nunca com um "cliente genérico". O método `calcularImposto()` é abstrato: cada subclasse o implementa segundo suas regras.
- **`Pedido ◆── ItemPedido`** é composição: um item de pedido não tem sentido fora do pedido a que pertence.
- **`ItemPedido ── Produto`** é apenas associação: o item *referencia* o produto, mas o produto continua existindo se o pedido for cancelado.
- **Autoassociação em `Categoria`** com multiplicidade `0..1` no papel "pai" e  no papel "filhas" modela a hierarquia recursiva. O `0..1` permite que existam categorias-raiz (sem pai).
- Note como o `precoUnit` é replicado em `ItemPedido` (e não derivado de `Produto`): é uma decisão de modelagem proposital, pois o preço pago no momento da compra deve ser preservado mesmo que o preço do produto mude depois.

> 💡 **Intuição:** Repare como cada pequena decisão (composição vs. associação, abstrata vs. concreta, replicar atributo vs. derivar) é uma *afirmação* sobre o domínio. Modelar não é escolher caixinhas e setas — é declarar como o mundo do problema funciona.
> 

---

## Exercícios Práticos

### Exercício 1 — Sistema Acadêmico ⭐

Modele em um diagrama de classes o seguinte recorte de um sistema universitário:

> A universidade possui *cursos* (ex.: Ciência da Computação, Engenharia Civil). Cada curso tem um conjunto de *disciplinas*. Cada disciplina é ministrada por um único *professor* em um *semestre* específico, mas pode ter vários alunos *matriculados*. Um aluno pode estar matriculado em várias disciplinas simultaneamente, e o sistema precisa registrar a *nota final* de cada matrícula.
> 

Sua solução deve identificar pelo menos uma classe associativa e justificar por que ela é necessária. Inclua multiplicidades em todas as associações.

### Exercício 2 — Sistema de Streaming ⭐⭐

Considere o seguinte enunciado:

> Uma plataforma de streaming oferece *filmes*, *séries* e *documentários*. Séries são compostas por *temporadas*, e temporadas por *episódios* (note que um episódio não existe fora de sua temporada). Toda mídia (filme, série, documentário) tem um *título*, *ano* e uma lista de *gêneros*. Usuários têm um histórico de visualizações, e podem dar uma *avaliação* (de 1 a 5) a qualquer mídia que já tenham assistido. A plataforma oferece três tipos de plano: *Básico*, *Padrão* e *Premium*, cada um com regras de qualidade de vídeo e número máximo de telas simultâneas.
> 

Modele este sistema, atendendo aos seguintes requisitos:

(a) Identifique e use pelo menos uma classe abstrata e uma interface.
(b) Diferencie corretamente composição e agregação onde aplicável.
(c) Modele a relação "usuário avalia mídia" usando uma classe associativa.
(d) Justifique em prosa, ao final, pelo menos três decisões não-óbvias do seu modelo.

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Fowler, M. *UML Distilled: A Brief Guide to the Standard Object Modeling Language*. 3ª ed., Addison-Wesley, 2003. — Capítulos 3 e 5.
- Booch, G.; Rumbaugh, J.; Jacobson, I. *The Unified Modeling Language User Guide*. 2ª ed., Addison-Wesley, 2005. — Parte II.
- Larman, C. *Applying UML and Patterns: An Introduction to Object-Oriented Analysis and Design and Iterative Development*. 3ª ed., Prentice Hall, 2004. — Capítulos 9 e 16.
- Bezerra, E. *Princípios de Análise e Projeto de Sistemas com UML*. 3ª ed., Elsevier, 2015. — Capítulo 5.
- OMG. *OMG Unified Modeling Language Specification*, versão 2.5.1, 2017. (Referência normativa.)

---