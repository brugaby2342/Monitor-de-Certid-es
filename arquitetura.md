# Monitor Certidão — Arquitetura e Contexto do Projeto

> **Propósito deste arquivo:** servir como documentação de portfólio. 

---

## 1. Visão Geral

**Nome:** Monitor de Certidões (Monitor de Prazos e Expedição de Certidões)
**Plataforma:** OutSystems 11 — Reactive Web App
**Ambiente:** OutSystems Personal (`personal-fso2pvhh.outsystemscloud.com`)
**Contexto:** Projeto universitário (IA e Automação Digital — UniFECAF/Rocketseat), inspirado na rotina real de um Ofício de Registro de Imóveis.

**O que o app faz:**
- Registra pedidos de certidão (matrícula, solicitante, tipo de certidão)
- Calcula prazos em dias úteis para expedição
- Exibe os pedidos em um **Kanban** por status + **gráfico de pizza** no dashboard
- **Preenchimento assistido por IA:** o usuário digita o pedido em linguagem natural e a integração com a **Groq API (Llama 3.3 70b)** extrai os campos do formulário
- Filosofia da apresentação: **"IA assiste, humano valida"** — a IA sugere, o usuário confirma antes de enviar

---

## 2. Módulos (Canvas Architecture)

Arquitetura em 4 módulos, seguindo o Architecture Canvas:

| Módulo | Camada | Responsabilidade |
|--------|--------|------------------|
| `MonitordeCertidoes_Foundation` | Integration/Foundation | Integração REST com a Groq API (consume REST, estruturas de request/response) |
| `MonitordeCertidoes_CS` | Core Services | Entidades (PedidoCertidao, StatusPedido, TipoCertidao...), Server Actions de CRUD e regras de negócio (cálculo de dias úteis...) |
| `MonitordeCertidoes_CW` | Core Widgets | Blocos reutilizáveis (cards do Kanban) |
| `MonitordeCertidoes_UI` | End-User | Dashboard, Formulário Novo Pedido, Kanban |

---

## 3. Modelo de Dados

### PedidoCertidao
| Atributo | Tipo | Observações |
|----------|------|-------------|
| Id | Long Integer | PK |
| NumMatricula | Text | obrigatório |
| NomeSolicitante | Text | obrigatório |
| TipoCertidaoId | TipoCertidao Identifier | FK |
| StatusPedidoId | StatusPedido Identifier | FK |
| DataEntrada | DateTime | obrigatório |
| DataFinal | DateTime | calculado em dias úteis - obrigatório |

### StatusPedido
Tabela de status do Kanban (ex.: Pendente, Em Andamento, Expedida...).
*(Recomendação registrada: converter para Static Entity para garantir os registros no publish.)*

### TipoCertidao
| Atributo | Tipo |
|----------|------|
| Id | Integer |
| Label | Text |
| Order | Integer |
| Is_Active | Boolean |
| PrazoDiasUteis | Integer |

Records: Inteiro Teor, Completa, Vintenária. 

---

## 4. Integração com IA (Groq API)

**Endpoint:** `https://api.groq.com/openai/v1/chat/completions` (consume REST no módulo IS-Foundation)
**Modelo:** `llama-3.3-70b-versatile`
**Fluxo:**
1. Usuário digita pedido livre no textarea (ex.: "matricula 1, completa, nome fulano")
2. Botão **"Preencher com IA"** → Server Action monta o payload JSON com system prompt instruindo resposta em JSON puro
3. Resposta é desserializada (JSON Deserialize) para uma estrutura com os campos do formulário
4. Server Action resolve `TipoCertidao` a partir do texto retornado (query na entidade)
5. Campos do formulário são preenchidos — **usuário revisa e clica em Enviar Pedido**

**Estrutura response:** `JSON_RespostaIA`
**Atributos:** 
1. `NumMatricula`
2. `NomeSolicitante`
3. `TipoCertidao`

**Lições do debugging (histórico):**
- HTTP 400: payload JSON mal construído / model não atribuído no request
- Erros de Assign invertido (source/target trocados) ao mapear a resposta
- Chaves do prompt divergentes da estrutura de deserialização
- `DataBaseException` no INSERT: `StatusPedidoId` sem valor → violação de FK (corrigido com Assign do status inicial antes do Create)

---

## 5. Regras de Negócio

- **Cálculo de prazo:** dias úteis a partir da data do pedido, conforme `PrazoDiasUteis` do tipo de certidão 
- **Status inicial de um pedido novo:** definido A Fazer
- **Tipo certidão padrão:** definida Inteiro Teor caso o usuário não saiba o nome da certidão que deseja, visto que é a mais solicitada. Solução para não quebrar o fluxo, já que é atributo obrigatório.
- **Atualização do dashboard:** `refresh de dados via Aggregate` após operações

---

## 6. Decisões Técnicas Registradas

- Kanban com **containers nativos** em vez do componente Forge (mais controle visual)
- `location.reload()` para refresh do dashboard
- Groq API escolhida por ser gratuita e rápida para o caso de uso acadêmico
- Framing da apresentação: "IA assiste, humano valida"

---

## 7. Próximos Passos

- [ ] Adicionar mais tipos de certidões
- [ ] Distribuir pedidos para escreventes específicos
- [ ] Adicionar gráfico em barras para registrar a evolução dos pedidos prontos referentes a cada mês

---

