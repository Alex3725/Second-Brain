# 01 — Progetto Generale: BrainOS (Second Brain)

> Documento di riferimento principale del progetto. Raccoglie e organizza tutto ciò che è descritto nella repository **Alex3725/Second-Brain**, integrando spiegazioni, tabelle e diagrammi per renderlo un documento di progettazione completo e consultabile nel tempo.

---

## Indice

1. [Spiegazione completa del progetto](#1-spiegazione-completa-del-progetto)
2. [Obiettivi](#2-obiettivi)
3. [Architettura](#3-architettura)
4. [Componenti](#4-componenti)
5. [Flusso dei dati](#5-flusso-dei-dati)
6. [Tecnologie utilizzate](#6-tecnologie-utilizzate)
7. [Hardware consigliato](#7-hardware-consigliato)
8. [Software necessario](#8-software-necessario)
9. [Servizi utilizzati](#9-servizi-utilizzati)
10. [Vantaggi e svantaggi](#10-vantaggi-e-svantaggi)
11. [Sicurezza](#11-sicurezza)
12. [Backup](#12-backup)
13. [Sincronizzazione](#13-sincronizzazione)
14. [AI utilizzate](#14-ai-utilizzate)
15. [Database](#15-database)
16. [Networking](#16-networking)
17. [Storage](#17-storage)
18. [Docker](#18-docker)
19. [VM](#19-vm)
20. [Proxmox](#20-proxmox-non-presente-nella-repository)
21. [Ollama e modelli LLM](#21-ollama-e-modelli-llm)
22. [Automazioni](#22-automazioni)
23. [Tabella riassuntiva delle caratteristiche](#23-tabella-riassuntiva-delle-caratteristiche)
24. [Decisioni progettuali da scegliere](#24-decisioni-progettuali-da-scegliere)

---

## 1. Spiegazione completa del progetto

**BrainOS** (chiamato anche *Second Brain*) è un progetto personale nato con l'obiettivo di costruire una **memoria permanente e indipendente dal modello AI** per uno sviluppatore che lavora su più progetti contemporaneamente (Unreal Engine, applicazioni desktop, tool Python, Home Assistant, Docker, ecc.).

L'idea centrale, espressa chiaramente nel README della repository, è questa:

> "L'obiettivo non è creare un assistente AI, ma costruire un cervello indipendente dal modello utilizzato."

Questo significa che il progetto **non è un chatbot**, ma un **sistema di gestione della conoscenza** (una sorta di *Personal Knowledge Operating System*, PKOS) che:

- conserva in modo strutturato tutta la conoscenza tecnica accumulata (architetture, decisioni, bug risolti, pattern, prompt, note);
- resta leggibile e utilizzabile indipendentemente dal modello AI che la interroga (GPT, Claude, Gemini, Ollama, Qwen, DeepSeek, Llama, Mistral...);
- sopravvive ai cambi di software, ai cambi di provider AI e agli anni di sviluppo.

Il concetto filosofico riassunto nella repository è:

> "Le AI dimenticano. Il Brain ricorda. Le AI ragionano. Il Brain conserva esperienza."

In pratica, il progetto propone un'evoluzione in più fasi: si parte da un semplice **vault Obsidian in Markdown**, gestito manualmente, e si arriva — nelle fasi più avanzate — a un vero e proprio **server locale (BrainOS Server)** che indicizza, collega, arricchisce con AI e rende consultabile la conoscenza da qualsiasi dispositivo, con un layer di **retrieval semantico (RAG)** e un **orchestratore multi-modello**.

> 💡 **Nota terminologica importante** (chiarita in `spiegazionePcUbuntu.md`): *BrainOS non è un sistema operativo scaricabile* come Windows o Ubuntu. È il nome dato al **software applicativo** che si costruisce sopra un sistema operativo standard (tipicamente Ubuntu Server), in esecuzione tramite Docker.

---

## 2. Obiettivi

Dalla lettura di README.md e Architecture.md emergono obiettivi chiari, organizzati su tre assi:

### 2.1 Memoria permanente
Conservare in modo strutturato e duraturo:
- Architetture di progetto
- Decisioni progettuali (Decision Log)
- Standard di codice
- Bug risolti (Bug Database)
- Soluzioni trovate
- Ricerche ed esperimenti
- Pattern di progettazione
- Prompt utili
- Note personali

### 2.2 Gestione multi-progetto
Il Brain deve gestire simultaneamente conoscenza proveniente da ambiti diversi:

| Ambito | Esempio |
|---|---|
| Unreal Engine | Progetti multiplayer, plugin, Gameplay Ability System |
| Applicazioni Desktop | Tool interni, utility |
| Tool Python | Script di automazione |
| Home Assistant | Domotica |
| Docker | Infrastruttura servizi |
| Server / Raspberry | Infrastruttura fisica |
| Blender | Asset 3D |
| Web | Progetti web vari |

Ogni progetto ha la propria documentazione dedicata, ma può condividere conoscenza trasversale (es. una soluzione trovata nel progetto A può essere utile nel progetto B).

### 2.3 Indipendenza dal modello AI (Multi-Model)
Il sistema non deve legarsi a un singolo provider. Deve poter collegarsi, tramite adapter, a:
- GPT (OpenAI)
- Claude (Anthropic)
- Gemini (Google)
- Ollama (locale)
- Qwen, DeepSeek, Llama, Mistral (modelli locali/open)
- Futuri modelli non ancora esistenti

Il modello diventa un **motore di ragionamento intercambiabile**; la memoria appartiene sempre e solo al Brain.

---

## 3. Architettura

### 3.1 Architettura concettuale (da README.md)

```mermaid
flowchart TD
    A[Obsidian Vault] --> B[Knowledge Management]
    B --> C[Retrieval / Search Layer]
    C --> D[GPT]
    C --> E[Claude]
    C --> F[Ollama]
    F --> G[Qwen]
    F --> H[DeepSeek]
    F --> I[Llama]
```

### 3.2 Architettura tecnica dettagliata (da Architecture.md)

```mermaid
flowchart TD
    U[USER] --> P[Pi Coding / IDE]
    P --> C[BrainOS Client]
    C --> RM[Request Manager]
    RM --> KR[Knowledge Retrieval]
    RM --> AO[AI Orchestrator]
    KR --> OB[Obsidian Vault]
    OB --> VDB[Vector Database]
    VDB --> MD[Markdown Files]
    AO --> GA[GPT Adapter]
    AO --> CA[Claude Adapter]
    AO --> OA[Ollama Adapter]
```

### 3.3 Architettura "server" (da BrainOsSystem.md)

Questa è l'evoluzione che trasforma il semplice vault in un **servizio** accessibile da più dispositivi:

```mermaid
flowchart TD
    Internet --> HTTPS[HTTPS + Login]
    HTTPS --> BS[BrainOS Server]
    BS --> OV[Obsidian Vault]
    BS --> AE[AI Engine]
    BS --> DB[Database]
    AE --> M1[GPT]
    AE --> M2[Ollama]
    AE --> M3[Claude]
```

Tutti i client (PC fisso, notebook, tablet, telefono, IDE, ChatGPT, app future) parlano con **un solo BrainOS**, che a sua volta è l'unico punto che tocca il vault. Questo elimina il problema delle "copie diverse della memoria".

### 3.4 Architettura di rete multi-macchina (da ideaNetwork.md)

```mermaid
flowchart TD
    Internet --> VPN[WireGuard / Tailscale VPN]
    VPN --> US[Ubuntu Server - BrainOS]
    US --> OB[Obsidian Vault]
    US --> GIT[Git / Gitea]
    US --> API[API Server FastAPI]
    OB --> VDB[Vector Database]

    Desktop[Desktop potente - Unreal/GPU/Ollama] -.consulta.-> US
    Laptop[Laptop] -.Tailscale.-> US
    Telefono[Telefono - Obsidian] -.sync.-> US
```

---

## 4. Componenti

Dalla combinazione di Architecture.md e BrainOsSystem.md, i componenti logici del sistema sono:

| Componente | Responsabilità |
|---|---|
| **Brain (Vault)** | Memoria permanente, organizzazione, collegamenti, versionamento (Markdown + Git + Obsidian) |
| **Indexer / File Watcher** | Osserva il vault, rileva modifiche, aggiorna indice, embeddings, link, cache |
| **Retrieval Engine** | Analizza la richiesta dell'utente, recupera documenti/decisioni/bug/codice rilevanti, ordina per rilevanza |
| **AI Orchestrator (Router)** | Decide quale modello AI usare in base al tipo di richiesta (locale vs cloud); l'utente può forzare la scelta |
| **Memory Writer** | Dopo una risposta rilevante, individua se è nata una decisione, un bug risolto, un nuovo pattern, e propone di aggiornare il Brain (mai in automatico, sempre con conferma) |
| **Brain Keeper** (concetto avanzato da Idee.md) | Servizio di manutenzione: deduplica, crea backlink, aggiorna il knowledge graph, propone la suddivisione di documenti grandi, segnala contraddizioni e documentazione obsoleta |
| **Vector Database** | Ricerca semantica basata su embeddings |
| **Database relazionale** | Utenti, configurazioni, log, sessioni |
| **API REST** | Punto di accesso unico per tutti i client |
| **Dashboard** | Visualizzazione stato del Brain, statistiche, salute della conoscenza |

### 4.1 Struttura logica applicativa proposta (da BrainOsSystem.md)

```text
brainos/
├── api/              # API REST (FastAPI)
├── brain/            # Gestione del vault Obsidian
├── retrieval/        # Ricerca testuale e semantica
├── embeddings/       # Aggiornamento del DB vettoriale
├── ai/
│   ├── openai.py
│   ├── ollama.py
│   ├── anthropic.py
│   └── router.py
├── maintenance/      # Deduplicazione, link, suggerimenti
├── auth/             # Login e permessi
├── database/         # SQLite/PostgreSQL
└── dashboard/        # Interfaccia web
```

### 4.2 Struttura del Vault (Knowledge Base)

```text
Brain/
├── AGENTS.md
├── 00_System/
├── 01_Identity/
├── 02_Projects/
├── 03_Knowledge/
├── 04_Architecture/
├── 05_Code/
├── 06_AI/
├── 07_Research/
├── 08_Decisions/
├── 09_Bugs/
├── 10_Snippets/
├── 11_Prompts/
├── 12_Library/
├── 13_Journal/
└── 99_Archive/
```

Ogni progetto, all'interno di `02_Projects/`, ha una struttura dedicata, ad esempio:

```text
Projects/
    Unreal RPG/
        Architecture.md
        Gameplay.md
        GAS.md
        Inventory.md
        AI.md
        SaveGame.md
        UI.md
        Decisions/
        Bugs/
        TODO.md
```

---

## 5. Flusso dei dati

Il flusso completo, descritto in dettaglio in `SpiegazioneAnalisi.md`, funziona così:

```mermaid
sequenceDiagram
    participant Utente
    participant Vault as Vault Obsidian
    participant FW as File Watcher
    participant BOS as BrainOS
    participant VDB as Vector DB
    participant AI as Modello AI (GPT/Claude/Ollama)

    Utente->>Vault: Scrive/modifica un file .md
    Vault->>FW: Nuovo file rilevato
    FW->>BOS: Notifica cambiamento
    BOS->>BOS: Analisi senza AI (titolo, tag, data, link)
    BOS->>AI: Analisi con AI (estrazione concetti, tag suggeriti)
    AI-->>BOS: Concetti, related_files, tag
    BOS->>VDB: Aggiorna embedding
    BOS->>BOS: Crea collegamenti (link automatici)
    BOS-->>Utente: Propone modifiche (mai automatiche)
    Utente->>BOS: Approva
    BOS->>Vault: Aggiorna il Brain
```

### 5.1 Flusso di una richiesta (query)

```mermaid
sequenceDiagram
    participant Client as Client (telefono/IDE/PC)
    participant BOS as BrainOS Server
    participant Ret as Retrieval Engine
    participant AI as Modello AI

    Client->>BOS: POST /chat {"question": "Come funziona il Save System?"}
    BOS->>Ret: Classifica la richiesta (categoria: Unreal > SaveGame)
    Ret->>Ret: Cerca documenti, decisioni, bug correlati
    Ret-->>BOS: SaveGame.md, Decision 31, Bug 8, Architecture.md
    BOS->>BOS: Costruisce il prompt con contesto
    BOS->>AI: Invia domanda + contesto
    AI-->>BOS: Risposta
    BOS-->>Client: Risposta finale
```

### 5.2 Gerarchia delle fonti (priorità di consultazione)

Per evitare che il codice diventi l'unica fonte di verità, la repository (Idee.md) propone questa gerarchia obbligatoria per qualsiasi agente AI collegato:

```text
1. Brain (conoscenza globale)
        ↓
2. Documentazione del progetto
        ↓
3. Codice del progetto
        ↓
4. Internet
```

---

## 6. Tecnologie utilizzate

Elenco delle tecnologie citate nella repository, organizzate per categoria.

| Categoria | Tecnologie citate |
|---|---|
| Editor conoscenza | Obsidian |
| Formato dati | Markdown |
| Versionamento | Git, Gitea, Forgejo |
| Backend applicativo | Python, FastAPI (preferito), Flask, Node.js |
| Database relazionale | SQLite (iniziale), PostgreSQL (avanzata) |
| Database vettoriale | ChromaDB, Qdrant, FAISS, LanceDB, PgVector |
| Embedding | OpenAI Embeddings, Nomic Embed, BGE, Jina Embeddings |
| AI Cloud | GPT, Claude, Gemini |
| AI Locale | Ollama, LM Studio, Open WebUI |
| Modelli locali | Qwen, DeepSeek, Llama, Mistral |
| Containerizzazione | Docker, Docker Compose |
| Rete / VPN | Tailscale, WireGuard |
| DNS locale | Pi-hole, AdGuard Home, Technitium DNS |
| Reverse proxy | Caddy, Nginx |
| Cloud personale / File sync | Nextcloud |
| Foto | Immich |
| Monitoraggio | Grafana, Prometheus |
| Dashboard iniziale server | Homepage |

---

## 7. Hardware consigliato

La repository fornisce indicazioni pratiche (`BrainOsSystem.md`, `ideaNetwork.md`, `opzioneAumentoMemoriaUbuntu.md`) su come dimensionare l'hardware, distinguendo tra un server "leggero" dedicato alla memoria e una macchina potente dedicata al calcolo AI/Unreal.

### 7.1 Requisiti minimi per la prima versione (FastAPI + Obsidian + ChromaDB + SQLite)

| Risorsa | Requisito minimo |
|---|---|
| CPU | 4 core o superiore |
| RAM | 8–16 GB |
| Storage | SSD (consigliato) |
| Virtualizzazione servizi | Docker (opzionale ma molto utile) |

### 7.2 Requisiti se si usano anche modelli locali via Ollama

| Risorsa | Requisito consigliato |
|---|---|
| RAM | 32 GB (buon punto di partenza per modelli 7–14B) |
| GPU | NVIDIA con almeno 12 GB di VRAM (non indispensabile, ma migliora molto le prestazioni) |
| Alternativa senza GPU | Modelli più piccoli su CPU, accettando tempi di risposta più lunghi |

### 7.3 Separazione dei ruoli hardware (proposta in ideaNetwork.md)

| Macchina | Ruolo |
|---|---|
| **PC 1 (vecchio, sempre acceso)** | Ubuntu Server: BrainOS, DNS locale, Git server, Vault, Vector DB, API, Dashboard, Backup. Non fa AI pesante, fa memoria. |
| **PC 2 (fisso potente)** | Unreal Engine, build, compilazione, AI locale pesante (Ollama, modelli 14B/32B), GPU. Non conserva il Brain, lo consulta. |
| **Laptop** | Sviluppo, studio, appunti al volo. Sincronizza col Brain ovunque ci si trovi. |

### 7.4 Storage esterno

Consigliato un **SSD esterno USB 3.2** (es. Samsung T7, 1 TB) per separare **sistema** (SSD interno) e **dati** (SSD esterno). In caso di reinstallazione del sistema operativo, i dati restano intatti.

```text
Ubuntu Server
│
├── SSD interno
│   ├── Ubuntu Server
│   ├── Docker
│   ├── BrainOS
│   └── Servizi
│
└── SSD esterno (es. 1 TB)
    ├── Obsidian Vault
    ├── Git Repositories
    ├── Database vettoriale
    ├── Backup
    ├── Allegati (PDF, immagini, video)
    └── Archivio
```

> ⚠️ Attenzione: montare il disco tramite **UUID** in `/etc/fstab`, non tramite il nome del device (`/dev/sdb1`), che può cambiare tra un riavvio e l'altro.

---

## 8. Software necessario

| Software | Ruolo | Obbligatorio? |
|---|---|---|
| Ubuntu Server (o altra distro Linux) | Sistema operativo host | Sì |
| Docker + Docker Compose | Gestione isolata dei servizi | Fortemente consigliato |
| Obsidian | Editor del vault Markdown | Sì (lato client) |
| Git | Versionamento del Brain | Sì |
| Python 3.x | Linguaggio per BrainOS | Sì (se si sviluppa BrainOS in proprio) |
| FastAPI | Framework API | Consigliato |
| SQLite / PostgreSQL | Database applicativo | Sì (uno dei due) |
| ChromaDB / Qdrant / FAISS / LanceDB | Database vettoriale | Sì, per il retrieval semantico |
| Ollama | Runtime AI locale | Opzionale, ma centrale per la privacy |
| Tailscale o WireGuard | Accesso remoto sicuro | Consigliato se si vuole accesso fuori casa |
| Nextcloud | Sincronizzazione file generica | Opzionale |
| Gitea / Forgejo | Git server privato | Opzionale |

---

## 9. Servizi utilizzati

La repository (in particolare `spiegazionePcUbuntu.md`) descrive un "server casa" completo, dove BrainOS diventa uno dei servizi ospitati assieme ad altri:

```text
Ubuntu Server
├── BrainOS
│   ├── AI
│   ├── Ricerca
│   ├── Memoria
│   └── API
├── Obsidian Vault
├── Gitea
├── Nextcloud
├── Immich
├── Pi-hole
├── Tailscale
├── Grafana
├── Prometheus
├── Homepage (dashboard)
└── Backup
```

| Servizio | Funzione | Analogia |
|---|---|---|
| BrainOS | Cervello/memoria AI personale | "ChatGPT privato collegato alla tua conoscenza" |
| Gitea/Forgejo | Repository Git privato | GitHub personale |
| Nextcloud | Sincronizzazione file | Google Drive privato |
| Immich | Gestione foto | Google Foto privato |
| Pi-hole / AdGuard Home | DNS con blocco pubblicità/tracker | — |
| Tailscale / WireGuard | Accesso remoto sicuro | VPN privata |
| Grafana + Prometheus | Monitoraggio e dashboard | — |
| Homepage | Pannello di controllo iniziale | — |
| Caddy / Nginx | Reverse proxy | Unico ingresso per tutti i servizi |

---

## 10. Vantaggi e svantaggi

| Aspetto | Vantaggi | Svantaggi |
|---|---|---|
| **Indipendenza dal modello AI** | Nessun lock-in verso un singolo provider; si può cambiare modello senza perdere la conoscenza | Richiede la costruzione di adapter per ogni provider |
| **Conoscenza centralizzata** | Un solo posto per tutta l'esperienza tecnica accumulata negli anni | Richiede disciplina nella scrittura/manutenzione dei documenti |
| **Formato Markdown** | Leggibile, portabile, versionabile con Git, indipendente da software proprietario | Meno strutturato di un database relazionale puro |
| **BrainOS Server** | Accesso da qualsiasi dispositivo con un'unica fonte di verità | Aggiunge complessità infrastrutturale (server sempre acceso, sicurezza, manutenzione) |
| **AI locale (Ollama)** | Privacy, nessun costo per token, funziona offline | Richiede hardware più performante (RAM/GPU) |
| **AI cloud (GPT/Claude)** | Qualità di risposta più alta, nessun hardware dedicato | Costo per uso, dati inviati a terzi |
| **Automazioni (Brain Keeper)** | Riduce il lavoro manuale di manutenzione (deduplica, link, suggerimenti) | Richiede sviluppo custom, non esiste "pronto all'uso" |
| **Architettura modulare** | Ogni componente sostituibile senza rifare tutto | Più componenti da imparare e mantenere insieme |

---

## 11. Sicurezza

Dalla repository (Architecture.md, Idee.md) emergono i seguenti principi di sicurezza:

- **Nessuna modifica automatica del Brain**: ogni modifica proposta dall'AI deve essere approvata dall'utente.
- **Nessuna cancellazione automatica** di documenti o dati.
- **Versionamento obbligatorio** tramite Git, per poter sempre tornare indietro.
- **Backup automatico** come requisito di sistema, non opzionale.
- **Accesso remoto solo tramite VPN** (Tailscale o WireGuard): BrainOS non dovrebbe mai essere esposto direttamente su Internet senza un livello di protezione aggiuntivo.
- **Autenticazione/login** previsto esplicitamente nello schema `HTTPS + Login` quando si accede da Internet.
- **Isolamento dei servizi tramite Docker**: se un servizio (es. Nextcloud) viene compromesso o cancellato, gli altri (es. BrainOS) non vengono impattati.

> 📝 Nota: la repository non entra nel dettaglio di meccanismi di autenticazione specifici (OAuth, JWT, ecc.) — questo è uno dei punti lasciati aperti e trattato nella sezione "Decisioni progettuali da scegliere" più avanti in questo documento.

---

## 12. Backup

La repository insiste molto sulla ridondanza dei dati, poiché il Brain è definito "il bene più prezioso del sistema" (`opzioneAumentoMemoriaUbuntu.md`).

### 12.1 Strategia delle "tre copie"

```mermaid
flowchart LR
    A[Copia principale<br/>SSD server] --> B[Repository Git<br/>locale o remoto privato]
    A --> C[Backup periodico<br/>secondo supporto o cloud]
```

- **Copia principale**: sull'SSD collegato al server (interno o esterno).
- **Repository Git**: fornisce cronologia e possibilità di recupero puntuale delle modifiche.
- **Backup periodico**: su un secondo supporto fisico o su un servizio cloud.

### 12.2 Combinazione consigliata dalla repository (Idee.md)

```text
Obsidian Vault
    ↓
Git
    ↓
GitHub (privato)
    ↓
NAS
    ↓
Backup automatico
```

> ⚠️ La repository sconsiglia esplicitamente di tenere il Brain in un solo posto (solo PC, solo GitHub, o solo NAS), perché si perderebbe ridondanza.

---

## 13. Sincronizzazione

La repository elenca quattro metodi alternativi per portare i file scritti "fuori casa" (es. su un laptop) fino al server (`SpiegazioneAnalisi.md`):

| Metodo | Come funziona | Adatto a |
|---|---|---|
| **A. Obsidian Sync** | Sincronizzazione nativa di Obsidian tra dispositivi | Utenti non tecnici, massima semplicità |
| **B. Git** | `git add / commit / push` dal client, `git pull` sul server | Sviluppatori, chi vuole storia delle modifiche |
| **C. Nextcloud** | Sincronizzazione tipo Google Drive verso il server | File misti (PDF, immagini, documenti) |
| **D. VPN diretta (Tailscale)** | Accesso diretto alla cartella del Brain sul server | Chi vuole un'unica fonte, senza copie locali |

---

## 14. AI utilizzate

### 14.1 Modelli Cloud citati
- GPT (OpenAI)
- Claude (Anthropic)
- Gemini (Google)

### 14.2 Modelli locali/open citati (tramite Ollama)
- Qwen
- DeepSeek
- Llama
- Mistral

### 14.3 Strategie di utilizzo dei modelli (da SpiegazioneAnalisi.md)

| Strategia | Descrizione | Pro | Contro |
|---|---|---|---|
| **Solo Cloud** | BrainOS → API OpenAI/Anthropic → GPT/Claude | Qualità alta, semplice da implementare | Costo, dati inviati fuori casa |
| **Solo Locale** | BrainOS → Ollama → Qwen/Llama/DeepSeek | Privato, gratuito, offline | Richiede hardware adeguato |
| **Sistema ibrido (Router)** — consigliato dalla repository | Un router sceglie il modello in base alla complessità del compito (es. appunto semplice → modello locale piccolo; documento enorme → GPT/Claude) | Bilancia costo, privacy e qualità | Più complesso da progettare |

### 14.4 Ruolo dell'AI nel sistema

L'AI nel progetto **non è mai l'unica fonte di verità**: analizza, suggerisce, collega concetti, ma ogni modifica al Brain deve passare da un'approvazione umana. L'AI viene inoltre usata per:

- estrazione di concetti chiave da nuovi documenti;
- suggerimento di tag e collegamenti;
- individuazione di documenti duplicati o in conflitto;
- generazione di documentazione mancante quando cambia il codice;
- riassunto e proposta di suddivisione di documenti troppo grandi.

---

## 15. Database

| Tipo | Tecnologie | Uso |
|---|---|---|
| Relazionale | SQLite (iniziale) / PostgreSQL (avanzata) | Utenti, configurazioni, sessioni, log |
| Vettoriale | ChromaDB (iniziale) / Qdrant (avanzata) / FAISS / LanceDB / PgVector | Ricerca semantica sui contenuti del vault |

Il concetto chiave, spiegato in modo semplice in `spiegazionePcUbuntu.md`, è la differenza tra ricerca classica e ricerca semantica:

> Un database normale cerca parole esatte ("salvataggio giocatore" trova solo quella frase). Un database vettoriale cerca il significato: la domanda "Come salvo i dati del personaggio?" trova comunque `PlayerSaveSystem.md`, anche se il testo non contiene esattamente quelle parole.

---

## 16. Networking

| Elemento | Ruolo | Fonte |
|---|---|---|
| Tailscale | VPN semplice, quasi zero configurazione, per accedere al server da fuori casa "come se si fosse in LAN" | ideaNetwork.md, spiegazionePcUbuntu.md |
| WireGuard | VPN più manuale/amministrativa (Tailscale la usa internamente) | ideaNetwork.md |
| Pi-hole / AdGuard Home | DNS locale con blocco pubblicità e tracker per tutta la rete domestica | spiegazionePcUbuntu.md |
| Technitium DNS | DNS locale alternativo, utile per risolvere nomi come `brain.local`, `vault.home` | pcServerToDoListInstallation.md |
| Reverse Proxy (Caddy/Nginx) | Un unico punto di ingresso per più servizi web, evitando di ricordare porte diverse (`server:8000`, `server:3000`...) | spiegazionePcUbuntu.md |
| DHCP reservation sul router | Alternativa all'IP statico: il server riceve sempre lo stesso IP senza configurazione manuale | pcServerToDoListInstallation.md |

---

## 17. Storage

Vedi anche sezione [7.4](#74-storage-esterno). La struttura dati consigliata dalla repository, pensata per crescere nel tempo (es. passaggio da 1 TB a 2-4 TB copiando semplicemente la cartella):

```text
/data
│
├── brain/
│   ├── vault/
│   ├── vectors/
│   ├── database/
│   └── cache/
│
├── git/
├── backups/
├── projects/
├── media/
└── docker/
```

Tutti i servizi fanno riferimento a `/data/...`, indipendentemente dal disco fisico sottostante.

---

## 18. Docker

Docker è considerato dalla repository **il modo corretto di gestire i servizi** (`spiegazionePcUbuntu.md`), per evitare conflitti tra versioni di Python, database e librerie di servizi diversi installati sulla stessa macchina.

```text
Ubuntu Server
Docker
├── Container BrainOS
├── Container Gitea
├── Container Nextcloud
├── Container Grafana
└── Container Pi-hole
```

Ogni servizio vive isolato: cancellare o aggiornare un container non impatta gli altri.

---

## 19. VM

La repository **non tratta esplicitamente l'uso di macchine virtuali classiche** (VMware, VirtualBox, Hyper-V) come componente del progetto: l'isolamento dei servizi è affidato interamente a **Docker**, non a VM dedicate per servizio. L'unico concetto vicino a una "virtualizzazione dell'infrastruttura" presente nei file è l'idea di separare fisicamente i ruoli tra più macchine (PC vecchio da server, PC potente da workstation AI/Unreal), ma non tramite virtualizzazione.

---

## 20. Proxmox (non presente nella repository)

> ⚠️ **Elemento non presente nella repository.** Nessuno dei file letti cita Proxmox VE o altre piattaforme di virtualizzazione/orchestrazione hardware.

Questo è un punto in cui la repository può essere considerata **incompleta rispetto a un'infrastruttura enterprise-like**: se in futuro si vorrà ospitare BrainOS assieme a più VM isolate (es. una VM per il server BrainOS, una per Home Assistant, una per servizi di test), **Proxmox VE** sarebbe un'alternativa valida a "Ubuntu Server nudo + Docker", perché permetterebbe:

- snapshot e rollback a livello di intera VM (non solo di container);
- isolamento più forte tra servizi critici (es. DNS) e servizi sperimentali;
- gestione centralizzata di più macchine fisiche/virtuali da un'unica dashboard.

Questo è indicato come **possibile miglioramento rispetto all'approccio descritto nella repository**, da valutare nella sezione decisioni più avanti.

---

## 21. Ollama e modelli LLM

Ollama è il runtime scelto nella repository per l'esecuzione di modelli AI **in locale** (`README.md`, `BrainOsSystem.md`, `SpiegazioneAnalisi.md`).

```text
BrainOS
    ↓
Ollama
    ↓
Qwen / DeepSeek / Llama
```

Punti chiave:
- Ollama gira idealmente sul **PC potente** (con GPU), non sul server "leggero" che ospita solo la memoria.
- Il server BrainOS può quindi inoltrare le richieste AI locale al PC potente, mantenendo il server stesso poco carico.
- Per modelli locali di fascia 7-14B, la repository consiglia almeno 32 GB di RAM; una GPU NVIDIA con 12+ GB di VRAM migliora sensibilmente le prestazioni ma non è obbligatoria.

---

## 22. Automazioni

Le automazioni descritte (soprattutto in `Idee.md` e `ideaNetwork.md`) ruotano attorno al concetto di **Brain Keeper**, un servizio di manutenzione che gira in background (es. ogni notte):

```mermaid
flowchart TD
    V[Vault] --> S[Scanner]
    S --> E[Embedding]
    E --> D[Deduplicazione]
    D --> K[Knowledge Graph]
    K --> R[Report]
```

Compiti automatizzati proposti:
- rilevare documenti duplicati;
- rilevare contraddizioni tra documenti;
- rilevare documenti troppo grandi e proporne la suddivisione;
- rilevare link mancanti o rotti;
- rilevare documentazione obsoleta rispetto al codice (Code Sync);
- generare automaticamente collegamenti tra documenti simili (via embeddings);
- generare un **Knowledge Score** per ogni nota (qualità, ultimo aggiornamento, numero di collegamenti, duplicazioni, copertura, AI confidence, necessità di review).

> Regola fondamentale ribadita più volte nella repository: **nessuna automazione modifica mai il Brain senza conferma esplicita dell'utente.**

---

## 23. Tabella riassuntiva delle caratteristiche

| Caratteristica | Presente nella repository | Fase prevista |
|---|---|---|
| Vault Markdown + Obsidian | ✅ | Fase 1 |
| AGENTS.md come entry point per l'AI | ✅ | Fase 1 |
| Versionamento Git | ✅ | Fase 1 |
| Decision Log strutturato | ✅ | Fase 1 |
| Bug Database strutturato | ✅ | Fase 1 |
| Indicizzazione automatica / Tag / Template | ✅ | Fase 2 |
| Ricerca semantica (RAG) / Vector DB | ✅ | Fase 3 |
| Memoria attiva (AI propone aggiornamenti) | ✅ | Fase 4 |
| Brain autonomo / orchestrazione multi-agente | ✅ | Fase 5 |
| Server BrainOS accessibile da remoto | ✅ | Fase avanzata (server) |
| Brain Keeper (manutenzione automatica) | ✅ | Fase avanzata |
| Knowledge Score per documento | ✅ (proposta) | Fase avanzata |
| Dashboard (statistiche, salute del Brain) | ✅ | Fase avanzata |
| API pubbliche REST | ✅ | Fase avanzata |
| Plugin per IDE/Unreal | ✅ (proposta futura) | Fase avanzata |
| Docker per isolamento servizi | ✅ | Trasversale |
| VPN (Tailscale/WireGuard) | ✅ | Trasversale |
| DNS locale (Pi-hole/Technitium) | ✅ | Opzionale |
| Backup su più copie | ✅ | Trasversale |
| **Proxmox / virtualizzazione hardware** | ❌ Non presente | — |
| Autenticazione dettagliata (OAuth/JWT ecc.) | ❌ Non dettagliata | Da definire |
| CI/CD per BrainOS | ❌ Non trattata | Da definire |

---

# 24. Decisioni progettuali da scegliere

> ⚠️ In questa sezione **non vengono prese decisioni al posto tuo**. Per ogni punto tecnico che richiede una scelta, trovi vantaggi, svantaggi, quando conviene una opzione rispetto a un'altra, e un consiglio finale — ma la scelta resta tua. Rispondimi punto per punto (anche in più messaggi) e aggiornerò questo documento come riferimento definitivo del progetto.

## 24.1 Sistema operativo del server

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **Ubuntu Server LTS** | Enorme community, supporto lunghissimo (5 anni), compatibilità totale con Docker, molta documentazione | Nessuna interfaccia grafica di default (va bene per un server) | Caso generale, consigliato dalla repository stessa |
| **Debian** | Estremamente stabile, leggero, alla base di Ubuntu | Pacchetti spesso più "vecchi", community leggermente più tecnica | Se vuoi il massimo della stabilità e sei già a tuo agio con Linux |
| **Proxmox VE** (come OS host) | Aggiunge virtualizzazione nativa (VM + container LXC), snapshot, dashboard web di gestione | Più complesso, pensato per gestire VM oltre che container Docker | Se vuoi isolare BrainOS in una VM dedicata e ospitare altri servizi/VM sullo stesso hardware |
| **Windows con WSL2/Docker Desktop** | Comodo se il PC è condiviso anche per altro uso | Meno efficiente come "server sempre acceso", overhead maggiore | Solo se non hai un PC dedicato da trasformare in server |

**Cosa consiglierei:** Ubuntu Server LTS, in linea con quanto già indicato nella repository, salvo tu voglia sperimentare la virtualizzazione con Proxmox come base.

**Quale preferisci?**

---

## 24.2 Database applicativo (relazionale)

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **SQLite** | Zero configurazione, un solo file, perfetto per uso singolo utente | Non gestisce bene accessi concorrenti multipli, meno scalabile | Uso personale/domestico, pochi utenti simultanei |
| **PostgreSQL** | Robusto, scalabile, supporta anche estensioni vettoriali (pgvector), ottimo per crescita futura | Richiede un servizio dedicato (container), più configurazione iniziale | Se prevedi crescita, più utenti, o vuoi unificare DB relazionale e vettoriale in un solo motore (pgvector) |

**Cosa consiglierei:** iniziare con SQLite se l'uso è singolo-utente domestico; passare a PostgreSQL (con pgvector) se prevedi di far crescere BrainOS in un servizio multi-utente o multi-dispositivo con accessi concorrenti.

**Quale preferisci?**

---

## 24.3 Database vettoriale (ricerca semantica)

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **ChromaDB** | Semplicissimo da avviare, ottimo per iniziare, integrazione Python immediata | Meno performante su grandi volumi, funzionalità avanzate limitate | Fase iniziale, vault di dimensioni contenute (poche migliaia di documenti) |
| **Qdrant** | Molto performante, pensato per produzione, supporta filtri complessi | Richiede un servizio dedicato, curva di apprendimento leggermente maggiore | Quando il vault cresce molto o vuoi ricerche più sofisticate (filtri per progetto, tag, data) |
| **FAISS** | Velocissimo, libreria matura (Meta), nessun server esterno necessario | Meno "pronto all'uso" come database (è più una libreria di indicizzazione) | Se preferisci gestire tu la persistenza e vuoi il massimo controllo |
| **LanceDB** | Moderno, pensato per dati multimodali, buona integrazione con Python | Ecosistema più giovane, meno maturo di Qdrant | Se vuoi sperimentare con ricerca multimodale (testo + immagini) in futuro |
| **PgVector (estensione PostgreSQL)** | Un solo database per tutto (relazionale + vettoriale), meno servizi da gestire | Prestazioni leggermente inferiori a un vector DB dedicato su carichi molto grandi | Se hai già scelto PostgreSQL come DB principale e vuoi semplificare l'infrastruttura |

**Cosa consiglierei:** ChromaDB per partire subito senza attriti; se in seguito senti il bisogno di più potenza o di un'infrastruttura più unificata, migrare a Qdrant (standalone) o PgVector (se hai scelto PostgreSQL).

**Quale preferisci?**

---

## 24.4 Docker oppure installazione nativa dei servizi

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **Docker / Docker Compose** | Isolamento tra servizi, facilità di backup/ripristino, portabilità tra macchine, aggiornamenti puliti | Leggero overhead di risorse, richiede imparare Docker Compose | Praticamente sempre, soprattutto con più servizi (BrainOS + Nextcloud + Gitea + ecc.) — è anche la scelta esplicitamente indicata nella repository |
| **Installazione nativa** | Massima performance, nessun overhead | Conflitti tra dipendenze, difficile da fare backup/rollback, "sporca" il sistema operativo nel tempo | Solo per componenti molto semplici o test rapidi |

**Cosa consiglierei:** Docker Compose per tutto, com'è già indicato chiaramente nella repository. È lo standard de facto per questo tipo di homelab/infrastruttura personale.

**Quale preferisci?**

---

## 24.5 Reverse proxy

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **Caddy** | Configurazione semplicissima, HTTPS automatico (Let's Encrypt) senza configurazione manuale | Meno flessibile su configurazioni molto avanzate/esotiche | Consigliato per la maggior parte degli usi domestici, specialmente se si vuole accesso via dominio |
| **Nginx** | Standard de facto, massima flessibilità, moltissima documentazione | Configurazione più verbosa, HTTPS richiede più passaggi manuali (o Certbot) | Se hai già esperienza con Nginx o esigenze di configurazione molto specifiche |
| **Nessun reverse proxy (solo IP:porta)** | Zero configurazione aggiuntiva | Scomodo con molti servizi, nessun HTTPS automatico | Solo per un setup minimo, temporaneo o puramente domestico via LAN senza dominio |

**Cosa consiglierei:** Caddy, per la semplicità con cui gestisce automaticamente i certificati HTTPS, specialmente se in futuro vorrai esporre qualche servizio con nome di dominio.

**Quale preferisci?**

---

## 24.6 Metodo di backup

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **Script manuale + cron (rsync/tar)** | Semplice, gratuito, pieno controllo | Va costruito e testato a mano, meno funzionalità avanzate | Setup semplice, budget zero, controllo totale |
| **Restic** | Backup incrementali, cifrati, deduplicati, supporta molte destinazioni (locale, cloud, NAS) | Richiede configurazione iniziale e comprensione dei concetti (repository, snapshot) | Consigliato per backup seri e automatizzati con cifratura |
| **BorgBackup** | Simile a Restic, molto maturo, ottima deduplicazione | Community leggermente più piccola di Restic | Alternativa valida a Restic, preferibile se già la conosci |
| **Servizio cloud gestito (es. Backblaze B2, storage S3-compatibile)** | Offsite automatico, protezione da disastri fisici (incendio, furto) | Costo ricorrente, dati fuori casa (da cifrare comunque) | Come terza copia, complementare al backup locale |

**Cosa consiglierei:** Restic (o Borg) con cron per gli snapshot locali/NAS, più eventualmente una destinazione cloud cifrata come terza copia, in linea con la "regola delle tre copie" indicata nella repository.

**Quale preferisci?**

---

## 24.7 Hardware del server

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **PC vecchio riciclato** | Costo zero/basso, già indicato come strategia nella repository | Prestazioni limitate, consumo energetico non ottimizzato, affidabilità incerta nel tempo | Se hai già un PC dismesso e vuoi partire subito senza spese |
| **Mini PC dedicato (es. Intel NUC o simili)** | Basso consumo, silenzioso, compatto | Costo iniziale, espandibilità limitata | Se vuoi un server "sempre acceso" 24/7 con consumi contenuti |
| **NAS con supporto Docker (es. Synology/QNAP)** | Storage e servizi integrati, interfaccia gestionale comoda | Meno flessibile di un PC Linux puro, CPU spesso meno potente | Se vuoi unificare storage e servizi in un solo apparecchio |
| **Server rack/tower dedicato** | Massima espandibilità e affidabilità | Costo, rumore, consumo energetico maggiore | Setup più "serio", se prevedi crescita significativa |

**Cosa consiglierei:** Partire con il PC vecchio (come già suggerito nella repository) per validare l'idea a costo zero, valutando un mini PC dedicato in un secondo momento se il server dovrà restare acceso 24/7 a lungo termine.

**Quale preferisci?**

---

## 24.8 Modello AI principale

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **Solo Cloud (GPT/Claude/Gemini)** | Qualità di risposta massima, nessun hardware dedicato necessario | Costo per token, dati inviati a terzi, richiede connessione Internet | Se privacy e offline non sono prioritari e vuoi la massima qualità |
| **Solo Locale (Ollama + Qwen/Llama/DeepSeek/Mistral)** | Privacy totale, nessun costo ricorrente, funziona offline | Richiede hardware adeguato (RAM/GPU), qualità generalmente inferiore ai modelli cloud di punta | Se la privacy dei dati tecnici è prioritaria o vuoi azzerare i costi |
| **Ibrido con Router (consigliato dalla repository)** | Bilancia costo/qualità/privacy in base al tipo di richiesta | Più complesso da progettare e mantenere | Soluzione più equilibrata nel lungo periodo, specialmente con più tipi di richieste (appunti veloci vs analisi complesse) |

**Cosa consiglierei:** partire in modalità ibrida semplice — Ollama locale per richieste quotidiane leggere, un modello cloud (Claude o GPT) per analisi complesse o refactoring importanti — così come indicato nella "Strategia 3" descritta in `SpiegazioneAnalisi.md`.

**Quale preferisci?**

---

## 24.9 Storage (interno vs esterno vs NAS)

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **SSD interno unico** | Setup più semplice, meno cavi/periferiche | Sistema e dati mescolati: reinstallare l'OS rischia i dati se non gestito bene | Setup minimale, dati poco critici |
| **SSD interno (sistema) + SSD esterno USB (dati)** — consigliato dalla repository | Separazione sistema/dati, reinstallazione OS senza perdere il Brain | Dipendenza da una connessione USB esterna | Setup consigliato per la maggior parte degli utenti singoli |
| **NAS dedicato in rete** | Massima flessibilità, RAID per ridondanza, accessibile da più macchine | Costo, maggiore complessità di rete | Se hai già un NAS o prevedi di gestire molti più dati (foto, video, backup di altri dispositivi) |

**Cosa consiglierei:** SSD interno per il sistema + SSD esterno USB 3.2 per i dati, esattamente come indicato nella repository, con eventuale NAS in futuro come ulteriore livello di backup.

**Quale preferisci?**

---

## 24.10 Orchestrazione dei servizi

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **Docker Compose puro** | Semplice, sufficiente per un singolo host, ampiamente documentato | Nessuna alta disponibilità, nessun failover automatico | Setup domestico su singola macchina (il caso descritto nella repository) |
| **Docker Swarm** | Introduce redundanza/scaling multi-nodo restando nell'ecosistema Docker | Complessità aggiuntiva non necessaria per un singolo host | Se in futuro avrai più server fisici da orchestrare insieme |
| **Kubernetes (K3s/K8s)** | Massima scalabilità e resilienza, standard industriale | Overhead enorme per un uso domestico single-user, non necessario | Solo se il progetto crescesse fino a diventare un servizio multi-utente serio |

**Cosa consiglierei:** Docker Compose semplice. Kubernetes sarebbe eccessivo per il caso d'uso descritto nella repository (single user, uso domestico) — a meno che tu non voglia usarlo come progetto di apprendimento a sé stante.

**Quale preferisci?**

---

## 24.11 Sistema di autenticazione (accesso remoto)

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **Nessuna autenticazione, solo VPN (Tailscale/WireGuard)** | Semplicissimo, la VPN stessa fa da "porta chiusa" | Se qualcuno accede alla VPN, accede anche a BrainOS senza ulteriore barriera | Uso strettamente personale, un solo utente, rete privata fidata |
| **Autenticazione semplice (username/password + sessione)** | Facile da implementare, aggiunge un secondo livello di protezione | Va gestita con cura (hashing password, gestione sessioni) | Se in futuro più persone (o dispositivi) accederanno a BrainOS |
| **OAuth2 / SSO (es. Authelia, Keycloak)** | Sicurezza avanzata, single sign-on per tutti i servizi del server | Complessità di configurazione significativa | Se il server ospita molti servizi diversi e vuoi un login unico e centralizzato |

**Cosa consiglierei:** per un uso singolo-utente in ambito domestico, Tailscale da solo è già un livello di sicurezza molto solido; se prevedi comunque un'esposizione più ampia (accesso da Internet non solo via VPN), aggiungerei almeno un'autenticazione semplice sopra la VPN.

**Quale preferisci?**

---

## 24.12 Virtualizzazione dell'infrastruttura (Proxmox sì/no)

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **Nessuna virtualizzazione (Ubuntu Server + Docker diretto)** — approccio della repository | Più semplice, meno risorse "sprecate" in overhead di virtualizzazione | Meno isolamento tra "esperimenti" e servizi core, backup/rollback solo a livello di container | Setup singolo, hardware limitato, vuoi restare aderente a quanto già descritto nella repository |
| **Proxmox VE come hypervisor** | VM isolate per servizi critici (es. DNS, BrainOS) separate da VM sperimentali, snapshot a livello di intera macchina virtuale | Overhead di risorse, curva di apprendimento aggiuntiva, hardware deve reggere sia Proxmox sia i servizi | Se prevedi di ospitare più servizi eterogenei e vuoi poter "distruggere e ricreare" ambienti sperimentali senza rischi per BrainOS |

**Cosa consiglierei:** dato che la repository stessa non prevede Proxmox e punta su un'infrastruttura semplice a singolo host con Docker, manterrei questo approccio per partire; Proxmox potrebbe diventare interessante in una fase successiva se deciderai di consolidare più server fisici in uno solo più potente.

**Quale preferisci?**

---

*Fine documento. Attendo le tue risposte punto per punto per trasformare la sezione 24 in scelte definitive, che verranno poi riportate come "Decisioni Finali" in un aggiornamento di questo documento.*
