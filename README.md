# 🇧🇷 VERSÃO EM PORTUGUÊS

# 🧠 KoreAI: O Core de Orquestração para LLMs

O **KoreAI** é um *AI Gateway* e orquestrador de alta performance projetado para resolver as maiores dores na adoção de Inteligência Artificial corporativa: **altos custos de tokens, instabilidade de APIs e vendor lock-in**.

Ao introduzir uma DSL própria (**KoreScript**), o KoreAI atua como um middleware inteligente que roteia requisições dinamicamente, garantindo resiliência, economia e baixa latência sem a necessidade de reescrever o código da aplicação cliente.

---

## 🎯 O Problema vs. A Solução KoreAI

| A Dor do Mercado (O Problema) | Como o KoreAI Resolve |
| --- | --- |
| **Vendor Lock-in** | Compatibilidade nativa com a API da OpenAI. Troque de provedor por trás dos panos (Anthropic, Gemini, Ollama) sem mudar o código cliente. |
| **Custos Exorbitantes** | **Roteamento Inteligente:** Tarefas complexas vão para o GPT-4o; tarefas simples são roteadas para modelos locais ou mais baratos (Llama-3). |
| **Instabilidade / Downtime** | **Circuit Breaker:** Se uma API falhar, o KoreAI intercepta o erro e faz um fallback automático para o próximo modelo disponível na política. |
| **Requisições Redundantes** | **Semantic Caching:** Respostas são cacheadas com base no significado da pergunta, evitando pagar duas vezes pela mesma resposta. |

---

## 🇧🇷 Arquitetura Poliglota: O Propósito de Cada Linguagem

O **KoreAI** utiliza uma arquitetura baseada em microsserviços onde cada linguagem foi escolhida de forma pragmática para resolver gargalos específicos do ecossistema de Inteligência Artificial. Não acreditamos em uma "linguagem de prata", mas sim em usar a ferramenta certa para o trabalho certo.

### 🔵 Go (Golang): A Espinha Dorsal de Rede e Concorrência

* **Por que usamos:** O gargalo de um *AI Gateway* não é a IA em si, mas a rede (I/O). Go possui um modelo de concorrência nativo (*Goroutines*) extremamente leve e um coletor de lixo otimizado para baixa latência.
* **Como é implementado:** O Go atua como o servidor de borda (Reverse Proxy). Ele gerencia as conexões HTTP de entrada e se comunica com as outras camadas via chamadas gRPC de alta velocidade.
* **Para que serve:** * Receber milhares de requisições simultâneas da aplicação cliente.
* Gerenciar o estado do **Circuit Breaker** e o *Rate Limiting*.
* Roteamento de rede de forma assíncrona, garantindo que o gateway nunca trave a aplicação cliente enquanto aguarda a resposta da OpenAI.


### 🐍 Python: O Plano de Controle, DSL e Ecossistema

* **Por que usamos:** Python é o padrão ouro (*lingua franca*) da Inteligência Artificial. Ele possui o ecossistema mais rico para integração, tooling e construção de analisadores sintáticos (parsers).
* **Como é implementado:** Atua na camada de "Control Plane" (Plano de Controle) e interface de usuário. Não lida com o tráfego pesado de rede, mas orquestra as regras do sistema.
* **Para que serve:**
* **KoreScript Parser:** Ler, tokenizar e compilar a nossa DSL (arquivos `.kore`), transformando texto em regras lógicas para o Go executar.
* **CLI e Ferramentas:** O utilitário `kore-cli` para desenvolvedores validarem políticas antes do deploy.
* **Agentes de Interface:** Executar a integração com frameworks como LangChain/LangGraph para criar o chatbot interno que auxilia no debug do roteamento.



---



## 🇺🇸 Polyglot Architecture: The Purpose of Each Language

**KoreAI** utilizes a microservices-based architecture where each language was pragmatically chosen to solve specific bottlenecks in the Artificial Intelligence ecosystem. We do not believe in a "silver bullet" language, but rather in using the right tool for the right job.

### 🔵 Go (Golang): The Network & Concurrency Backbone

