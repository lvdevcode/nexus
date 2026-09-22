    nexus
sistema distribuído de observabilidade e monitoramento em tempo real.
o nexus é uma plataforma de observabilidade projetada para coletar, processar, armazenar e transmitir métricas de infraestrutura em tempo real.
a arquitetura foi desenvolvida com foco em modularidade, baixo acoplamento, comunicação assíncrona, escalabilidade e tolerância a falhas.
---
    visão geral
```text
                         ┌─────────────────────┐
                         │      dashboard      │
                         │   react / typescript│
                         └──────────┬──────────┘
                                    │
                              websocket / http
                                    │
                                    ▼
┌──────────────┐           ┌─────────────────────┐
│    agent     │──────────▶│        api          │
│              │   http    │       fastapi       │
└──────┬───────┘           └──────────┬──────────┘
       │                              │
       │                              ▼
       │                    ┌─────────────────────┐
       │                    │   metrics service   │
       │                    └──────────┬──────────┘
       │                               │
       │                     ┌─────────┴─────────┐
       │                     ▼                   ▼
       │              ┌─────────────┐     ┌─────────────┐
       │              │ postgresql  │     │    redis    │
       │              └─────────────┘     └─────────────┘
       │
       ▼
┌────────────────────────────────────────────────────────┐
│                    infraestrutura                       │
│ cpu • ram • disk • network • processes • system info   │
└────────────────────────────────────────────────────────┘

⸻

arquitetura

o nexus utiliza uma arquitetura baseada em serviços independentes.

agent

executado na máquina monitorada.

responsável por:

* coleta de métricas
* identificação do host
* informações do sistema
* envio periódico dos dados
* tratamento de falhas de comunicação

api

responsável pela comunicação entre os componentes.

principais responsabilidades:

* autenticação
* validação
* gerenciamento de máquinas
* ingestão de métricas
* consulta histórica
* comunicação em tempo real

metrics service

processa e normaliza os dados recebidos pelos agentes antes da persistência.

postgresql

responsável pela persistência das métricas e informações das máquinas.

redis

utilizado como camada de cache e infraestrutura para operações de baixa latência.

dashboard

interface responsável pela visualização dos dados e acompanhamento das máquinas monitoradas.

⸻

stack

backend

python
fastapi
pydantic
sqlalchemy
postgresql
redis
websockets

frontend

react
typescript
vite

infraestrutura

docker
docker compose
github actions
linux

qualidade

pytest
ruff
mypy
eslint
prettier

⸻

princípios de engenharia

o projeto segue alguns princípios fundamentais:

* separação de responsabilidades
* baixo acoplamento
* alta coesão
* código tipado
* interfaces bem definidas
* validação de entrada
* tratamento explícito de erros
* configuração baseada em ambiente
* testes automatizados
* observabilidade desde a aplicação

⸻

fluxo de dados

┌──────────┐
│  máquina │
└────┬─────┘
     │
     │ coleta
     ▼
┌──────────┐
│  agent   │
└────┬─────┘
     │
     │ post /metrics
     ▼
┌──────────┐
│   api    │
└────┬─────┘
     │
     ▼
┌──────────────┐
│ normalização │
└──────┬───────┘
       │
   ┌───┴────┐
   ▼        ▼
postgresql redis
   │        │
   └───┬────┘
       ▼
   websocket
       │
       ▼
   dashboard

⸻

api

autenticação

post /api/v1/auth/login

máquinas

get    /api/v1/machines
get    /api/v1/machines/{id}
post   /api/v1/machines
delete /api/v1/machines/{id}

métricas

post /api/v1/metrics
get  /api/v1/machines/{id}/metrics

tempo real

ws /ws/metrics

⸻

segurança

o nexus foi projetado considerando:

* autenticação baseada em tokens
* validação de payloads
* variáveis sensíveis fora do código
* controle de acesso
* rate limiting
* comunicação segura
* princípio do menor privilégio

nenhuma credencial deve ser armazenada diretamente no código-fonte.

⸻

observabilidade

o próprio nexus deve ser observável.

logs
 │
 ├── aplicação
 ├── autenticação
 ├── agentes
 └── erros
métricas
 │
 ├── latência
 ├── throughput
 ├── utilização
 └── disponibilidade
traces
 │
 └── requisições distribuídas

⸻

resiliência

falhas de componentes não devem necessariamente interromper todo o sistema.

o projeto considera:

* timeouts
* retries
* tratamento de exceções
* reconexão de agentes
* health checks
* isolamento de serviços
* degradação controlada

⸻

escalabilidade

a arquitetura permite evoluir de uma execução local para múltiplas máquinas monitoradas.

             ┌── agent a
             │
             ├── agent b
             │
agents ──────┼── agent c ───▶ api
             │
             ├── agent d
             │
             └── agent n

a camada de ingestão pode posteriormente ser expandida para utilizar filas e processamento assíncrono.

⸻

estrutura

nexus/
│
├── agent/
│   ├── collectors/
│   ├── transport/
│   ├── config/
│   └── main.py
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── services/
│   │   ├── repositories/
│   │   └── main.py
│   │
│   └── tests/
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── types/
│   │
│   └── package.json
│
├── infrastructure/
│   ├── docker/
│   ├── postgres/
│   └── redis/
│
├── .github/
│   └── workflows/
│
├── docker-compose.yml
├── .env.example
├── makefile
└── readme.md

⸻

desenvolvimento

clone o projeto:

git clone https://github.com/seu-usuario/nexus.git
cd nexus

crie o ambiente:

cp .env.example .env

execute os serviços:

docker compose up --build

a api estará disponível em:

http://localhost:8000

documentação da api:

http://localhost:8000/docs

⸻

testes

execute os testes:

pytest

verificação de código:

ruff check .

verificação de tipos:

mypy .

⸻

roadmap

core

* [x]	coleta de métricas
* [x]	api rest
* [x]	websocket
* [x]	arquitetura modular

persistência

* [ ]	postgresql
* [ ]	migrações
* [ ]	retenção de métricas
* [ ]	consultas históricas

segurança

* [ ]	jwt
* [ ]	rbac
* [ ]	rate limiting
* [ ]	rotação de credenciais

observabilidade

* [ ]	logs estruturados
* [ ]	métricas internas
* [ ]	distributed tracing
* [ ]	health checks

inteligência

* [ ]	detecção de anomalias
* [ ]	alertas
* [ ]	limiares dinâmicos
* [ ]	análise histórica

infraestrutura

* [ ]	ci/cd
* [ ]	containerização completa
* [ ]	deploy automatizado
* [ ]	orquestração
* [ ]	escalabilidade horizontal

⸻

objetivo

o nexus não foi desenvolvido apenas como um monitor de cpu e memória.

o objetivo é construir, de forma incremental, uma plataforma capaz de demonstrar conceitos reais de:

sistemas distribuídos
        +
arquitetura de software
        +
backend
        +
redes
        +
banco de dados
        +
concorrência
        +
observabilidade
        +
devops

⸻

status

nexus
────────────────────────────────────
architecture       ████████████░░  80%
backend            █████████░░░░░  65%
agent              ██████████░░░░  70%
frontend           ██████░░░░░░░░  45%
infrastructure     ███████░░░░░░░  50%
observability      ████░░░░░░░░░░  30%
status: active development

⸻

licença

mt license