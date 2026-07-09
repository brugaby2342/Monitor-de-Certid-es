# 📋 Monitor de Prazos e Expedição de Certidões

## 🗂 Sobre o Projeto
Aplicação Inteligente com Low-Code — Construindo uma Solução Digital com OutSystems, IA e Arquitetura Escalável produzida para projeto para a UniFECAF / Rocketseat · IA e Automação Digital · 2026.

Os Ofícios de Registro de Imóveis operam sob rigorosos prazos legais para a expedição de certidões, conforme estabelecido pela Lei de Registros Públicos (Lei n. 6.015/1973). O descumprimento desses prazos gera responsabilidade funcional ao escrevente responsável e prejuízos aos solicitantes.

O controle manual apresenta problemas críticos:

❌ Dificuldade em visualizar o status de múltiplos pedidos simultaneamente</br>
❌ Risco de perda de prazos legais pela falta de alerta visual</br>
❌ Ausência de cálculo automático de dias úteis</br>
❌ Ausência de histórico rastreável de pedidos expedidos</br>

O Monitor de Certidões resolve esses problemas com uma aplicação web desenvolvida em OutSystems, com painel Kanban, cálculo automático de prazos e triagem inteligente de pedidos via IA (Groq API).

## ✅ Funcionalidades

📊 Kanban Visual</br>
Painel com 3 colunas: A Fazer, Processando e Expedido</br>
⏱️ Cálculo de Prazo</br>
Cálculo automático em dias úteis (descarta sábados e domingos)</br>
🎨 Alerta Visual</br>
Cards mudam de cor: azul → laranja → verde conforme o status</br>
🤖 Triagem por IA</br>
Campo de texto livre → IA extrai matrícula, tipo e nome automaticamente</br>
📈 Gráfico de Pizza</br>
Volume de certidões por status em tempo real (OutSystems Charts)</br>
📝 Formulário Novo Pedido</br>
Cadastro com dropdown de tipo de certidão e cálculo automático do prazo</br>
🔁 CRUD Completo</br>
Criar, listar e atualizar pedidos de certidão</br>

---

## 🏗 Arquitetura Canvas
A aplicação segue o modelo de Arquitetura Canvas do OutSystems, com separação clara entre as camadas de Foundation, Core e End-User (Interface).</br>

Módulo - MonitorCertidoes_IS</br>
Tipo - Service</br>
Camada - Foundation</br>
Responsabilidade - Consumo da API Groq para triagem por IA</br>

Módulo - MonitorCertidoes_CS</br>
Tipo - Service</br>
Camada - Core</br>
Responsabilidade - Entidades, lógica de negócio e CRUD</br>

Módulo - MonitorCertidoes_CW</br>
Tipo - Reactive Web</br>
Camada - Core</br>
Responsabilidade - Web Block CardCertidao com alerta visual</br>

Módulo - MonitorCertidoes_UI</br>
Tipo - Reactive Web</br>
Camada - End-User</br>
Responsabilidade - Dashboard, Kanban, Gráfico e Formulário</br>

---

## 🗄 Modelagem de Dados
PedidoCertidao (Entidade Transacional)</br>
StatusPedido (Entidade Estática)</br>
TipoCertidao (Entidade Estática)</br>

---

## ⚙️ Regras de Negócio
### Fluxo das Server Actions e Client Actions
A lógica das principais Server Actions e Client Actions da aplicação **Monitor de Certidões**, organizadas pelas camadas do OutSystems (Foundation → CS → UI):

1. `AnalisarTexto` (Foundation)
**Regra de negócio:** o prompt força a IA a devolver **apenas** um JSON com as 3 chaves esperadas, restringindo `TipoCertidao` exclusivamente aos valores presentes em `TipoCertidaoOptions` (evita alucinação de tipos inexistentes no cadastro).

2. `TriagemIA` (CS)
**Regra de negócio:** é essa action que faz o **De-Para** entre o texto livre devolvido pela IA e o cadastro real de tipos de certidão. Se a IA não retornar um valor reconhecido, o campo fica em branco — o sistema nunca assume um tipo por conta própria.

3. `CalcularDataFinal` (CS)
**Regra de negócio:** calcula o prazo final do pedido somando **dias úteis** (não corridos) a partir da data de entrada, considerando o prazo específico de cada tipo de certidão (`PrazoDiasUteis`).

4. `AvancarStatusPedido` (CS)
**Regra de negócio:** o status avança sempre **uma etapa por vez** (A Fazer → Processando → Expedido), nunca pula etapas nem retrocede. Quando o pedido já está "Expedido", o botão de avanço deixa de ser exibido (`MostrarBotaoAvancar = False`).

