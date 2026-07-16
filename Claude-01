# 02 — Second Brain Casalingo (Versione Domestica Semplice)

> Guida passo passo per installare una versione **semplificata** del progetto BrainOS descritto nella repository, pensata per un utilizzo puramente domestico: un solo utente, nessun accesso da Internet, nessun dominio, nessuna alta disponibilità, nessun clustering.

---

## Scenario di riferimento

| Requisito | Valore |
|---|---|
| Utenti | Uno solo (tu) |
| Accesso da Internet | ❌ Nessuno |
| Dominio pubblico | ❌ Nessuno |
| Cloud | ❌ Nessuno |
| Alta disponibilità / clustering | ❌ Nessuno |
| Accesso dal PC principale | ✅ Sì |
| Accesso dagli altri PC di casa (LAN) | ✅ Sì |
| Accesso dal telefono via WiFi locale | ✅ Sì |
| Accesso da fuori casa | ❌ Non richiesto in questa versione |

Questa versione **riprende i concetti della repository** (Obsidian + Vault + Docker + AI locale/cloud) ma **rimuove tutta la complessità enterprise**: niente VPN per l'esterno, niente reverse proxy con HTTPS pubblico, niente autenticazione avanzata, niente Gitea/Nextcloud/Immich/Grafana/Prometheus a meno che tu non li voglia in un secondo momento.

---

## Indice

1. Hardware minimo
2. Software necessario
3. Installazione completa (passo passo)
4. Configurazione della rete locale
5. Struttura delle cartelle
6. Docker Compose per i servizi
7. AI locali e modelli consigliati
8. Configurazione semplice del Brain
9. Backup
10. Aggiornamenti
11. Errori comuni
12. Checklist finale

---

## 1. Hardware minimo

Per questo scenario domestico (un solo utente, nessun bisogno di alta disponibilità) l'hardware richiesto è molto più contenuto di quanto descritto per lo scenario "enterprise" della repository.

| Componente | Minimo | Consigliato |
|---|---|---|
| CPU | 4 core | 4-6 core |
| RAM | 8 GB | 16 GB |
| Storage | 128 GB SSD | 256-512 GB SSD (o SSD esterno da 1 TB) |
| GPU | Non necessaria | NVIDIA con 8+ GB VRAM se vuoi AI locale più veloce |
| Rete | Wi-Fi/Ethernet di casa | Ethernet (più stabile per un "server sempre acceso") |

> 💡 Nota: se hai già un PC principale potente che usi anche per altro, puoi installare tutto direttamente lì (come richiesto in questo scenario), senza bisogno di un secondo PC dedicato.

---

## 2. Software necessario

| Software | A cosa serve |
|---|---|
| Sistema operativo (Windows, macOS o Linux) | Il PC principale di casa dove installerai tutto |
| Docker Desktop (Windows/macOS) o Docker Engine (Linux) | Per far girare i servizi in container |
| Obsidian | Editor per il tuo vault Markdown |
| Ollama | Per far girare modelli AI in locale |
| Git (opzionale ma consigliato) | Per il versionamento del Brain |

---

## 3. Installazione completa (passo passo)

### Passo 1 — Verifica i requisiti del PC

1.1. Controlla che il PC abbia almeno 8 GB di RAM libera e almeno 20 GB di spazio disco libero.

1.2. Se vuoi usare l'AI in locale con modelli più grandi (7B+), verifica di avere almeno 16 GB di RAM totali.

### Passo 2 — Installa Docker

2.1. Se usi **Windows**: scarica e installa **Docker Desktop** da `docker.com`, assicurandoti che il backend WSL2 sia abilitato.

2.2. Se usi **macOS**: scarica e installa **Docker Desktop** per Mac.

2.3. Se usi **Linux (Ubuntu/Debian)**, esegui da terminale:

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

2.4. Verifica l'installazione:

```bash
docker --version
docker compose version
```

Dovresti vedere un numero di versione per entrambi i comandi, senza errori.

2.5. (Solo Linux) Aggiungi il tuo utente al gruppo `docker` per non dover usare `sudo` ogni volta:

```bash
sudo usermod -aG docker $USER
```

Poi disconnettiti e riconnettiti (o riavvia il PC) perché la modifica abbia effetto.

### Passo 3 — Installa Obsidian

3.1. Scarica Obsidian da `obsidian.md` per il tuo sistema operativo.

