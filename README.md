# AWS Bedrock AgentCore: Enterprise Customer Support & Research Platform

[![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Amazon Bedrock](https://img.shields.io/badge/Amazon%20Bedrock-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/bedrock/)
[![Claude 4.6 Sonnet](https://img.shields.io/badge/Claude%204.6%20Sonnet-D97706?style=for-the-badge&logo=anthropic&logoColor=white)](https://www.anthropic.com/)
[![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-000000?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![Cedar](https://img.shields.io/badge/Cedar%20Policies-4F46E5?style=for-the-badge&logo=security&logoColor=white)](https://www.cedarpolicy.org/)
[![CloudWatch](https://img.shields.io/badge/CloudWatch-FF4F00?style=for-the-badge&logo=amazon-cloudwatch&logoColor=white)](https://aws.amazon.com/cloudwatch/)

---

## Objective
The primary objective of this project is to implement, secure, govern, and optimize an enterprise-grade AI Agent environment using **Amazon Bedrock AgentCore** managed service. Rather than treating GenAI as a standalone chatbot demo, this project demonstrates a robust end-to-end engineering pipeline required to transition an AI agent from local prototyping to a secure, governed, and optimized production environment.

Key educational and technical areas covered include:
* **Agent Anatomy & Custom Tool Orchestration** using the **Strands** framework.
* **Stateful Multi-Session Memory** across underlying microVM recycles using semantic and summarization strategies.
* **Enterprise Tool Scaling** via secure Gateways wrapping AWS Lambda behind natural language schemas.
* **Production Security & User Isolation** using JWT-based Cognito User Pools and PyJWT validation.
* **Continuous Online Evaluations** to automatically audit response helpfulness, correctness, and tool selection accuracy.
* **Fine-Grained Governance** using AWS Cedar Policies to enforce strict behavioral limits.
* **Zero-Code Declarative Orchestration** with AgentCore Harness and Human-in-the-Loop (HITL) approval gates.
* **Data-Driven Telemetry & A/B Canary Deployments** to evaluate optimized prompts on live production traffic with statistical significance.

---

## Business Problem 
### **Apex SmartTech Customer Support**
**Apex SmartTech** is a rapidly growing consumer electronics enterprise selling smartwatches, laptops, and smartphones. To keep pace with massive order volumes, they must automate their customer support operations. However, a basic chatbot is insufficient due to severe business and compliance challenges:
1. **Context Fragmentation**: Returning customers find themselves repeating their preferences and order histories during follow-ups because standard LLM API calls are stateless.
2. **Legacy Backend Systems**: Core business logic (such as real-time warranty checks and automated refunds) resides in legacy REST APIs and serverless AWS Lambda functions.
3. **Governance & Compliance Risks**: Without strict boundaries, a LLM might erroneously process a $1,000 refund request without manager authorization or leak PII such as email addresses.
4. **Operational Security**: Customer support endpoints must restrict access strictly to authenticated customers with valid JWT tokens, ensuring complete data isolation between customer sessions.
5. **Quality Degradation**: Support leads have no direct visibility into whether the LLM is giving factual, cohesive, or policy-compliant answers to customers.

### **The Solution**
By building on **Amazon Bedrock AgentCore**, Apex SmartTech establishes:
* A **stateful, secure customer-facing web chat** that automatically recognizes customers (e.g., Sara) and remembers past interactions and delivery preferences (e.g., preferred email updates).
* An **authorized, microservice-connected gateway** that wraps backend Lambda tools and is governed by strict **Cedar Policies** (e.g., automatic refunds must be under $100 and only for defective products).
* A **low-code back-office Order Research Specialist** that handles complex cross-product comparisons, creates Excel reports, and escalates higher-value refunds to human managers via a secure Human-in-the-Loop approval script.
* An **A/B testing infrastructure** to run 80/20 canary tests comparing system prompts, ensuring that telemetry-derived optimizations are statistically validated before full rollout.

---

## Key Features
* **Stateless Local Prototyping to Stateful Cloud Runtime**: Scaffolded via the `agent-core` CLI, moving seamlessly from local testing under `agentcore dev` to Fargate/microVM runtime in the cloud.
* **Dual-Strategy Shared Memory**: Implements both **Semantic memory** (stores declarative facts, preferences, and details) and **Summarization memory** (retains conversation arc summaries) persisting across agent microVM recycles.
* **MCP-Compatible Secure Gateway**: Wraps legacy serverless AWS Lambdas inside a natural language tool schema, routing requests securely via gateway OAuth egress.
* **Cognito OIDC Security**: Enforces JWT bearer token validation on both the Agent Runtime and Gateway, ensuring that all client requests are authenticated and authorized.
* **Built-In Online Evaluators**: Configured evaluation monitors to measure goal success, answer correctness, and tool choice with CloudWatch observability integration.
* **Cedar Policy Guardrails**: Declares deterministic boundaries for LLM behavior (e.g., forbid refunds above $100, enforce "defective" product check) to avoid financial and compliance exposure.
* **PII Tool Guardrails**: Implements gateway-boundary filters to block sensitive PII (e.g., emails) from ever being sent to the LLM backend.
* **Zero-Code Declarative Harness**: Deploys secondary specialists utilizing `harness.json` configuration, local file system session persistence, and custom skills.
* **Human-In-The-Loop (HITL)**: Interrupts reasoning traces for expensive transactions, requesting explicit manager approval via AWS SDK (boto3) before resuming execution.
* **Continuous Prompt Optimization & A/B Testing**: Pulls trace telemetry from CloudWatch to auto-recommend optimized prompts, packaging them into control/treatment config bundles to split live traffic 80/20 and calculate statistical p-value significance.

---

## Technology Stack
* **Foundation Models**: Anthropic Claude 4.6 Sonnet (configured in `load.py`), Amazon Nova (cheaper, high-speed model configured in `harness.json`).
* **Generative AI Orchestration**: Amazon Bedrock AgentCore, Strands Framework.
* **Compute & Backends**: AWS Lambda (Warranty Check & Process Refund services), MCP (Model Context Protocol) Clients (EXA.AI Web Search engine).
* **Identity & Security**: Amazon Cognito User Pools (JWT OIDC), AWS IAM, PyJWT validation library.
* **Observability & Telemetry**: Amazon CloudWatch GenAI Observability, CloudWatch Logs, CloudWatch Application Signals.
* **Data & Configuration**: AWS Systems Manager (SSM) Parameter Store, AgentCore Config (`agentcore.json`), Target Configurations (`targets.json`).
* **Frontend Frameworks**: Python Flask web server, HTML5 / CSS3 / JavaScript.
* **Developer Tooling**: Python 3.12, Node.js v22+, AWS CDK, boto3 (AWS SDK for Python).

---

## Implementation Summary

### **Part 1: Scaffolding & Local Prototyping**
* Initialized the customer support agent project using the Strands framework:
```bash
agentcore create --name customer-support --framework strands --provider bedrock
```
* Configured the LLM to use **Claude 4.6 Sonnet** inside `load.py`.
* Replaced the mock mathematical tools in `main.py` with custom customer support logic (`get_return_policy`, `get_product_info`).
* Connected the **mcp.exa.ia MCP client** to enable live web-search capabilities for real-time troubleshooting.
* Tested the agent locally via `agentcore dev` to verify the accuracy of the local knowledge integration.

<figure>
  <img src="https://i.gyazo.com/8d5b08cb8914c001b5e9e1b499d8b413.png">
  <figcaption style="text-align: center;">Initial architecture</figcaption>
</figure>

* Deployed the prototype to the cloud using `agentcore deploy`, creating an active production endpoint.

<figure>
  <img src="https://i.gyazo.com/df3cce543782cd42ba5eedbe7f467595.png">
  <figcaption style="text-align: center;">Architecture after deployment </figcaption>
</figure>


### **Part 2: State-Preserving Multi-Session Memory**
* Initialized shared memory configurations:
```bash
agentcore add memory
```
* Configured two memory strategies inside `agentcore.json`: **Semantic memory** (stores core facts and preferences) and **Summarization memory** (tracks user interaction summaries).
* Constructed `session.py` to manage memory session states.
* Modified `main.py` to wrap reasoning paths within the `MemorySessionManager`.
* Allowed Bedrock to inject the session context by including the `Amazon-Bedrock-AgentCore-User-ID` header inside the allowlist.
* Deployed the stateful agent (Version 2). Verified that the agent successfully remembered customer preferences (e.g., Sara preferring email updates) even when sessions were recycled across microVM lifecycles.

<figure>
  <img src="https://i.gyazo.com/ae6bc4fe3b3a960dce0cda9f6b8bfcd5.png">
  <figcaption style="text-align: center;">Architecture after adding memory </figcaption>
</figure>


### **Part 3: Scale via Secure Enterprise Gateway**
* Configured the **AgentCore Gateway** inside `agentcore.json` to bridge natural language reasoning with backend services.
* Constructed a tool schema JSON (`warranty.json`) defining AWS Lambda inputs and descriptions in descriptive natural language.
* Registered the **Warranty Check AWS Lambda function** as an active gateway target.
* Wired the gateway client (`client.py`) and updated `main.py` to route queries through the newly secured Gateway.

<figure>
  <img src="https://i.gyazo.com/eddf1114694edd35165cb9184c246314.png">
  <figcaption style="text-align: center;">Tool usage validation</figcaption>
</figure>

* Deployed Version 3. Validated that the agent successfully query-resolved a product warranty (e.g., for a smartwatch or laptop) by routing requests through the Gateway to the live Lambda function.

<figure>
  <img src="https://i.gyazo.com/7753b5be404d2cb6b8c28c814081fbd5.png">
  <figcaption style="text-align: center;">Lambda as a tool</figcaption>
</figure>



### **Part 4: Multi-User Security & CloudWatch Observability**
* Differentiated conversational session persistence from long-term memory: validated that temporary user sessions (e.g., Alex) isolate conversational context, whereas registered memory (e.g., Sara) survives across disconnected sessions.
* Queried the Cognito parameter configurations (Discovery URL, Client ID, Pool ID, Web Client ID) from the **SSM Parameter Store**.
* Secured the runtime in `agentcore.json` by replacing IAM authorization with JWT authentication.
* Updated `main.py` to decode and validate JWT tokens using the `PyJWT` package, safely extracting the customer’s `user_id`.
* Deployed Version 4, validating that unauthenticated requests (missing JWT tokens) were strictly blocked.
* Extracted traces via CLI (`agentcore traces`), downloaded tracing reports as `trace.json`, and filtered CloudWatch Logs for warranty and error trends.

<figure>
  <img src="https://i.gyazo.com/15f7eab90abd17d70b106e5d6778e592.png">
  <figcaption style="text-align: center;">Authentication and observability  implementation </figcaption>
</figure>


### **Part 5: Continuous Online Evaluation**
* Added an online evaluation configuration block to `agentcore.json`:
```bash
agentcore add online-eval \
  --name QualityMonitor \
  --runtime CustomerSupport \                                 
  --evaluator Builtin.GoalSuccessRate Builtin.Correctness Builtin.ToolSelectionAccuracy \
  --sampling-rate 100 \
  --enable-on-create
```
* Deployed monitors covering three key dimensions: **Goal Success Rate**, **Answer Correctness**, and **Tool Selection Accuracy**.
* Injected test interaction scripts to generate varied user traffic.
* Queried on-demand evaluators to output performance summaries directly to the terminal:
```bash
agentcore run eval \
  --runtime CustomerSupport
  --evaluator Builtin.GoalSuccessRate Builtin.Correctness \
  --days 1
```
* Monitored the evaluation dashboard inside CloudWatch to inspect verbose evaluation telemetry, ensuring response quality remained above established thresholds.

<figure>
  <img src="https://i.gyazo.com/e14cfc29a9909ad69f99aab191b8ef54.png">
  <figcaption style="text-align: center;">Architecture with continuous evaluation implemented </figcaption>
</figure>

### **Part 6: Full-Stack Web Interface Integration**
* Developed a Python **Flask web application** (`app.py`) incorporating Cognito OIDC authorization flows.
* Crafted a user-friendly HTML/JS chat dashboard (`index.html`) using template variables to establish authorized sessions.
* Linked Cognito login credentials to retrieve secure JWT tokens.
* Connected the browser chat window directly to the Bedrock AgentCore REST endpoints by appending JWT bearer headers, enabling real-time, authenticated UI interactions.

<figure>
  <img src="https://i.gyazo.com/1aacd4f9e03464e4388597de2d8e84f7.png">
  <figcaption style="text-align: center;">Chat interface implementation </figcaption>
</figure>

### **Part 7: Governance with Cedar Policies**
* Initialized policy engine specifications inside `agentcore.json`.
* Deployed AWS Lambda-backed refund endpoints (`process_refund`) to the gateway.
* Wrote deterministic **Cedar Policies** defining exact guardrails:
 * *Rule 1*: Permit automated refund requests under $100.
 * *Rule 2*: Forbid any refund request where the reason is not explicitly marked as "defective".
* Deployed Version 6. Verified that the Cedar engine successfully blocked a $500 refund query and auto-rejected non-defective refund claims.

<figure>
  <img src="https://i.gyazo.com/da11841df02fb65ef3d12707d1c47c0f.png">
  <figcaption style="text-align: center;">Cedar policies testing</figcaption>
</figure>

* Wired **Sensitive Guardrails** to prevent critical PII (e.g., email addresses) from leaking into LLM system logs.

<figure>
  <img src="https://i.gyazo.com/41e62b5b921c40618d0854c52f2a4fac.png">
  <figcaption style="text-align: center;">Cedar policies implementation architecture</figcaption>
</figure>

### **Part 8: Zero-Code Harness Agent & Human-in-the-Loop**
* Built a secondary specialist (the **Order Research Specialist**) using the zero-code **AgentCore Harness**.

<figure>
  <img src="https://i.gyazo.com/232d305a9eb23f6aafba42007ca47529.png">
  <figcaption style="text-align: center;">Harness Agent architecture </figcaption>
</figure>

* Configured the agent declaratively inside `harness.json`, specifying the system persona, Amazon Nova model, and target tool connections.
* Connected the Harness to the existing secure Gateway, inheriting existing Cedar policies and tools.

<figure>
  <img src="https://i.gyazo.com/2c9c87103401bcd1cd38dabaea625d2f.png">
  <figcaption style="text-align: center;">Cedar policies inheritance</figcaption>
</figure>

* Configured gateway egress OAuth credentials (`credentials` config) for machine-to-machine authentication.
* Designed an **inline Human-in-the-Loop (HITL)** function to pause execution for high-value refunds, prompting a human manager for manual approval.
* Developed a `boto3` script to invoke the Harness, capture tool requests, await approval input, and resume execution on AWS Bedrock.

<figure>
  <img src="https://i.gyazo.com/c5ae5d8176bf8a1392ee3c0cae662b31.png">
  <figcaption style="text-align: center;">HITL testing</figcaption>
</figure>

* Expanded agent skills by importing an **Anthropic Excel Skill** to compile and output formatted warranty comparison reports.

<figure>
  <img src="https://i.gyazo.com/a6946fba54c3214b4ae84793971cf987.png">
  <figcaption style="text-align: center;">Added Excel Skill</figcaption>
</figure>


### **Part 9: Trace-Driven A/B Canary Deployments**
* Analyzed traces in CloudWatch Application Signals to generate optimized system prompts and refined tool descriptions.
* Created immutable, versioned snapshot packages: **Control Config Bundle** (original prompt) and **Treatment Config Bundle** (recommended prompt).
* Deployed a lightweight test runtime (Customer Support AB Runtime) utilizing IAM authentication.
* Initiated an A/B gateway split allocating **80% traffic to Control** and **20% traffic to Treatment**. 
* Executed a automated load generator script sending back-and-forth prompt iterations to gather telemetry.
* Queried statistical evaluation reports to verify the treatment outperformed control with high statistical confidence ($p$-value $< 0.05$), subsequently promoting the optimized configuration to full production.

<figure>
  <img src="https://i.gyazo.com/7df1cd5df8989e96c5aad68fd35a6aca.png">
  <figcaption style="text-align: center;">Statistical report of A/B testing</figcaption>
</figure>

---

## Testing/Validation

To run and validate the capabilities built across the project, the following `agent-core` CLI commands were used:

### **1. Local Verification & Development**
Initiate local testing mode to run conversational prompts within your terminal, avoiding cloud charges during tool debugging:
```bash
agentcore dev
```
* *Test Prompt (Knowledge Integration)*: `"What is the return policy for electronics?"`
* *Verification*: Verify the agent outputs the exact return policy (e.g., 30-day window, original packaging, full refund).
* *Test Prompt (Multi-Tool Query)*: `"I bought product 001 and want to return it under the standard smartwatch policy. What do I do?"`
* *Verification*: Verify the model concurrently invokes `get_product_info` and `get_return_policy` within a single reasoning loop.

<figure>
  <img src="https://i.gyazo.com/4343cee966023e4cc39145da0fa9690f.png">
  <figcaption style="text-align: center;">Local Agent testing</figcaption>
</figure>

### **2. Cloud Deployment & Active Endpoint Check**
Compile local code structures and deploy them directly to AWS Fargate/microVM resources:
```bash
agentcore deploy
```
Verify the status, active version, target AWS accounts, and Amazon Resource Names (ARNs):
```bash
agentcore status
```

<figure>
  <img src="https://i.gyazo.com/592efef7032aa91ac3d4e796baa0e115.png">
  <figcaption style="text-align: center;">Agent deployment</figcaption>
</figure>



### **3. Multi-Session Memory Verification**
Simulate distinct customer sessions to verify long-term memory persistence:
1. **Session 1 (Establish memory)**: Invoke the runtime passing user `Sara` and record her preference:
```bash
agentcore invoke --session-id sess-001 --user-id sara --message "My name is Sara and I prefer email updates."
```
2. **Session 2 (Verify persistence across recycled VM resources)**: Invoke using a completely new session ID, but keeping user ID `sara`:
```bash
agentcore invoke --session-id sess-002 --user-id sara --message "What are my preferences?"
```
 * *Verification*: The agent must output that she prefers email updates, proving persistent memory retrieval.

<figure>
  <img src="https://i.gyazo.com/684d9730efebb1aabbf109361ea1be2e.png">
  <figcaption style="text-align: center;">Memory persistence </figcaption>
</figure>


### **4. Security Validation (Cognito JWT Authorization)**
Attempt to invoke the agent runtime endpoint with and without valid security credentials:
* **Unauthorized Request**:
```bash
agentcore invoke "what's the return policy for electronics?" \
  --session-id $SESSION_3 --stream --json 
```
 * *Expected Response*: `401 Unauthorized` (access denied due to missing bearer token).
* **Authorized Request**: Generate OIDC credentials via Cognito CLI and inject bearer token:
```bash
SESSION_3=$(python3 -c 'import uuid; print(uuid.uuid4( ))')
agentcore invoke "What's the return policy for electronics?" \
  --session-id $SESSION_3 --bearer-token "$TOKEN" --stream
```
 * *Expected Response*: Successful resolution of the policy.

 <figure>
  <img src="https://i.gyazo.com/2cf55b4acd0906c6e61846c9ff98e4e0.png">
  <figcaption style="text-align: center;">Securing runtime with JWT</figcaption>
</figure>

### **5. Cedar Policy Engine Verification**
Test strict governance parameters:
* **Valid Refund Request**: Defective smartwatch refund under $100:
```bash
agentcore invoke --token $TOKEN --message "I need to return order 12345 because it arrived defective. The price is $50. Please refund me."
```
 * *Expected Response*: `Refund processed successfully.`
* **Violated Limit Request**: Defective smartwatch refund above $100 limit:
```bash
agentcore invoke --token $TOKEN --message "I need to return order 12345 because it arrived defective. The price is $500. Please refund me."
```
 * *Expected Response*: `Refund denied. The request exceeds the automated transaction limit of $100.`
* **Violated Defect Policy Request**: Standard refund request without defective reasoning:
```bash
agentcore invoke --token $TOKEN --message "Please refund my smartwatch order 12345. I simply changed my mind."
```
 * *Expected Response*: `Refund denied. Our policies require items to be unused or defective to process automated refunds.`

### **6. Observability & Evaluation Audit**
Fetch detailed telemetry traces directly via the CLI to audit errors, system latency, and model tokens:
```bash
agentcore traces --limit 10
```
Analyze online evaluator history to inspect goal success ratings:
```bash
agentcore evaluate --built_in goal_success_rate correctness --history
```

---

## Key Learnings

1. **Memory is the bridge Between a Demo and a Product**: Traditional LLM wrapper pipelines are completely stateless. By isolating short-term session variables from long-term shared Semantic/Summarization memory spaces, AgentCore enables a highly personalized customer experience that persists across backend VM recycles.
2. **Decouple Governance from Agent Logic**: Embedding rules directly within LLM system prompts is highly unreliable and prone to prompt injection. Utilizing Cedar Policies decouples safety rules and spending limits from model logic, allowing compliance teams to define deterministic boundaries that are evaluated at the gateway level.
3. **Observability is a First-Class Citizen**: When deploying LLM agents at scale, you cannot rely on manual chatbot audits. Continuous online evaluators (e.g., Goal Success Rate, Faithfulness, Correctness) running silently on production traffic are critical to detecting regressions before they impact the bottom line.
4. **A/B Testing is Mandatory for GenAI Upgrades**: Because generative AI models are inherently non-deterministic, you should never deploy a "better" system prompt directly to all users. Running an 80/20 canary split and validating improvements using statistical p-values ($p < 0.05$) guarantees that changes are backed by hard data.
5. **Declarative Frameworks (Harness) Drastically Speed Up Time-to-Market**: While custom python runtimes provide maximum control, declarative YAML/JSON configurations like AgentCore Harness enable rapidly scaffolding specialized, compliant back-office agents with pre-integrated security, skills, and manual manager approval gates.
