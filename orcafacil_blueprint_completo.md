# OrçaFácil — Blueprint Completo

## Visão Geral

O **OrçaFácil** é um aplicativo de controle financeiro pessoal desenvolvido para demonstrar arquitetura moderna fullstack utilizando:

- Backend em Python com FastAPI
- Mobile em Flutter
- Painel Web em Python
- PostgreSQL como banco de dados
- JWT para autenticação
- Arquitetura escalável e modular

O projeto foi pensado para servir como um produto real e também como um excelente projeto de portfólio.

---

# Objetivos do Projeto

O aplicativo permitirá ao usuário:

- Controlar receitas e despesas
- Gerenciar contas financeiras
- Criar categorias personalizadas
- Definir orçamentos mensais
- Criar metas financeiras
- Visualizar dashboards financeiros
- Importar extratos CSV
- Acompanhar evolução financeira

---

# Stack Tecnológica

## Backend

- Python 3.12+
- FastAPI
- SQLAlchemy
- Alembic
- PostgreSQL
- JWT Authentication
- Pydantic
- Uvicorn

## Mobile

- Flutter
- Riverpod
- Dio
- go_router
- fl_chart

## Painel Web

- Streamlit

## Infraestrutura

- Docker
- Docker Compose
- Railway ou Render
- Supabase PostgreSQL

---

# Arquitetura

## Estrutura Backend

```txt
backend/
│
├── app/
│   ├── core/
│   ├── auth/
│   ├── users/
│   ├── accounts/
│   ├── categories/
│   ├── transactions/
│   ├── budgets/
│   ├── goals/
│   ├── dashboard/
│   ├── imports/
│   ├── database/
│   └── main.py
│
├── alembic/
├── requirements.txt
└── Dockerfile
```

---

# Funcionalidades MVP

# 1. Autenticação

## Funcionalidades

- Cadastro de usuário
- Login
- JWT Access Token
- Refresh Token futuramente
- Hash de senha
- Middleware de autenticação

## Endpoints

```txt
POST /auth/register
POST /auth/login
GET /users/me
```

---

# 2. Gestão de Contas

## Exemplos de contas

- Conta Corrente
- Poupança
- Dinheiro
- Cartão de Crédito

## Campos

```txt
- id
- user_id
- name
- type
- initial_balance
- current_balance
- is_active
- created_at
```

## Endpoints

```txt
GET /accounts
POST /accounts
PATCH /accounts/{id}
DELETE /accounts/{id}
```

---

# 3. Categorias

## Categorias padrão

- Alimentação
- Transporte
- Saúde
- Educação
- Moradia
- Lazer
- Assinaturas
- Outros

## Funcionalidades

- Criar categoria
- Editar categoria
- Excluir categoria
- Cor personalizada
- Ícone personalizado

## Endpoints

```txt
GET /categories
POST /categories
PATCH /categories/{id}
DELETE /categories/{id}
```

---

# 4. Transações

## Tipos

- Receita
- Despesa

## Campos

```txt
- id
- user_id
- account_id
- category_id
- type
- amount
- date
- description
- payment_method
- is_recurring
- created_at
```

## Funcionalidades

- Criar transação
- Editar transação
- Excluir transação
- Filtros
- Ordenação
- Busca textual

## Endpoints

```txt
GET /transactions
POST /transactions
PATCH /transactions/{id}
DELETE /transactions/{id}
```

---

# 5. Orçamento Mensal

## Objetivo

Permitir ao usuário definir limites de gastos por categoria.

## Exemplo

```txt
Alimentação: R$ 800
Transporte: R$ 300
Lazer: R$ 250
```

## Funcionalidades

- Definir limite mensal
- Visualizar percentual utilizado
- Alertas visuais
- Comparativo mensal

## Endpoints

```txt
GET /budgets
POST /budgets
PATCH /budgets/{id}
```

---

# 6. Metas Financeiras

## Exemplos

- Reserva de emergência
- Viagem
- Comprar notebook
- Quitar dívida

## Campos

```txt
- id
- user_id
- name
- target_amount
- current_amount
- deadline
- status
```

## Funcionalidades

- Criar metas
- Atualizar progresso
- Visualizar percentual concluído

## Endpoints

```txt
GET /goals
POST /goals
PATCH /goals/{id}
DELETE /goals/{id}
```

---

# 7. Dashboard Financeiro

## Indicadores

- Receita mensal
- Despesa mensal
- Saldo mensal
- Evolução financeira
- Gastos por categoria
- Progresso de metas

## Endpoints