* **Why we use it:** The bottleneck of an AI Gateway is not the AI itself, but the network (I/O). Go features a remarkably lightweight native concurrency model (*Goroutines*) and a garbage collector optimized for ultra-low latency.
* **How it is implemented:** Go acts as the edge server (Reverse Proxy). It handles incoming HTTP connections and communicates with the other architectural layers via high-speed gRPC calls.
* **What it is used for:**
* Handling thousands of simultaneous requests from client applications.
* Managing **Circuit Breaker** state and Rate Limiting operations.
* Asynchronous network routing, ensuring the gateway never blocks the client application while waiting for OpenAI's response.



### 🐍 Python: The Control Plane, DSL & Ecosystem Glue

* **Why we use it:** Python is the gold standard (*lingua franca*) of Artificial Intelligence. It possesses the richest ecosystem for integrations, tooling, and building syntactic analyzers (parsers).
* **How it is implemented:** It operates in the "Control Plane" and user interface layer. It doesn't handle the heavy network traffic but rather orchestrates the system's underlying rules.
* **What it is used for:**
* **KoreScript Parser:** Reading, tokenizing, and compiling our custom DSL (`.kore` files), transforming plain text into logical execution rules for Go.
* **CLI and Tooling:** Powering the `kore-cli` utility for developers to validate routing policies prior to deployment.
* **Agent Interfaces:** Running integrations with frameworks like LangChain/LangGraph to build the internal chatbot that assists in routing debugging.




## 🏗️ KoreScript: Infraestrutura como Código para IA

O grande diferencial do KoreAI é a separação entre lógica de negócios e política de IA. Em vez de espalhar `if/else` pelo código, você define regras de roteamento usando o **KoreScript**, uma linguagem declarativa projetada para este ecossistema.

### `policies/production.kore`

```hcl
route "finance-analysis" {
  priority: 1
  model: "gpt-4o"
  fallback: "claude-3-5-sonnet"
  cache: true
}

route "customer-support" {
  priority: 2
  model: "llama-3-local"
  circuit_breaker {
    threshold: 0.8
    duration: "60s"
  }
}

```

---

## 🚀 Quickstart

### 1. Inicie a KoreVM e o Dashboard

O projeto já inclui um `docker-compose.yml` pré-configurado com Redis (para o cache semântico) e Grafana (para métricas de economia de custos).

```bash
docker-compose up -d

```

### 2. Valide suas Políticas (Kore CLI)

O motor de compilação valida as regras antes do deploy, garantindo segurança em produção.

```bash
kore-cli validate policies/production.kore

```

### 3. Integração Transparente (Drop-in Replacement)

Apenas mude a `BASE_URL` da sua aplicação. O KoreAI faz o resto.

```python
from openai import OpenAI

client = OpenAI(
    api_key="sua-chave",
    base_url="http://localhost:8080/v1" # Apontando para o gateway KoreAI
)

response = client.chat.completions.create(
    model="customer-support", # Nome da rota definida no KoreScript
    messages=[{"role": "user", "content": "Qual o status do meu pedido?"}]
)

```

---

## 👤 Sobre o Autor & Contato

Este projeto foi construído para demonstrar habilidades em **Engenharia de Software de Backend, Arquitetura de Sistemas Distribuídos e LLMOps**.

Estou aberto a novas oportunidades e desafios em Engenharia de Software (Sênior/Staff) e posições focadas em arquitetura de IA.

