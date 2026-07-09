<img src="banner.png">

![](https://img.shields.io/badge/Estudos-ServiceNow-blue)

# Estudos ServiceNow

ServiceNow é uma plataforma inteligente em nuvem focada em transformação digital e automação de fluxos de trabalho. Ela conecta diferentes departamentos de uma empresa (como TI, RH, jurídico e financeiro) em um único sistema, eliminando trabalhos manuais e melhorando a produtividade de funcionários e clientes.

## Tabelas e Conceitos Centrais

Referência rápida das principais entidades do ServiceNow, com o nome técnico da tabela para facilitar buscas via **Table API**, **Background Scripts** ou navegação direta pela URL (`/nome_da_tabela_list.do`).

---

### 👤 Usuário
**Definição:** Representa qualquer pessoa que interage com a instância — funcionário, cliente ou fornecedor. É a base para autenticação, atribuição de tarefas e controle de permissões (roles).

- **Tabela:** `sys_user`
- **Campos principais:** 
  - `user_id` – login do usuário (identificador geral)
  - `first_name` / `last_name` – nome completo
  - `email` – e-mail
  - `department` – departamento (referência à tabela `cmn_department`)
  - `roles` – roles atribuídas ao usuário (via tabela `sys_user_has_role`)
  - `active` – se o usuário está ativo

---

### 👥 Grupo de Atribuição
**Definição:** Conjunto de usuários organizados para receber e resolver tarefas em conjunto (ex: "Suporte N1", "Infraestrutura").

- **Tabela:** `sys_user_group`
- **Campos principais:**
  - `name` – nome do grupo
  - `manager` – gestor do grupo
  - `parent` – Define o pai do grupo que está sendo criado - "este grupo que estou criando é filho de tal grupo."
  - `Description` - Discrição do grupo para complementar

---

### 🔑 Role (Papel de Acesso)
**Definição:** Define o que um usuário pode ver e fazer na plataforma. Roles concedem acesso a módulos, tabelas e funcionalidades — podem ser atribuídas diretamente ao usuário ou herdadas por um grupo.

- **Tabela:** `sys_user_role`
- **Tabela de relação (usuário ↔ role):** `sys_user_has_role`
- **Tabela de relação (grupo ↔ role):** `sys_group_has_role`
- **Campos principais:**
  - `name` – nome da role
  - `description` – descrição do que ela permite
  - `contains_roles` – roles "filhas" incluídas automaticamente (herança)

#### Roles mais usadas

| Role | Descrição |
|---|---|
| `admin` | Acesso total à instância — configurações, tabelas, scripts. Usado por administradores do sistema. |
| `itil` | Acesso básico ao ITSM (Incidente, Problema, Mudança). A role mais comum para analistas de suporte. |
| `catalog_admin` | Gerencia itens, categorias e variáveis do Service Catalog. |
| `knowledge_admin` | Gerencia artigos e categorias da Base de Conhecimento. |
| `approver_user` | Permite atuar como aprovador em fluxos de aprovação (mudanças, solicitações). |
| `itil_admin` | Acesso administrativo dentro do módulo ITSM, sem ser admin geral da instância. |
| `security_admin` | Gerencia ACLs (Access Control Lists) e configurações de segurança. |
| `personalize_choices` | Permite customizar opções de campos do tipo *choice*. |
| `snc_platform_stats_read` | Acesso a relatórios e estatísticas da plataforma. |
| `report_admin` | Gerencia relatórios (criação, edição, exclusão de qualquer relatório). |

> 💡 **Dica:** a role `itil` é praticamente obrigatória pra qualquer pessoa que for trabalhar no dia a dia com Incidentes, Problemas e Mudanças. Já `admin` deve ser usada com cuidado — é o acesso mais amplo possível.


### 🎫 Incidente
**Definição:** Registro de uma interrupção não planejada ou redução na qualidade de um serviço. É o processo mais usado do ITSM.

- **Tabela:** `incident`
- **Campos principais:**
  - `number` – número do incidente (ex: INC0010023)
  - `short_description` – descrição curta
  - `caller_id` – quem abriu o chamado (referência a `sys_user`)
  - `assignment_group` – grupo responsável (referência a `sys_user_group`)
  - `priority` – prioridade
  - `state` – estado (Novo, Em Andamento, Resolvido, etc.)

---

### 🔧 Problema
**Definição:** Causa raiz de um ou mais incidentes. Usado para investigação e prevenção.

- **Tabela:** `problem`
- **Campos principais:**
  - `number` – número (ex: PRB0000123)
  - `short_description` – descrição curta
  - `related_incidents` – incidentes relacionados

---

### 🔄 Mudança (Change Request)
**Definição:** Processo controlado para implementar alterações em serviços ou infraestrutura, minimizando riscos.

- **Tabela:** `change_request`
- **Campos principais:**
  - `number` – número (ex: CHG0000456)
  - `type` – tipo (Normal, Padrão, Emergencial)
  - `risk` – nível de risco
  - `state` – estado do fluxo de aprovação

---

### 📦 Solicitação (Request) e Item de Catálogo
**Definição:** Pedido feito por um usuário através do **Service Catalog** (ex: solicitar um notebook, acesso a um sistema).

- **Tabela da Solicitação:** `sc_request`
- **Tabela do Item Solicitado:** `sc_req_item`
- **Tabela de Itens do Catálogo:** `sc_cat_item`
- **Campos principais:**
  - `number` – número da solicitação (ex: REQ0000789)
  - `requested_for` – para quem é a solicitação
  - `price` – preço do item (quando aplicável)

---

### 🗂️ Tarefa (Task)
**Definição:** Tabela "mãe" (base table) da qual Incidente, Problema, Mudança e outros herdam campos e comportamentos.

- **Tabela:** `task`
- **Campos principais (herdados por outras tabelas):**
  - `number`, `short_description`, `state`, `priority`, `assigned_to`, `opened_by`