3.2. Installalo normalmente (Windows: eseguibile `.exe`; macOS: trascina in Applicazioni; Linux: `.AppImage` o pacchetto `.deb`).

3.3. Al primo avvio, **non aprire ancora un vault**: lo creeremo nel Passo 5.

### Passo 4 — Installa Ollama

4.1. Scarica Ollama da `ollama.com` per il tuo sistema operativo.

4.2. Su Linux puoi installarlo anche da terminale:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

4.3. Verifica che sia attivo:

```bash
ollama --version
```

4.4. Ollama parte automaticamente come servizio in background sulla porta `11434`.

### Passo 5 — Crea la struttura delle cartelle del Brain

5.1. Scegli dove vivrà il tuo Second Brain sul disco. Esempio su Windows: `D:\SecondBrain`. Esempio su Linux/macOS: `~/SecondBrain`.

5.2. Crea la struttura di base (da terminale, esempio Linux/macOS):

```bash
mkdir -p ~/SecondBrain/vault
mkdir -p ~/SecondBrain/docker
mkdir -p ~/SecondBrain/backups
mkdir -p ~/SecondBrain/database
```

Su Windows puoi crearle semplicemente da Esplora File, oppure con PowerShell:

```powershell
New-Item -ItemType Directory -Force -Path "D:\SecondBrain\vault"
New-Item -ItemType Directory -Force -Path "D:\SecondBrain\docker"
New-Item -ItemType Directory -Force -Path "D:\SecondBrain\backups"
New-Item -ItemType Directory -Force -Path "D:\SecondBrain\database"
```

5.3. Apri Obsidian, scegli **"Apri cartella come vault"** e seleziona la cartella `vault` appena creata.

5.4. Dentro il vault, crea la struttura ispirata al README della repository (semplificata per uso singolo):

```text
vault/
├── AGENTS.md
├── 00_System/
├── 02_Projects/
├── 03_Knowledge/
├── 08_Decisions/
├── 09_Bugs/
├── 11_Prompts/
└── 13_Journal/
```

Puoi crearle direttamente dentro Obsidian con clic destro → "Nuova cartella", oppure da terminale:

```bash
cd ~/SecondBrain/vault
mkdir -p 00_System 02_Projects 03_Knowledge 08_Decisions 09_Bugs 11_Prompts 13_Journal
touch AGENTS.md
```

5.5. Apri `AGENTS.md` e scrivi un contenuto minimo iniziale, ad esempio:

```markdown
# AGENTS.md

Questo file è il punto di ingresso per qualsiasi AI che consulta questo Brain.

Prima di rispondere:
1. Consulta la cartella 03_Knowledge per informazioni generali.
2. Consulta 02_Projects per il progetto specifico.
3. Consulta 08_Decisions per decisioni già prese.
4. Consulta 09_Bugs per problemi già risolti.
5. Solo dopo, rispondi.
```

### Passo 6 — Scarica un modello AI locale con Ollama

6.1. Scegli un modello adatto alla RAM disponibile (vedi tabella nella sezione 7).

6.2. Scaricalo con:

```bash
ollama pull llama3.1:8b
```

6.3. Testa che funzioni:

```bash
ollama run llama3.1:8b "Ciao, presentati in una frase"
```

Se ricevi una risposta testuale, il modello funziona correttamente.

### Passo 7 — Avvia un'interfaccia web per parlare col Brain (Open WebUI)

Per non dover usare solo il terminale, installiamo **Open WebUI**, un'interfaccia web che si collega a Ollama ed è pensata per girare in Docker.

7.1. Crea un file `docker-compose.yml` dentro `~/SecondBrain/docker/`:

```yaml
version: "3.8"

services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    ports:
      - "3000:8080"
    volumes:
      - ./open-webui-data:/app/backend/data
    environment:
      - OLLAMA_BASE_URL=http://host.docker.internal:11434
    extra_hosts:
      - "host.docker.internal:host-gateway"
    restart: unless-stopped
```

**Spiegazione riga per riga:**

