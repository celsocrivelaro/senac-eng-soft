# Plano de Testes de Software

## 1. O que é um plano de testes?

Um **plano de testes** é um documento que descreve **o que será testado, como, quando, por quem, e em que condições**. Ele não é o código dos testes em si — é o documento que organiza e racionaliza o esforço de testes em um projeto.

A definição formal mais influente vem do padrão **IEEE 829** (revisado em ISO/IEC/IEEE 29119, atual desde 2013):

> Um plano de testes é a descrição abrangente do escopo, abordagem, recursos e cronograma de atividades de teste pretendidas. Ele identifica os itens de teste, as funcionalidades a serem testadas, as tarefas, quem fará cada tarefa, riscos e contingências.
> 

Em prosa simples: é o **mapa** que guia o esforço de testes durante um projeto ou ciclo de release.

### 1.1 Por que escrever um plano de testes?

A pergunta honesta é: por que se dar ao trabalho? Em projetos pequenos ou em culturas ágeis radicais, frequentemente o plano “implícito” (na cabeça da equipe) basta. Em projetos maiores, no entanto, escrever o plano traz benefícios concretos:

- **Tornar explícito o que será testado e o que não será.** Decisões de “fora do escopo” frequentemente são as mais polêmicas — e é melhor tê-las decididas antes de iniciar do que depois.
- **Permitir orçamento de tempo e de recursos.** Quanto tempo de QA? Quantas máquinas de teste de carga? Quantos perfis de usuário para teste manual? Sem plano, esses números não saem.
- **Distribuir responsabilidades.** Em projetos com múltiplas equipes, quem testa o quê precisa ficar claro.
- **Definir critérios objetivos para o “pronto”**. Sem critérios escritos, “está pronto” vira opinião — e opiniões variam.
- **Identificar riscos antes que virem problemas.** O processo de escrever o plano força a equipe a pensar: o que pode dar errado? Onde estamos vulneráveis?

> 💡 **Intuição:** O plano de testes é para o esforço de testes o que o projeto arquitetônico é para a construção de um edifício: nem sempre é estritamente necessário (você pode reformar uma cozinha sem planta), mas sem ele um projeto de porte vira improviso caro. O custo de fazer o plano é pequeno comparado ao custo de descobrir, em produção, que ninguém pensou em testar o cenário de pico de Black Friday.
> 

### 1.2 Plano vs estratégia vs caso de teste

Três conceitos relacionados que costumam ser confundidos:

| Conceito | O que é | Granularidade |
| --- | --- | --- |
| **Estratégia de teste** | Documento institucional, geralmente da empresa ou produto, que define princípios e práticas de teste — independente de projeto. | Mais ampla, transversal |
| **Plano de teste** | Aplicação da estratégia a um **projeto ou ciclo de release específico**, com escopo, recursos, cronograma. | Específica do projeto |
| **Caso de teste** | Uma **instância concreta** — uma situação a testar, com passos, dados, e resultado esperado. | Mais focal, executável |

Uma analogia útil: a estratégia é a *constituição* (princípios estáveis), o plano é a *política pública* (aplicação a um contexto), e os casos de teste são as *ações concretas* (o que se faz no dia a dia).

---

## 2. Componentes do plano de testes

O padrão IEEE 829 lista 16 seções para um plano completo. Na prática, planos exagerados ninguém lê. Adotamos uma versão **pragmática** com os componentes que efetivamente importam:

### 2.1 Identificação

Cabeçalho do plano: nome do projeto, versão, autores, data, status (rascunho, aprovado, em revisão).

### 2.2 Introdução e referências

Contexto rápido (1-2 parágrafos) sobre o sistema sendo testado e referências a documentos relacionados — especificação de requisitos, casos de uso, decisões arquiteturais relevantes.

### 2.3 Escopo

A seção mais importante e mais frequentemente mal-feita. Define duas listas explícitas:

- **O que será testado** (in scope): funcionalidades, módulos, integrações, atributos de qualidade.
- **O que NÃO será testado** (out of scope): muito mais valioso explicitar, porque evita expectativas erradas.

Exemplos no nosso e-commerce:

| Em escopo | Fora de escopo |
| --- | --- |
| Fluxo de checkout completo | Integração com novo gateway de pagamento (próximo release) |
| Cálculo de impostos para Brasil | Cálculo de impostos para outros países |
| Cadastro e edição de perfil | Recuperação de senha (testado separadamente) |
| Performance da busca de produtos | Performance do painel administrativo |

### 2.4 Estratégia de teste

Como a equipe vai abordar os testes. Três decisões principais:

- **Que níveis serão executados?** (unitário, integração, sistema, aceitação — veremos cada um)
- **Que técnicas serão usadas?** (caixa-preta, caixa-branca, baseado em risco)
- **Qual o balanço entre manual e automatizado?** (e por quê)

### 2.5 Recursos

- **Humanos**: quantos testadores? Que perfis? (analistas de teste, devs em SDET, usuários para UAT)
- **Técnicos**: ambientes de teste, hardware, licenças de ferramentas
- **Dados**: massa de teste, geração de dados, anonimização

### 2.6 Cronograma e marcos

Quando começa o teste de cada módulo? Quando deve estar concluído? Quais são os marcos (gates) entre fases?

### 2.7 Critérios de entrada e saída

**Entrada**: condições mínimas para começar a testar (ex.: build estável, ambiente disponível, casos de teste revisados).