* **LinkedIn:** (https://www.linkedin.com/in/kauan-oliveira-324264378/)
* **Email:** kauandias747474@gmail.com

---

---

# 🇺🇸 ENGLISH VERSION

# 🧠 KoreAI: The Core LLM Orchestrator

**KoreAI** is a high-performance AI Gateway and orchestrator designed to solve the biggest pain points in enterprise AI adoption: **high token costs, API instability, and vendor lock-in**.

By introducing a proprietary Domain-Specific Language (**KoreScript**), KoreAI acts as an intelligent middleware that dynamically routes requests, ensuring resilience, cost-efficiency, and low latency without requiring application-level code changes.

---

## 🎯 The Problem vs. The KoreAI Solution

| Industry Pain Point (The Problem) | How KoreAI Solves It |
| --- | --- |
| **Vendor Lock-in** | Native compatibility with the OpenAI API structure. Switch providers (Anthropic, Gemini, Ollama) under the hood with zero client-side code changes. |
| **Skyrocketing Costs** | **Intelligent Routing:** Complex tasks are sent to GPT-4o; simple tasks are dynamically routed to local or cheaper models (Llama-3). |
| **API Instability / Downtime** | **Circuit Breaker:** If an API fails, KoreAI intercepts the error and triggers an automatic fallback to the next available model. |
| **Redundant Requests** | **Semantic Caching:** Answers are cached based on the semantic meaning of the prompt, saving tokens on repetitive queries. |

---

## 🏗️ KoreScript: Infrastructure as Code for AI

KoreAI's killer feature is the separation of business logic and AI routing policies. Instead of hardcoding complex fallback `if/else` statements, developers define rules using **KoreScript**, a declarative language built for AI routing.

### `policies/production.kore`

```hcl
route "finance-analysis" {
  priority: 1
  model: "gpt-4o"
  fallback: "claude-3-5-sonnet"
  cache: true
}

route "customer-support" {
  priority: 2
  model: "llama-3-local"
  circuit_breaker {
    threshold: 0.8
    duration: "60s"
  }
}

```

---

## 🚀 Quickstart

### 1. Boot up the KoreVM and Metrics Dashboard

The project includes a ready-to-use `docker-compose.yml` with Redis (for semantic caching) and Grafana (for cost-saving metrics).

```bash
docker-compose up -d

```

### 2. Validate your Policies (Kore CLI)

The built-in parser validates your rules before deployment to ensure production safety.

```bash
kore-cli validate policies/production.kore

```

### 3. Transparent Integration (Drop-in Replacement)

Simply change your application's `BASE_URL`. KoreAI handles the rest.

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-key",
    base_url="http://localhost:8080/v1" # Pointing to the KoreAI Gateway
)

response = client.chat.completions.create(
    model="customer-support", # The route name defined in KoreScript
    messages=[{"role": "user", "content": "What is the status of my order?"}]
)

```

---
# 🎛️ KoreAI Control Plane (Frontend)

[![Stack](https://img.shields.io/badge/Stack-Next.js_%7C_TypeScript_%7C_Tailwind-black)](#)
[![Design](https://img.shields.io/badge/UI-Shadcn%20%7C%20Zustand%20%7C%20Recharts-zinc)](#)

> O **KoreAI Control Plane** é a interface visual de alta densidade (Dashboard SaaS) desenvolvida para gerenciar e monitorar o gateway poliglota de inteligência artificial. Inspirado no design de ferramentas líderes como Vercel e Linear.

---

## 🇧🇷 Conteúdo em Português

### 📋 Visão Geral
A interface atua como o painel de controle do sistema, abstraindo a complexidade do roteamento de IA. Ela foca em **Observabilidade** e **Mutação Segura de Estado**, permitindo que os desenvolvedores monitorem a latência da rede, ajustem regras de *Circuit Breaker* e adicionem novas políticas de roteamento em tempo real.

### 🛠️ Stack Tecnológica
* **Next.js (App Router) & TypeScript:** Renderização de ponta e tipagem rigorosa para escalabilidade.
* **TailwindCSS & Shadcn/ui:** Estética minimalista focada no *Dark Mode* (`zinc-950` e bordas finas), reduzindo a fadiga cognitiva.
* **Zustand:** Gerenciamento de estado global leve para lidar com streams de telemetria sem engasgar a renderização.
* **React Hook Form + Zod:** Validação implacável no cliente, garantindo que regras de infraestrutura malformadas nunca cheguem ao backend.
* **Recharts:** Visualização tática e interativa de consumo de tokens e latência.

### 📂 Estrutura de Pastas (Frontend)
```text
frontend/
├── src/
│   ├── app/                  # Next.js App Router (Layouts e Páginas)
│   │   └── dashboard/        # Painel central de telemetria e gestão
│   ├── components/           # Componentes atômicos (Shadcn) e formulários
│   ├── store/                # Estado global de telemetria com Zustand
│   └── schemas/              # Validações Zod para mutação de infraestrutura

```

### 🚀 Como Executar Localmente

1. Certifique-se de ter o Node.js instalado (v18+ recomendado).
2. Entre na pasta do frontend e instale as dependências:

```bash
   cd frontend
   npm install

