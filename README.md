# Sidecar Policy Enforcement Engine

A reference implementation of a **sidecar** service in Python that intercepts requests to/from AI agents, enforces organizational policies, and optionally uses an LLM for advanced checks or redactions. This sidecar can be adapted to **CrewAI, LangGraph, or any agent framework** by leveraging simple request interception.

---

## Table of Contents
1. [Overview](#overview)  
2. [Features](#features)  
3. [Architecture](#architecture)  
   - [Sidecar Flow](#sidecar-flow)  
   - [Key Components](#key-components)  
4. [Policy Types](#policy-types)  
   - [Rule-Based Checks](#rule-based-checks)  
   - [LLM-Based Semantic Checks (Optional)](#llm-based-semantic-checks-optional)   
8. [Quick Start](#quick-start)  
   - [1. Define Your Policy](#1-define-your-policy)  
   - [2. Run the Mock Agent](#2-run-the-mock-agent)  
   - [3. Run the Sidecar](#3-run-the-sidecar)  

---

## Overview
In modern AI applications, autonomous agents (like CrewAI or LangGraph) often handle sensitive operations—reading user data, generating content, using system APIs, etc. **This repository** provides a **sidecar** solution that sits between clients (users/applications) and AI agents to **enforce policies** such as:

- Prohibiting specific phrases or types of content  
- Disallowing certain tools or actions  
- Blocking or redacting private user information  
- Checking compliance with organizational or regulatory standards  

You can also optionally integrate a **Large Language Model** for **semantic** or **contextual** checks—e.g., to detect hidden PII or decide if content is disallowed.

---

## Features
- **Sidecar Deployment**: Intercepts requests before they reach the agent and inspects responses before they go back to the client.  
- **Rule-Based Checks**: Straightforward blocklists for phrases, disallowed tools, etc.  
- **LLM Integration** (Optional): For advanced classification, rewriting, or redaction.  
- **Extensible**: Easily adapt to different agent frameworks or multiple endpoints.  
- **Lightweight**: Minimal overhead using local networking inside a container or host.  
- **Configurable**: Policies are defined in YAML/JSON, loaded at runtime.

---

## Architecture

### Sidecar Flow

<img width="687" alt="Screenshot 2025-04-13 at 10 55 48 PM" src="https://github.com/user-attachments/assets/c269b9b7-9502-40ed-86fb-eb3591794b4d" />



**Key points:**
- Every inbound user request and outbound agent response must pass the **policy checks**.  
- If a policy violation is detected, the sidecar can **block** or **sanitize** the data.

### Key Components

1. **Policy Definitions** – YAML/JSON specifying rules, e.g. disallowed phrases, disallowed tools.  
2. **Enforcement Engine** – The Python code that loads policies, parses requests/responses, and applies checks.  
3. **LLM Integration** (Optional) – For advanced compliance decisions.  
4. **Mock Agent** (for demo/testing) – A minimal service that simulates an agent. In real deployments, you replace this with your actual AI agent(s).

---

## Policy Types

### Rule-Based Checks
- **Prohibited Phrases**: e.g., “social security number,” “credit card.”  
- **Disallowed Tools**: e.g., “runSystemCommands.”  
- **Length or Regex-based** checks (like blocking excessively long input).

### LLM-Based Semantic Checks (Optional)
- Use an **LLM** (e.g., OpenAI API or a local model) to detect if text is harmful, private, or sensitive.  
- Optionally **sanitize or redact** with an LLM if partial compliance is desired.

---

## Handling Unknown Request Structures
If your sidecar must handle requests from **unknown** or **varying** formats:

1. **Raw Content Scanning**: Inspect the entire request body for key substrings/regex patterns.  
2. **JSON Parsing**: If the content-type is JSON, parse fields (if known). If unknown, fallback to raw scanning.  
3. **LLM-based** semantic check: Turn the raw request into a text prompt for an LLM-based classification.  

This hybrid approach ensures your sidecar can **adapt** even when you don’t fully control or know the request’s structure.

---


### Roadmap
- Fine-grained Redaction: LLM-based partial removal of sensitive fields.

- Role-Based Policy: Different rules for different user roles or request types.

- Stateful Checks: Storing conversation context to detect repeated attempts or cumulative policy violations.

- Anomaly Detection: Record request/response patterns for suspicious behavior.

- UI for Policy Management: A dashboard to edit, enable, or disable policies on the fly.

### Quick Start
1. Define Your Policy


```yaml

agent_name: "DemoPolicy"
rules:
  - name: "Block sensitive phrases"
    description: "Common sensitive or privacy-related phrases"
    prohibited_phrases:
      - "social security number"
      - "credit card"
    allowed_tools: []
    disallowed_tools:
      - "runSystemCommands"
Feel free to add more rules (disallowed file types, max length checks, etc.).
```
2. Run the Mock Agent
```
cd agent_mock
python mock_agent.py
```
This starts a mock agent on port 5001.

In real usage, you’d replace this with your actual AI agent service (CrewAI, LangGraph, etc.).

3. Run the Sidecar
```
Open a new terminal:
cd sidecar
python main.py
The sidecar starts on port 8000.
```
It reads the policy from ../policies/policy.yaml by default (configurable in code).



### Suggestions:
- Please create an github issue for any feedback/suggestion etc.