**Saída**: condições para declarar o teste concluído (ex.: 100% dos casos críticos passaram, 0 bugs bloqueantes abertos, cobertura de código ≥ X%).

### 2.8 Riscos

Quais riscos podem comprometer o esforço de teste? Atrasos do desenvolvimento? Indisponibilidade do ambiente? Falta de pessoal especializado? Cada risco identificado deve ter mitigação proposta.

### 2.9 Entregáveis

O que o esforço de teste produz como artefato: relatórios de execução, listas de bugs, métricas de cobertura, certificado de aceitação.

> 💡 **Intuição:** Um plano de testes bem feito tem entre 5 e 20 páginas. Planos de 100 páginas costumam ser cerimoniais — escritos para auditoria e nunca lidos. Planos de 1 página costumam ser superficiais. O bom plano cabe na cabeça da equipe inteira.
> 

---

## 3. Níveis de teste

A pirâmide de testes que vimos na aula passada se materializa em quatro níveis principais. Cada nível tem propósito, audiência e técnicas distintas.

### 3.1 Teste unitário

**O que é**: testa a **menor unidade isolável** do código — uma função, um método, uma classe — sem dependências externas (banco de dados, rede, sistema de arquivos).

**Quem escreve**: o próprio desenvolvedor, idealmente *ao mesmo tempo* em que escreve o código de produção.

**Características**: rápido (milissegundos), determinístico, isolado, executado a cada commit.

**Exemplo**: testar isoladamente a função `calcularFrete(pedido)` para verificar se retorna valores corretos para diferentes CEPs e pesos.

```java
@Test
public void freteParaSudesteAteUmKilo() {
    Pedido p = new Pedido();
    p.adicionar(new Item("Livro", 1, BigDecimal.valueOf(50)));
    p.setCepEntrega("01310-100"); // SP

    BigDecimal frete = calculadora.calcularFrete(p);

    assertEquals(BigDecimal.valueOf(15.00), frete);
}
```

### 3.2 Teste de integração

**O que é**: testa **a interação entre dois ou mais componentes**. Verifica que as peças se encaixam — dados fluem entre camadas, integrações com banco e APIs funcionam.

**Quem escreve**: desenvolvedores, possivelmente em colaboração com QAs.

**Características**: mais lentos que unitários (segundos), exigem ambiente com dependências reais ou *test doubles* sofisticados.

**Exemplo**: testar que o método `criarPedido()` do `PedidoService`:
- valida o pedido (chama o `Validador`)
- persiste no banco (chama o `Repositorio`)
- envia evento (chama o `Broker`)
- retorna ID gerado

Essa é a “cola” entre componentes que testes unitários (que mockam tudo) podem deixar passar.

### 3.3 Teste de sistema

**O que é**: testa o **sistema como um todo**, do ponto de vista externo. Tipicamente atravessa toda a stack (UI/API → camadas → BD → integrações).

**Quem escreve**: equipe de QA, possivelmente com suporte de ferramentas de automação (Selenium, Cypress, Playwright para web; Appium para mobile).

**Características**: lentos (minutos), frágeis a mudanças de UI, mas validam fluxos end-to-end como o usuário real veria.

**Exemplo**: cenário automatizado que abre o navegador, faz login, busca um produto, adiciona ao carrinho, finaliza compra, e valida que o e-mail de confirmação foi recebido.

### 3.4 Teste de aceitação (UAT)

**O que é**: validação final pelo *cliente* (interno ou externo) de que o sistema atende às necessidades de negócio. Não é sobre “o sistema funciona?” — é sobre “o sistema serve?”.

**Quem escreve**: representantes do negócio, *product owners*, usuários-chave.

**Características**: tipicamente manuais; usam linguagem de negócio, não técnica.

**Critério-chave**: cumpre os critérios de aceitação (acceptance criteria) definidos nos casos de uso ou histórias de usuário.

> 💡 **Intuição:** Cada nível responde a uma pergunta diferente. Unitário: *“essa função faz o que eu pedi?”*. Integração: *“essas peças se conversam?”*. Sistema: *“o software inteiro funciona?”*. Aceitação: *“é isso que o cliente queria?”*. Pular qualquer nível compromete uma das perguntas — e cada uma é insubstituível.
> 

---

## 4. Tipos transversais de teste

Os níveis acima descrevem **escopo do que se testa**. Há também **tipos** que descrevem **a finalidade do teste**, e que podem aparecer em qualquer nível.

| Tipo | O que verifica | Exemplo |
| --- | --- | --- |
| **Funcional** | O sistema faz o que deveria fazer? | “O cálculo de frete retorna R$ 15 para SP.” |
| **Não-funcional** | O sistema atende atributos de qualidade? | Performance, segurança, usabilidade, etc. |
| **Smoke** | O básico funciona, ou nem vale continuar? | “A aplicação sobe? A tela inicial carrega?” |
| **Sanity** | Após uma mudança pequena, nada óbvio quebrou? | “Após corrigir bug de checkout, ainda dá para fazer login?” |
| **Regressão** | Mudanças recentes quebraram algo que funcionava? | Suíte automatizada inteira a cada PR/deploy. |
| **Performance** | O sistema responde rápido o suficiente sob carga? | “API atende 1.000 req/s com p95 < 200ms.” |
| **Carga / Stress** | Onde o sistema quebra? | “Aumentar carga até falha; documentar limite.” |
| **Segurança** | Resiste a ataques conhecidos? | OWASP Top 10, SQL injection, XSS, autenticação. |
| **Usabilidade** | Usuários conseguem realizar tarefas? | Testes com usuários reais; tempo para concluir tarefa. |
| **Recuperação** | O sistema se recupera de falhas? | Cair o BD, derrubar nó: o sistema volta? |
| **Compatibilidade** | Funciona em diferentes ambientes? | Browsers, OSs, dispositivos, versões. |