```

3. Inicie o servidor de desenvolvimento:

```bash
   npm run dev

```

4. Acesse o painel pelo navegador em: `http://localhost:3000`

---

## 🇺🇸 Content in English

### 📋 Overview

The interface serves as the command center for the system, abstracting the complexity of AI routing. It focuses deeply on **Observability** and **Secure State Mutation**, enabling developers to monitor network latency, tune *Circuit Breaker* thresholds, and provision new routing policies in real-time.

### 🛠️ Tech Stack

* **Next.js (App Router) & TypeScript:** Cutting-edge rendering paradigms and strict end-to-end type safety.
* **TailwindCSS & Shadcn/ui:** Minimalist, *Dark Mode* focused aesthetic (`zinc-950` and 1px borders) crafted to prevent cognitive overload.
* **Zustand:** Atomic, low-latency global state management that processes incoming telemetry without cascading DOM re-renders.
* **React Hook Form + Zod:** Rigorous client-side payload validation, ensuring malformed infrastructure mutations never breach the backend.
* **Recharts:** Tactical and interactive data visualization for token burn rates and proxy latency.

### 📂 Directory Structure (Frontend)

```text
frontend/
├── src/
│   ├── app/                  # Next.js App Router (Layouts and Pages)
│   │   └── dashboard/        # Centralized telemetry and management panel
│   ├── components/           # Atomic UI components (Shadcn) and forms
│   ├── store/                # Zustand global state (Live telemetry)
│   └── schemas/              # Zod schemas for infrastructure mutations

```

### 🚀 How to Run Locally

1. Ensure Node.js is installed (v18+ recommended).
2. Navigate to the frontend directory and install dependencies:

```bash
   cd frontend
   npm install

```

3. Spin up the development server:

```bash
   npm run dev

```

4. Access the dashboard via your browser at: `http://localhost:3000`



---
Perfeito. Para integrar o **Guardian.io** dentro do README do **KoreAI** de forma que ele pareça uma peça fundamental da sua "Stack de Orquestração Segura", siga este layout.

Copie e substitua a parte relevante do seu README por este bloco:

---

## 🛡️ Guardian.io: AI Security Middleware

Como o **KoreAI** orquestra dados sensíveis, a segurança não pode ser um detalhe — é a fundação. O **Guardian.io** é o sentinela do KoreAI, garantindo que nenhum tenant acesse os dados de outro.

### 🇧🇷 Versão em Português

O Guardian.io é um middleware de segurança de nível arquitetural integrado ao KoreAI para resolver vazamentos de dados entre clientes (*Cross-Tenant Data Leaks*).

* **Motor Anti-Caos:** Antes do KoreAI processar qualquer prompt, o Guardian injeta automaticamente restrições de isolamento na camada de acesso aos dados.
* **Arquitetura:** Interceptação em tempo real (Hooks de ORM) com latência < 1ms.
* **Segurança:** Bloqueio automático de qualquer query que tente evadir o isolamento do tenant.

### 🇺🇸 English Version

Guardian.io is an architectural-level security middleware integrated into KoreAI to solve cross-tenant data leaks.

* **Anti-Chaos Engine:** Before KoreAI processes any prompt, Guardian automatically injects isolation constraints at the data access layer.
* **Architecture:** Real-time interception (ORM Hooks) with < 1ms latency.
* **Security:** Automatically blocks any query attempting to bypass tenant isolation.

---

### ⚙️ Integração KoreAI + Guardian / KoreAI + Guardian Integration

```typescript
import { app } from './kore-gateway';
import { GuardianMiddleware } from 'guardian-io';

// 1. O Guardian protege a borda do KoreAI
// Guardian protects the edge of KoreAI
app.use(GuardianMiddleware.captureTenantContext({
  extractFrom: 'headers.x-tenant-id' 
}));

// 2. O KoreAI roteia, o Guardian garante a integridade
// KoreAI routes, Guardian ensures integrity
app.post('/v1/chat/completions', async (req, res) => {
  // A requisição só chega aqui se o tenant for validado.
  // The request only reaches here if the tenant is validated.
});

```

---

#  Architecture & Decision Log (PT-BR)

Esta seção detalha as decisões de engenharia por trás de cada componente do KoreAI. Nosso foco é: **Alta performance (Go), Segurança Zero-Trust, e Infraestrutura como Código (KoreScript).**

