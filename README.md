# notebooks
Notebooks using the Hugging Face libraries 🤗


%% Suna – container-level architecture
%% Save as suna-architecture.mmd or embed directly in Markdown
%% (Mermaid v10 syntax)

flowchart TD
    %% === External actors ===
    User{{"Browser / Client"}}
    LLMs[(Anthropic<br>OpenAI<br>… via LiteLLM)]
    Internet((Web))
    
    %% === Front-end ===
    subgraph Frontend ["Next.js / React SPA"]
        FE[Chat UI<br>Dashboard<br>Auth flows]
    end
    
    %% === Back-end ===
    subgraph Backend_API ["Python FastAPI service"]
        BE[REST & WS endpoints]
        ThreadMgr[Thread / Agent Manager]
        LiteLLM[LiteLLM Adapter]
    end

    %% === Execution layer ===
    subgraph Agent_Runtime ["Ephemeral Docker container per agent"]
        Playwright[Playwright<br>Chromium]
        CLI[Linux shell & Python runtime]
        FS[(Ephemeral file-system)]
    end

    %% === Data & infra ===
    Supabase[(Supabase<br>Postgres + Auth + Storage)]
    Redis[(Redis / Upstash)]
    
    %% === Edges ===
    User -->|HTTPS| FE
    FE -->|REST / WebSockets| BE
    BE -->|Pub/Sub| Redis
    BE -->|SQL & Storage| Supabase
    BE -->|Spawn / manage| Agent_Runtime
    ThreadMgr --> LiteLLM -->|LLM calls| LLMs
    Agent_Runtime -->|Streaming logs<br>& artefacts| BE
    Playwright --> Internet
    CLI --> Internet
    Agent_Runtime --> FS
    FS -. persist .-> Supabase

    %% Styling
    classDef ext fill:#f7f7f7,stroke-dasharray: 4 4;
    class LLMs,Internet ext;