5. `SavePedidoCertidao` (CS)
**Regra de negócio:** é a **única** action que efetivamente grava um pedido no banco. Nenhuma outra rotina (incluindo a Triagem por IA) persiste dados diretamente — a gravação só ocorre quando o usuário confirma explicitamente o formulário.

6. `PreencherComIaOnClick` (Client Action — tela Dashboard)
**Regra de negócio:** apenas **pré-preenche** o formulário para revisão do usuário. Não chama `SavePedidoCertidao` — a gravação depende de uma confirmação manual separada (botão "Novo Pedido").

7. `AvancarStatusOnClick` (Client Action — bloco CardCertidao)
**Regra de negócio:** ao invés de navegar/recarregar a página, o bloco **notifica** a tela pai de que um status mudou, permitindo que ela decida como atualizar sua própria interface.

8. `CardCertidaoStatusAvancado` (Client Action — tela Dashboard, handler do Notify)
**Regra de negócio:** garante que, independentemente de qual card disparou o avanço de status, as **três colunas do Kanban** sejam recarregadas via Ajax, refletindo a movimentação do pedido entre colunas sem depender de reload de página.

---

## Alerta Visual por Prazo
Os alertas incluem ícone de relógio, expressão "vencido" e ícone check, cada um com cor correspondente, garantindo acessibilidade para daltônicos.

---

## 🤖 IA e Automação
Triagem Automática via Groq API</br>
No formulário de Novo Pedido, o escrevente pode colar um texto livre (ex.: e-mail do solicitante) e clicar em "Preencher com IA".</br>

Fluxo:
[Usuário digita texto livre]

        │
        ▼
        
[Preencher com IA] ──► PreencherComIaOnClick (UI)

        │
        ▼
        
    TriagemIA (CS)
        │
        ├──► GetTipoCertidaos (lista tipos ativos)
        ├──► AnalisarTexto (Foundation → IA/Groq)
        └──► De-Para texto → TipoCertidaoId
        │
        ▼
        
[Formulário pré-preenchido, editável]

        │
        ▼
        
[Usuário confirma "Novo Pedido"] ──► SavePedidoCertidao (CS)

        │
        ▼
        
[Pedido criado, entra na coluna "A Fazer"]

---

## 🧪 Como Testar
1. Acessar a aplicação
[MonitordeCertidoes](https://personal-fso2pvhh.outsystemscloud.com/MonitordeCertidoes_UI/Dashboard?_ts=639183496705102230)

2. Testar o Kanban
Acesse o Dashboard
Verifique os cards distribuídos nas colunas A Fazer, Processando e Expedido
Observe as cores dos cards: azul, laranja e verde
Verifique o gráfico de pizza mostrando o volume por status

3. Testar o Formulário de Novo Pedido
Clique em "Novo Pedido" - abrirá o formulário
Preencha-o:
Número da Matrícula: ex. 99999
Nome do Solicitante: ex. Maria da Silva
Tipo de Certidão: selecione Inteiro Teor
Clique em Enviar Pedido
O card deve aparecer em A Fazer com o prazo calculado

4. Testar a Triagem por IA
Acesse "Novo Pedido"
No campo de texto livre, cole o seguinte texto:

Boa tarde, meu nome é João Augusto e preciso de uma certidão de inteiro teor do imóvel de matrícula número 12345. Aguardo retorno. Obrigado.

Clique em "Preencher com IA"
Os campos Matrícula, Tipo de Certidão e Nome devem ser preenchidos automaticamente
Clique em Enviar Pedido
O card deve aparecer em A Fazer com o prazo calculado

---

## ♿ Acessibilidade
Daltonismo: alertas combinam cor + ícone (não dependem apenas de cor)
Leitores de tela: labels descritivos associados a todos os inputs do formulário

---

## 🛠 Tecnologias Utilizadas

1. OutSystems 11
Plataforma low-code principal
2. OutSystems Charts (Forge)
Gráfico de pizza no Dashboard
3. Groq API
Triagem automática de pedidos por IA

---

## 📸 Prints da Aplicação
Dashboard — Kanban + Gráfico
Formulário Novo Pedido — Com IA
Service Studio — CalcularDataFinal

---


Monitor de Prazos e Expedição de Certidões
UniFECAF / Rocketseat · IA e Automação Digital · 2026
Desenvolvido por Bruna Gabriela Ribeiro Sartor