### 1. Control Plane (Frontend - Next.js)

*O objetivo aqui é a observabilidade. Usamos Next.js pela renderização híbrida e Shadcn/ui pela consistência visual.*

| Arquivo | Decisão Técnica |
| --- | --- |
| `app/layout.tsx` & `page.tsx` | **Roteamento:** Adotamos o App Router para melhor performance e SEO. |
| `dashboard/page.tsx` | **UI:** O painel de controle centraliza a telemetria, garantindo que o DevOps tenha foco total no tráfego de IA. |
| `forms/create-policy-form.tsx` | **UX/Segurança:** Formulário tipado para evitar erros humanos na criação de políticas críticas. |
| `ui/dashboard-shell.tsx` | **Design:** Componentização para manter a UI consistente e reduzir a repetição de código (DRY). |
| `charts/*.tsx` | **Visualização:** Recharts para renderizar dados complexos com baixo custo computacional. |
| `security/guardian-status.tsx` | **Observabilidade:** Monitoramento do Guardian.io integrado para reação imediata a incidentes. |
| `hooks/use-telemetry.ts` | **Performance:** Polling otimizado para não sobrecarregar a rede do cliente. |
| `lib/utils.ts` | **Manutenibilidade:** Padronização de classes Tailwind para manter o design system limpo. |
| `schemas/kore-policy-schema.ts` | **Segurança:** Zod garante que nenhum payload malformado chegue ao backend (Validation-at-the-Edge). |
| `store/use-titan-store.ts` | **State:** Zustand para gerenciamento de estado global sem o *boilerplate* e o *overhead* do Redux. |

### 2. Motor Anti-Caos (Guardian.io)

*Fundação de segurança focada em isolamento de tenants e conformidade.*

* **`proxy/security/guardian_middleware.go`**: Interceptação em C-level speed. O Go permite checagem de header em < 1ms, essencial para evitar *data leaks*.
* **`proxy/security/isolation_engine.go`**: Lógica de "containerização de dados". Garante que o contexto da query não vaze entre tenants.
* **`proxy/security/audit_logger.go`**: Imutabilidade. Logs de segurança são o padrão ouro para conformidade (compliance).
* **`security/tenant_registry.py`**: A *Source of Truth* de acesso. Separação clara entre a política (Python) e a execução (Go).
* **`security/policy_validator.py`**: Validação estática. Evita que regras inseguras entrem em produção (Fail-Fast).

### 3. Edge Gateway (Go)

*Performance extrema. O Go foi escolhido por sua capacidade de lidar com I/O intensivo.*

* **`proxy/main.go`**: Entrypoint. O uso de `net/http` padrão garante estabilidade e facilidade de deploy.
* **`proxy/proxyconnector.go`**: Abstração de API. O Go atua como um adaptador de protocolo entre o mundo externo e o ecossistema Ollama.
* **`proxy/cache.go`**: Otimização de Custo. O Cache Semântico reduz o gasto com tokens, otimizando o ROI da infraestrutura.
* **`proxy/circuitbreaker.go`**: Resiliência. Implementa o padrão de *Failover* para garantir 99.9% de uptime, mesmo se o modelo local falhar.
* **`proxy/metrics.go`**: Exportação de telemetria. Essencial para analisar o *Time-to-First-Token* (TTFT).
* **`core/types/gateway-types.go`**: Tipagem Forte. Garante que contratos de dados sejam respeitados em todo o pipeline.

### 4. DSL & Ecossistema (Python)

*O cérebro do sistema. Aqui transformamos texto em lógica de infraestrutura.*

* **`parser/lexer.py` & `parser.py**`: Compilação. O uso de uma DSL (`.kore`) permite que o time de infra configure o sistema sem tocar em código binário.
* **`policies/production.kore`**: IaC. A declaração do estado desejado da infraestrutura.
* **`proto/kore.proto`**: Comunicação robusta. gRPC para garantir troca de mensagens entre Python e Go com contrato de dados rígido.
* **`interface/chat_agent.py`**: IA para IA. Agente de suporte interno que usa os próprios modelos para debugar logs.
* **`openclaw/openclaw_bridge.py`**: Interoperabilidade. Extensibilidade para sistemas externos.