```txt
GET /dashboard/monthly-summary
GET /dashboard/category-breakdown
GET /dashboard/cashflow
```

---

# 8. Importação CSV

## Objetivo

Permitir importação de extratos bancários.

## Funcionalidades

- Upload de CSV
- Parse automático
- Criação de transações
- Regras automáticas de categorização

## Regras simples

```txt
"UBER" → Transporte
"IFOOD" → Alimentação
"NETFLIX" → Assinaturas
```

## Endpoint

```txt
POST /imports/csv
```

---

# Banco de Dados

# Tabelas

## users

```txt
id
name
email
password_hash
created_at
```

## accounts

```txt
id
user_id
name
type
initial_balance
current_balance
is_active
```

## categories

```txt
id
user_id
name
type
color
icon
```

## transactions

```txt
id
user_id
account_id
category_id
type
amount
date
description
payment_method
is_recurring
```

## budgets

```txt
id
user_id
category_id
month
year
limit_amount
```

## goals

```txt
id
user_id
name
target_amount
current_amount
deadline
status
```

---

# Telas Flutter

## Fluxo Inicial

1. Splash Screen
2. Login
3. Cadastro

## Aplicação

4. Dashboard
5. Lista de Transações
6. Nova Transação
7. Editar Transação
8. Categorias
9. Orçamento
10. Metas
11. Perfil
12. Configurações

---

# Estrutura Flutter

```txt
lib/
│
├── core/
├── shared/
├── features/
│   ├── auth/
│   ├── dashboard/
│   ├── transactions/
│   ├── accounts/
│   ├── categories/
│   ├── budgets/
│   └── goals/
│
├── routes/
└── main.dart
```

---

# Regras de Negócio

## Transações

- Receita soma saldo
- Despesa reduz saldo

## Orçamento

- Limite acima de 80% gera alerta
- Limite acima de 100% gera status crítico

## Metas

- Meta concluída quando:

```txt
current_amount >= target_amount
```

---

# Segurança

## Backend

- JWT Authentication
- Hash de senha
- Middleware autenticado
- CORS configurado
- Rate limiting futuramente

## Banco

- Constraints
- Foreign Keys
- Índices

---

# Deploy

## Backend

- Railway
- Render
- Docker

## Banco

- Supabase PostgreSQL

## Mobile

- Android APK
- Play Store futuramente

---

# Roadmap

# Fase 1 — Backend Base

- Configurar FastAPI
- PostgreSQL
- SQLAlchemy
- Alembic
- JWT

# Fase 2 — CRUD Principal

- Accounts
- Categories
- Transactions

# Fase 3 — Flutter MVP

- Login
- Dashboard
- CRUD de transações

# Fase 4 — Orçamentos

- Limites
- Alertas
- Dashboard financeiro

# Fase 5 — Metas

- Objetivos financeiros
- Evolução visual

# Fase 6 — CSV Import

- Upload
- Parse
- Regras automáticas

# Fase 7 — Painel Web

- Streamlit
- Relatórios
- Analytics

# Fase 8 — IA Futuramente

## Possibilidades futuras

- Classificação automática de gastos
- Previsão financeira
- Sugestões de economia
- Assistente financeiro conversacional

---

# Diferenciais para Portfólio

## Técnicos

- Arquitetura limpa
- API REST profissional
- JWT
- Banco relacional
- Docker
- Mobile moderno
- Dashboard analítico

## Produto

- Aplicação real
- Escalável
- Evolução natural para IA
- Fácil demonstração em entrevistas

---

# Nome do Projeto

## Nome Principal

**OrçaFácil**

## Alternativas

- BudgetFlow
- MoneyTrack
- WalletWise
- FinanceHub

---

# Descrição Curta

> Aplicativo de controle financeiro pessoal com backend em FastAPI, app mobile em Flutter e painel web em Python, permitindo gestão de receitas, despesas, metas financeiras e orçamentos mensais.

---

# Possíveis Melhorias Futuras

- Dark Mode
- Notificações Push
- Multiusuário familiar
- Cartão virtual
- OCR de comprovantes
- Integração bancária
- Exportação PDF
- Exportação Excel
- Backup automático
- Sincronização offline

---

# Conclusão

O OrçaFácil é um projeto excelente para portfólio porque demonstra:

- Backend profissional
- Arquitetura escalável
- Integração mobile
- Modelagem de banco
- APIs REST
- UX real
- Organização de produto
- Evolução incremental

É um projeto suficientemente grande para impressionar recrutadores e suficientemente modular para evoluir continuamente.
