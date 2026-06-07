# Introdução a Devops

## 1. O que é DevOps?

### 1.1 Origens — o problema que DevOps resolve

Para entender DevOps é preciso entender o problema que ele resolve. Até meados dos anos 2000, em organizações de TI tradicionais, havia uma divisão estanque:

- **Dev** (desenvolvimento) era responsável por **escrever código novo** — entregar features, atender requisitos, fechar tickets.
- **Ops** (operações) era responsável por **manter sistemas estáveis** — uptime, backups, segurança, capacidade.

Os incentivos desses dois grupos **conflitavam**:

| Dev quer… | Ops quer… |
| --- | --- |
| Entregar features rápido | Mudanças raras (mudança = risco) |
| Liberdade para mexer | Controle rígido sobre produção |
| “Funciona na minha máquina” | “Cadê o documento de mudança?” |
| Tecnologias novas | Tecnologias estáveis e provadas |

O resultado clássico: deploys eram **eventos raros e dolorosos**, planejados com semanas de antecedência, executados em fins de semana, frequentemente revertidos. Code finishes ficavam em **filas de “homologação”** até que Ops aceitasse rodar. Quando algo dava errado, Dev culpava Ops e vice-versa.

### 1.2 O nascimento

Em 2009, na conferência *Velocity*, John Allspaw e Paul Hammond apresentaram a palestra histórica **“10+ Deploys per Day: Dev and Ops Cooperation at Flickr”** — mostrando que era possível fazer **dezenas de deploys por dia** com **alta estabilidade**, derrubando a falsa dicotomia *velocidade × confiabilidade*.

No mesmo ano, Patrick Debois cunhou o termo **DevOps** ao organizar a primeira *DevOpsDays* em Ghent, na Bélgica. O termo é deliberadamente uma fusão — **Dev + Ops** — sinalizando que a separação rígida entre os dois grupos era o problema, não a solução.

### 1.3 Definindo DevOps

Não há definição oficial única. Três caracterizações úteis e complementares:

> **DevOps é um conjunto de práticas que integra desenvolvimento de software (Dev) e operações de TI (Ops), buscando reduzir o ciclo de vida de desenvolvimento e fornecer entrega contínua com alta qualidade.** — definição operacional comum.
> 

> **DevOps é um movimento cultural focado em colaboração entre engenheiros de software e operadores ao longo de todo o ciclo de vida de serviços, do design ao desenvolvimento e à produção.** — Patrick Debois.
> 

> **DevOps é a aplicação dos princípios** Lean** ao fluxo de trabalho de TI.** — Gene Kim.
> 

Note como cada definição enfatiza algo diferente — **práticas** (a primeira), **cultura** (a segunda), **filosofia** (a terceira). DevOps é os três simultaneamente. Reduzi-lo a só ferramentas é a primeira armadilha.

> 💡 **Intuição:** DevOps **não é uma ferramenta**, **não é um cargo** e **não é um departamento**. Quando você ouve “vamos contratar um DevOps” ou “vamos criar um time de DevOps”, é sinal de que a organização entendeu errado. DevOps é uma forma de **organizar trabalho** — Dev e Ops colaboram em vez de jogar a bola por cima do muro.
> 

### 1.4 DevOps vs Agile vs Lean

A confusão também é frequente. As três filosofias se sobrepõem mas têm focos distintos:

| Filosofia | Foco principal | Fronteira que dissolve |
| --- | --- | --- |
| **Lean** (anos 1950+, Toyota) | Eliminar desperdício no fluxo de valor | Operário × Engenheiro |
| **Agile** (2001) | Responder a mudança em vez de seguir plano | Cliente × Desenvolvedor |
| **DevOps** (2009) | Integrar desenvolvimento e operação | Dev × Ops |

DevOps frequentemente é descrito como “aplicar Agile aos sistemas em produção” — Agile parou no *deploy*, e DevOps continua dali em diante. Lean é a filosofia mãe que inspira ambos.

---

## 2. Os Três Caminhos de Gene Kim

Gene Kim, em *The DevOps Handbook* e na trilogia ficcional *The Phoenix Project*, organiza os princípios DevOps em **três caminhos**. É a estrutura mental mais útil que conheço para a disciplina.

### 2.1 Primeiro Caminho — Fluxo

**Princípio**: o trabalho deve **fluir rapidamente do Dev para Ops para o cliente**, sem represamentos, sem retrabalho, sem dependências paralisantes.

**Práticas concretas**:
- Automatizar o pipeline de build/test/deploy.
- Reduzir o tamanho dos lotes (*small batches*) — entregar pequenas mudanças frequentemente.
- Tornar o trabalho visível (*kanban*, dashboards, métricas).
- Identificar e remover gargalos sistematicamente.
- Eliminar handoffs desnecessários — cada handoff é oportunidade de atraso e erro.

**Origem Lean**: o conceito de *flow* vem direto da Toyota Production System (TPS). Tornar o trabalho fluir é o oposto de “tornar cada estação eficiente isoladamente” — uma tradução direta de Lean para TI.

### 2.2 Segundo Caminho — Feedback

**Princípio**: criar **fluxos de feedback rápidos do downstream para o upstream** — para que problemas sejam detectados e corrigidos perto da origem, antes que se amplifiquem.

**Práticas concretas**:
- Testes automatizados em CI (feedback de minutos, não dias).
- Monitoring e observabilidade em produção.
- Alertas inteligentes (não barulho, não silêncio).
- *Postmortems* sem culpa após incidentes.
- Telemetria do produto chegando a desenvolvedores (não só a Ops).
- Pareamento de Dev e Ops em incidentes — o desenvolvedor que escreveu o código entende a falha em produção.

**Insight central**: defeitos detectados próximo da origem custam ordens de magnitude menos do que defeitos detectados em produção. Feedback lento amplifica problemas.

### 2.3 Terceiro Caminho — Aprendizado Contínuo

**Princípio**: criar uma **cultura de experimentação contínua, aprendizado e melhoria** — onde tirar sistematicamente lições do passado é parte do trabalho.

