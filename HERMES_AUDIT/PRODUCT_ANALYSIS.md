# Product Analysis: Hermes Workspace

## 1. Product Purpose
The primary purpose of Hermes Workspace is to provide a **high-autonomy agent control plane**. It enables human operators to manage a "Swarm" of specialized AI agents that can see what the developer sees and act where the developer acts.

It specifically bridges the "Isolation Gap" where LLMs are typically restricted to text-in/text-out interfaces, giving them direct access to:
*   **Filesystem:** Reading and writing project code.
*   **Terminal:** Running builds, tests, and deployments.
*   **Memory:** Retaining project-specific context across sessions.
*   **Skills:** Executing pre-defined modular capabilities (e.g., GitHub PR management).

---

## 2. Target User Personas
### A) The AI Infrastructure Engineer
Needs a sandbox to test agent skills, benchmark model performance, and build autonomous implementation pipelines.

### B) The Technical Lead
Manages complex projects by dispatching specialized agents (Builder, Reviewer, Researcher) to parallelize development workflows.

### C) The "Power Agent" User
Individuals who want to automate their local machine tasks using natural language, from web research to file organization.

---

## 3. Core Workflows
### A) Onboarding & Connection
1.  **Probe:** Workspace automatically probes ports 8642 (Gateway) and 9119 (Dashboard).
2.  **Capabilities Detection:** System maps available APIs (Sessions, Skills, Jobs).
3.  **Auth Setup:** User configures LLM provider keys (OpenRouter, OpenAI, etc.).
4.  **Readiness Gate:** UI unlocks only when a usable backend is verified.

### B) Mission Control (Swarm Mode)
1.  **Mission Input:** User provides a high-level goal (e.g., "Implement a search bar").
2.  **Decomposition:** An Orchestrator agent breaks the goal into a DAG (Directed Acyclic Graph) of sub-tasks.
3.  **Specialist Dispatch:** Tasks are assigned to specialist workers (e.g., `builder` for code, `qa` for verification).
4.  **Checkpoint Loop:** Workers return structured proof-bearing checkpoints.
5.  **Greenlight Gate:** Human-in-the-loop review for destructive actions (e.g., merging code).

### C) Memory Management
1.  **Observation:** Real-time logging of agent actions into episodic memory.
2.  **Compaction:** Background process summarizes activity into durable knowledge.
3.  **Curation:** Human editing of `MEMORY.md` to establish long-term agent guidelines.

---

## 4. System Features
*   💬 **Multi-Session Chat:** Persistent threads with SSE streaming and tool-call visualization.
*   📁 **VFS Explorer:** Full project directory navigation with Monaco Code Editor integration.
*   🐚 **PTY Terminal:** Real-time interactive shell via a Python-based bridge.
*   🧠 **Knowledge Browser:** Unified interface for browsing and searching agent memory markdown files.
*   🧩 **Skills Registry:** A library of 2,000+ modular capabilities for agents.
*   🤖 **Swarm Dashboard:** Visualization of multi-agent state, roles, and task queues.

---

## 5. UX Structure
The UX is architected as a **Vertical Shell Layout**:
*   **Primary Rail (Left):** High-level module switching (Chat, Swarm, Files, Terminal, Settings).
*   **Inner Sidebar:** Contextual navigation (e.g., Session list in Chat, File tree in Files).
*   **Main Stage (Center):** The primary execution context (Chat thread, Code Editor, Dashboard).
*   **Inspector (Right):** Real-time agent metadata, "Thinking" blocks, and usage metrics.