Um plano de testes maduro decide explicitamente **quais tipos** serão executados e **com que profundidade**. Para a maioria dos sistemas, performance, segurança e regressão são prioridades não-negociáveis; usabilidade e compatibilidade variam conforme o produto.

---

## 5. Técnicas de design de casos de teste

Saber **que** testar é metade do trabalho. A outra metade é saber **quais casos específicos** escrever — e aqui há técnicas formais que evitam tanto subteste (deixar furos) quanto sobreteste (gastar tempo em casos redundantes).

As técnicas se dividem em duas grandes famílias.

### 5.1 Caixa-preta — sem olhar para o código

O testador conhece apenas a **especificação** (o que o sistema deve fazer), não a implementação. Técnicas principais:

### 5.1.1 Particionamento por equivalência

Identifica **classes de equivalência** — conjuntos de entradas que o sistema deveria tratar do mesmo jeito. Testa-se um representante de cada classe em vez de todas as entradas possíveis.

**Exemplo**: validação de idade para cadastro no e-commerce (regra: 18+ anos).

| Classe | Representante | Resultado esperado |
| --- | --- | --- |
| Idade negativa (inválida) | -5 | Erro |
| Idade 0 a 17 (menor de idade) | 10 | Erro: precisa ser maior de idade |
| Idade 18 a 120 (válida) | 30 | Cadastro aceito |
| Idade > 120 (improvável) | 200 | Erro: idade implausível |
| Texto não-numérico | “abc” | Erro de formato |

**5 casos** cobrem o que **infinitos casos** não cobririam melhor.

### 5.1.2 Análise de valores limítrofes (Boundary Value Analysis — BVA)

Testa especificamente **as fronteiras** das classes de equivalência — porque é onde os bugs mais frequentemente aparecem (`<` vs `<=`, off-by-one, overflow).

Para cada fronteira, testa-se **três valores**: o limite, logo abaixo, e logo acima.

**Exemplo**: regra “frete grátis para pedidos a partir de R$ 100,00”.

| Valor | Resultado esperado |
| --- | --- |
| R$ 99,99 (logo abaixo) | Frete cobrado |
| R$ 100,00 (limite) | Frete grátis |
| R$ 100,01 (logo acima) | Frete grátis |

Se o desenvolvedor escreveu `if (valor > 100)` em vez de `if (valor >= 100)`, o caso `R$ 100,00` falha. BVA pega esse tipo de bug.

### 5.1.3 Tabela de decisão

Quando o resultado depende de **combinações de condições**, uma tabela de decisão organiza o espaço.

**Exemplo**: política de descontos no e-commerce.

| Cliente VIP? | Compra > R$ 500? | Cupom válido? | Desconto |
| --- | --- | --- | --- |
| Não | Não | Não | 0% |
| Não | Não | Sim | 10% |
| Não | Sim | Não | 5% |
| Não | Sim | Sim | 12% |
| Sim | Não | Não | 8% |
| Sim | Não | Sim | 15% |
| Sim | Sim | Não | 12% |
| Sim | Sim | Sim | 20% |

Cada linha é um caso de teste. **Oito casos** cobrem todas as combinações de três variáveis booleanas (2³ = 8). A técnica é exaustiva onde precisa ser.

### 5.1.4 Teste de transição de estados

Quando o sistema tem **estados bem definidos** (vimos isso no diagrama de estados), testa-se as transições válidas e inválidas.

**Exemplo**: ciclo de vida do `Pedido` (Criado → Pago → Separado → Enviado → Entregue, com cancelamento possível em alguns estados).

Casos a cobrir:
- Cada transição válida (Criado → Pago, Pago → Separado, etc.)
- Cada transição inválida (Entregue → Pago não deve funcionar)
- Auto-loops e estados terminais

### 5.2 Caixa-branca — olhando o código

Aqui o testador conhece a implementação e desenha casos para **exercitar caminhos do código**.

### 5.2.1 Cobertura de linha

Cada linha de código deve ser executada por pelo menos um caso de teste. É a métrica mais comum, mas a mais fraca.

### 5.2.2 Cobertura de branch (decisão)

Cada **ramificação** de cada `if`, `switch`, ou ternário deve ser exercitada — ambos os caminhos (verdadeiro e falso). É consideravelmente mais forte que cobertura de linha.

### 5.2.3 Cobertura de condição

Cada **subcondição** dentro de uma condição composta (`if (a && b)`) deve ser testada com `true` e com `false` independentemente. Mais rigorosa que cobertura de branch.

### 5.2.4 Cobertura de caminho

Cada caminho possível através do código é exercitado. Ideal teoricamente, **inviável** na prática para código não-trivial — número de caminhos cresce exponencialmente.

> 💡 **Intuição:** Caixa-preta e caixa-branca não competem — se complementam. Caixa-preta encontra bugs em **funcionalidades** (“o sistema faz o errado”); caixa-branca encontra bugs em **implementação** (“o sistema tem um caminho não exercitado que falha em produção”). Bons planos usam ambas.
> 