**Práticas concretas**:
- Reservar tempo para melhoria contínua (*kaizen*) — não só para entregar features.
- Transformar incidentes em **oportunidades de aprendizado** — postmortems compartilhados, lições documentadas.
- **Game days** e *chaos engineering* — provocar falhas controladas para aprender como o sistema responde.
- Compartilhamento de conhecimento dentro e entre equipes.
- Aceitar que **falhas são esperadas** em sistemas complexos — o foco é responder bem, não nunca falhar.

> 💡 **Intuição:** Os três caminhos não são fases sequenciais — são **dimensões simultâneas** de uma cultura saudável. Uma equipe pode ter pipeline impecável (primeiro caminho) e ainda ter cultura de culpa em incidentes (segundo caminho ferido) ou nenhum tempo para melhoria (terceiro caminho ausente). A maturidade DevOps é **balanço entre os três**, não excelência em um.
> 

---

## 3. CI / CD — três conceitos, frequentemente confundidos

A sigla “CI/CD” virou tão onipresente que esconde uma confusão importante. Há **três** conceitos distintos:

### 3.1 Continuous Integration (CI) — Integração Contínua

**O que é**: a prática de **integrar código de todos os desenvolvedores em uma branch principal** com alta frequência (idealmente várias vezes ao dia), validando cada integração com **build automatizado e testes automatizados**.

**Por que existe**: antes de CI, equipes faziam *integration hell* — desenvolvedores trabalhavam em *branches* longas (semanas, meses), e quando merge acontecia, o conflito era brutal. CI torna a integração **frequente e barata**.

**Pré-requisitos**:
- Repositório único versionado (Git, idealmente com *trunk-based development* ou GitFlow leve).
- Build automatizado — uma única entrada, um artefato pronto.
- Suíte de testes automatizada e rápida.
- Política: *broken build* trava o trabalho de todos até consertar.

CI é **antes do deploy**. O resultado de CI é um *artefato testado* — não um sistema em produção.

### 3.2 Continuous Delivery (CD-ent) — Entrega Contínua

**O que é**: a prática de manter o sistema em **estado deployável a qualquer momento**, com pipeline automatizado que leva o artefato de CI até **um ambiente de staging ou produção** — porém o **deploy final em produção** ainda requer **decisão humana**.

**Característica-chave**: você *pode* fazer deploy a qualquer momento. Você *escolhe* quando fazer.

Em código: o pipeline para antes do último estágio, aguardando *approval*.

```
[Commit] → CI → Build → Tests → Staging → [⏸ Aprovação manual] → Produção
```

### 3.3 Continuous Deployment (CD-ploy) — Implantação Contínua

**O que é**: cada commit que passa por todas as fases de teste **vai automaticamente para produção**, sem intervenção humana.

**Característica-chave**: a única coisa entre o commit e produção são os testes.

```
[Commit] → CI → Build → Tests → Staging → Produção (automático)
```

A diferença frente à entrega contínua é a aprovação humana — e ela é **enorme** culturalmente. Implantação contínua exige confiança extrema na suíte de testes, observabilidade robusta, capacidade de reverter rápido.

### 3.4 Quadro comparativo

|  | Integração Contínua | Entrega Contínua | Implantação Contínua |
| --- | --- | --- | --- |
| **Sigla** | CI | CD (Delivery) | CD (Deployment) |
| **Onde para** | Após testes em CI | Antes da produção | Não para |
| **Aprovação humana?** | N/A | Sim, antes do prod | Não |
| **Frequência típica de prod** | N/A | Semanal/mensal | Múltiplos por dia |
| **Maturidade** | Base | Intermediária | Avançada |

### 3.5 Pipeline típico

Um pipeline maduro tem múltiplos estágios, cada um sendo um *gate*:

```
   Commit
      ↓
   1. Lint + format check (segundos)
      ↓
   2. Build (1-3 min)
      ↓
   3. Unit tests (segundos a 1 min)
      ↓
   4. Integration tests (1-5 min)
      ↓
   5. Static analysis / security scan
      ↓
   6. Build container image
      ↓
   7. Deploy → ambiente de QA
      ↓
   8. E2E tests + smoke tests
      ↓
   9. Deploy → staging
      ↓
   10. [Aprovação manual ou automática]
      ↓
   11. Deploy → produção (canary, blue-green ou rolling)
      ↓
   12. Smoke tests pós-deploy
      ↓
   13. Métricas observadas por X tempo
      ↓
   14. Promoção total (ou rollback)
```

Cada estágio é executado em **paralelo onde possível**. Falha em qualquer estágio **interrompe a pipeline** — princípio do *fail fast*.

> 💡 **Intuição:** O pipeline de CI/CD é a **linha de produção da fábrica de software**. Em uma fábrica de carros, você não monta o motor sem testar peças individuais; não pinta antes de montar; não envia ao cliente sem inspeção final. O pipeline é exatamente isso. Deploys que pulam estágios são equivalentes a carros saindo da fábrica sem inspeção final — funcionam até o dia em que não funcionam, e aí o custo é gigantesco.
> 

---

## 4. Estratégias de deploy

Como você vai do **artefato testado** ao **sistema em produção** sem quebrar usuários? Há várias estratégias, cada uma com tradeoffs distintos.

### 4.1 Recreate (substituição completa)

A estratégia mais simples: **derrube a versão antiga, suba a nova**. Há um período de indisponibilidade.

```
Versão A rodando → [Indisponível] → Versão B rodando
```

Funciona para sistemas que toleram **janela de manutenção** — sistemas internos, fim de semana, fora do horário comercial. Para um e-commerce 24/7, é inaceitável.

### 4.2 Rolling deployment

Atualiza **gradualmente** as instâncias — uma de cada vez, ou um lote por vez. Em qualquer momento, parte das instâncias roda v1 e parte roda v2.

```
[A][A][A][A] → [A][A][A][B] → [A][A][B][B] → [A][B][B][B] → [B][B][B][B]
```

Sem indisponibilidade, mas:
- Por um período, **duas versões coexistem** atendendo usuários — o que pode quebrar funcionalidades dependentes de versão.
- Rollback é **lento** (precisa fazer rolling para trás).

### 4.3 Blue-Green deployment

