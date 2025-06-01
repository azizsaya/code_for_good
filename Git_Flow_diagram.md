Here's a **color-coded Git Flow diagram** written in **[Mermaid](https://mermaid.js.org/)** syntax. You can paste this into a Markdown file that supports Mermaid rendering (like on GitHub, GitLab, Obsidian, or VS Code with plugins) to visualize it interactively.

---

### ✅ **Mermaid Git Flow Diagram with Color-Coding**

```mermaid
%%{ init : { "theme": "default", "flowchart": { "curve": "linear" } } }%%
graph LR

subgraph Production [MASTER Branch]
    M1[master: Init]:::master
    M2[master: Release v1.0.0]:::master
    M3[master: Hotfix v1.0.1]:::master
end

subgraph Develop [DEVELOP Branch]
    D1[develop: Init]:::develop
    D2[develop: Feature A merged]:::develop
    D3[develop: Feature B merged]:::develop
    D4[develop: After release]:::develop
    D5[develop: After hotfix]:::develop
end

subgraph Features
    F1[feature/login - A1]:::feature
    F2[feature/login - A2]:::feature
    F3[feature/search - B1]:::feature
    F4[feature/search - B2]:::feature
end

subgraph Release
    R1[release/v1.0.0]:::release
    R2[release/v1.0.0 done]:::release
end

subgraph Hotfix
    H1[hotfix/v1.0.1 fix]:::hotfix
end

%% Flow
M1 --> D1
D1 --> F1 --> F2 --> D2
D2 --> F3 --> F4 --> D3
D3 --> R1 --> R2 --> M2
R2 --> D4
M2 --> H1 --> M3
H1 --> D5

%% Styling
classDef master fill:#ffcccc,stroke:#cc0000,stroke-width:2px;
classDef develop fill:#cce5ff,stroke:#0066cc,stroke-width:2px;
classDef feature fill:#e6ffe6,stroke:#33cc33,stroke-width:2px;
classDef release fill:#fff2cc,stroke:#e6ac00,stroke-width:2px;
classDef hotfix fill:#ffe6e6,stroke:#cc3333,stroke-width:2px;
```

---

### 🧩 Color Key:

* 🔴 `master`: Production branch (stable, tagged)
* 🔵 `develop`: Integration branch (merges features and hotfixes)
* 🟢 `feature/*`: New features
* 🟡 `release/*`: Pre-production, testing before release
* 🔴 (light) `hotfix/*`: Emergency fixes

---

Let me know if you'd like a **horizontal timeline**, Git commands for each step, or a Mermaid Gantt chart version!
