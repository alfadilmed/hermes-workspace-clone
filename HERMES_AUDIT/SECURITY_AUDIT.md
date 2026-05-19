# Security Audit: Hermes Workspace

## 1. Authentication & Authorization
*   **Current Mechanism:** Optional password protection via `HERMES_PASSWORD`.
*   **Cookie Security:** Uses `HttpOnly`, `SameSite=Strict`, and conditionally `Secure` cookies.
*   **IP Whitelisting:** Automatically trusts loopback (`127.0.0.1`) and known LAN/VPN ranges (192.168.x, 10.x, 100.x for Tailscale).
*   **Vulnerability Risk:** Medium. On a shared LAN, if no password is set, any user on the network could potentially access the terminal and filesystem via the workspace.

---

## 2. Filesystem & VFS Security
*   **Path Traversal Prevention:** The system uses `real-path` boundary checks on all file and memory routes.
*   **Sandbox Isolation:** Low. By default, the workspace and its agents run with the permissions of the host user. Agents can read/write any file accessible to that user.
*   **Vulnerability Risk:** High (Theoretical). If a malicious prompt causes an agent to delete critical system files or exfiltrate SSH keys, the system has no built-in sandbox to stop it unless configured via the backend (Docker/SSH).

---

## 3. Prompt Injection Risks
*   **Mechanism:** Agents execute tasks based on user prompts.
*   **Risk:** An attacker could provide a malicious markdown file or a "honeypot" repository that, when read by an agent, instructs it to perform unauthorized actions (e.g., "Delete the .git folder" or "Send the .env file to a remote URL").
*   **Remediation:** The system relies on **Human-in-the-loop (Greenlight Gates)**. This is a critical security feature that must remain enabled for any destructive or external-sending capability.

---

## 4. Multi-Tenant Risks
*   **Status:** Currently not designed for multi-tenancy.
*   **Risks:**
    *   **Context Leakage:** Multiple users sharing the same Node.js server might see each other's agent runs or session history due to local filesystem storage of state.
    *   **Port Collision:** Simultaneous PTY sessions or capability probes on the same machine could lead to state corruption.
*   **Remediation:** For a production clone, implement a user-isolated VFS and containerized agent execution.

---

## 5. Theoretical Attack Vectors
| Vector | Description | Impact | Likelihood |
| :--- | :--- | :--- | :--- |
| **SSRF (Agent)** | Agent is instructed to fetch a URL that points to local network services (e.g., `http://192.168.1.1/admin`). | High | Medium |
| **RCE (Terminal)** | Exploiting the PTY bridge to execute commands outside the intended workspace root. | Critical | Low |
| **Data Exfiltration** | Using an agent's `web` skill to post sensitive local files to an external endpoint. | High | Medium |

---

## 6. Audit Recommendations
1.  **Enforce Mandatory Auth:** Password protection should be forced for any non-loopback bind by default (fail-closed).
2.  **Containerized Backends:** Transition the default agent execution environment from "Local" to "Docker" or "SSH" to ensure filesystem sandboxing.
3.  **VFS Hardening:** Move from direct `fs` calls to a virtualized filesystem layer that enforces root boundaries at the kernel/OS level.
4.  **Audit Logs:** Implement an immutable audit log that records every command executed by every agent for forensic review.