---

## 6. Estrutura de um caso de teste

Um caso de teste bem-escrito tem campos padronizados. Não há um formato universal, mas um modelo comum inclui:

| Campo | Descrição |
| --- | --- |
| **ID** | Identificador único (ex.: TC-CHECKOUT-042) |
| **Título** | Descrição curta e específica do que se testa |
| **Pré-condições** | Estado do sistema antes do teste (cliente cadastrado, produto em estoque, etc.) |
| **Dados de entrada** | Valores específicos a usar |
| **Passos** | Sequência exata de ações a executar |
| **Resultado esperado** | O que o sistema deve fazer/exibir |
| **Resultado obtido** | O que o sistema fez na execução (preenchido durante a execução) |
| **Status** | Aprovado / Reprovado / Bloqueado / Não Executado |
| **Severidade** | Crítica / Alta / Média / Baixa (em caso de falha) |
| **Notas** | Observações, links para bugs, evidências |

Casos de teste vivem em **ferramentas de gestão** (TestRail, Zephyr, Xray, qTest) ou — em times mais ágeis — em arquivos de código (BDD com Cucumber, scripts em Cypress/Playwright).

### 6.1 Casos automatizados vs manuais

A pergunta inevitável: automatizar ou não?

**Automatizar** é certo quando:
- O teste será executado **muitas vezes** (regressão).
- O teste é **determinístico** (mesmo input → mesmo output).
- O teste é **frequentemente quebrado** por mudanças (cobre código volátil).

**Manual** continua sendo certo quando:
- É um teste **exploratório** (descobrir comportamento, não confirmar).
- Avalia **usabilidade** ou **estética** (humanos veem coisas que código não vê).
- Será executado **uma única vez** (custo de automatizar > de executar manualmente).

---

## 7. Cobertura: o que mede e o que NÃO mede

Cobertura de testes é **a métrica mais citada e a mais mal-entendida** no universo de testes. Cabe um esclarecimento honesto.

### 7.1 O que cobertura mede

Cobertura mede **quanto do código foi exercitado pelos testes**. É uma métrica **quantitativa** sobre execução.

### 7.2 O que cobertura NÃO mede

- **Não mede qualidade dos testes.** Você pode ter 100% de cobertura com testes que não verificam nada (executam o código sem fazer asserções).
- **Não mede correção do comportamento.** Cobertura prova que o código foi *executado*, não que *funciona corretamente*.
- **Não mede cobertura de requisitos.** Você pode ter 100% de cobertura de código e 0% de cobertura de requisitos importantes (que talvez nem tenham sido implementados).
- **Não mede valor de negócio testado.** Testar 100% de uma funcionalidade obscura não compensa 0% de uma funcionalidade crítica.

### 7.3 O número mágico “80%”

A regra prática “ter 80% de cobertura” está em livros, em discussões de equipe, e em políticas corporativas. De onde vem?

Empiricamente, projetos que perseguem 100% gastam esforço desproporcional nos últimos 20% — frequentemente cobrindo getters/setters triviais ou caminhos de erro improváveis. 80% costuma ser o ponto onde o **retorno marginal** começa a cair.

Mas o número correto depende do **contexto**: software de aviônica precisa de cobertura próxima a 100% (com requisitos formais como o RTCA DO-178C); um *spike* exploratório talvez não precise de testes nenhum.

### 7.4 Métricas que complementam cobertura

- **Mutation testing** (testes de mutação): introduz pequenas alterações no código e verifica se os testes detectam. Mede **qualidade dos testes**, não só execução.
- **Cobertura de requisitos**: para cada requisito, há ao menos um caso de teste? Esta é a cobertura que importa do ponto de vista do usuário.
- **Tempo médio para detectar regressão**: quão rápido a suíte avisa quando algo quebra?
- **Taxa de escape de defeitos**: bugs encontrados em produção / bugs encontrados pré-produção. Quanto menor, melhor.

> 💡 **Intuição:** Cobertura é como média de notas na escola: indica algo, mas resume demais. Você pode ter média 8 com performance constante ou com 10 nas matérias fáceis e 4 nas críticas. A cobertura é a média; a cobertura *de requisitos* é o que importa.
> 

---

## 8. Critérios de entrada e saída

Esta é a seção do plano que mais frequentemente vira política após o projeto.

### 8.1 Critérios de entrada (Entry Criteria)

Condições que devem estar satisfeitas **antes** de iniciar uma fase de teste. Exemplos:

- A *build* do sistema está estável (passa em testes unitários internos).
- O ambiente de teste está disponível e configurado.
- Os casos de teste foram revisados e aprovados.
- Os documentos de requisito/casos de uso estão na versão correta.
- A massa de teste está disponível.

Se um critério de entrada falha, **não se começa a testar** — porque testar com base instável é desperdício.

### 8.2 Critérios de saída (Exit Criteria)

Condições que devem estar satisfeitas **antes** de declarar a fase de testes concluída. Exemplos típicos:

- 100% dos casos de teste de severidade crítica executados.
- 0 bugs com severidade “Bloqueante” abertos.
- Bugs “Alta” com mitigação documentada (correção no próximo ciclo, *workaround* aceito).
- Cobertura de código ≥ 80% nos módulos críticos.
- Performance: APIs principais com p95 < limite acordado.
- Aprovação formal do *Product Owner* ou *stakeholder* equivalente.

