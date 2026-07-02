# Mermaid Syntax Reference

Load this file when the host cannot render inline SVG and mermaid.js code blocks are required (terminal agents like Claude Code, markdown-only chat, GitHub/GitLab renderers).

## When to use mermaid instead of SVG

- Host renders markdown only (no inline SVG/HTML panel)
- User explicitly asks for mermaid
- Target is a markdown file that will be viewed on GitHub/GitLab
- Database ERDs (always mermaid `erDiagram`, never SVG)

## Theme alignment

Prefix the code block with an init directive:

Dark host:
```
%%{init: {'theme': 'dark'}}%%
flowchart TD
    A --> B
```

Light host (default, omit directive or use):
```
%%{init: {'theme': 'default'}}%%
flowchart TD
    A --> B
```

## Supported diagram types

| Type | Syntax keyword | Use case |
|------|----------------|----------|
| Flowchart | `flowchart TD` / `flowchart LR` | Sequential processes, decisions |
| Sequence | `sequenceDiagram` | Interactions between actors/systems over time |
| Class | `classDiagram` | OOP class relationships |
| State | `stateDiagram-v2` | State machines |
| ERD | `erDiagram` | Database schemas (always this, never SVG) |
| Gantt | `gantt` | Project timelines |
| Pie | `pie` | Proportional data |
| Journey | `journey` | User journey maps |
| Git graph | `gitGraph` | Branch/commit history |

## Common patterns

### Flowchart with decision
```
%%{init: {'theme': 'dark'}}%%
flowchart TD
    A[Start] --> B{Decision}
    B -->|Yes| C[Action 1]
    B -->|No| D[Action 2]
    C --> E[End]
    D --> E
```

### Sequence diagram
```
%%{init: {'theme': 'dark'}}%%
sequenceDiagram
    participant Client
    participant Server
    participant DB
    Client->>Server: GET /users
    Server->>DB: SELECT * FROM users
    DB-->>Server: rows
    Server-->>Client: 200 OK + JSON
```

### ERD
```
%%{init: {'theme': 'dark'}}%%
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ LINE_ITEM : contains
    USER {
        int id PK
        string name
        string email
    }
    ORDER {
        int id PK
        int user_id FK
        date created_at
    }
```

### State machine
```
%%{init: {'theme': 'dark'}}%%
stateDiagram-v2
    [*] --> Draft
    Draft --> Submitted: submit
    Submitted --> Approved: approve
    Submitted --> Rejected: reject
    Approved --> [*]
    Rejected --> Draft: revise
```

## Mermaid limitations vs SVG

| Capability | SVG | Mermaid |
|------------|-----|---------|
| Custom colors per node | Full control | Limited (classDef) |
| Custom layout positioning | Full control | Auto-layout only |
| Inline streaming | Yes | No (renders on client) |
| Interactive controls | Yes | No |
| Theme adaptation | Manual stops | `theme` directive |
| Complex nested structures | Yes | Limited |
| Portability | Needs SVG renderer | Renders in most markdown viewers |

Prefer SVG when the host supports it. Fall back to mermaid only for markdown-only hosts or ERDs.
