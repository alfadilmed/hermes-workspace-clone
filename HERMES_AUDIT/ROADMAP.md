# Roadmap: Hermes Workspace Clone

## Phase 1: Core Foundation (The Skeleton)
**Objective:** Establish the communication bridge and basic chat interface.
*   Setup React 19 + TanStack Start project structure.
*   Implement `ConnectionProber` to detect `hermes-agent` Gateway and Dashboard.
*   Build the `send-stream` API route for Server-Sent Events (SSE).
*   Create basic chat UI with streaming support and Markdown rendering.
*   Implement 8-theme system via CSS variables.

---

## Phase 2: Environment Agency (The Eyes and Hands)
**Objective:** Give the interface access to the host machine.
*   Integrate `xterm.js` for Terminal view.
*   Build the `pty-helper.py` bridge for real interactive shells.
*   Implement the File Explorer with directory tree navigation.
*   Integrate Monaco Editor for code viewing and editing.
*   Add File-to-Agent handoff logic (injecting file content into chat).

---

## Phase 3: Orchestration & Autonomy (The Swarm)
**Objective:** Enable multi-agent coordination.
*   Implement the Swarm Dashboard with Kanban-style task tracking.
*   Develop the Mission Decomposer to split goals into parallel assignments.
*   Integrate `swarm.yaml` parsing to manage specialist worker roles.
*   Implement the Checkpoint system for structured agent-to-UI reporting.
*   Add the "Greenlight" human-approval gate for destructive actions.

---

## Phase 4: Knowledge & Persistent Memory (The Brain)
**Objective:** Manage long-term agent learning.
*   Build the Knowledge Browser for `MEMORY.md` and episodic logs.
*   Implement high-speed keyword search across memory markdown files.
*   Create the Memory Editor with live preview and "Save to Brain" capability.
*   Develop background compaction logic to summarize episodic memory into durable context.

---

## Phase 5: Production & Enterprise Readiness (The Scale)
**Objective:** Harden the system for multi-user and SaaS deployment.
*   Implement Multi-tenant Auth with JWT-based session management.
*   Transition agent execution to containerized Docker environments for sandboxing.
*   Develop a Virtual Filesystem (VFS) layer to isolate user workspaces.
*   Add immutable audit logs for compliance tracking.
*   Implement Cloud Sync for persistent sessions across devices.
