This repository is my **Obsidian-based knowledge base and project hub**, designed for coaching, software development, and personal knowledge management.

---

**FOLDER STRUCTURE**

00-notes/ → Evergreen knowledge: validated, linked, and ready-to-use notes for coaching or reference.

01-drafts/ → Enriched drafts: notes in progress, waiting for final links/metadata before moving to 00-notes.

03-research-inbox/ → Raw files from automations (web research, exports, etc.). → Process: Automatically parsed/formatted → moved to 01-drafts.

97-views/ → Obsidian Dataview queries for dynamic note exploration (e.g., coaching topics, tech stacks).

98-projects/ → Project documentation, mostly software-related (specs, roadmaps, code snippets). → Example: luxi/ (SaaS project docs).

99-images/ → Assets for documentation (images, diagrams). → Naming: YYYY-MM-DD-description.png

---

**KEY WORKFLOWS**

1. Research → Knowledge 03-research-inbox/ → [Auto-parsing] → 01-drafts/ → [Manual review] → 00-notes/
    
2. Project Management
    
    - Use templates for projects (status, tech stack, links).
    - Example: 98-projects/luxi/README.md
3. Automation
    
    - Mistral Note Enricher plugin: enriches metadata/links in 03-research-inbox/.
    - Git auto-commits enriched notes to this repo.

---

**NAMING CONVENTIONS**

- Notes: YYYY-MM-DD-title-kebab-case.md
- Projects: project-name/README.md + subfolders (specs/, code/).

---

**GETTING STARTED**

1. Clone the repo.
2. Open in Obsidian with Dataview + Mistral Note Enricher plugins.
3. Contribute via PRs (focus on 00-notes/ or 98-projects/).

---