---

# 📚 Architecture & Decision Log (EN-US)

This section outlines the engineering decisions behind KoreAI. Our core pillars: **High Performance (Go), Zero-Trust Security, and Infrastructure as Code (KoreScript).**

### 1. Control Plane (Frontend - Next.js)

*Focus: Observability. We selected Next.js for its hybrid rendering capabilities and Shadcn/ui for consistent, enterprise-grade visuals.*

| File | Technical Decision |
| --- | --- |
| `app/layout.tsx` & `page.tsx` | **Routing:** App Router for optimal performance and SEO. |
| `dashboard/page.tsx` | **UI:** Centralizes telemetry, allowing DevOps to focus on AI traffic. |
| `forms/create-policy-form.tsx` | **UX/Safety:** Typed forms to prevent human error when defining critical AI policies. |
| `ui/dashboard-shell.tsx` | **Design:** Component-based architecture to ensure UI consistency and DRY code. |
| `charts/*.tsx` | **Visualization:** Recharts for low-overhead, complex data rendering. |
| `security/guardian-status.tsx` | **Observability:** Real-time Guardian.io monitoring for instant incident response. |
| `hooks/use-telemetry.ts` | **Performance:** Optimized polling to prevent network congestion. |
| `lib/utils.ts` | **Maintainability:** Standardized Tailwind classes for a cohesive design system. |
| `schemas/kore-policy-schema.ts` | **Security:** Zod-based validation ensuring malformed payloads are dropped at the edge. |
| `store/use-titan-store.ts` | **State:** Zustand for global state without Redux boilerplate and overhead. |

### 2. Anti-Chaos Engine (Guardian.io)

*Foundation: Zero-Trust isolation and compliance.*

* **`proxy/security/guardian_middleware.go`**: Edge performance. Go's low-latency allows header checks in < 1ms to prevent data leaks.
* **`proxy/security/isolation_engine.go`**: Data containerization. Ensures query contexts cannot leak between tenants.
* **`proxy/security/audit_logger.go`**: Immutability. Security logs are the gold standard for regulatory compliance.
* **`security/tenant_registry.py`**: Source of Truth. Clear separation between policy logic (Python) and edge execution (Go).
* **`security/policy_validator.py`**: Static analysis. Prevents insecure rules from ever reaching production (Fail-Fast).

### 3. Edge Gateway (Go)

*Performance: Go was chosen for its high-concurrency capability in I/O-bound tasks.*

* **`proxy/main.go`**: Entrypoint. Standard `net/http` ensures deployment stability.
* **`proxy/proxyconnector.go`**: API Abstraction. Acts as a protocol adapter between the world and the Ollama ecosystem.
* **`proxy/cache.go`**: Cost optimization. Semantic caching reduces token usage and boosts ROI.
* **`proxy/circuitbreaker.go`**: Resilience. Implements failover patterns for high availability, even during model outages.
* **`proxy/metrics.go`**: Telemetry. Vital for monitoring Time-to-First-Token (TTFT).
* **`core/types/gateway-types.go`**: Strong typing. Ensures data contracts are respected across the pipeline.

### 4. DSL & AI Ecosystem (Python)

*The Brain: Transforming human-readable text into infrastructure logic.*

* **`parser/lexer.py` & `parser.py**`: Compilation. Using a DSL (`.kore`) enables infra teams to configure the system without touching binary code.
* **`policies/production.kore`**: IaC. Declarative state of the infrastructure.
* **`proto/kore.proto`**: Robust communication. gRPC ensures strict data contracts between Python and Go.
* **`interface/chat_agent.py`**: AI for AI. Internal support agent using local models to debug infra logs.
* **`openclaw/openclaw_bridge.py`**: Interoperability. Extensibility for external ecosystem integration.

---

# 🎓 Fundamentos Teóricos Aplicados | Applied Theoretical Foundations

Esta seção cobre os pilares de Ciência da Computação, Arquitetura de Software e Engenharia de Código que sustentam o KoreAI.

---

### 1. Teoria da Computação e Compiladores | Compiler Theory & DSL Design