Mantenha **dois ambientes idênticos** — *blue* (versão atual em produção) e *green* (nova versão sendo testada). Quando *green* está pronto, troca-se o roteamento — o tráfego inteiro passa de *blue* para *green* instantaneamente.

```
   Tráfego → Blue (v1) ✓        Green (v2) — testado, parado
       ↓ [switch]
   Tráfego → Blue (v1) — parado   Green (v2) ✓
```

**Vantagens**:
- Indisponibilidade zero.
- Rollback **instantâneo** — basta voltar o switch para *blue*.
- Possibilidade de testar *green* completamente antes de receber tráfego.

**Desvantagens**:
- Custo: dois ambientes em paralelo.
- Complicações com banco de dados (migrações precisam ser compatíveis com ambas as versões).

### 4.4 Canary deployment

A versão nova é exposta a uma **fração pequena** de usuários (ex.: 5%). Se métricas seguem normais, vai gradualmente expondo para 25%, 50%, 100%.

```
[5% v2 / 95% v1] → métricas OK → [25% / 75%] → [50% / 50%] → [100% v2]
                               ↘ métricas ruins → rollback
```

Originário das **minas de carvão** — mineiros levavam canários nas gaiolas; se o pássaro morria, era sinal de gás tóxico, e os mineiros saíam. O *canary deployment* opera com a mesma metáfora: a fração inicial são os “canários” — se eles “morrem” (métricas pioram), você sabe antes de afetar a maioria.

**Vantagens**:
- **Risco minimizado**: bug afeta poucos usuários, é detectado cedo.
- Testes reais com tráfego de produção (impossível de simular em staging).

**Desvantagens**:
- Complexidade de roteamento (Istio, service mesh, balanceadores sofisticados).
- Métricas precisam ser sólidas para detectar problemas precocemente.

### 4.5 Feature flags (toggle)

Estratégia diferente em natureza: a **nova versão é deployada para todos**, mas a **funcionalidade está desligada** atrás de um *flag*.

```java
if (featureFlag.isOn("novo_checkout", clienteId)) {
    return novoCheckout.processar(pedido);
} else {
    return checkoutLegado.processar(pedido);
}
```

A funcionalidade é **ligada gradualmente** — para 1% dos usuários, depois 10%, depois 100%. Ferramentas: LaunchDarkly, Split.io, Unleash, ou implementação caseira.

**Vantagens**:
- Desacopla **deploy** (técnico) de **release** (de negócio).
- Permite *A/B testing* nativamente.
- *Kill switch* — se algo der errado, desliga o flag (sem deploy).
- **Trunk-based development** real é viável: features inacabadas vão para produção atrás de flags desligados.

**Desvantagens**:
- Crescimento de **dívida de flags** — cada `if (flag)` é complexidade. Política de remoção é essencial.
- Necessidade de testar **múltiplos caminhos** (flag on, flag off, combinações).

### 4.6 Quadro de escolha

| Estratégia | Indisponibilidade | Risco | Custo | Complexidade |
| --- | --- | --- | --- | --- |
| Recreate | Sim | Alto | Baixo | Trivial |
| Rolling | Não | Médio | Baixo | Baixa |
| Blue-Green | Não | Baixo | Alto | Média |
| Canary | Não | Muito baixo | Médio | Alta |
| Feature flags | Não | Configurável | Baixo (infra) | Média (código) |

A escolha depende do **risco aceitável**, **orçamento** e **complexidade que a equipe pode suportar**. Sistemas pequenos podem viver com rolling. Sistemas críticos mesclam canary + feature flags. Não há resposta universal.

> 💡 **Intuição:** Note como cada estratégia desacopla algo. Blue-Green desacopla *novo deploy* de *receber tráfego*. Canary desacopla *expor para 100%* de *expor para alguns*. Feature flags desacoplam *deploy* de *ativação de funcionalidade*. Cada uma é uma forma de **reduzir risco através de desacoplamento temporal** — o oposto do “big bang deploy”.
> 

---

## 5. Infraestrutura como Código (IaC)

### 5.1 O conceito

Tradicionalmente, infraestrutura era criada **manualmente** — alguém de Ops conectava em servidores, instalava pacotes, configurava arquivos. Isso era:

- **Não-replicável**: cada servidor era ligeiramente diferente (*snowflake servers*).
- **Não-auditável**: quem mudou o quê, quando, por quê?
- **Não-versionável**: voltar a um estado anterior era arqueologia.
- **Lento**: dias ou semanas para subir um ambiente novo.

**IaC inverte o jogo**: a infraestrutura é descrita em **arquivos versionados** (em Git, junto com código). Ferramentas leem esses arquivos e materializam a infraestrutura.

### 5.2 Exemplo conceitual

```yaml
# infrastructure.yml (formato fictício, simplificado)
servers:
web:
count:3
instance_type: t3.medium
image: ubuntu-22.04
packages:
- nginx
- certbot
config_files:
-/etc/nginx/nginx.conf:"{{ template_nginx }}"

database:
count:1
instance_type: db.r5.large
engine: postgres-16
backup_retention_days:30
```

Uma ferramenta lê isso e — idempotentemente — cria os recursos no provedor de cloud (AWS, GCP, Azure, etc.). Rodar duas vezes dá o mesmo resultado. Versionar permite voltar a qualquer estado anterior.

### 5.3 Categorias de ferramentas

Há duas grandes famílias:

### 5.3.1 Provisionamento (criar recursos)

- **Terraform** (HashiCorp) — domina o mercado; multi-cloud; declarativo (HCL).
- **Pulumi** — IaC em linguagens reais (Python, TypeScript, Go) em vez de DSL.
- **AWS CloudFormation** — específico AWS.
- **Azure Resource Manager**, **Google Deployment Manager** — equivalentes nativos.

### 5.3.2 Configuração (estado dentro dos recursos)

- **Ansible** — agentless, popular para configuração de servidores.
- **Puppet**, **Chef** — pioneiros, ainda usados em organizações maduras.
- **SaltStack** — alternativa.

A divisão tende a se diluir com **contêineres** (Docker, OCI) e **orquestração** (Kubernetes), onde a configuração frequentemente vai junto com o artefato.