### 8.3 Definition of Done

Em métodos ágeis, os critérios de saída costumam ser sintetizados na **Definition of Done** (DoD) — um *checklist* curto que define quando uma história está concluída. Um DoD razoável para uma história de e-commerce pode incluir:

```
- [ ] Código revisado por pelo menos 1 colega
- [ ] Testes unitários cobrem novo código (≥ 80% nas classes alteradas)
- [ ] Testes de integração relevantes adicionados
- [ ] Documentação técnica atualizada se necessário
- [ ] Demo aprovada pelo PO
- [ ] Sem bugs Bloqueantes ou Críticos abertos
```

> 💡 **Intuição:** Critérios de entrada e saída protegem a equipe de teste — e o projeto inteiro — de cair em ciclos viciosos de “testar sem propósito” e “declarar pronto sem critério”. São a versão acionável das fronteiras do plano.
> 

---

## 9. Massa de testes e ambientes

Um plano de testes que ignora **dados de teste** sofre na execução. Considerações importantes:

### 9.1 Geração de massa

- **Dados sintéticos**: gerados por scripts; previsíveis e controlados; bons para testes funcionais.
- **Dados anonimizados de produção**: realistas em volume e diversidade; críticos para teste de performance; mas exigem **anonimização cuidadosa** para LGPD/GDPR.
- **Dados de fixture**: pequenos *datasets* fixos versionados junto ao código; padrão em testes de integração.

### 9.2 Isolamento

Testes não podem **interferir uns nos outros**. Princípios:

- Cada teste **cria** seus dados ou usa fixtures isoladas.
- Testes paralelos usam *datasets* ou *databases* distintos.
- Após cada teste, o **estado é limpo** (rollback de transação, reset de banco em memória).

### 9.3 Ambientes

A pirâmide típica:

| Ambiente | Propósito | Característica |
| --- | --- | --- |
| **Dev** | Desenvolvimento individual | Cada dev tem o seu, ou ambiente compartilhado leve |
| **Test / QA** | Execução de testes manuais e automatizados | Próximo de produção, mas com massa de teste |
| **Staging / Pre-prod** | Validação final | Espelha produção: mesmo hardware, mesmas integrações |
| **Produção** | Sistema real | Onde os usuários reais estão |

Cada ambiente tem seu propósito — pular um (ex.: testar direto em produção) só é aceitável em sistemas pequenos ou em técnicas explícitas como *canary deployment* e *feature flags*, com salvaguardas adequadas.

---

## 10. Risk-based testing

Não há tempo para testar tudo. A pergunta é: **onde concentrar o esforço de teste?** A resposta racional vem da **priorização por risco**.

### 10.1 A matriz de risco

Para cada funcionalidade, estima-se:

- **Probabilidade de falha**: quão provável é que essa funcionalidade tenha bugs? (Alta para código novo, complexo, ou recém-refatorado; baixa para código maduro pouco mexido.)
- **Impacto da falha**: se falhar em produção, quão ruim é? (Alto para checkout — usuário não compra, empresa perde dinheiro; baixo para tema escuro do painel — usuário se incomoda mas usa.)

A matriz combina os dois eixos:

|  | Baixo impacto | Alto impacto |
| --- | --- | --- |
| **Baixa probabilidade** | Mínimo de testes | Testes-chave, focados |
| **Alta probabilidade** | Testes funcionais básicos | **Esforço máximo de testes** |

### 10.2 Aplicando ao e-commerce

| Funcionalidade | Probabilidade | Impacto | Prioridade de teste |
| --- | --- | --- | --- |
| Checkout (autenticação, cálculo, pagamento, gravação) | Média | Crítico (perda de venda + insatisfação) | **Máxima** |
| Recomendações personalizadas | Alta (algoritmo novo) | Médio (UX prejudicada) | Alta |
| Painel administrativo (relatórios) | Baixa | Médio | Média |
| Tema visual / cor de fundo | Baixa | Baixo | Mínima |

Essa priorização guia **quanto** se testa de cada coisa — não **se** se testa.

### 10.3 Decisões pragmáticas

- **Coleções massivas de testes E2E** geralmente terminam dolorosas: lentas, frágeis, abandonadas. O risco de manter justifica focar.
- **Testes de carga** custam tempo e infra — fazem sentido para cenários reais (Black Friday) e fluxos de alta vazão (checkout), não para a aplicação inteira.
- **Testes de segurança** tendem a ser desproporcionalmente importantes para módulos que tocam dados sensíveis (pagamento, autenticação, perfil).

> 💡 **Intuição:** Testar tudo igualmente é não priorizar — é gastar igualmente em código crítico e em código de baixo impacto. Bons planos de teste **declaram** explicitamente a priorização e justificam onde se gasta atenção. “Testamos isso 5x mais que aquilo, porque o risco é maior” é uma frase saudável.
> 

---

## 11. Exemplos resolvidos

### Exemplo Resolvido 1 — Plano de testes resumido para módulo de Checkout (Introdutório) ⭐

**Enunciado:** O time recebeu a tarefa de implementar uma reescrita do módulo de checkout do e-commerce. Esboce um plano de testes resumido (1-2 páginas) cobrindo escopo, estratégia, recursos, critérios de saída e principais riscos.

**Solução:**

