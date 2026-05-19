# Repository Forensic Report: Hermes Workspace

## 1. Technical Stack Audit
| Layer | Technology |
| :--- | :--- |
| **Framework** | TanStack Start (React 19 + SSR) |
| **Build Tool** | Vite 7 |
| **Styling** | Tailwind CSS 4 |
| **State (UI)** | Zustand 5 |
| **State (Server)** | TanStack React Query 5 |
| **Terminal** | xterm.js 5 + Custom Python PTY Helper |
| **Editor** | Monaco Editor |
| **Real-time** | Server-Sent Events (SSE) |
| **Validation** | Zod |
| **Icons** | Hugeicons + Lobehub Icons |

---

## 2. Directory Structure Analysis
The repository follows a modern monolithic structure, strictly separating UI components from server-side logic.

### `/src/routes/` (Routing & API)
*   Uses file-based routing.
*   Includes both View routes (e.g., `chat/$sessionKey.tsx`) and API routes (e.g., `api/send-stream.ts`).
*   **Forensic Note:** API routes act as a "headless" backend proxy, handling the heavy lifting of SSE streaming and capability probing.

### `/src/server/` (Core Logic)
*   `gateway.ts`: The primary client for communicating with the `hermes-agent`. Handles port discovery and circuit breaking.
*   `pty-helper.py`: A Python script that spawns real PTYs. This is an ingenious solution to avoid native Node-PTY binary dependency issues.
*   `run-store.ts`: Manages the persistence of active agent runs in `~/.hermes/webui-mvp/runs/`.

### `/src/components/` (UI Layer)
*   `prompt-kit/`: Specialized components for agent interaction (message bubbles, tool pills, thinking indicators).
*   `ui/`: Atomic primitives (buttons, inputs, alerts) built using `class-variance-authority`.
*   `workspace-shell.tsx`: The main application shell that provides the layout context for all sub-pages.

### `/src/stores/` (State Layer)
*   `chat-store.ts`: Tracks messages, tool-call phases, and streaming text.
*   `workspace-store.ts`: Tracks layout preferences (sidebar status, active tabs).

---

## 3. Key Forensic Observations
1.  **"Claude" Legacy:** The codebase contains significant legacy naming from its origin as a "Claude Workspace" (e.g., `CLAUDE_API`, `claude-auth` cookies, `/api/claude-config`). This represents non-trivial technical debt for a complete rebrand.
2.  **Streaming Reliability:** The SSE implementation in `api/send-stream.ts` includes robust heartbeat and padding logic (2048 bytes of whitespace) to prevent Cloudflare/Proxy connection culling—a sign of production-aware engineering.
3.  **Dynamic Capability Probe:** On startup, the server probes ports 8642, 8643, and 8645. This "port-walking" logic makes the workspace highly resilient to varying backend environments (Docker vs Local vs WSL).
4.  **Security Guards:** The `auth-middleware.ts` performs IP-based local request detection, allowing bypass for loopback/LAN (Tailscale) while enforcing passwords for remote hosts.

---

## 4. Resource Usage
*   **Memory:** Configured for 2GB old space (`--max-old-space-size=2048`) in scripts, suggesting the build process or Monaco/Markdown rendering can be intensive.
*   **Dependencies:** Heavy use of modern "headless" libraries (Base UI, Motion) keeps the bundle size manageable while delivering high interactivity.