### 5.4 Imutabilidade

Princípio relacionado: **infraestrutura imutável**. Em vez de modificar servidores existentes, você **destrói e recria**. Isso elimina o problema de *drift* (servidores que vão divergindo com o tempo).

Containers são imutáveis por natureza. Kubernetes leva esse princípio a sério: pods morrem e renascem, e isso é **esperado**, não exceção.

### 5.5 GitOps

Evolução natural: a infraestrutura é descrita em Git, e **o estado real do cluster é continuamente reconciliado** com o estado descrito no repositório. Mudanças em produção sem ir pelo Git são detectadas e revertidas automaticamente.

Ferramentas: ArgoCD, Flux. Princípio central — **Git é a única fonte da verdade**.

> 💡 **Intuição:** IaC é a aplicação direta do princípio **“trate operações como software”**. Antes, infraestrutura era trabalho manual, com toda a fragilidade, falta de auditoria e não-reprodutibilidade que isso implica. IaC traz para infraestrutura tudo que aprendemos sobre código: versionamento, revisão, testes, refatoração. Quando bem feito, subir um clone do ambiente de produção vira **questão de minutos**, não semanas.
> 

---

## 6. Observabilidade

### 6.1 Monitoring vs Observability

Termos relacionados, conceitos distintos:

- **Monitoring** (monitoramento): coleta de métricas pré-definidas para verificar se o sistema está em estado conhecido — saudável ou não. Foco: **perguntas conhecidas** (“CPU está acima de 80%?”).
- **Observability** (observabilidade): capacidade de **inferir o estado interno** do sistema a partir de seus *outputs* externos. Foco: **perguntas novas** (“por que esse cliente específico está vendo lentidão no checkout às 14h ontem?”).

Sistemas distribuídos modernos exigem observabilidade — não apenas monitoring — porque o número de combinações de estado é maior do que se pode antecipar.

### 6.2 Os três pilares

Observabilidade é construída em três tipos de sinal:

### 6.2.1 Logs

Eventos discretos com timestamp e contexto. Tradicionalmente, texto livre. Modernamente, **logs estruturados** (JSON) — chave-valor que ferramentas indexam.

```json
{
  "timestamp": "2026-04-29T14:23:15.342Z",
  "level": "ERROR",
  "service": "checkout",
  "trace_id": "abc-123",
  "user_id": "user-42",
  "message": "Falha na autorização do pagamento",
  "gateway": "Cielo",
  "error_code": "DECLINED_INSUFFICIENT_FUNDS"
}
```

Ferramentas: ELK Stack (Elasticsearch + Logstash + Kibana), Loki, Splunk, Datadog Logs.

### 6.2.2 Métricas

Valores numéricos agregados ao longo do tempo. Usadas para alertas e dashboards.

```
http_requests_total{service="checkout", status="200"} = 1453
http_requests_total{service="checkout", status="500"} = 12
checkout_latency_seconds{percentile="95"} = 0.234
```

Ferramentas: Prometheus (de longe o mais popular em ambientes cloud-native), Datadog, New Relic, CloudWatch.

### 6.2.3 Traces (rastreamento distribuído)

Em sistemas com múltiplos serviços, uma requisição atravessa vários componentes. *Tracing* mostra **a árvore de chamadas**:

```
[Frontend]──┐
            ↓
       [API Gateway] (12ms)
            ↓
     ┌──[Auth Service] (8ms)
     │      ↓
     │ ┌──[User DB] (3ms)
     │ │
     ↓ ↓
   [Catalog Service] (45ms)
        ↓
   [Catalog DB] (40ms)        ← gargalo!
        ↓
   [Checkout Service] (15ms)
        ↓
   [Payment Gateway] (180ms)  ← latência externa
```

Cada caixa é um *span* — uma operação com duração. *Spans* relacionados pertencem ao mesmo *trace* (identificado por `trace_id`).

Ferramentas: Jaeger, Zipkin, Datadog APM, Tempo. Padrão emergente: **OpenTelemetry** — instrumentação unificada para os três pilares.

### 6.3 Os “Four Golden Signals” (Google SRE)

Quais métricas observar primeiro? O livro do Google sobre SRE propõe quatro sinais essenciais:

| Sinal | O que mede |
| --- | --- |
| **Latency** | Tempo para atender uma requisição |
| **Traffic** | Quanta demanda o sistema está recebendo (req/s) |
| **Errors** | Taxa de requisições que falharam |
| **Saturation** | Quão “cheio” o sistema está (CPU, memória, fila) |

Para qualquer serviço, esses quatro sinais cobrem a maior parte dos problemas operacionais.

### 6.4 SLI, SLO, SLA

Ainda do mundo SRE, três siglas relacionadas:

- **SLI** (Service Level Indicator): a *medida*. Ex.: “% de requisições com latência < 200ms”.
- **SLO** (Service Level Objective): o *alvo interno*. Ex.: “99,9% das requisições devem ter latência < 200ms ao longo de 30 dias”.
- **SLA** (Service Level Agreement): o *contrato externo* com cliente, com **consequências se não cumprido** (créditos, multas).

A regra prática: **SLA é mais frouxo que SLO**, porque você quer descobrir e corrigir problemas internamente antes que virem violação contratual.

### 6.5 Error Budget

Conceito derivado: se seu SLO é 99,9%, você tem um **orçamento de 0,1% de “falhas aceitáveis”** — cerca de 43 minutos por mês. Esse é seu *error budget*.

A consequência cultural: enquanto há *budget*, a equipe pode arriscar (deploys mais agressivos, experimentos). Quando o *budget* se esgota, **freezes** entram — só correções, sem features novas, até a saúde voltar.

Isso transforma uma discussão polêmica (“quando devo parar de empurrar features?”) em **dado objetivo**.

> 💡 **Intuição:** Boa observabilidade transforma “o sistema está com problema?” em uma pergunta **respondível em segundos**. Más observabilidade transforma cada incidente em arqueologia. A qualidade da observabilidade é o que diferencia equipes que lidam bem com produção de equipes que apagam incêndios eternamente.
> 

---

