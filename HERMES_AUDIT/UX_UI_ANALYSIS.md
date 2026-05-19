# UX/UI Analysis: Hermes Workspace

## 1. Navigation System
Hermes Workspace utilizes a **layered navigation architecture** designed for high-density information management.

*   **Global Rail (Level 1):** A permanent vertical strip on the far left. Icons provide instant switching between major domains: Dashboard, Chat, Swarm, Files, Terminal, Skills, Memory, and Settings.
*   **Contextual Sidebar (Level 2):** Collapsible panel that manages the entities within the active domain (e.g., the list of Chat Sessions or the File Tree).
*   **Breadcrumb/Status Bar (Level 3):** Top-level context indicators showing the active session name, model selection, and connection status.
*   **Mobile Fallback:** Transition to a bottom-tab bar for primary navigation, with a hamburger menu for contextual secondary actions.

---

## 2. Workspace Layout Patterns
The UI follows a **"Command Center"** multi-pane strategy:

*   **The Main Stage:** Centered area for the primary task (Markdown chat thread, Monaco code editor, or Swarm Kanban board).
*   **The Inspector Panel:** A right-aligned panel that provides metadata about the active agent run. It includes:
    *   **Activity Feed:** Real-time tool-call logs.
    *   **Memory Snips:** Relevant context being injected.
    *   **Usage Meter:** Token consumption and cost ledger.
*   **Modals & Overlays:** Used for focused "Wizard" flows (Onboarding, Provider Setup) and global Command Palette (`⌘K`).

---

## 3. Chat System Design
The chat interface is the heart of the workspace, featuring:
*   **SSE Streaming:** Progressive text reveal for low perceived latency.
*   **Thinking Indicators:** Animated braille/spinner states that visualize the LLM's reasoning process.
*   **Tool Call Pills:** Inline UI components that represent agent actions (e.g., `read_file`). These are expandable to show the raw input/output of the tool.
*   **Rich Markdown:** Full GFM (GitHub Flavored Markdown) support with Shiki-based syntax highlighting for code blocks.
*   **Context Meter:** A visual percentage bar showing how much of the model's context window is currently consumed.

---

## 4. Component Structure
The UI is built using a **modular, atomic component library**:
*   **Prompt-Kit:** A specialized set of components for agent-human interaction (`Message`, `ToolPill`, `ThinkingBlock`).
*   **UI Primitives:** Built on **Base UI** and **Tailwind CSS 4**, ensuring high accessibility and themeability.
*   **Complex Feature Modules:**
    *   `xterm.js` for Terminal.
    *   `Monaco Editor` for code editing.
    *   `Framer Motion` for state transitions and buttery-smooth animations.

---

## 5. Interaction Flows
### A) The "Orchestrator Loop"
1.  **Drafting:** User types in the multi-line chat composer.
2.  **Dispatch:** User hits Enter; the UI optimistically updates with a User message.
3.  **Reasoning:** The agent enters a "Thinking" state (visualized in the Inspector).
4.  **Action:** The agent executes a tool (visualized as an expanding Tool Pill).
5.  **Output:** Final response streams into the chat thread.

### B) File-to-Agent Handoff
1.  User selects a file in the File Explorer.
2.  UI opens the file in Monaco.
3.  User can "Send to Chat" or "Ask Agent about this file," which automatically injects the file path/content as context.

---

## 6. Design System Blueprint
*   **Themes:** 8 distinct variants including "Nous Official" (Indigo/Navy), "Slate" (Dev-focused), and "Mono" (High contrast).
*   **Typography:** System-ui for maximum performance, with robust Monospace fallbacks for code/terminal contexts.
*   **Iconography:** Custom Hugeicons core set for clear, minimalist module identification.
