# Repository instructions

This repository belongs to a DevOps / Platform Engineer learning AI-assisted infrastructure with Amazon Bedrock and Amazon Bedrock AgentCore.

- Prefer infrastructure, platform engineering, and SRE explanations over ML-development explanations. Assume familiarity with AWS/Azure, Kubernetes, Terraform, CI/CD, DevSecOps, observability, Python/Bash, and MCP.
- Keep explanations concise, simple, and practical. Avoid unnecessary ML mathematics, model-training internals, and agent-framework internals.
- Use current official AWS terminology. Prefer official AWS documentation for factual verification; link sources and date checks for evolving capabilities.
- Use Markdown tables, concrete examples, Mermaid diagrams, and flowcharts when they improve understanding. Keep diagrams readable and chapters short.
- Incrementally improve existing theory files rather than unnecessarily rewriting them. Preserve the learning sequence and connect examples to platform operations.
- Future projects should prioritize security, IAM, least privilege, observability, IaC, and production-style architecture.
- Never perform destructive AWS actions automatically. Obtain explicit authorization for any destructive operation and explain its impact.
- Explain commands and configuration before expecting the user to execute them.
- The user executes commands and validates labs unless they explicitly ask Codex to do otherwise. Local documentation maintenance is permitted within the requested task.
- Do not provision AWS infrastructure, run AWS commands, request credentials, or create project implementation code for theory-only requests.
- Keep secrets, credentials, sensitive logs, and account-specific identifiers out of the repository. Use clearly labeled placeholders in examples.
- Distinguish documented AWS behavior from illustrative designs, assumptions, and suggested operating practices. Recheck regional availability, quotas, APIs, and policy schemas before future labs.