## 7. SRE — Site Reliability Engineering

### 7.1 O que é

**SRE** é uma abordagem operacional originária do Google, formalizada em 2003. O princípio central é radical:

> Operações é problema de software. Resolva com software.
> 

Equipes SRE são **equipes de engenharia** — engenheiros de software com expertise operacional, que aplicam disciplinas de Dev a problemas de Ops.

### 7.2 Princípios

- **Engenheiros**, não operadores. SREs codificam.
- ***Toil* (trabalho repetitivo)** é inimigo. Tudo que é feito mais de uma vez deve ser automatizado.
- **Limite de toil**: tipicamente 50%. SREs gastam pelo menos metade do tempo em **engenharia** (automação, ferramentas), não respondendo a alertas.
- **Erro humano é sintoma, não causa**. Se humanos errando produção causa incidente, o sistema permitiu — corrija o sistema.
- **SLOs guiam decisões**, não opiniões.

### 7.3 SRE vs DevOps

Confusão comum. A relação prática:

- **DevOps** é uma **filosofia/cultura** — “Dev e Ops colaboram”.
- **SRE** é uma **implementação concreta** dessa filosofia, com práticas específicas (SLOs, error budget, toil cap).

O Google diz que “SRE é a forma como o Google faz DevOps”. Outras organizações fazem DevOps de outras formas.

### 7.4 You build it, you run it

Princípio cunhado por Werner Vogels (Amazon, 2006):

> *“Giving developers operational responsibilities has greatly enhanced the quality of the services, both from a customer and a technology point of view.”*
> 

Ou seja: **a equipe que constrói o serviço também o opera em produção**. Quando o pager toca às 3h da manhã, é o desenvolvedor que escreveu o código que atende — não Ops.

Consequências práticas:
- Devs sentem **dor real** dos problemas operacionais → escrevem código mais operável.
- Não há “muro” entre Dev e Ops — só há **a mesma equipe** com dois chapéus.
- Equipes pequenas, autônomas, *full-stack* são o desenho organizacional natural.

Esta é a versão **mais pura** de DevOps. Nem toda organização chega lá — algumas mantêm SRE como time separado, mediando entre Dev (autoria) e produção (operação).

---

## 8. Métricas DORA

Como medir maturidade DevOps? O grupo **DORA** (DevOps Research and Assessment), liderado por Nicole Forsgren e depois adquirido pelo Google, identificou **quatro métricas** que distinguem times de alta performance:

### 8.1 As quatro métricas

| Métrica | O que mede | Elite vs baixa |
| --- | --- | --- |
| **Deployment Frequency** | Frequência de deploys em produção | Múltiplos/dia vs raros |
| **Lead Time for Changes** | Tempo do commit ao deploy em prod | <1h vs >6 meses |
| **Change Failure Rate** | % de deploys que causam incidentes | 0-15% vs 60%+ |
| **Time to Restore** (MTTR) | Tempo para recuperar de uma falha | <1h vs >6 meses |

A pesquisa é robusta — anos de dados, milhares de organizações. O ponto crucial: **as quatro métricas estão correlacionadas**. Times que entregam frequentemente também recuperam rapidamente. Velocidade e estabilidade não são tradeoff — são **resultados da mesma cultura**.

### 8.2 Implicação contraintuitiva

Por décadas se acreditou: “para ter estabilidade, deploys raros e cuidadosos”. O DORA mostra o contrário: **deploys raros causam mais incidentes**, não menos. Por quê?

- Deploys grandes acumulam mudanças → mais coisas podem dar errado.
- Equipes que deployam pouco perdem **prática** de deployar — quando precisam, é evento.
- Sistemas que não recebem mudança regularmente acumulam **dívida operacional invisível**.

Times maduros descobriram: **a forma de deployar com segurança é deployar com frequência**. Pequenos lotes, automatização, observabilidade. Mais deploy, menos incidente.

### 8.3 Dois extremos a evitar

- **Otimizar só velocidade**: deploys frequentes mas com alta taxa de falha → sistema instável.
- **Otimizar só estabilidade**: deploys raros mas perfeitos → equipe lenta, irrelevante para o negócio.

A meta é **as quatro métricas simultaneamente** em níveis altos.

---

## 9. Cultura — o lado mais difícil

A maioria dos artigos sobre DevOps trata de ferramentas. **Mas o lado difícil é cultural** — e é onde organizações falham mesmo tendo todas as ferramentas certas.

### 9.1 Blameless postmortems

Após um incidente, é tentador encontrar “quem causou”. A cultura DevOps madura **rejeita** essa abordagem.

**Princípio**: humanos racionais, agindo com a informação que tinham, fizeram o que fizeram. **Se um humano causou incidente, o sistema permitiu** — e é o sistema que deve ser corrigido.

Postmortem blameless típico tem:
- **Linha do tempo factual**: o que aconteceu, em que ordem.
- ***Root cause analysis* sistêmica**: por que? por que? por que? (5 *whys* da Toyota)
- **Ações de melhoria**: o que vai mudar para que esse tipo de incidente não recorra (não a punição, mas a melhoria do sistema).
- **Ausência de nomes individuais como culpados**.

A primeira vez que uma organização faz isso é desconfortável. Depois, vira **acervo precioso de aprendizado**.

### 9.2 Anti-padrões culturais

- **“Vamos contratar um DevOps”**: cargo individual, frequentemente reduzido a “quem cuida do Jenkins”. Perde-se a colaboração.
- **“Time de DevOps”**: novo silo entre Dev e Ops, em vez de dissolver os silos existentes.
- **DevOps via decreto**: “a partir de segunda, somos DevOps” — sem formação, sem mudança de incentivos, sem tempo para aprender.
- ***Cargo cult* de ferramentas**: comprou o Datadog, achou que adotou observabilidade. Comprou Kubernetes, achou que adotou containerização. Ferramentas seguem cultura, não a precedem.
- **Métricas viradas meta única**: equipe é cobrada por “deploys por dia”. Vira foco — deploya por deployar, sem entregar valor.
- **Cultura de heroísmo**: “essa pessoa salvou a produção sozinha às 3h” virá história celebrada. Sintoma terrível: a infraestrutura depende de heróis. **Resilência ≠ heroísmo**.

