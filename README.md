# BrainOS - AI Knowledge Brain

## Vision

BrainOS è un sistema di conoscenza centralizzato progettato per essere la memoria permanente di uno sviluppatore.

L'obiettivo non è creare un assistente AI, ma costruire un cervello indipendente dal modello utilizzato.

Qualsiasi modello (GPT, Claude, Gemini, DeepSeek, Qwen, Llama, Mistral, ecc.) deve poter consultare la stessa base di conoscenza e contribuire ad aggiornarla.

Il cervello deve sopravvivere ai cambi di modello, ai cambi di software e agli anni di sviluppo.

---

# Obiettivi

## Memoria permanente

Conservare tutte le informazioni importanti:

* Architetture
* Decisioni progettuali
* Standard di codice
* Bug risolti
* Soluzioni trovate
* Ricerche
* Esperimenti
* Pattern
* Prompt
* Note personali

---

## Multi-progetto

Il cervello deve gestire contemporaneamente:

* Progetti Unreal Engine
* Plugin Unreal
* Applicazioni Desktop
* Tool Python
* Home Assistant
* Docker
* Server
* Raspberry
* Blender
* Web
* Qualsiasi altro progetto futuro

Ogni progetto possiede la propria documentazione ma può condividere conoscenza comune.

---

## Multi Modello AI

Il sistema non deve dipendere da un provider.

Deve funzionare con:

* GPT
* Claude
* Gemini
* Ollama
* Qwen
* DeepSeek
* Llama
* Mistral
* Futuri modelli

Il modello diventa solamente il motore di ragionamento.

La memoria appartiene al Brain.

---

# Filosofia

Le AI dimenticano.

Il Brain ricorda.

Le AI ragionano.

Il Brain conserva esperienza.

---

# Architettura

```
                BrainOS

              Obsidian Vault

                     │

          Knowledge Management

                     │

        Retrieval / Search Layer

                     │

        ┌────────────┼─────────────┐
        │            │             │

      GPT        Claude        Ollama

                                 │

                     ┌───────────┼───────────┐
                     │           │           │

                  Qwen      DeepSeek      Llama
```

---

# Struttura della Knowledge Base

```
Brain/

AGENTS.md

00_System/

01_Identity/

02_Projects/

03_Knowledge/

04_Architecture/

05_Code/

06_AI/

07_Research/

08_Decisions/

09_Bugs/

10_Snippets/

11_Prompts/

12_Library/

13_Journal/

99_Archive/
```

---

# Livelli della memoria

## Livello 1

Conoscenza Permanente

Contiene:

* Identità
* Regole
* Filosofia
* Standard
* Convenzioni
* Workflow

Modifiche molto rare.

---

## Livello 2

Conoscenza dei Progetti

Ogni progetto contiene:

Vision

Roadmap

Architettura

API

Componenti

TODO

Problemi aperti

Diagrammi

Documentazione

---

## Livello 3

Memoria Dinamica

Aggiornata continuamente.

Contiene:

Nuove scoperte

Bug

Esperimenti

Errori

Sessioni

Decisioni

Lezioni apprese

---

# Gestione dei Progetti

Ogni progetto possiede una cartella dedicata.

Esempio:

```
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

    Python Tools/

    Home Assistant/

    Docker/
```

---

# Decision Log

Ogni decisione importante deve essere registrata.

Formato consigliato:

```
Decision ID

Data

Problema

Soluzione

Motivazione

Alternative considerate

Conseguenze

Stato
```

Questo permette di capire il motivo delle scelte anche dopo anni.

---

# Bug Database

Ogni bug diventa conoscenza.

Formato:

```
Titolo

Descrizione

Cause

Come riprodurlo

Soluzione

File coinvolti

Data

Tag
```

---

# Lesson Learned

Ogni errore importante diventa una lezione.

Esempio:

```
Mai serializzare Gameplay Tags direttamente.

Usare Data Assets.

Motivazione...

```

---

# Knowledge condivisa

La conoscenza non appartiene ai progetti.

Esempio:

Una soluzione trovata nel progetto A potrebbe essere utile nel progetto B.

Il Brain deve permettere questa condivisione.

---

# AI Instructions

AGENTS.md rappresenta il punto di ingresso.

L'agente deve:

1. Comprendere il contesto.
2. Identificare il progetto.
3. Recuperare la documentazione pertinente.
4. Leggere gli standard.
5. Analizzare le decisioni precedenti.
6. Verificare bug già risolti.
7. Produrre la risposta.

---

# Ricerca intelligente

Prima di rispondere l'AI dovrebbe cercare:

* documenti simili
* decisioni correlate
* bug correlati
* codice correlato
* note correlate

In futuro questa ricerca potrà essere realizzata con una pipeline RAG.

---

# Evoluzione prevista

## Fase 1

Obsidian

Markdown

AGENTS.md

Manuale

---

## Fase 2

Indicizzazione automatica

Tag

Link automatici

Template

Dashboard

---

## Fase 3

RAG

Database vettoriale

Embedding

Ricerca semantica

Recupero automatico del contesto

---

## Fase 4

Memoria attiva

L'AI propone automaticamente:

* aggiornamenti documentazione
* nuove decisioni
* bug ricorrenti
* documentazione mancante

---

## Fase 5

Brain autonomo

Sistema di orchestrazione che:

* seleziona il modello migliore
* recupera il contesto
* aggiorna la memoria
* genera documentazione
* mantiene sincronizzato il Brain

---

# Possibili implementazioni

## Backend

* Python
* FastAPI
* SQLite (fase iniziale)
* PostgreSQL (fase avanzata)

---

## Indicizzazione

* ChromaDB
* Qdrant
* FAISS
* LanceDB

---

## Embedding

* OpenAI Embeddings
* Nomic Embed
* BGE
* Jina Embeddings

---

## AI

Cloud:

* GPT
* Claude
* Gemini

Locale:

* Ollama
* LM Studio
* Open WebUI

---

## Automazioni

* Watcher delle cartelle Markdown
* Aggiornamento automatico degli indici
* Generazione di backlink
* Creazione automatica di template
* Versionamento tramite Git

---

# Obiettivo finale

Realizzare un cervello digitale indipendente da qualsiasi modello AI.

L'intelligenza può cambiare nel tempo.

La conoscenza rimane.

Il Brain diventa il patrimonio tecnico permanente dello sviluppatore, consultabile e aggiornabile da qualsiasi modello presente o futuro.
