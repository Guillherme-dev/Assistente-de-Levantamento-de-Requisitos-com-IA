# Assistente de Levantamento de Requisitos com IA

Projeto prático desenvolvido para demonstrar a aplicação de **Engenharia de Prompt estruturada (LLMs)** na rotina de **Análise de Sistemas**, transformando relatos informais de clientes em especificações técnicas organizadas e prontas para a equipe de desenvolvimento.

---

## Objetivo do Projeto

Na etapa inicial de desenvolvimento de software, clientes frequentemente descrevem suas necessidades de forma aberta, incompleta ou desorganizada. Este assistente atua como um **Analista de Sistemas** que recebe essas demandas brutas e as converte automaticamente em um documento técnico padronizado, identificando lacunas de negócio antes mesmo da escrita de código.

---

## Arquitetura do Prompt (System Prompt)

Para garantir consistência na saída e evitar "alucinações" (invenção de funcionalidades não solicitadas), o prompt foi estruturado em inglês utilizando o framework **RTF (Role, Task, Format)** aliado a regras de restrição:

```PROMPT
Role: You are a Systems Analyst specialized in requirements gathering.

Task: Receive an informal description or request from a client and organize it into clear technical topics. Focus on designing a functional, intuitive system that is easy for new employees to learn and features a clean interface for users.

Always structure your response into these 4 topics:
1. Use Cases (Casos de Uso): Who uses the system and what actions they perform.
2. Functional Requirements (Requisitos Funcionais): What the system must do.
3. Business Rules (Regras de Negócio): Conditions, validations and restrictions.
4. Non-Functional Requirements (Requisitos Não Funcionais): Usability, clean interface, ease of learning and availability.

Rules:
- Keep the language simple, objective, and professional.
- Do not invent complex features that the client did not ask for. If something is unclear, list it at the end under "Questions for the Client".
```

``` Foi colocado para que novos funcionários pudessem usar e colocar dados caso algum cliente tenha dificuldade em não saber usar muito bem sites ou softwares!! ```

---

## Estudo de Caso Real: Sistema para Empresa de Manutenção de Ar-Condicionado

Para validar o comportamento do modelo no **Google AI Studio**, foi conduzido um ciclo completo e iterativo de levantamento e refinamento de requisitos baseado em um cenário real de prestação de serviços.

### 1. Entrada Inicial (Relato Informal do Cliente)
> *"Empresa de manutenção de ar condicionado:*
> *1 - Cliente coloque seus dados: CPF/CNPJ, nome, endereço (rua, bairro), WhatsApp, modelo da máquina, se é casa ou apartamento (interno).*
> *2 - Criar um status após aprovação orçamento (avisando o cliente e mudando o status de 'pendente' para 'aceito', após a conclusão mudar o status 'aceito' para 'concluído'), criar uma caixa de texto onde o cliente possa colocar orientações de como será feita a manutenção.*
> *3 - Criar ordem de serviço após aprovação da manutenção, apenas as cidades aceitas pela empresa e apenas manutenção em casas e partes internas de apartamento.*
> *4 - Precisa funcionar em horário comercial (08:00 às 18:00), precisa gerar um feedback no número cadastrado perguntando ao cliente sua satisfação."*

### 2. Ciclo de Refinamento e Controle de Escopo (Iterações)
Graças à regra de restrição do prompt, a IA identificou ambiguidades e levantou perguntas técnicas que permitiram refinar o escopo do sistema (MVP):
* **Redução de Escopo:** Descartada a geração de PDF da Ordem de Serviço (mantendo apenas visualização em tela) e removida a funcionalidade de pesquisa de satisfação automática para simplificar a primeira entrega.
* **Cidades Atendidas (Hardcoded):** Definida uma lista fechada (*dropdown*) contendo apenas as cidades de **Piracicaba, Americana e Santa Gertrudes**, sem opção de edição pelo usuário.
* **Disponibilidade vs. Atendimento:** O sistema passou a operar **24 horas** para recebimento de solicitações, exibindo um aviso de retorno comercial (segunda a sexta, das 08:00 às 18:00) **apenas** quando o envio ocorrer antes das 08:00, após as 18:00 ou em qualquer horário aos sábados e domingos.
* **Obrigatoriedade de Histórico:** O campo de orientações sobre a manutenção foi definido como obrigatório no formulário, mesmo havendo negociação paralela via WhatsApp.