### 9.3 O que muda culturalmente

Em organizações DevOps maduras:
- Equipes são **multidisciplinares e autônomas** — têm tudo que precisam para entregar valor sem dependência externa.
- **Falhas são esperadas** — o foco é responder bem, não nunca falhar.
- **Aprendizado é parte do trabalho** — tempo para postmortem, para refatorar, para experimentar é orçado.
- **Métricas são para a equipe**, não contra a equipe.

> 💡 **Intuição:** Se a sua organização tem postmortem blameless, *toil* abaixo de 50%, deploys diários sem drama, e dev no pager — você está em uma cultura DevOps madura. Se está fazendo CI mas continua fazendo deploy mensal de big-bang, em fim de semana, com janela de manutenção, mesmo com Jenkins/Kubernetes/Terraform — você tem **as ferramentas mas não a cultura**. E a cultura é o que importa.
> 

---

## 10. Onde DevOps encaixa na arquitetura

Voltamos por um momento à aula de arquitetura, fechando o ciclo.

DevOps **influencia decisões arquiteturais** — e *vice-versa*:

- **Microsserviços** se tornam viáveis com pipelines maduros. Sem CI/CD, gerenciar 30 serviços é caos.
- **Hexagonal/Ports&Adapters** facilita testes em CI — núcleo testável sem subir banco/rede.
- **Event-driven** demanda observabilidade forte (tracing distribuído).
- **Infraestrutura imutável** dialoga com **stateless services** — uma escolha arquitetural.
- **Feature flags** são, ao mesmo tempo, prática DevOps e padrão arquitetural.

A inversa também é verdadeira:
- Arquiteturas **fortemente acopladas** travam entrega contínua — não importa o pipeline.
- Sistemas com **estado compartilhado massivo** (banco gigante único) tornam blue-green pesado.
- **Monólitos modulares** bem-feitos podem ter ótimo DevOps; **monólitos bagunçados**, não.

A frase de Werner Vogels (“you build it, you run it”) implica: **arquitetar bem é também arquitetar para operar bem**. Sistemas difíceis de operar acabam sendo abandonados, não importa quão elegantes no diagrama.

---

## 11. Exemplos resolvidos

### Exemplo Resolvido 1 — Mapeando o pipeline de CI/CD do e-commerce (Introdutório) ⭐

**Enunciado:** Para o nosso e-commerce monolítico tradicional (Java/Spring, PostgreSQL, frontend React), descreva um **pipeline completo de CI/CD** com pelo menos 8 estágios. Para cada estágio, indique: (a) o que faz, (b) ferramenta-tipo, (c) duração esperada, (d) o que acontece em caso de falha.

**Solução:**

```
[Commit em branch feature]
        ↓
[Pull Request aberto]
        ↓
=== CI ===
        ↓
1. Lint + format check
        ↓
2. Build do backend (Maven) + frontend (npm)
        ↓
3. Testes unitários (backend)
        ↓
4. Testes unitários (frontend)
        ↓
5. Static analysis + security scan
        ↓
6. Build de container image
        ↓
[Merge para main]
        ↓
=== CD-delivery ===
        ↓
7. Deploy automático em QA
        ↓
8. Testes de integração
        ↓
9. Testes E2E + smoke tests
        ↓
10. Deploy em staging
        ↓
=== Aprovação ===
        ↓
11. [Aprovação manual de PO/Tech Lead]
        ↓
=== CD-deployment ===
        ↓
12. Deploy canary em produção (5% tráfego)
        ↓
13. Monitoramento de métricas (10 min)
        ↓
14. Promoção total ou rollback
```

**Tabela de estágios:**

| # | Estágio | Ferramenta-tipo | Duração | Em caso de falha |
| --- | --- | --- | --- | --- |
| 1 | Lint + format | ESLint, Checkstyle, Prettier | <30s | Bloqueia PR; dev corrige local |
| 2 | Build | Maven, npm, GitHub Actions | 1-3 min | Bloqueia PR |
| 3 | Unit tests backend | JUnit 5 + Mockito | 30s-1min | Bloqueia PR; mostra qual teste quebrou |
| 4 | Unit tests frontend | Jest + React Testing Library | 1-2 min | Bloqueia PR |
| 5 | Static analysis + security | SonarQube, Snyk, OWASP DC | 2-3 min | Bloqueia se critical issue |
| 6 | Container image | Docker, BuildKit | 1-2 min | Bloqueia PR |
| 7 | Deploy QA | ArgoCD, Helm, kubectl | 1 min | Avisa equipe; QA não recebe versão |
| 8 | Testes de integração | Testcontainers, REST Assured | 3-5 min | Bloqueia PR; ambiente de QA mantido para investigação |
| 9 | E2E + smoke | Cypress, Playwright | 5-10 min | Bloqueia merge; screenshots/vídeos para análise |
| 10 | Deploy staging | ArgoCD | 1 min | Alerta SRE |
| 11 | Aprovação manual | GitHub Environments, Slack approval | Variável | Bloqueia até decisão |
| 12 | Canary 5% | Istio, AWS App Mesh | 1-2 min | Rollback automático se métricas degradarem |
| 13 | Monitoring | Prometheus + Grafana | 10 min | Rollback automático em alerta |
| 14 | Promoção total | Istio (ajuste de pesos) | Instantâneo | Manter canary se não confiável |

**Discussão:**

- **Estágios 1-9 são CI estrito**: garantem que o que entra na main está saudável.
- **Estágio 11 (aprovação)** é o que faz dele *Continuous Delivery* (humano decide quando vai para produção). Removendo o estágio 11, vira *Continuous Deployment*.
- **Canary + monitoring** automatizam o “deploy seguro”: menos de 5% dos usuários veem versão nova até 10 minutos sem incidente.
- **O total** do commit ao deploy total em produção: cerca de **20-30 minutos** com aprovações ágeis. Comparado a “release mensal de 2 dias” do mundo pré-DevOps, é uma ordem de magnitude diferente.
- **O custo principal**: testes precisam ser rápidos e estáveis. Suíte E2E *flaky* destrói esse pipeline imediatamente.

