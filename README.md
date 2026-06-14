

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

* **LinkedIn:** `[Seu Link Aqui]`
* **Email:** `[Seu Email Aqui]`

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

## 👤 About the Author & Contact

This project was built to demonstrate expertise in **Backend Software Engineering, Distributed Systems Architecture, and LLMOps**.

I am currently open to new opportunities and challenges in Software Engineering (Senior/Staff) and AI Architecture roles. Let's connect!

* **LinkedIn:** https://www.linkedin.com/in/kauan-oliveira-324264378/
* **Email:** kauandias747474@gmail.com