```markdown
# Plano de Testes — Reescrita do Módulo de Checkout v2.0

**Projeto:** E-commerce Acme — Reescrita do Checkout
**Versão:** 1.0 (aprovada em 2026-04-22)
**Autor:** Equipe de Engenharia

## 1. Introdução

Este plano descreve a estratégia de testes para a reescrita do módulo de
checkout, que substituirá a implementação atual (legada, sem testes) por
uma nova arquitetura modular com cobertura completa de testes.

Documentos de referência:
-Casos de uso UC-CHK-001 a UC-CHK-012 (especificação funcional)
-ADR-014: arquitetura hexagonal do novo checkout
-Plano de migração v1.2

## 2. Escopo

### Em escopo
-Fluxo completo de checkout: validação de carrinho, autenticação, cálculo
  de impostos, cálculo de frete, autorização de pagamento, criação do pedido
-Integração com gateway de pagamento existente (Cielo)
-Performance sob carga típica e de pico (Black Friday)
-Compatibilidade com Chrome, Firefox, Safari (versões recentes)
-Acessibilidade (WCAG AA) na nova UI

### Fora de escopo
-Integração com novo gateway PicPay (planejada para v2.1)
-Versão mobile nativa (web-responsiva apenas neste ciclo)
-Otimizações de SEO da página de carrinho

## 3. Estratégia

-**Pirâmide invertida ao normal:** muitos testes unitários (alvo: 90%+
  cobertura), testes de integração para cada caminho do hexágono, suíte
  E2E de 12 cenários críticos.
-**Caixa-preta:** particionamento e BVA para validações; tabelas de
  decisão para regras de desconto e frete.
-**Caixa-branca:** cobertura de linha + branch; mutation testing nos
  módulos de cálculo financeiro.
-**Performance:** JMeter; cenário de Black Friday com 5.000 checkouts/min.
-**Segurança:** revisão OWASP Top 10; foco em armazenamento e transmissão
  de dados de cartão (PCI-DSS).
-**80% automatizado, 20% manual** (UAT e exploratórios).

## 4. Recursos

-2 desenvolvedores (escrevem testes unitários e de integração ao codar)
-1 QA dedicado (planeja, executa testes E2E, exploratórios)
-1 SRE (consultor para testes de performance/carga)
-Ambiente de QA: Kubernetes em namespace isolado, BD anonimizado de
  produção, gateway de pagamento em modo sandbox

## 5. Cronograma

| Marco | Data |
|---|---|
| Início desenvolvimento | 2026-04-29 |
| Primeira build testável (gate de smoke) | 2026-05-13 |
| Testes funcionais completos | 2026-06-03 |
| Testes de performance | 2026-06-10 |
| UAT com PO + 5 usuários-chave | 2026-06-17 |
| Go/No-go para produção | 2026-06-24 |

## 6. Critérios de saída

-100% dos casos críticos (CRT-001 a CRT-027) executados e aprovados
-0 bugs Bloqueantes ou Críticos abertos
-≤ 3 bugs Altos abertos com workaround documentado e aceito
-Cobertura ≥ 90% (linha) e ≥ 85% (branch) nos módulos novos
-Performance: p95 da finalização ≤ 800ms sob carga normal; ≤ 2s sob pico
-Aprovação formal do PO em UAT

## 7. Riscos

| Risco | Mitigação |
|---|---|
| Atraso nas dependências (gateway, anti-fraude) | Iniciar contratos cedo; usar mocks no ambiente de QA |
| Massa de teste anonimizada inadequada | SRE valida representatividade na primeira semana |
| Regressões no fluxo do carrinho (integração com módulo antigo) | Suíte de regressão automatizada do carrinho deve passar 100% antes de cada deploy |
| Performance degradada por novo modelo de dados | Teste de carga em mid-sprint para detectar cedo |

## 8. Entregáveis

-Suítes de teste automatizadas (em GitHub)
-Relatório de cobertura por módulo (Jacoco)
-Relatório de execução em UAT (PDF assinado pelo PO)
-Relatório de testes de performance com gráficos
```

**Discussão:**

- **Tamanho**: o plano cabe em 2 páginas. Adequado para uma reescrita de módulo bem definido — não inflado com cerimônia desnecessária.
- **Escopo explícito em duas listas**: “em escopo” + “fora de escopo”. Evita que o time de QA seja cobrado depois por testes de funcionalidades que nunca estiveram no plano.
- **Critérios de saída numéricos e verificáveis**: “p95 ≤ 800ms” e “≥ 90% cobertura” são verificáveis automaticamente. “Sistema deve estar rápido” não.
- **Riscos concretos com mitigações específicas**: cada risco identificado tem ação. Riscos sem mitigação são teatro.

### Exemplo Resolvido 2 — Design de casos de teste com técnicas formais (Intermediário) ⭐⭐

**Enunciado:** Para a funcionalidade de **cálculo de frete** do nosso e-commerce, projete casos de teste usando: (a) particionamento por equivalência, (b) análise de valores limítrofes, e (c) tabela de decisão. As regras de negócio são:

- Frete grátis para pedidos a partir de R$ 100,00 e CEP no Sudeste (SP, RJ, MG, ES).
- Frete fixo de R$ 15,00 para pedidos abaixo de R$ 100,00 com CEP no Sudeste.
- Frete fixo de R$ 25,00 para qualquer pedido com CEP fora do Sudeste.
- Pedidos com peso > 30 kg adicionam R$ 30,00 ao frete (sobretaxa).
- Pedidos com CEP inválido (formato errado, ou inexistente) devem retornar erro.

