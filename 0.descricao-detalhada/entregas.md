## Plataforma de Entregas Multicategoria (CityDash)

### 1. Escopo e Objetivos

Muitas pessoas precisam enviar ou receber itens rapidamente: documentos, compras de lojas, pacotes, presentes. Nem sempre é fácil encontrar alguém disponível para fazer a entrega de forma rápida e confiável.

A plataforma **CityDash** conecta pessoas que precisam enviar itens a entregadores disponíveis na região. O objetivo principal é permitir que o usuário solicite uma entrega, acompanhe em tempo real e receba seu item de forma segura.

### 2. Descrição do Sistema

O sistema funciona como um **marketplace de entregas**. Qualquer pessoa pode solicitar uma entrega informando o que vai enviar, de onde vai sair e para onde vai. O sistema calcula o valor automaticamente e encontra um entregador disponível na região.

O fluxo básico de uma entrega:
1. Cliente informa o que vai enviar e os endereços de coleta e entrega
2. Sistema calcula o valor do frete com base na distância
3. Cliente confirma e paga a solicitação
4. Sistema oferece a entrega para entregadores próximos
5. Entregador aceita, coleta o item e leva até o destino
6. Cliente confirma o recebimento

### 3. Atores e Fluxos Principais

#### Administrador da Plataforma
- Gerenciar cadastros de entregadores
- Definir regras de precificação (valor por km, taxa mínima)
- Visualizar relatórios de entregas realizadas
- Resolver disputas entre clientes e entregadores
- Gerenciar pagamentos e repasses

**Permissões:** Acesso administrativo geral da plataforma.

#### Entregador
- Cadastrar-se como entregador
- Definir disponibilidade e região de atuação
- Receber ofertas de entrega baseadas na localização
- Aceitar ou recusar entregas
- Atualizar status da entrega (coletou, em trânsito, entregue)
- Visualizar ganhos acumulados e solicitar saque

**Permissões:** Vê apenas informações necessárias para a entrega. Não vê dados pessoais completos do cliente.

#### Cliente
- Solicitar entregas informando item, coleta e destino
- Visualizar estimativa de preço e tempo
- Escolher entre entrega imediata ou agendada
- Acompanhar a localização do entregador em tempo real
- Confirmar recebimento do item
- Avaliar o serviço do entregador

**Permissões:** Acessa apenas suas próprias solicitações de entrega.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir cadastro de entregadores
- **RF02:** O sistema deve permitir solicitar entregas com endereços de coleta e destino
- **RF03:** O sistema deve calcular o valor do frete automaticamente
- **RF04:** O sistema deve permitir escolher entre entrega imediata ou agendada
- **RF05:** O sistema deve oferecer entregas para entregadores disponíveis na região
- **RF06:** O sistema deve mostrar a localização do entregador em tempo real
- **RF07:** O sistema deve permitir que o cliente confirme o recebimento
- **RF08:** O sistema deve permitir avaliação do entregador
- **RF09:** O sistema deve manter histórico de entregas do cliente
- **RF10:** O sistema deve permitir que entregadores visualizem seus ganhos

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 1.000-5.000 usuários ativos por mês
- **Volume de dados:** ~15.000 entregas no primeiro ano
- **Segurança básica:** Login com usuário e senha; entregadores e clientes não veem dados pessoais completos um do outro
- **Disponibilidade esperada:** Alta (entregas podem ser solicitadas a qualquer hora)

### 6. Integrações Externas

- **Sistema de mapas:** Para calcular distâncias e mostrar localização do entregador
- **Sistema de pagamentos:** Para processar pagamentos das entregas
- **Envio de notificações:** Para alertar entregadores sobre novas ofertas e clientes sobre status

### 7. Riscos e Desafios Técnicos

1. **Encontrar entregador:** Pode não haver entregadores disponíveis na região. O sistema deve informar o cliente sobre a indisponibilidade.

2. **Localização imprecisa:** O GPS pode não funcionar bem em alguns lugares. O entregador pode ter dificuldade em encontrar os endereços.

3. **Item não entregue:** O destinatário pode não estar no local. É preciso definir o que fazer nessas situações (devolver, tentar novamente).

4. **Disputas:** Podem ocorrer problemas (item danificado, entrega não realizada). O sistema precisa ter um processo para resolver essas situações.

---