### 3. Especificação Técnica Consolidada (Saída Final)

#### 1. Use Cases (Casos de Uso)
* **Cliente:** Solicita orçamento preenchendo seus dados cadastrais, endereço (dentro das cidades permitidas), tipo de imóvel, modelo do equipamento e orientações obrigatórias sobre a manutenção.
* **Funcionário / Atendente:** Negocia o orçamento via WhatsApp, atualiza o status da solicitação (*Pendente* > *Aceito* > *Concluído*) e visualiza a Ordem de Serviço na tela.

#### 2. Functional Requirements (Requisitos Funcionais)
* **RF01 – Cadastro de Solicitação:** O sistema deve disponibilizar formulário com CPF/CNPJ, nome, rua, bairro, cidade (lista suspensa fechada), WhatsApp, modelo da máquina, tipo de imóvel (casa ou apartamento - parte interna) e campo de texto obrigatório para orientações da manutenção.
* **RF02 – Gestão de Status:** O sistema deve permitir que a equipe altere o status do pedido de *Pendente* para *Aceito* (após aprovação do orçamento) e de *Aceito* para *Concluído* (após realização do serviço).
* **RF03 – Geração de Ordem de Serviço (OS):** Ao mudar o status para *Aceito*, o sistema deve gerar uma Ordem de Serviço para visualização direta em tela (sem necessidade de exportação em PDF).
* **RF04 – Notificação Condicional de Horário:** Após o envio do formulário, o sistema deve exibir um aviso informando que o retorno via WhatsApp ocorre em horário comercial (segunda a sexta, das 08:00 às 18:00) exclusivamente se o envio for feito fora desse intervalo ou aos finais de semana.

#### 3. Business Rules (Regras de Negócio)
* **RN01 – Restrição Geográfica (Hardcoded):** Apenas solicitações das cidades de Piracicaba, Americana e Santa Gertrudes são aceitas; a lista de cidades é fixa no código e não pode ser alterada pelo usuário.
* **RN02 – Restrição de Tipo de Serviço:** Em apartamentos, a empresa realiza manutenção exclusivamente na unidade interna.
* **RN03 – Obrigatoriedade de Descrição:** O cliente não pode concluir o envio sem preencher as orientações sobre a manutenção, garantindo registro no sistema independentemente do contato via WhatsApp.
* **RN04 – Fluxo Comercial Externo:** A negociação de valores e o envio de avisos de mudança de status ocorrem via WhatsApp.

#### 4. Non-Functional Requirements (Requisitos Não Funcionais)
* **RNF01 – Disponibilidade 24 Horas:** O sistema deve permanecer disponível 24/7 para cadastro de solicitações e consulta interna.
* **RNF02 – Interface Limpa e Intuitiva:** O formulário deve utilizar campos claros e listas fechadas para evitar erros de digitação pelo cliente.
* **RNF03 – Facilidade de Aprendizado:** O painel interno deve ser direto e simples para que novos funcionários aprendam rapidamente a atualizar status e consultar Ordens de Serviço.

---

## Ferramentas Utilizadas
* **Google AI Studio** (Testes e calibração de *System Instructions*)
* **Engenharia de Prompt** (Framework RTF e controle de alucinação)
* **Análise de Requisitos** (Levantamento, refinamento iterativo e definição de MVP)

---

## Autor
**Guilherme dos Santos Francisco** — Estudante de Análise e Desenvolvimento de Sistemas (ADS)
