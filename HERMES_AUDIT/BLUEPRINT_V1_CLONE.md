# Blueprint: Hermes Workspace Clone (V1)

## 1. System Reconstruction Strategy
To rebuild Hermes Workspace, the engineering team must follow a **Zero-Fork, Decoupled Frontend** architecture. This ensures the UI remains robust regardless of the specific agent version.

---

## 2. Modules Breakdown
### A) Connection Manager (The Prober)
*   **Responsibility:** Automated port-walking (8642/9119) and capability mapping.
*   **Logic:** Detect presence of `/health`, `/api/sessions`, and `/api/skills`.
*   **State:** Store results in a `ConnectionStore`.

### B) Command Center (The Frontend)
*   **Stack:** React 19 + TanStack Start.
*   **Components:** Modular Prompt-Kit (Streaming text, Thinking indicators, Tool pills).
*   **Editor:** Monaco Editor wrapper with File-to-Agent context injection.

### C) Environment Bridge (The Backend)
*   **PTY Management:** Spawning a stateless Python helper for shell buffers.
*   **SSE Streamer:** Managing the persistent HTTP connection between the LLM deltas and the UI.
*   **Proxy Logic:** Managing Authorization headers for multiple LLM providers.

### D) Orchestration Engine (Swarm V1)
*   **Roster:** Logic to read `swarm.yaml` and spawn agents.
*   **Decomposer:** Prompt-based task generator that creates a list of sub-tasks.
*   **Checkpointing:** JSON-parsing of agent outputs to update the Kanban board state.

---

## 3. Database Design (Conceptual)
| Entity | Storage Method | Key Fields |
| :--- | :--- | :--- |
| **Sessions** | SQLite / JSON | `id`, `user_id`, `title`, `history[]` |
| **Missions** | SQLite | `id`, `mission_goal`, `state` (Enum), `results` |
| **Memory** | Markdown (VFS) | Curated rules in `MEMORY.md`; logs in `/memory/` |
| **Workers** | YAML (`swarm.yaml`) | `id`, `role`, `skills[]`, `model` |

---

## 4. API Design (Conceptual)
### Core Workspace APIs
*   `POST /api/send-stream`: The main SSE endpoint for chat and reasoning.
*   `GET /api/connection-status`: Returns the results of the capability probe.
*   `POST /api/terminal-stream`: Spawns a PTY and returns the session UUID.
*   `POST /api/files`: CRUD operations on the local project directory.

### Agent Gateway APIs (Internal Proxy)
*   `POST /v1/chat/completions`: Standard OpenAI-compatible route.
*   `GET /v1/models`: Listing available providers.

---

## 5. Technical Requirements for Parity
1.  **SSE Lifecycle Management:** Must handle reconnection, heartbeats, and browser-tab suspension without killing the LLM run.
2.  **State Persistence:** Zustand stores must sync with `localStorage` to survive page refreshes.
3.  **Cross-Platform PTY:** The Python bridge approach is mandatory if binary Node dependencies are to be avoided.
4.  **Modular Themes:** CSS-variable based theme system to support the 8 variant palettes.

---

## 6. Development Priorities
1.  **MVP Phase:** Core SSE Chat + Connection Probing.
2.  **Agency Phase:** File Explorer + Terminal integration.
3.  **Autonomy Phase:** Mission decomposition + Kanban state tracking.