**Solução:**

### (a) Particionamento por equivalência

Identifico as classes de equivalência para cada variável:

**Variável: valor do pedido**
- Classe A: ≥ R$ 100 (qualifica para frete grátis se Sudeste)
- Classe B: < R$ 100 e > 0 (frete normal)
- Classe C: ≤ 0 (inválido — pedido não pode ser zero ou negativo)

**Variável: região do CEP**
- Classe D: Sudeste (SP, RJ, MG, ES)
- Classe E: Outras regiões (válido)
- Classe F: CEP inválido (formato ou inexistente)

**Variável: peso**
- Classe G: ≤ 30 kg (sem sobretaxa)
- Classe H: > 30 kg (sobretaxa de R$ 30)
- Classe I: ≤ 0 ou inválido (erro)

**Casos de teste por equivalência (representativos):**

| ID | Valor | Região | Peso | Esperado |
| --- | --- | --- | --- | --- |
| TC-EQV-01 | R$ 150 | SP (D) | 5 kg (G) | Frete grátis |
| TC-EQV-02 | R$ 50 | RJ (D) | 5 kg (G) | R$ 15,00 |
| TC-EQV-03 | R$ 150 | BA (E) | 5 kg (G) | R$ 25,00 |
| TC-EQV-04 | R$ 50 | BA (E) | 5 kg (G) | R$ 25,00 |
| TC-EQV-05 | R$ 150 | SP (D) | 35 kg (H) | R$ 30,00 (sobretaxa, frete grátis) |
| TC-EQV-06 | R$ 50 | RJ (D) | 35 kg (H) | R$ 45,00 (R$ 15 + R$ 30) |
| TC-EQV-07 | R$ 150 | “00000” (F) | 5 kg | Erro: CEP inválido |
| TC-EQV-08 | R$ 0 (C) | SP | 5 kg | Erro: valor inválido |

### (b) Valores limítrofes (BVA)

Para a fronteira de **R$ 100** (grátis vs cobrado), com CEP no Sudeste:

| ID | Valor | Esperado |
| --- | --- | --- |
| TC-BVA-01 | R$ 99,99 | R$ 15,00 (logo abaixo) |
| TC-BVA-02 | R$ 100,00 | R$ 0 (limite — frete grátis) |
| TC-BVA-03 | R$ 100,01 | R$ 0 (logo acima) |

Para a fronteira de **30 kg** (sobretaxa):

| ID | Peso | Esperado |
| --- | --- | --- |
| TC-BVA-04 | 29,99 kg | sem sobretaxa |
| TC-BVA-05 | 30,00 kg | depende da regra (>= ou >); BVA expõe a ambiguidade |
| TC-BVA-06 | 30,01 kg | com sobretaxa |

**Observação importante**: o caso `TC-BVA-05` revelou que a especificação (“peso > 30 kg”) não é clara sobre o que acontece *exatamente* em 30 kg. BVA não só testa: também **força clarificação de requisitos**.

### (c) Tabela de decisão

Variáveis: valor (≥100 ou <100), região (Sudeste ou Outra), peso (≤30 ou >30) → 8 combinações.

| Caso | Valor ≥ 100 | Sudeste | Peso > 30 | Frete |
| --- | --- | --- | --- | --- |
| TC-DEC-01 | Não | Não | Não | R$ 25,00 |
| TC-DEC-02 | Não | Não | Sim | R$ 55,00 (R$25 + R$30) |
| TC-DEC-03 | Não | Sim | Não | R$ 15,00 |
| TC-DEC-04 | Não | Sim | Sim | R$ 45,00 (R$15 + R$30) |
| TC-DEC-05 | Sim | Não | Não | R$ 25,00 |
| TC-DEC-06 | Sim | Não | Sim | R$ 55,00 |
| TC-DEC-07 | Sim | Sim | Não | R$ 0 (grátis) |
| TC-DEC-08 | Sim | Sim | Sim | R$ 30,00 |

A tabela revela uma **regra implícita**: o caso 06 cobra R$ 55 (frete fora do Sudeste + sobretaxa), mas o caso 02 também — porque o valor do pedido não muda nada se for fora do Sudeste. A tabela explicita que “valor ≥ 100” só importa **combinado com Sudeste** — útil para reduzir casos redundantes em times maduros.

**Discussão:**

- **As três técnicas se complementam**: equivalência cobre amplitude (todas as classes), BVA aprofunda em fronteiras (onde bugs moram), tabela de decisão cobre combinações sistematicamente.
- **O total**: ~17-20 casos de teste cobrem essa funcionalidade de forma robusta — sem testar exaustivamente, mas sem deixar buracos óbvios.
- **A tabela de decisão expôs ambiguidades** (caso BVA-05) e redundâncias (a interação valor × região). Boas técnicas de design de teste **clarificam requisitos** como subproduto.
- **Onde automatizar?** Todos os 17 são **automatizáveis** em testes unitários da função `calcularFrete()` — cada um fica em uma linha de código de teste, e a suíte inteira roda em segundos. Esses são exatamente os testes de regressão que devem rodar a cada commit.

> 💡 **Intuição:** Boa engenharia de testes não é heroica — é metódica. Você não escreve “todos os casos que pensar” — você aplica técnicas que produzem casos sistematicamente, com confiança de que cobriu o essencial sem desperdício. O resultado é uma suíte que detecta regressões rápido e que outros engenheiros podem ler e entender.
> 

