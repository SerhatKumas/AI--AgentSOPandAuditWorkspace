# SOP Token Usage and Best Practices

This document outlines the token usage sizing for the `SOP - Agent.md` files and provides the standard best practices for injecting them into an AI Chat Agent's context effectively.

## 📊 Token Breakdown per File

> **Note:** Token sizes are estimations calculated by the **Gemini 3.1 Pro** model, based on the standard heuristic (1 word ≈ 1.33 tokens or 1 token ≈ 4 characters).

| File Name | Approx. Word Count | Token Estimate |
| :--- | :--- | :--- |
| `mobile_agent.md` (Mobile) | 5,766 | ~7,700 tokens |
| `frontend_agent.md` (Frontend) | 5,468 | ~7,300 tokens |
| `devops_agent.md` (DevOps) | 5,346 | ~7,100 tokens |
| `agent.md` (Base Rules) | 5,194 | ~6,900 tokens |
| `backend_agent.md` (Backend) | 5,051 | ~6,700 tokens |
| `llm_agent.md` (LLMs) | 4,692 | ~6,200 tokens |
| `testing_agent.md` (Testing) | 4,651 | ~6,200 tokens |
| **All combined** | **36,168** | **~48,000 - 60,000 tokens** |

---

## 🚀 Recommended Approach: The "Base + Domain" Strategy

**DO NOT send all files at once.** Passing 60,000+ tokens of combined system prompts saturates the AI context window, slows down response times, runs up unnecessary API costs, and causes the AI to selectively ignore certain instructions (known as the "Lost in the Middle" effect). 

Instead, leverage the highly efficient **Domain-Basis Strategy**.

### The Workflow

1. **Copy the necessary files:** At the start of your project, take the base `agent.md` AND your specific active domain file (e.g., `frontend_agent.md`) and place them together in the root directory of your active codebase.
2. **Initialize your Session:** At the **very beginning** of the chat session or vibe-coding sprint, feed the `agent.md` (Base Rules) **along with** the single relevant Domain `agent.md` to the Chat Agent.
3. **Refer back automatically:** Explicitly instruct the Chat Agent in your opening message that if it ever gets stuck or needs to make an architectural decision, it must refer back to those `.md` files residing in the root folder.

### 💡 Estimated Token Usage per Session (Base File + 1 Domain)
When you combine the base rules with exactly one specific domain file, the context footprint is incredibly lightweight and hyper-focused:

*   **Mobile Session:** `agent.md` + `mobile_agent.md` = **~14,600 tokens**
*   **Frontend Session:** `agent.md` + `frontend_agent.md` = **~14,200 tokens**
*   **DevOps Session:** `agent.md` + `devops_agent.md` = **~14,000 tokens**
*   **Backend Session:** `agent.md` + `backend_agent.md` = **~13,600 tokens**
*   **Testing / LLM Session:** `agent.md` + `testing_agent.md` = **~13,100 tokens**

### Why this is the Best Practice:
*   **Laser Focus (High Attention):** The AI strictly adheres to the exact rules the domain requires, completely unbothered by irrelevant rules (e.g., database rules popping up during UI work).
*   **Speed and Savings:** ~14k tokens loads instantly and takes full advantage of Prompt Caching in modern web interfaces and APIs.
*   **Ground Truth Persistence:** Your system instructions act as living "Ground Truth" files that sit peacefully in the root of the project to act as the authoritative reference if the AI ever strays off course.
