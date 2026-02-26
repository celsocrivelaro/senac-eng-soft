## Sistema de Telemedicina e Gestão de Saúde (HealthLink)

### 1. Escopo e Objetivos

O projeto **HealthLink** visa desenvolver uma plataforma integrada de assistência à saúde que conecte pacientes e profissionais de forma remota e segura. O objetivo é centralizar a jornada do paciente: desde a busca por especialistas e agendamento de consultas até o monitoramento de sinais vitais e o acesso ao prontuário eletrônico. O sistema deve garantir a continuidade do cuidado, permitindo que a informação médica flua de forma precisa entre os envolvidos.

### 2. Descrição do Sistema

O ecossistema HealthLink deve atender a três perfis principais com funcionalidades distintas:

1. **Módulo do Paciente:** Busca de profissionais por especialidade e convênio, agendamento de horários, acesso a histórico de prescrições e resultados de exames. Inclui ferramentas de **Autocuidado**, onde o usuário registra dados como pressão arterial e glicemia (manualmente ou via integração com *wearables*).
2. **Módulo do Profissional de Saúde:** Prontuário Eletrônico do Paciente (PEP) com atualização em tempo real, gestão de agenda (cancelamentos/reagendamentos) e emissão de receitas digitais com assinatura eletrônica.
3. **Módulo de Teleconsulta:** Ambiente seguro para chamadas de vídeo integradas ao prontuário, com chat criptografado para troca de mensagens e orientações pós-consulta.

### 3. Integração e Interoperabilidade

A plataforma deve ser capaz de se integrar a dispositivos de monitoramento de saúde (como Fitbit ou MyFitnessPal) para coletar dados de atividade física. Além disso, deve prever a integração com gateways de pagamento para consultas particulares e sistemas de autorização de planos de saúde. O sistema deve suportar a exportação de dados em formatos padronizados na saúde para garantir a portabilidade das informações do paciente.

### 4. Arquitetura e Requisitos Não Funcionais

- **Arquitetura:** Padrão **MVC**. A camada de *Model* deve ser rigorosa quanto à trilha de auditoria (quem acessou qual registro e quando).
- **Segurança (Pilar Central):** Criptografia de dados em trânsito e em repouso. Conformidade absoluta com a **LGPD** e normas específicas de sigilo médico.
- **Disponibilidade:** Sendo um sistema de saúde, a disponibilidade deve ser de $99,99\%$. O sistema de agendamento deve impedir conflitos de horários em fuso horários distintos.
- **Usabilidade:** A interface deve ser acessível para diferentes faixas etárias, priorizando a clareza nas instruções de uso das consultas virtuais.

### 5. Riscos e Desafios Técnicos

1. **Vazamento de Dados Sensíveis:** O risco de exposição de diagnósticos e registros médicos, o que exige testes de intrusão recorrentes.
2. **Latência em Teleconsultas:** Garantir a qualidade de áudio e vídeo em conexões instáveis para não comprometer o diagnóstico remoto.
3. **Sincronização de Dispositivos IoT:** Tratar a volumetria e a inconsistência de dados provenientes de diferentes sensores de saúde.
4. **Responsabilidade Civil:** O desafio técnico de garantir que todas as interações médico-paciente sejam devidamente registradas e armazenadas para fins legais.

---

### Estrutura Pedagógica: Foco em Ética e Requisitos Não Funcionais

Neste projeto, é essencial que os alunos compreendam que os **Requisitos Não Funcionais (RNF)** são tão importantes quanto as funcionalidades:

| **Requisito Não Funcional** | **Descrição Técnica Sugerida** |
| --- | --- |
| **Privacidade** | Somente o médico assistente e o próprio paciente podem acessar o conteúdo do prontuário. |
| **Auditabilidade** | Todo acesso a um registro médico deve gerar um log imutável contendo ID do usuário, data e IP. |
| **Escalabilidade** | O sistema deve suportar 1.000 teleconsultas simultâneas sem perda de pacotes de vídeo. |
| **Conformidade** | O sistema de prescrição deve validar o CRM do médico junto ao banco de dados do conselho. |