**PT-BR:** Para criar o KoreScript, aplicamos técnicas de **Teoria de Linguagens Formais**. O Lexer transforma o texto em tokens através de análise léxica (padrões regulares). O Parser transforma essa lista de tokens em uma **Abstract Syntax Tree (AST)**, permitindo que o computador entenda a estrutura hierárquica das políticas. Isso é o coração de qualquer compilador ou interpretador.

**EN:** To build KoreScript, we applied **Formal Language Theory**. The Lexer converts text into tokens via lexical analysis (regex patterns). The Parser then converts this token stream into an **Abstract Syntax Tree (AST)**, allowing the computer to understand the hierarchical policy structure. This is the core mechanism behind any compiler or interpreter.

---

### 2. Padrões de Arquitetura de Sistemas | System Architecture Patterns

**PT-BR:** Utilizamos o **Proxy Pattern** para interceptar chamadas entre o cliente e o serviço, permitindo a injeção de lógica (Cache, Segurança). Também aplicamos o **Circuit Breaker Pattern**, que é fundamental em sistemas distribuídos para evitar falhas em cascata; se o serviço de IA falha, o sistema "abre o circuito" e redireciona o tráfego para um fallback, garantindo resiliência.

**EN:** We utilized the **Proxy Pattern** to intercept calls between the client and the service, enabling logic injection (Cache, Security). We also applied the **Circuit Breaker Pattern**, which is fundamental in distributed systems to prevent cascading failures; if the AI service fails, the system "opens the circuit" and reroutes traffic to a fallback, ensuring resilience.

---

### 3. Segurança e Zero-Trust | Security and Zero-Trust Architecture

**PT-BR:** O Guardian.io segue o princípio de **Zero-Trust**. Na segurança da informação, isso significa "nunca confiar, sempre verificar". Implementamos isso através de um **Middleware de Autenticação** que valida a identidade do Tenant (`x-tenant-id`) a cada requisição na borda (Edge), garantindo o **Isolamento de Tenant** (Multi-tenancy isolation).

**EN:** The Guardian.io follows the **Zero-Trust** principle. In information security, this means "never trust, always verify." We implemented this through an **Authentication Middleware** that validates the Tenant identity (`x-tenant-id`) on every request at the Edge, ensuring **Multi-tenancy isolation**.

---

### 4. Clean Code e SOLID | Software Engineering Principles

**PT-BR:** Aplicamos **SOLID**, com foco especial no **Single Responsibility Principle (SRP)**. Cada arquivo em nosso projeto tem uma única responsabilidade: o `lexer.py` só faz análise léxica, o `cache.go` só gerencia o cache. Isso torna o código desacoplado e fácil de testar. Também seguimos o **DRY (Don't Repeat Yourself)** ao centralizar utilitários em `lib/utils.ts`.

**EN:** We applied **SOLID**, with a specific focus on the **Single Responsibility Principle (SRP)**. Each file in our project has one single responsibility: `lexer.py` only performs lexical analysis, `cache.go` only manages caching. This makes the code decoupled and testable. We also follow **DRY (Don't Repeat Yourself)** by centralizing utilities in `lib/utils.ts`.

---

### 5. Sistemas Distribuídos e Performance | Distributed Systems & Performance

**PT-BR:** A escolha do Go para o Gateway baseia-se no modelo de concorrência por **Goroutines** e canais (Communicating Sequential Processes - CSP). Isso nos permite processar I/O pesado (rede) com latência ultrabaixa. Além disso, o **Semantic Caching** é uma otimização de performance onde não comparamos strings, mas vetores de intenção (embeddings), o que é a base da inteligência artificial moderna.

**EN:** The choice of Go for the Gateway is based on its concurrency model using **Goroutines** and channels (Communicating Sequential Processes - CSP). This allows us to process heavy I/O (network) with ultra-low latency. Furthermore, **Semantic Caching** is a performance optimization where we don't compare strings, but intention vectors (embeddings), which is the foundation of modern artificial intelligence.

---

## 👤 About the Author & Contact

This project was built to demonstrate expertise in **Backend Software Engineering, Distributed Systems Architecture, and LLMOps**.

I am currently open to new opportunities and challenges in Software Engineering and AI Architecture roles. Let's connect!

* **LinkedIn:** https://www.linkedin.com/in/kauan-oliveira-324264378/
* **Email:** kauandias747474@gmail.com
