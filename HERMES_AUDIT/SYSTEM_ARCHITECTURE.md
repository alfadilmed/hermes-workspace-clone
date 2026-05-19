# System Architecture: Hermes Workspace

## 1. High-Level Architecture Overview
Hermes Workspace is built on a **Zero-Fork, Decoupled Architecture**. It separates the human-facing interface (Workspace UI) from the agent-facing execution engine (Hermes Agent).

```text
[ User Browser ] <---( SSE / REST )---> [ Node.js Server (TanStack Start) ]
                                                |
                                        [ Capability Probe ]
                                                |
                                    +-----------+-----------+
                                    |                       |
                        [ Gateway Service (:8642) ] [ Dashboard Service (:9119) ]
                                    |                       |
                          [ LLM Provider Proxy ]    [ SQLite / VFS / Memory ]
```

---

## 2. Frontend Structure
*   **Framework:** React 19 with TanStack Start for unified SSR/Client routing.
*   **Routing:** Type-safe, file-based routing via `TanStack Router`.
*   **State Management:**
    *   **Zustand:** Used for persisted UI state (sidebar status, theme, connection settings).
    *   **TanStack Query:** Manages server state and data fetching.
*   **Real-time Engine:** Custom SSE listener that parses streaming assistant deltas and tool-call lifecycle events.

---

## 3. Backend Structure (Node.js/Python)
The backend acts as a high-performance bridge between the web and the host machine.
*   **Terminal Bridge:** Uses a Python-based PTY helper (`pty-helper.py`) to spawn real interactive shells. It bypasses the need for Node-native binary addons (like `node-pty`), ensuring better cross-platform compatibility.
*   **Proxy Layer:** TanStack Start server functions act as a secure proxy to the LLM providers, injecting necessary auth headers (OpenRouter, Anthropic, etc.) without exposing them to the client.
*   **VFS Wrapper:** A server-side module that performs safety checks on all file/memory operations, preventing path-traversal attacks.

---

## 4. AI & Agent System Design (Swarm Mode)
The Swarm system is an advanced orchestration layer:
*   **Orchestrator:** A high-level agent responsible for mission planning and task decomposition.
*   **Specialist Workers:** Agents defined in `swarm.yaml` with specific roles, skills, and model preferences (e.g., `builder`, `reviewer`, `qa`).
*   **Persistence:** Workers run in persistent `tmux` sessions. This allows the agents to maintain state even if the user closes their browser or the Node server restarts.
*   **Communication:** Workers communicate back to the workspace via structured checkpoints (JSON), which are then visualized in the Swarm Dashboard.

---

## 5. Memory System
The memory system is **markdown-centric and local-first**:
*   **Episodic Memory:** Individual runs log activity to daily markdown files in `~/.hermes/memory/`.
*   **Long-term Memory:** Curated project rules and agent guidelines are stored in `MEMORY.md`.
*   **Search Mechanism:** The system currently uses a high-speed grep-style keyword search over markdown files to retrieve relevant context.

---

## 6. Task Orchestration
Task management is handled via a **Kanban-style backend**:
*   **Phases:** `Planning` -> `Ready` -> `Running` -> `Review` -> `Done`.
*   **Dependencies:** Tasks can have prerequisites, allowing the Orchestrator to sequence complex development cycles (e.g., don't run tests until the code is written).
*   **Human-in-the-Loop:** Optional "Greenlight" gates pause the workflow until a user approves a specific task output.

---

## 7. Database Design (Conceptual)
While largely file-based for portability, the system manages several conceptual entities:
*   **Sessions:** UUID, title, model, lastActive, messageHistory[].
*   **Missions:** id, goal, state, assignments[].
*   **Jobs:** id, schedule (cron), prompt, status.
*   **Workers:** id, role, specialty, current_task_id.