---

## Exercícios Práticos

### Exercício 1 — Esboço de plano de testes ⭐

Considere o seguinte sistema:

> **Sistema de gestão escolar para uma rede de cursos de idiomas** — funcionalidades: matrícula de alunos, alocação em turmas, registro de presença, lançamento de notas, geração de boletins, emissão de certificados de conclusão. Atende 12 unidades, 800 alunos ativos, 60 professores. Equipe de desenvolvimento de 4 pessoas (todas backend), 1 QA dedicado, prazo de 6 meses para v1.
> 

Sua tarefa:

1. Escreva o **escopo** do plano de testes em duas listas explícitas (em escopo / fora de escopo) — pelo menos 5 itens em cada.
2. Defina a **estratégia de testes** em até 6 linhas: que níveis serão executados, balanço manual/automatizado, técnicas principais.
3. Liste **5 critérios de saída objetivos e verificáveis**.
4. Identifique **3 riscos** que você antecipa para esse projeto, com mitigação concreta para cada um.
5. Discuta em 4-6 linhas: que **tipos transversais** de teste merecem atenção especial neste sistema, e por quê?

### Exercício 2 — Design completo de casos de teste ⭐⭐

Considere a funcionalidade de **autenticação** do nosso e-commerce, com as seguintes regras:

- Usuário tenta logar com e-mail e senha.
- Senha incorreta: incrementa contador de falhas; após 5 falhas em 15 minutos, conta fica bloqueada por 30 minutos.
- E-mail não cadastrado: retorna erro genérico (“Credenciais inválidas”) — sem revelar se o problema é o e-mail ou a senha (segurança).
- Login bem-sucedido: gera token JWT válido por 24h, registra IP e horário.
- Se o usuário tem 2FA ativo, após senha correta, exige código adicional.
- Senha expirada (mais de 90 dias): exige troca antes de prosseguir.

Sua tarefa:

1. Identifique e liste pelo menos **6 classes de equivalência** relevantes para esta funcionalidade. Para cada classe, indique o resultado esperado.
2. Identifique pelo menos **3 fronteiras** (limites) da especificação onde valores limítrofes devem ser testados. Liste os 3 valores a testar em cada fronteira (logo abaixo, no limite, logo acima).
3. Construa uma **tabela de decisão** com pelo menos **2 variáveis booleanas** (ex.: senha correta?, 2FA ativo?, senha expirada?, bloqueio ativo?) — escolha 3 dessas variáveis, gerando 8 combinações, e para cada uma indique o comportamento esperado.
4. Identifique pelo menos **dois testes de transição de estados** (estados típicos: Não Autenticado → Aguardando 2FA → Autenticado → Bloqueado). Descreva os passos.
5. Identifique **2 testes de segurança** específicos que deveriam estar no plano para esta funcionalidade — para cada um, descreva o cenário e o resultado esperado.
6. Discuta em pelo menos 5 linhas: dos casos que você projetou, quais devem ser **automatizados** e quais devem ser **manuais**? Justifique cada decisão com base nos critérios vistos na aula (frequência, determinismo, custo de manutenção).

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- ISO/IEC/IEEE 29119:2013 — *Software and systems engineering — Software testing*. (Substitui IEEE 829; padrão internacional contemporâneo para documentação de testes.)
- Myers, G.; Sandler, C.; Badgett, T. *The Art of Software Testing*. 3ª ed., Wiley, 2011. (Clássico fundador, ainda atual nas técnicas básicas.)
- Beizer, B. *Software Testing Techniques*. 2ª ed., Van Nostrand Reinhold, 1990. (Tratamento aprofundado de caixa-branca, cobertura, e teoria de teste.)
- Crispin, L.; Gregory, J. *Agile Testing: A Practical Guide for Testers and Agile Teams*. Addison-Wesley, 2009. (Contraponto ágil ao formalismo do IEEE; perspectiva de equipes ágeis.)
- Crispin, L.; Gregory, J. *More Agile Testing*. Addison-Wesley, 2014. (Sequência da obra anterior; aprofunda automação e testes não-funcionais.)
- Feathers, M. *Working Effectively with Legacy Code*. Prentice Hall, 2004. (Testes de caracterização para sistemas sem testes; ponte entre realidade legada e disciplinas de teste.)
- Khorikov, V. *Unit Testing: Principles, Practices, and Patterns*. Manning, 2020. (Tratamento contemporâneo e prático de testes unitários, com discussão valiosa sobre o que faz um teste ser de qualidade.)
- ISTQB — *Foundation Level Syllabus*, versão atual. (Referência de certificação; bom resumo conciso de conceitos e técnicas.)
- Petrović, G.; Ivanković, M.; Fraser, G.; Just, R. “Practical Mutation Testing at Scale”. *IEEE Transactions on Software Engineering*, 2022. (Estado da arte em mutation testing como alternativa à cobertura tradicional.)
- Cohn, M. *Succeeding with Agile: Software Development Using Scrum*. Addison-Wesley, 2009. (Capítulo sobre Definition of Done — origem prática do conceito em ágil.)

---

*Próxima aula: Engenharia de Requisitos — captura, modelagem e gestão de requisitos funcionais e não-funcionais; rastreabilidade requisito-teste; ligação entre o que se documenta e o que se valida.*