| Riga | Significato |
|---|---|
| `image: ghcr.io/open-webui/open-webui:main` | Usa l'immagine ufficiale di Open WebUI |
| `container_name: open-webui` | Nome del container, utile per i comandi Docker |
| `ports: "3000:8080"` | Espone la porta interna 8080 sulla porta 3000 del tuo PC |
| `volumes: ./open-webui-data:/app/backend/data` | Salva i dati (chat, impostazioni) in una cartella locale persistente |
| `environment: OLLAMA_BASE_URL` | Dice a Open WebUI dove trovare Ollama (che gira fuori da Docker, sul PC host) |
| `extra_hosts` | Permette al container di raggiungere il PC host tramite `host.docker.internal` |
| `restart: unless-stopped` | Riavvia automaticamente il servizio se il PC si riavvia (a meno che tu lo fermi manualmente) |

7.2. Avvia il servizio:

```bash
cd ~/SecondBrain/docker
docker compose up -d
```

7.3. Apri il browser e vai su `http://localhost:3000`. Crea il tuo account amministratore locale (email e password a tua scelta — restano solo sul tuo PC).

7.4. Nelle impostazioni di Open WebUI, verifica che il modello scaricato con Ollama (es. `llama3.1:8b`) sia visibile e selezionabile.

### Passo 8 — Verifica l'accesso dagli altri dispositivi della rete locale

8.1. Trova l'indirizzo IP locale del PC principale.

Su Windows (PowerShell):
```powershell
ipconfig
```
Cerca la voce "Indirizzo IPv4" della tua scheda di rete attiva (es. `192.168.1.50`).

Su Linux/macOS:
```bash
ip addr show   # Linux
ifconfig       # macOS
```

8.2. Da un altro PC o dal telefono, connesso alla **stessa rete Wi-Fi**, apri il browser e vai su:

```
http://192.168.1.50:3000
```

(sostituendo con il tuo IP reale)

8.3. Se la pagina di Open WebUI si apre, l'accesso dalla rete locale funziona correttamente.

> ⚠️ Nota importante: questo indirizzo funziona **solo mentre sei connesso alla stessa rete Wi-Fi di casa**. Non è raggiungibile da fuori casa, esattamente come richiesto in questo scenario.

---

## 4. Configurazione della rete locale

### Passo 9 — Rendi stabile l'indirizzo IP del PC principale

Se l'IP del PC cambia ogni volta che si riavvia il router, dovrai aggiornare ogni volta l'indirizzo usato dagli altri dispositivi. Per evitarlo:

9.1. Accedi alla pagina di amministrazione del tuo router (solitamente `192.168.1.1` o `192.168.0.1`, controlla l'etichetta sotto al router).

9.2. Cerca la sezione **DHCP Reservation** (a volte chiamata "Indirizzo IP statico" o "Riserva DHCP").

9.3. Associa l'indirizzo MAC del PC principale a un IP fisso (es. `192.168.1.50`).

9.4. Salva e riavvia il router se richiesto.

> 💡 In alternativa, puoi impostare un IP statico direttamente sul PC nelle impostazioni di rete, ma la riserva DHCP sul router è generalmente più semplice da gestire nel tempo.

---

## 5. Struttura delle cartelle

Struttura finale consigliata per questo scenario domestico:

```text
SecondBrain/
├── vault/                  → Vault Obsidian (Markdown)
│   ├── AGENTS.md
│   ├── 00_System/
│   ├── 02_Projects/
│   ├── 03_Knowledge/
│   ├── 08_Decisions/
│   ├── 09_Bugs/
│   ├── 11_Prompts/
│   └── 13_Journal/
├── docker/
│   ├── docker-compose.yml
│   └── open-webui-data/
├── database/                → (se in futuro aggiungerai un DB vettoriale)
└── backups/                 → Copie di sicurezza periodiche
```

---

## 6. Docker Compose per i servizi

Il file `docker-compose.yml` creato al Passo 7 è sufficiente per iniziare. Se in un secondo momento vuoi aggiungere anche un servizio di ricerca semantica (ChromaDB) restando comunque in ambito "casalingo", puoi estenderlo così:

```yaml
version: "3.8"

services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    ports:
      - "3000:8080"
    volumes:
      - ./open-webui-data:/app/backend/data
    environment:
      - OLLAMA_BASE_URL=http://host.docker.internal:11434
    extra_hosts:
      - "host.docker.internal:host-gateway"
    restart: unless-stopped

  chromadb:
    image: chromadb/chroma:latest
    container_name: chromadb
    ports:
      - "8000:8000"
    volumes:
      - ../database/chroma-data:/chroma/chroma
    restart: unless-stopped
```

**Spiegazione del blocco aggiunto (`chromadb`):**

| Riga | Significato |
|---|---|
| `image: chromadb/chroma:latest` | Immagine ufficiale di ChromaDB |
| `ports: "8000:8000"` | Espone il database vettoriale sulla porta 8000 |
| `volumes` | Salva i dati vettoriali in modo persistente fuori dal container |

> Questo blocco è **opzionale** in questa versione domestica: aggiungilo solo quando vorrai iniziare a sperimentare la ricerca semantica sul tuo vault.

---

## 7. AI locali e modelli consigliati

Per un utilizzo domestico su un solo PC, la scelta del modello dipende dalla RAM disponibile.

| RAM disponibile | Modello consigliato | Comando di download |
|---|---|---|
| 8 GB | `llama3.2:3b` o `phi3:mini` | `ollama pull llama3.2:3b` |
| 16 GB | `llama3.1:8b` o `mistral:7b` | `ollama pull llama3.1:8b` |
| 32 GB (senza GPU dedicata) | `qwen2.5:14b` | `ollama pull qwen2.5:14b` |
| 32 GB + GPU 12GB+ VRAM | `qwen2.5:32b` o modelli 30B+ | `ollama pull qwen2.5:32b` |

> 💡 Suggerimento: parti sempre con un modello piccolo (3B-8B) per verificare che tutto il flusso funzioni, poi eventualmente passa a modelli più grandi se hai hardware sufficiente e vuoi risposte più accurate.

---

## 8. Configurazione semplice del Brain

### Passo 10 — Collega Obsidian al flusso di lavoro quotidiano

10.1. Ogni volta che impari qualcosa di rilevante, crei una decisione tecnica o risolvi un bug, scrivi una nota nella cartella appropriata del vault (`08_Decisions/`, `09_Bugs/`, `03_Knowledge/`).

10.2. Usa un formato coerente per le note. Esempio per una decisione:

```markdown
# Decision 001 - Scelta del database vettoriale

Data: 2026-07-16

## Problema
Serve un database per la ricerca semantica.

## Soluzione
Ho scelto ChromaDB per iniziare.

## Motivazione
Semplice da avviare, sufficiente per un vault di piccole dimensioni.

## Alternative considerate
Qdrant (rimandato a una fase successiva).

## Stato
Attiva
```

### Passo 11 — Usa Open WebUI insieme al vault

11.1. Apri Open WebUI dal browser (`http://localhost:3000` oppure dall'IP locale da altri dispositivi).

11.2. Per ora, in questa versione semplice, il collegamento tra vault e AI è **manuale**: puoi copiare il contenuto di una nota rilevante e incollarlo nella chat quando fai una domanda, oppure caricare direttamente i file Markdown tramite la funzione di upload documenti di Open WebUI, se disponibile nella versione che hai installato.

11.3. Una integrazione automatica (il vault che alimenta l'AI senza copia-incolla manuale) richiede il layer di ricerca semantica descritto nella sezione 6 — è un passo più avanzato, non necessario per iniziare.

---

## 9. Backup

### Passo 12 — Configura un backup locale semplice

Anche in una versione domestica minimale, il backup **non va saltato**: è la parte del progetto originale che vale sempre, indipendentemente dalla scala.

12.1. Crea uno script di backup. Esempio su Linux/macOS (`~/SecondBrain/backup.sh`):

```bash
#!/bin/bash
DATA=$(date +%Y-%m-%d_%H-%M)
DESTINAZIONE=~/SecondBrain/backups/backup_$DATA.tar.gz

tar -czf $DESTINAZIONE ~/SecondBrain/vault ~/SecondBrain/database

echo "Backup completato: $DESTINAZIONE"
```

Rendilo eseguibile:

```bash
chmod +x ~/SecondBrain/backup.sh
```

Eseguilo manualmente quando vuoi:

```bash
~/SecondBrain/backup.sh
```

12.2. Su Windows, puoi usare un semplice script PowerShell (`backup.ps1`):

```powershell
$data = Get-Date -Format "yyyy-MM-dd_HH-mm"
$destinazione = "D:\SecondBrain\backups\backup_$data.zip"
Compress-Archive -Path "D:\SecondBrain\vault","D:\SecondBrain\database" -DestinationPath $destinazione

Write-Host "Backup completato: $destinazione"
```

12.3. **Copia periodicamente** la cartella `backups/` anche su una chiavetta USB o un hard disk esterno, per avere una seconda copia fisica separata dal PC.

12.4. (Opzionale ma consigliato) Inizializza anche un repository Git locale nel vault, per avere una cronologia delle modifiche:

```bash
cd ~/SecondBrain/vault
git init
git add .
git commit -m "Primo commit del vault"
```

Ripeti `git add . && git commit -m "..."` periodicamente (es. una volta a settimana) per avere checkpoint recuperabili.

---

## 10. Aggiornamenti

### Passo 13 — Aggiorna Ollama e i modelli

```bash
# Aggiorna Ollama (rieseguendo lo script di installazione)
curl -fsSL https://ollama.com/install.sh | sh

# Aggiorna un modello già scaricato
ollama pull llama3.1:8b
```

### Passo 14 — Aggiorna i container Docker

```bash
cd ~/SecondBrain/docker
docker compose pull
docker compose up -d
```

Questo scarica le versioni più recenti delle immagini (Open WebUI, ChromaDB) e riavvia i container mantenendo i dati salvati nei volumi.

### Passo 15 — Aggiorna Obsidian

Obsidian si aggiorna generalmente da solo, oppure te lo notifica all'apertura. In alternativa scarica manualmente l'ultima versione da `obsidian.md`.

---

## 11. Errori comuni

| Problema | Causa probabile | Soluzione |
|---|---|---|
| Open WebUI non trova Ollama | `OLLAMA_BASE_URL` errato o Ollama non in esecuzione | Verifica che Ollama sia attivo (`ollama list`) e che l'URL nel `docker-compose.yml` sia corretto |
| Non riesco ad accedere da telefono/altro PC | Firewall del PC blocca la porta, oppure dispositivi su reti Wi-Fi diverse (es. banda 2.4GHz/5GHz separate, o rete ospiti) | Verifica che tutti i dispositivi siano sulla stessa rete; controlla le regole del firewall per la porta 3000 |
| L'IP del PC cambia e i dispositivi non si connettono più | Nessuna riserva DHCP impostata | Configura la riserva DHCP sul router (vedi Passo 9) |
| Il modello AI risponde molto lentamente | Modello troppo grande per l'hardware disponibile | Passa a un modello più piccolo (vedi tabella sezione 7) |
| Docker non parte / errore "Cannot connect to the Docker daemon" | Docker Desktop non avviato (Windows/macOS) o servizio Docker non attivo (Linux) | Avvia Docker Desktop; su Linux esegui `sudo systemctl start docker` |
| I dati spariscono dopo `docker compose down` | Volumi non configurati correttamente nel `docker-compose.yml` | Verifica che la sezione `volumes` punti a cartelle locali persistenti, non a percorsi temporanei |
| Obsidian non mostra le nuove cartelle create da terminale | Cache dell'interfaccia non aggiornata | Chiudi e riapri Obsidian, oppure usa il comando "Reload app" dalla palette comandi (Ctrl/Cmd+P) |

---

## 12. Checklist finale

Usa questa checklist per verificare di aver completato correttamente l'installazione domestica.

- [ ] Docker installato e funzionante (`docker --version` restituisce una versione)
- [ ] Obsidian installato e vault creato in `SecondBrain/vault`
- [ ] Struttura di cartelle del vault creata (`AGENTS.md`, `02_Projects`, `03_Knowledge`, ecc.)
- [ ] Ollama installato e almeno un modello scaricato
- [ ] Open WebUI avviato tramite `docker compose up -d` e raggiungibile su `http://localhost:3000`
- [ ] Accesso verificato da un secondo dispositivo sulla rete locale (PC o telefono via Wi-Fi)
- [ ] IP del PC reso stabile tramite riserva DHCP sul router
- [ ] Script di backup creato e testato almeno una volta
- [ ] Copia di backup salvata anche su un supporto esterno (USB/HDD)
- [ ] (Opzionale) Repository Git inizializzato nel vault per la cronologia delle modifiche

---

> 🎯 **Risultato finale di questa guida**: un Second Brain funzionante, interamente locale, senza alcuna esposizione su Internet, accessibile dal PC principale e da qualsiasi altro dispositivo della rete Wi-Fi di casa (PC o telefono), con AI locale tramite Ollama e Open WebUI, e un sistema di backup semplice ma funzionante.
