# 📋 Monitor de Prazos e Expedição de Certidões

## 🗂 Sobre o Projeto
Aplicação Inteligente com Low-Code — Construindo uma Solução Digital com OutSystems, IA e Arquitetura Escalável produzida para projeto para a UniFECAF / Rocketseat · IA e Automação Digital · 2026.

Os Ofícios de Registro de Imóveis operam sob rigorosos prazos legais para a expedição de certidões, conforme estabelecido pela Lei de Registros Públicos (Lei n. 6.015/1973). O descumprimento desses prazos gera responsabilidade funcional ao escrevente responsável e prejuízos aos solicitantes.

O controle manual apresenta problemas críticos:

❌ Dificuldade em visualizar o status de múltiplos pedidos simultaneamente
❌ Risco de perda de prazos legais pela falta de alerta visual
❌ Ausência de cálculo automático de dias úteis
❌ Ausência de histórico rastreável de pedidos expedidos

O Monitor de Certidões resolve esses problemas com uma aplicação web desenvolvida em OutSystems, com painel Kanban, cálculo automático de prazos e triagem inteligente de pedidos via IA (Groq API).

## ✅ Funcionalidades

📊 Kanban Visual
Painel com 3 colunas: A Fazer, Processando e Expedido
⏱️ Cálculo de Prazo
Cálculo automático em dias úteis (descarta sábados e domingos)
🎨 Alerta Visual
Cards mudam de cor: azul → laranja → verde conforme o status
🤖 Triagem por IA
Campo de texto livre → IA extrai matrícula, tipo e nome automaticamente
📈 Gráfico de Pizza
Volume de certidões por status em tempo real (OutSystems Charts)
📝 Formulário Novo Pedido
Cadastro com dropdown de tipo de certidão e cálculo automático do prazo
🔁 CRUD Completo
Criar, listar e atualizar pedidos de certidão

## 🏗 Arquitetura Canvas
A aplicação segue o modelo de Arquitetura Canvas do OutSystems, com separação clara entre as camadas de Foundation, Core e End-User (Interface).

Módulo - MonitorCertidoes_IS
Tipo - Service
Camada - Foundation
Responsabilidade - Consumo da API Groq para triagem por IA

Módulo - MonitorCertidoes_CS
Tipo - Service
Camada - Core
Responsabilidade - Entidades, lógica de negócio e CRUD

Módulo - MonitorCertidoes_CW
Tipo - Reactive Web
Camada - Core
Responsabilidade - Web Block CardCertidao com alerta visual

Módulo - MonitorCertidoes_UI
Tipo - Reactive Web
Camada - End-User
Responsabilidade - Dashboard, Kanban, Gráfico e Formulário

## 🗄 Modelagem de Dados
PedidoCertidao (Entidade Transacional)
StatusPedido (Entidade Estática)
TipoCertidao (Entidade Estática)

## ⚙️ Regras de Negócio
1. Cálculo de Data Final (CalcularDataFinal)
Server Action no módulo CS que:

Recebe DataEntrada e DiasUteisParaAdicionar (do tipo de certidão)
Itera dia a dia verificando DayOfWeek
Conta apenas dias úteis (segunda a sexta)
Retorna DataFinal

2. Fluxo do Botão Salvar

## 3. Alerta Visual por Prazo

Os alertas incluem ícone de relógio, expressão "vencido" e ícone check, cada um com cor correspondente, garantindo acessibilidade para daltônicos.

## 🤖 IA e Automação
Triagem Automática via Groq API
No formulário de Novo Pedido, o escrevente pode colar um texto livre (ex.: e-mail do solicitante) e clicar em "Preencher com IA".

Fluxo:

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

## ♿ Acessibilidade
Daltonismo: alertas combinam cor + ícone (não dependem apenas de cor)
Leitores de tela: labels descritivos associados a todos os inputs do formulário

## 🛠 Tecnologias Utilizadas

1. OutSystems 11
Plataforma low-code principal

2. OutSystems Charts (Forge)
Gráfico de pizza no Dashboard

3. Groq API
Triagem automática de pedidos por IA

## 📸 Prints da Aplicação
Dashboard — Kanban + Gráfico

Formulário Novo Pedido — Com IA

Service Studio — CalcularDataFinal




Monitor de Prazos e Expedição de Certidões
UniFECAF / Rocketseat · IA e Automação Digital · 2026
Desenvolvido por Bruna Gabriela Ribeiro Sartor