### Exemplo Resolvido 2 — Lidando com um deploy ruim usando feature flags + canary (Intermediário) ⭐⭐

**Enunciado:** O time vai lançar uma **nova engine de cálculo de impostos** no e-commerce — refatoração completa do módulo de impostos, que afeta TODOS os pedidos do Brasil. A equipe está nervosa: bug aqui significa pedidos com valor errado, problemas com a Receita Federal, e dor de cabeça contábil. Descreva uma estratégia de deploy combinando **feature flags + canary deployment**, incluindo plano de rollback, e justifique cada decisão.

**Solução:**

### Estratégia escolhida: feature flag + canary, em fases

### Fase 0 — Preparação

Antes do deploy, garantir:

1. **Suíte de testes do novo módulo**: cobertura unitária ≥ 95% no novo código; testes de regressão comparando saídas da engine antiga e nova para um conjunto de 1.000 cenários reais (gerados a partir de dados anonimizados de produção dos últimos 90 dias).
2. **Sombra (shadow traffic)**: a nova engine **roda em paralelo** com a antiga em produção, mas seu resultado é **descartado**. Apenas a saída da engine antiga é usada. Comparações entre as duas são logadas para identificar divergências.
    
    Esse “modo sombra” tipicamente roda **por 7-14 dias** antes de qualquer corte real. É como um teste integrado a tráfego real, sem risco para o usuário.
    
3. **Feature flag** `nova_engine_impostos` criado, **desligado** para todos os usuários. Toda a infraestrutura de toggle e segmentação por usuário pronta.
4. **Dashboard dedicado**: métricas específicas — taxa de divergência, latência da nova engine, taxa de erros, valor médio calculado.
5. ***Runbook* de rollback**: documento curto, anexado ao deploy, descrevendo passo a passo como desligar o flag em ≤ 60 segundos.

### Fase 1 — Deploy do código (sem ativação)

Deploy do código novo para produção, mas com `nova_engine_impostos = OFF` para todos.

- Os 100% dos usuários ainda usam a engine antiga.
- A nova engine é exercitada apenas pelo *shadow traffic* (passo 2 acima).
- Coleta-se 7 dias de divergências; se houver, investiga-se cada uma — bugs em qualquer das duas engines podem ser identificados.

**Critério de saída desta fase**: < 0,1% de divergências, sem padrão sistemático.

### Fase 2 — Canary 1% (apenas funcionários internos)

Liga-se o flag para **uma lista de usuários internos** (funcionários da empresa que aceitaram participar do teste). ~50 pessoas.

- Por **2-3 dias**, esses usuários fazem checkouts reais usando a nova engine.
- Bugs visíveis emergem se houver — funcionários relatam diretamente.
- Métricas observadas: latência, taxa de erros, valores anômalos.

**Critério de saída**: zero bugs reportados com severidade Alta+; métricas dentro de SLOs.

### Fase 3 — Canary 5% (usuários reais)

Liga-se o flag para 5% dos usuários reais — segmentados de forma aleatória mas estável (mesmo usuário sempre cai no mesmo grupo, evitando inconsistência percebida).

- **48 horas** de observação contínua.
- **Comparação A/B**: tickets de suporte, taxa de cancelamento, tempo de checkout, valor médio do pedido.

**Critério de saída**: nenhuma métrica de negócio piora significativamente vs grupo de controle.

### Fase 4 — Canary 25% → 50% → 100%

Aumentos graduais com pelo menos 24h entre cada um, observando métricas.

### Plano de rollback

A cada fase, **rollback é desligar o flag**:

```
[Operador no PagerDuty / Slack] → /feature_flag set nova_engine_impostos=OFF
```

Tempo total: ~30 segundos. Imediatamente, **todos os checkouts voltam a usar a engine antiga**, sem deploy, sem restart, sem janela de manutenção.

Esse é o **superpoder do feature flag**: rollback é configuração, não deploy.

### Pós-100% — limpeza

Após estabilização (tipicamente 30-60 dias após 100%):

1. Remove-se o código da engine antiga.
2. Remove-se o feature flag (a “dívida de flag”).
3. Atualiza-se documentação.
4. Postmortem de retrospectiva: o que funcionou? o que mudaria?

**Discussão das decisões:**

- ***Shadow traffic* primeiro** — porque o risco de bug em cálculo financeiro é muito alto, e shadow traffic dá comparação real sem nenhum risco para o usuário.
- **Canary com funcionários antes de usuários** — eles toleram mais bugs e relatam melhor.
- **Aumento gradual com observação entre fases** — se houver problema, ele aparece em pequena fração antes de afetar a maioria.
- **Rollback via flag, não deploy** — porque velocidade é crítica em incidentes financeiros. Deploy de rollback levaria 5-10 minutos; flag leva 30 segundos.
- **Limpeza explícita** — feature flags acumulam dívida. Deixar o flag para sempre é problema futuro.

**Por que não simplesmente blue-green?**

Blue-green tem **rollback rápido** (segundos), mas:
- Troca **todos** os usuários de uma vez. Bug afeta 100% até o switch reverso.
- Não permite “ver como a nova versão se comporta com tráfego real *gradual*”.
- Combina mal com banco de dados — duas versões usando mesmo banco precisam ser **compatíveis**, o que limita refatoração.

A combinação **flag + canary** é mais sofisticada mas mais segura para mudanças críticas.

> 💡 **Intuição:** Note como o plano não é “deploy quase perfeito”. Ele assume que **algo vai dar errado** e organiza o deploy para que esse “algo” seja **detectado cedo**, **afete pouca gente**, e seja **revertido em segundos**. Essa é a mentalidade DevOps madura — não evitar falhas, mas tornar falhas baratas.
> 

---

## Exercícios Práticos

### Exercício 1 — Diagnóstico DevOps de uma equipe ⭐

Considere a seguinte equipe:

