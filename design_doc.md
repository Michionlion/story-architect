# Master Project Blueprint: Story-Architect (Flet Edition)

## 1. Core Philosophy
**Story-Architect** is a high-fidelity creative partner that guides users through a structured, three-phase journey: **Concept $\rightarrow$ Structure $\rightarrow$ Detail**. It utilizes a "Critic-in-the-Loop" architecture to ensure prose quality and narrative continuity, preventing the common AI pitfalls of drift, repetition, and "mushy" storytelling.

---

## 2. The Three-Phase Workflow

### Phase I: The Seed (Ideation)
* **User Input:** A "Seed Prompt".
* **AI Persona:** *The Concept Artist*.
* **Action:** Generates 3 divergent "Concept Branches".
* **Outcome:** User selects/refines a branch to establish the "Core Concept."

### Phase II: The Skeleton (Outlining)
* **User Input:** Refinement of the Concept.
* **AI Persona:** *The Narrative Architect*.
* **Action:** Generates a multi-act beat sheet.
* **Interaction:** **Interactive Beat Cards**. Users can:
    * `[+]` Add sub-beats.
    * `[🔄]` Re-roll a specific beat.
    * `[📌]` **Pin Detail:** Add specific instructions that act as permanent "Story Bible" entries.

### Phase III: The Flesh (Expansion & Refinement)
* **User Input:** Selection of a specific beat.
* **AI Persona:** *The Prose Stylist* $\rightarrow$ *The Literary Critic*.
* **The "Critic-in-the-Loop" Workflow:**
    1. **Drafting:** The Stylist writes prose based on the beat + Story Bible.
    2. **Self-Evaluation:** The **Critic Persona** reviews the draft against adherence, consistency, and quality.
    3. **Iterative Refinement:** If the Critic finds flaws, the Stylist is prompted to rewrite.

---

## 3. Technical Architecture (Flet Implementation)

### The Stack
* **Frontend/UI:** **Flet** (Flutter-based Python framework). This allows for a more desktop-like, highly responsive, and polished UI compared to Streamlit.
* **Orchestration:** OpenAI-compatible client (targeting `http://winbe:8080/v1`).
* **State Management:** Flet's internal `page.session` and custom state classes.
* **Data Format:** Single-file `.json` "Project File".

### Persistence & Storage Strategy
Since Flet runs as a local app (or web-based app), we will use a tiered approach:
1. **Local Filesystem:** The primary source of truth. Projects are saved as `.json` files in a designated `projects/` directory.
2. **Client-Side Auto-Save (Web/Desktop):**
    * If running in a **Web context**: Use `page.client_storage` (Flet's built-in wrapper for Browser LocalStorage) to save the current working state (autosave) every $N$ seconds or on every major change.
    * If running as a **Desktop app**: Use a background thread to write the current state to a `.tmp` JSON file in the user's local app data directory.
3. **Manual Export:** Standard "Save As" functionality to export the `.json` project file.

### The "Story Bible" (Context Injection Engine)
To prevent narrative drift, every LLM call in Phase III is wrapped in a **Context Packet**:
1. **The Global Concept**
2. **The Structural Map**
3. **The Story Bible** (Pinned facts, character traits, world rules)
4. **The Immediate Precedent** (Prose of the previous beat)

---

## 4. Prompt Engineering Strategy

| Persona | Role | Primary Instruction | Key Constraint |
| :--- | :--- | :--- | :--- |
| **Concept Artist** | Ideator | Provide divergent, high-concept premises. | No plot summaries; only premises. |
| **Narrative Architect** | Structuralist | Create a logical, cause-and-effect beat sheet. | No vague "filler" beats. |
| **Prose Stylist** | Writer | Write immersive, sensory-rich prose. | **Show, Don't Tell.** No summarizing. |
| **Literary Critic** | Evaluator | Grade the draft against the Story Bible/Beat. | Be harsh and specific about drift/flaws. |

---

## 5. Development Roadmap

### Phase 1: The "Engine" (MVP)
* [ ] Flet UI scaffolding (Navigation Rail/Tabs).
* [ ] Basic Concept $\rightarrow$ Outline $\rightarrow$ Draft pipeline.
* [ ] Integration with the local LLM endpoint.
* [ ] Basic `.json` Save/Load functionality.

### Phase 2: The "Brain" (Critic & Bible)
* [ ] Implementation of the **Critic-in-the-Loop** automated refinement.
* [ ] Development of the **Story Bible** injection logic.
* [ ] **Pinned Details** feature in the Outline phase.

### Phase 3: The "Studio" (UX & Persistence)
* [ ] **Auto-save** via Flet `client_storage` (Web) or background file writing (Desktop).
* [ ] **Style Controls:** Sliders for Temperature and Prose Density.
* [ ] **Visualizer:** A "Tension Map" graph showing the emotional arc of the beats.
* [ ] **Export:** Markdown and `.docx` export.

---

## 6. Risk Management

| Risk | Mitigation |
| :--- | :--- |
| **Context Window Overflow** | Implement a "Summarization" step for the Story Bible as the project grows. |
| **"Critic" becomes too lenient** | Hard-code specific "Checklist" items into the Critic's system prompt. |
| **Loss of Data** | Dual-layer saving: LocalStorage/Temp files for "oops" moments, and JSON files for "real" saves. |
