# Software Development SOP, Audits, & Agent Prompts Workspace

Welcome to the **Software Development SOP and Audit Workspace**. This repository serves as a centralized source of truth for engineering standards, generative AI agent prompts, comprehensive system audit procedures, and strict architectural naming conventions. 

By following the documentation in this repository, development teams can ensure high-quality, predictable, and secure software delivery while leveraging AI-assisted coding tools reliably and effectively.

---

## Token Usage Warning

**READ CAREFULLY BEFORE USE:** 
Many of the files in this repository (specifically within the `SOP - Agent.md Files` directory) contain extensive, highly detailed prompts designed to act as system instructions for Large Language Models (LLMs) and autonomous coding agents.

* **High Token Consumption:** Copying and pasting these prompts into web interfaces (like ChatGPT, Claude, etc.) or utilizing them via automation API calls may consume **a lot of tokens**. 

---

## 📂 Folder Structure & Usage Guide

This repository is intuitively organized into three distinct, highly focused directories:

### 1. `Naming Convention FIles/`
* **What it is:** A collection of exact semantic rules for naming everything across the software lifecycle.
* **What's inside:** API & Database Conventions, Git Branch Naming strategies, and Release/Semantic Versioning guidelines.
* **When to use it:** 
  * Reference before creating new database schemas, API routes, or deciding on JSON payload structures.
  * Use the Git rules before cutting a new development branch to ensure pipeline consistency.
  * Use the Release guidelines when tagging deployments and updating changelogs.

### 2. `System Audit Files/`
* **What it is:** A robust suite of 9 comprehensive checklists and evaluation documents spanning all technical domains to maintain overall system health.
* **What's inside:** AWS Architecture Audits, Security & IAM Audits, DevOps Audits, Frontend/Backend health checks, Database Audits, Privacy Compliance, etc.
* **When to use it:** 
  * Establish a proactive team calendar (e.g., run a Security Audit quarterly, or an AWS Cost Audit monthly).
  * Assign an engineer to duplicate the relevant `.md` file, fill it out with your system's current state, and generate action items (Jira/Linear tickets) to resolve the findings.

### 3. `SOP - Agent.md Files/`
* **What it is:** Highly specialized Standard Operating Procedure (SOP) instructions intended to act as **System Prompts** or context windows for AI Coding Agents.
* **What's inside:** Tailored prompt files including `Base Agent`, `Frontend Agent`, `Backend Agent`, `DevOps Agent`, `Testing Agent`, and `LLM Integration Agent`.
* **When to use it:** 
  * Feed the relevant `.md` file as context to your AI IDE assistant (e.g., Cursor, GitHub Copilot) or custom autonomous agents to strictly enforce your coding paradigms, technology stacks, and architectural patterns.
  * *Read the Token Usage Warning above before blindly utilizing these instructions.*

---

## 🚀 Getting Started

### Cloning the Repository

To use these files on your local machine or integrate them deeply into your team's knowledge base, clone the repository via Git:

```bash
# Using HTTPS
git clone https://github.com/YOUR_USERNAME/software-dev-sop-workspace.git

# Using SSH
git clone git@github.com:YOUR_USERNAME/software-dev-sop-workspace.git

```
*(Make sure to replace `YOUR_USERNAME/software-dev-sop-workspace.git` with your actual repository URL).*

### Direct Download
If you do not wish to use Git, you can download the repository directly:
1. Click the green **Code** button at the top right of the repository page.
2. Select **Download ZIP**.
3. Extract the folder into your preferred local environment.

---

## 👤 Author & Contributions

**Author:** Serhat Kumas

- Email: serhatkumas@outlook.com
- LinkedIn: [linkedin.com/in/serhatkumas](https://www.linkedin.com/in/serhatkumas/)
- GitHub: [github.com/SerhatKumas](https://github.com/SerhatKumas)
- Website: [serhatkumas.github.io](serhatkumas.github.io)

This compilation of software engineering standards represents industry best practices synthesized for modern, AI-assisted development teams. If you are using this within a startup or enterprise, you are highly encouraged to fork the repository and tailor the agent prompts, internal naming rules, and audit schedules to fit your specific tech stack. 

*If you spot a typo or want to contribute an even stricter convention, feel free to open a Pull Request!*