> A equipe tem 8 desenvolvedores e 1 pessoa de operações (“o cara que sabe deploy”). Eles usam Git, mas trabalham em *branches* longas — features ficam 3-6 semanas em branch antes de mergear. Deploys em produção acontecem a cada 6 semanas, em uma janela de manutenção noturna sábado, e tipicamente duram 3-4 horas. A taxa de rollback é alta — cerca de 1 em cada 3 deploys precisa ser revertido. O monitoramento existe (Datadog), mas tickets do suporte costumam ser o primeiro sinal de problema em produção. Quando há incidente, todos param tudo até resolver. Após o incidente, é comum ouvir “foi o fulano que mexeu naquele arquivo” — e o postmortem (quando acontece) lista a “raiz” como erro humano. Eles têm Jenkins rodando builds, mas testes E2E são manuais.
> 

Sua tarefa:

1. Avalie esta equipe nas **quatro métricas DORA** — para cada métrica, classifique como elite/alta/média/baixa, e justifique brevemente.
2. Identifique **pelo menos 5 anti-padrões DevOps** presentes na descrição. Para cada um, cite o nome do anti-padrão e o trecho da descrição onde aparece.
3. Para os **três princípios de Gene Kim** (fluxo, feedback, aprendizado), avalie em qual deles essa equipe está **mais ferida**. Justifique.
4. Proponha **um plano de melhoria gradual de 6 meses** com pelo menos **5 mudanças concretas**. Para cada mudança: (1) o que muda, (2) qual problema endereça, (3) qual seria a prioridade (alta/média/baixa) e ordem de implementação.
5. Discuta em pelo menos 5 linhas: por que é provável que **simplesmente comprar Kubernetes não vai resolver os problemas dessa equipe**? Use os conceitos de cultura DevOps vistos na aula.

### Exercício 2 — Escolhendo estratégias de deploy ⭐⭐

Para cada um dos seguintes cenários, **escolha a estratégia de deploy mais adequada** (recreate, rolling, blue-green, canary, feature flag, ou combinação) e justifique sua escolha em 4-6 linhas. Inclua:
- Por que essa estratégia é adequada ao contexto.
- Que riscos ela mitiga.
- Quais cuidados especiais são necessários.
- Plano de rollback.

1. **Hotfix de segurança crítica** (CVE recém-divulgado afeta o framework — patch de uma linha). Sistema é uma API pública usada por parceiros B2B. Janela de exploração estimada: horas.
2. **Mudança de UI**: novo design da página inicial do e-commerce. Funcionalmente idêntico, apenas estética. PO quer “ver como os usuários reagem antes de comprometer”.
3. **Migração de banco de dados**: mudança de schema (adicionar coluna `nivel_fidelidade` em `Cliente` e migrar dados existentes). 50 milhões de registros.
4. **Refatoração interna**: módulo de cálculo de frete reescrito (sem mudança de comportamento — mesma saída para mesma entrada). Coberto por testes.
5. **Lançamento de funcionalidade nova**: programa de fidelidade — aparição em UI, novo módulo de pontuação, integração com sistemas existentes. PO quer lançar dia 1º de junho com campanha de marketing.
6. **Sistema interno crítico**: sistema de folha de pagamento da empresa. Roda mensalmente, no dia 5. Manutenção é tolerável fora desse período. Não tem usuários simultâneos.
7. **Atualização da versão do PostgreSQL** (14 → 16) usado por um serviço de API com 10.000 req/s e SLO de 99,95%.
8. **Experimento A/B**: nova fórmula de recomendação de produtos. PO quer comparar duas variantes e decidir baseado em métricas de negócio.

> Os exercícios são graduados por dificuldade: ⭐ básico · ⭐⭐ intermediário
> 

---

## Referências

- Kim, G.; Humble, J.; Debois, P.; Willis, J. *The DevOps Handbook*. 2ª ed., IT Revolution Press, 2021. (A referência canônica; introduz os três caminhos e organiza toda a disciplina.)
- Kim, G. *The Phoenix Project: A Novel about IT, DevOps, and Helping Your Business Win*. IT Revolution Press, 2013. (Romance de negócios que ensina DevOps através de história — leitura acessível e didática.)
- Forsgren, N.; Humble, J.; Kim, G. *Accelerate: The Science of Lean Software and DevOps*. IT Revolution Press, 2018. (Pesquisa empírica DORA — base científica das métricas e práticas. Leitura obrigatória para argumentar com dados.)
- Humble, J.; Farley, D. *Continuous Delivery: Reliable Software Releases through Build, Test, and Deployment Automation*. Addison-Wesley, 2010. (O livro que cunhou o conceito de Continuous Delivery; ainda atual.)
- Beyer, B.; Jones, C.; Petoff, J.; Murphy, N. R. (eds.) *Site Reliability Engineering: How Google Runs Production Systems*. O’Reilly, 2016. (Livro do Google sobre SRE — definitivo. Disponível gratuitamente em sre.google.)
- Allspaw, J.; Hammond, P. “10+ Deploys per Day: Dev and Ops Cooperation at Flickr”. Velocity Conference, 2009. (Talk seminal — vale assistir no YouTube.)
- Vogels, W. “A Conversation with Werner Vogels”. *ACM Queue*, 2006. (Origem do “you build it, you run it”.)
- Allspaw, J. “Blameless PostMortems and a Just Culture”. Code as Craft (Etsy Engineering Blog), 2012. (Texto fundador da prática de postmortem blameless.)
- Skelton, M.; Pais, M. *Team Topologies: Organizing Business and Technology Teams for Fast Flow*. IT Revolution Press, 2019. (Como organizar equipes para fluxo DevOps — tipologias de equipes e padrões de interação.)
- Limoncelli, T.; Chalup, S.; Hogan, C. *The Practice of Cloud System Administration: DevOps and SRE Practices for Web Services*. Addison-Wesley, 2014. (Tratamento prático e abrangente de operação de sistemas em nuvem.)
- Morris, K. *Infrastructure as Code: Dynamic Systems for the Cloud Age*. 2ª ed., O’Reilly, 2020. (Profunda discussão sobre IaC além das ferramentas.)
- Beda, J.; Hightower, K.; Burns, B. *Kubernetes: Up & Running*. 3ª ed., O’Reilly, 2022. (Para a parte específica de Kubernetes — escrito pelos próprios criadores.)

---