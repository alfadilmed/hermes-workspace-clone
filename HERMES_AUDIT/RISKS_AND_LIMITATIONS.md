# Risks and Limitations: Hermes Workspace

## 1. Technical Debt & Rebranding Risks
*   **Claude Legacy:** The codebase is heavily coupled with naming conventions from its previous iteration as a "Claude Workspace." A clone must sanitize all internal and external identifiers (API routes, cookie keys, env vars) to avoid developer confusion and brand misalignment.
*   **Proprietary Prompt Logic:** Many of the complex agent behaviors are driven by hidden system prompts. Reconstructing these without the exact original "secret sauce" may lead to inferior agent performance in the clone.

---

## 2. Upstream Dependencies
*   **FastAPI Gateway Dependency:** The workspace is not an LLM runner; it is a UI for a runner. Any breaking changes in the `NousResearch/hermes-agent` API contract will immediately break the workspace.
*   **Provider Stability:** Relying on third-party proxies like OpenRouter or specific provider streaming behaviors (SSE) makes the platform vulnerable to upstream downtime or breaking protocol changes.

---

## 3. Scalability & Performance Limitations
*   **Local Filesystem Bottleneck:** The current architecture relies on direct local disk access for memory and sessions. This prevents horizontal scaling in a cloud environment without a complex shared VFS layer.
*   **SSE Connection Limits:** Browsers have strict limits on concurrent SSE connections to a single domain. Managing multiple simultaneous Swarm agents, terminals, and chat sessions may hit these limits, requiring a move to WebSockets for high-autonomy scenarios.
*   **Resource Intensity:** Monaco Editor and heavy Markdown rendering can lead to high client-side memory usage, potentially causing performance degradation on lower-end hardware or mobile devices.

---

## 4. Operational & Security Risks
*   **Sandbox "Shell" Risk:** While the workspace has path-traversal guards, it lacks a true OS-level sandbox. A "rogue" agent executing a Python script via a skill could potentially bypass UI-level restrictions.
*   **Human-in-the-Loop Fatigue:** The security of the system relies on users actually reviewing "Greenlight" requests. Over time, users may "auto-approve" these, creating a significant security vulnerability.

---

## 5. Deployment Constraints
*   **Tailscale/VPN Complexity:** While the system works over Tailscale, configuring remote agents and local workspaces to communicate securely requires non-trivial networking knowledge from the end-user.
*   **Docker Volume Persistence:** Ensuring that agent state (memory, config) persists across container restarts requires careful volume management, which is a common failure point in self-hosted deployments.
