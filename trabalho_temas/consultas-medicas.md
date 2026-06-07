## Plataforma de Telemedicina (HealthLink)

### 1. Escopo e Objetivos

Muitas pessoas têm dificuldade em agendar consultas médicas, especialmente com especialistas. As filas são longas e nem sempre é possível se deslocar até um consultório. Além disso, manter o histórico de consultas e exames organizados é um desafio.

A plataforma **HealthLink** conecta pacientes a profissionais de saúde de forma remota. O objetivo principal é permitir que o paciente encontre médicos, agende consultas (presenciais ou por vídeo) e mantenha seu histórico de saúde organizado em um único lugar.

### 2. Descrição do Sistema

O sistema é organizado por **Clínicas** (consultórios, clínicas ou hospitais). Cada clínica possui seus próprios médicos e pacientes. Uma clínica pode ter vários médicos de diferentes especialidades, e os pacientes podem agendar consultas com qualquer médico disponível.

O sistema oferece:
- **Busca de profissionais:** Por especialidade, localização e disponibilidade
- **Agendamento:** Escolha de data e horário disponíveis
- **Teleconsulta:** Chamada de vídeo para consultas remotas
- **Histórico:** Registro de consultas, receitas e orientações médicas

### 3. Atores e Fluxos Principais

#### Administrador da Clínica
- Criar e configurar a clínica no sistema
- Cadastrar médicos e definir suas especialidades
- Gerenciar horários de atendimento dos médicos
- Visualizar relatórios de consultas realizadas
- Gerenciar pagamentos e repasses

**Permissões:** Gerencia a clínica e seus profissionais. Não acessa prontuários de pacientes.

#### Médico
- Configurar sua agenda de atendimentos
- Visualizar consultas agendadas
- Realizar teleconsultas por vídeo
- Registrar anotações da consulta
- Emitir receitas e orientações para o paciente

**Permissões:** Acessa apenas os dados dos pacientes que está atendendo. Pode ver histórico de consultas anteriores do paciente.

#### Paciente
- Buscar médicos por especialidade e disponibilidade
- Agendar consultas (presenciais ou teleconsulta)
- Participar de teleconsultas por vídeo
- Visualizar seu histórico de consultas
- Acessar receitas e orientações recebidas

**Permissões:** Acessa apenas suas próprias informações e histórico.

### 4. Requisitos Funcionais

- **RF01:** O sistema deve permitir criar e gerenciar clínicas
- **RF02:** O sistema deve permitir cadastrar médicos com suas especialidades
- **RF03:** O sistema deve permitir que médicos configurem sua agenda
- **RF04:** O sistema deve permitir buscar médicos por especialidade
- **RF05:** O sistema deve permitir agendar consultas em horários disponíveis
- **RF06:** O sistema deve permitir realizar teleconsultas por vídeo
- **RF07:** O sistema deve permitir que médicos registrem anotações da consulta
- **RF08:** O sistema deve permitir emitir receitas digitais
- **RF09:** O sistema deve manter histórico de consultas do paciente
- **RF10:** O sistema deve enviar lembretes de consultas agendadas

### 5. Requisitos Não-Funcionais

- **Estimativa de usuários:** 1.000-5.000 usuários ativos por mês
- **Volume de dados:** ~5.000 consultas no primeiro ano
- **Segurança básica:** Login com usuário e senha; pacientes só acessam seus próprios dados; médicos só acessam dados de pacientes em atendimento
- **Disponibilidade esperada:** Alta (sistema deve estar disponível durante horário comercial)

### 6. Integrações Externas

- **Chamadas de vídeo:** Para realizar as teleconsultas entre médico e paciente
- **Envio de e-mails:** Para confirmar agendamentos e enviar lembretes
- **Sistema de pagamentos:** Para processar pagamentos de consultas particulares

### 7. Riscos e Desafios Técnicos

1. **Qualidade do vídeo:** A teleconsulta pode ser prejudicada se a internet do paciente ou médico estiver lenta. É importante ter uma conexão estável.

2. **Conflitos de agenda:** Risco de dois pacientes agendarem o mesmo horário. O sistema deve bloquear horários já reservados.

3. **Privacidade:** Informações de saúde são sensíveis. É importante garantir que apenas pessoas autorizadas acessem os dados.

4. **Disponibilidade:** Se o sistema ficar fora do ar durante uma teleconsulta, o atendimento é prejudicado. É importante ter um sistema confiável.

---