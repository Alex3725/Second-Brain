# 03 — Guida Completa all'Implementazione

> Guida operativa end-to-end: dal PC vuoto al progetto BrainOS/Second Brain funzionante. Ogni fase segue lo schema: **Obiettivo → Cosa fare → Comandi → Configurazione → Verifica → Problemi comuni → Come risolverli → Cosa fare dopo**.
>
> Questa guida usa come base **Ubuntu Server 24.04 LTS**, in linea con quanto indicato nella repository originale, e un'architettura Docker Compose su singolo host. Dove opportuno, sono segnalate alternative e miglioramenti rispetto a quanto descritto nei file originali della repository.

---

## Indice delle fasi

| Fase | Titolo |
|---|---|
| 0 | Pianificazione e scelte preliminari |
| 1 | Installazione del sistema operativo |
| 2 | Accesso remoto (SSH) |
| 3 | Configurazione dello storage |
| 4 | Installazione di Docker e Docker Compose |
| 5 | Rete locale, DNS e nome host |
| 6 | Struttura delle cartelle del Brain |
| 7 | Database applicativo |
| 8 | Database vettoriale |
| 9 | AI locale con Ollama |
| 10 | Interfaccia AI (Open WebUI) |
| 11 | Vault Obsidian e sincronizzazione |
| 12 | Accesso remoto sicuro (VPN) |
| 13 | Reverse proxy (opzionale) |
| 14 | Test end-to-end del sistema |
| 15 | Backup |
| 16 | Manutenzione e aggiornamenti |
| 17 | Automazioni future (Brain Keeper) |

---

## Fase 0 — Pianificazione e scelte preliminari

### Obiettivo
Definire con chiarezza cosa si sta costruendo prima di toccare qualsiasi hardware, evitando di dover rifare passaggi già completati.

### Cosa fare
1. Decidi quale macchina userai come server (PC vecchio, mini PC, o PC principale — vedi File 01, sezione "Decisioni progettuali").
2. Decidi se avrai accesso solo da LAN (come nel File 02) o anche da fuori casa tramite VPN (come descritto in questa guida completa).
3. Prepara una chiavetta USB da almeno 8 GB per l'immagine di installazione del sistema operativo.
4. Scarica l'immagine ISO di **Ubuntu Server 24.04 LTS** dal sito ufficiale `ubuntu.com/download/server`.

### Comandi
Nessun comando in questa fase: è puramente decisionale.

### Configurazione
Annota, prima di iniziare:
- Nome host che vuoi dare al server (es. `brainos-server`)
- Nome utente amministratore che creerai durante l'installazione
- Se userai un disco esterno per i dati (consigliato) o solo il disco interno

### Verifica
- [ ] Hai l'immagine ISO scaricata
- [ ] Hai la chiavetta USB pronta
- [ ] Hai deciso hardware e struttura di rete

### Problemi comuni
| Problema | Causa |
|---|---|
| Non sai quale versione di Ubuntu scaricare | Usa sempre la versione LTS (Long Term Support) più recente, non le versioni intermedie |

### Come risolverli
Scarica esclusivamente da `ubuntu.com`, verificando che sia la versione "Server" e non "Desktop".

### Cosa fare dopo
Procedi alla Fase 1.

---

## Fase 1 — Installazione del sistema operativo

### Obiettivo
Installare Ubuntu Server sul PC che farà da server, senza interfaccia grafica.

### Cosa fare
1. Scrivi l'immagine ISO sulla chiavetta USB usando **Balena Etcher** o **Rufus** (Windows) / `dd` (Linux/macOS).
2. Inserisci la chiavetta nel PC server e avvia da USB (di solito premendo F2, F10, F12 o Canc all'accensione, a seconda della scheda madre).
3. Segui la procedura guidata di Ubuntu Server:
   - Lingua e tastiera
   - Configurazione di rete (se disponibile DHCP, lascialo automatico per ora)
   - Configurazione del disco: scegli "Use an entire disk" sul disco dove vuoi installare il sistema
   - Crea l'utente amministratore e il nome host (es. `brainos-server`)
   - **Nella schermata "Featured Server Snaps", abilita l'installazione di OpenSSH Server** (fondamentale per il prossimo passo)
4. Completa l'installazione e riavvia, rimuovendo la chiavetta USB quando richiesto.

### Comandi
```bash
# Esempio di scrittura ISO su USB da terminale Linux/macOS (sostituisci /dev/sdX con il device corretto)
sudo dd if=ubuntu-24.04-live-server-amd64.iso of=/dev/sdX bs=4M status=progress oflag=sync
```

> ⚠️ Presta **estrema attenzione** al device di destinazione (`/dev/sdX`): un errore può cancellare dati da un altro disco.

### Configurazione
Durante l'installazione guidata, imposta:
- Nome host: `brainos-server`
- Nome utente: a tua scelta (es. `brainadmin`)
- OpenSSH Server: **abilitato**

### Verifica
Al riavvio, dovresti vedere un prompt di login testuale con il nome host che hai scelto (es. `brainos-server login:`).

### Problemi comuni
| Problema | Causa |
|---|---|
| Il PC non avvia dalla chiavetta USB | Ordine di boot nel BIOS/UEFI non corretto, oppure Secure Boot che blocca l'avvio |
| Schermo nero dopo l'installazione | Driver grafici mancanti (non rilevante per un server senza GUI, verifica solo che il sistema risponda via rete) |

### Come risolverli
- Entra nel BIOS/UEFI (di solito F2/Del all'accensione) e imposta l'avvio da USB come priorità, disabilitando temporaneamente il Secure Boot se necessario.
- Se lo schermo resta nero ma il sistema è acceso, prova a raggiungerlo comunque via SSH (Fase 2): spesso il problema è solo visivo.

### Cosa fare dopo
Procedi alla Fase 2 per l'accesso remoto.

---

## Fase 2 — Accesso remoto (SSH)

### Obiettivo
Poter amministrare il server da un altro PC, senza dover collegare monitor e tastiera ogni volta — esattamente come indicato nella repository originale.

### Cosa fare
1. Trova l'indirizzo IP del server (visibile nella schermata di login testuale di Ubuntu, oppure con un comando dedicato).
2. Dal tuo PC principale, connettiti via SSH.

### Comandi
Sul server, per vedere l'IP:
```bash
ip addr show
```

Dal PC principale (Windows con OpenSSH integrato, macOS, o Linux):
```bash
ssh brainadmin@192.168.1.50
```
(sostituendo `brainadmin` con il tuo utente e l'IP con quello reale del server)

### Configurazione
Per comodità, puoi creare un alias SSH sul tuo PC principale. Su Linux/macOS, modifica `~/.ssh/config`:

```text
Host brainos
    HostName 192.168.1.50
    User brainadmin
```

Dopo questa configurazione potrai connetterti semplicemente con:
```bash
ssh brainos
```

### Verifica
Dopo la connessione SSH, dovresti vedere il prompt del server (es. `brainadmin@brainos-server:~$`).

### Problemi comuni
| Problema | Causa |
|---|---|
| `Connection refused` | Il servizio SSH non è attivo sul server |
| `Connection timed out` | Firewall che blocca la porta 22, oppure IP errato |
| `Permission denied` | Credenziali errate |

### Come risolverli
```bash
# Sul server, verifica lo stato del servizio SSH
sudo systemctl status ssh

# Se non è attivo, avvialo e abilitalo all'avvio
sudo systemctl enable --now ssh

# Verifica che il firewall (se attivo) permetta la porta 22
sudo ufw allow OpenSSH
```

### Cosa fare dopo
Procedi alla Fase 3 per configurare lo storage.

---

## Fase 3 — Configurazione dello storage

### Obiettivo
Separare **sistema** e **dati**, come indicato in `opzioneAumentoMemoriaUbuntu.md`, così da poter reinstallare il sistema operativo in futuro senza perdere il Brain.

### Cosa fare
1. Collega un SSD esterno USB (o un secondo disco interno) al server.
2. Identifica il disco appena collegato.
3. Formattalo (se nuovo) e montalo in modo persistente usando l'UUID, non il nome del device.

### Comandi
```bash
# Elenca tutti i dischi collegati
lsblk

# Supponendo che il nuovo disco sia /dev/sdb, crea una partizione (se necessario)
sudo fdisk /dev/sdb
# All'interno di fdisk: n (nuova partizione) -> invio per i valori di default -> w (scrivi e esci)

# Formatta la partizione in ext4
sudo mkfs.ext4 /dev/sdb1

# Crea il punto di mount
sudo mkdir -p /data

# Trova l'UUID della partizione
sudo blkid /dev/sdb1
```

### Configurazione
Apri `/etc/fstab` e aggiungi una riga per il montaggio automatico all'avvio, usando l'UUID trovato al passo precedente:

```bash
sudo nano /etc/fstab
```

Aggiungi (sostituendo `UUID-DEL-TUO-DISCO` con il valore reale):

```text
UUID=UUID-DEL-TUO-DISCO   /data   ext4   defaults   0   2
```

**Spiegazione della riga:**

| Campo | Significato |
|---|---|
| `UUID=...` | Identificatore univoco della partizione, stabile anche se cambia la porta USB |
| `/data` | Punto di mount, cioè dove sarà accessibile il disco |
| `ext4` | Filesystem usato |
| `defaults` | Opzioni di mount standard |
| `0` | Non usato dal dump di backup tradizionale |
| `2` | Ordine di controllo del filesystem al boot (2 = dopo la partizione di root) |

Poi monta subito senza riavviare:
```bash
sudo mount -a
```

Crea la struttura dati consigliata:
```bash
sudo mkdir -p /data/brain/vault /data/brain/vectors /data/brain/database /data/brain/cache
sudo mkdir -p /data/git /data/backups /data/docker
sudo chown -R $USER:$USER /data
```

### Verifica
```bash
df -h /data
```
Dovresti vedere il disco montato con lo spazio disponibile corretto.

```bash
ls -la /data
```
Dovresti vedere le cartelle `brain`, `git`, `backups`, `docker`.

### Problemi comuni
| Problema | Causa |
|---|---|
| Il sistema non si avvia più dopo aver modificato `/etc/fstab` | Errore di sintassi nella riga aggiunta, o UUID sbagliato |
| `mount -a` restituisce errore | UUID errato o filesystem non corrispondente |

### Come risolverli
Se il sistema non si avvia per un errore in `fstab`, all'avvio Ubuntu offre solitamente una shell di emergenza (`(or press Control-D to continue)`): premi Ctrl+D o accedi in modalità di recovery, poi correggi `/etc/fstab` rimuovendo o correggendo la riga errata.

> 💡 Consiglio pratico: prima di riavviare dopo aver modificato `fstab`, testa sempre con `sudo mount -a` — se non dà errori, il riavvio sarà sicuro.

### Cosa fare dopo
Procedi alla Fase 4 per installare Docker.

---

## Fase 4 — Installazione di Docker e Docker Compose

### Obiettivo
Installare il motore che ospiterà tutti i servizi in container isolati, come indicato chiaramente nella repository (`spiegazionePcUbuntu.md`).

### Cosa fare
Installa Docker Engine e il plugin Docker Compose seguendo la procedura ufficiale per Ubuntu.

### Comandi
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
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker $USER
```

Dopo l'ultimo comando, disconnettiti e riconnettiti via SSH perché il gruppo abbia effetto:
```bash
exit
ssh brainos
```

### Configurazione
Verifica che Docker si avvii automaticamente al boot:
```bash
sudo systemctl enable docker
```

### Verifica
```bash
docker --version
docker compose version
docker run hello-world
```
L'ultimo comando dovrebbe scaricare ed eseguire un piccolo container di test, mostrando un messaggio di conferma.

### Problemi comuni
| Problema | Causa |
|---|---|
| `permission denied` eseguendo `docker` | L'utente non è (ancora) nel gruppo `docker`, o non hai fatto logout/login dopo l'aggiunta |
| `docker: command not found` | Installazione non completata correttamente |

### Come risolverli
```bash
# Verifica di essere nel gruppo docker
groups

# Se manca, riaggiungilo e rifai logout/login
sudo usermod -aG docker $USER
```

### Cosa fare dopo
Procedi alla Fase 5 per la configurazione di rete.

---

## Fase 5 — Rete locale, DNS e nome host

### Obiettivo
Rendere il server sempre raggiungibile con lo stesso indirizzo, senza dover configurare un IP statico manuale su ogni dispositivo, come discusso in `pcServerToDoListInstallation.md`.

### Cosa fare
1. Configura una **riserva DHCP** sul router per il server.
2. (Opzionale, ma consigliato se vuoi nomi tipo `brain.local`) Installa un DNS locale come **Technitium DNS**.

### Comandi
Trova l'indirizzo MAC del server (necessario per la riserva DHCP):
```bash
ip link show
```
Cerca l'interfaccia di rete attiva (es. `eth0` o `enp3s0`) e annota l'indirizzo MAC mostrato.

### Configurazione
1. Accedi al pannello di amministrazione del router (es. `http://192.168.1.1`).
2. Cerca la sezione "DHCP Reservation" o "IP statico via DHCP".
3. Associa il MAC address del server a un IP fisso, ad esempio `192.168.1.50`.
4. Salva e, se richiesto, riavvia il router.

(Opzionale) Installazione di Technitium DNS via Docker:

```bash
mkdir -p /data/docker/technitium
cd /data/docker/technitium
```

Crea un file `docker-compose.yml`:
```yaml
version: "3.8"
services:
  technitium-dns:
    image: technitium/dns-server:latest
    container_name: technitium-dns
    ports:
      - "5380:5380"   # Interfaccia web di amministrazione
      - "53:53/udp"   # Porta DNS
      - "53:53/tcp"
    volumes:
      - ./config:/etc/dns
    restart: unless-stopped
```

Avvia:
```bash
docker compose up -d
```

Accedi all'interfaccia su `http://192.168.1.50:5380` e configura una zona locale, ad esempio associando `brain.local` all'IP del server.

### Verifica
```bash
# Dal PC principale, verifica che l'IP del server resti sempre lo stesso dopo un riavvio del server
ping 192.168.1.50
```

Se hai configurato il DNS locale:
```bash
nslookup brain.local 192.168.1.50
```

### Problemi comuni
| Problema | Causa |
|---|---|
| L'IP del server cambia comunque | La riserva DHCP non è stata salvata correttamente, oppure il router non la supporta |
| Il DNS locale non risolve i nomi | I dispositivi client non usano ancora il DNS locale come server DNS primario |

### Come risolverli
- Verifica nel pannello del router che la riserva sia effettivamente elencata e attiva.
- Configura manualmente il DNS locale (`192.168.1.50`) come server DNS primario nelle impostazioni di rete dei dispositivi client, oppure imposta il router stesso per distribuirlo via DHCP a tutta la rete.

### Cosa fare dopo
Procedi alla Fase 6 per la struttura delle cartelle del Brain.

---

## Fase 6 — Struttura delle cartelle del Brain

### Obiettivo
Preparare la struttura dati che ospiterà il vault, il database e i backup, secondo quanto descritto nel README e in `opzioneAumentoMemoriaUbuntu.md`.

### Cosa fare
Crea la struttura completa dentro `/data`, già montata nella Fase 3.

### Comandi
```bash
mkdir -p /data/brain/vault/{00_System,01_Identity,02_Projects,03_Knowledge,04_Architecture,05_Code,06_AI,07_Research,08_Decisions,09_Bugs,10_Snippets,11_Prompts,12_Library,13_Journal,99_Archive}

touch /data/brain/vault/AGENTS.md

mkdir -p /data/brain/vectors
mkdir -p /data/brain/database
mkdir -p /data/brain/cache
mkdir -p /data/git
mkdir -p /data/backups
mkdir -p /data/docker
```

### Configurazione
Popola `AGENTS.md` con le istruzioni base per qualsiasi AI che consulterà il Brain:

```bash
cat > /data/brain/vault/AGENTS.md << 'EOF'
# AGENTS.md

Punto di ingresso per qualsiasi agente AI collegato a questo Brain.

## Priorità delle fonti
1. Brain (questa knowledge base)
2. Documentazione del progetto specifico
3. Codice del progetto
4. Internet (solo se le prime tre fonti non bastano)

## Prima di rispondere, l'agente deve:
1. Comprendere il contesto della richiesta.
2. Identificare il progetto pertinente in 02_Projects/.
3. Recuperare la documentazione pertinente.
4. Leggere gli standard in 00_System/.
5. Analizzare le decisioni precedenti in 08_Decisions/.
6. Verificare bug già risolti in 09_Bugs/.
7. Produrre la risposta.
EOF
```

Inizializza Git nel vault per il versionamento:
```bash
cd /data/brain/vault
git init
git add .
git commit -m "Inizializzazione struttura Brain"
```

### Verifica
```bash
find /data/brain/vault -maxdepth 1 -type d
```
Dovresti vedere tutte le 14 cartelle create.

```bash
cd /data/brain/vault && git log --oneline
```
Dovresti vedere il commit iniziale.

### Problemi comuni
| Problema | Causa |
|---|---|
| `git commit` fallisce chiedendo nome/email | Git non configurato globalmente per questo utente |

### Come risolverli
```bash
git config --global user.name "Il Tuo Nome"
git config --global user.email "tua-email@esempio.com"
```

### Cosa fare dopo
Procedi alla Fase 7 per il database applicativo.

---

## Fase 7 — Database applicativo

### Obiettivo
Predisporre il database che gestirà utenti, configurazioni, log e sessioni di BrainOS.

### Cosa fare
Questa guida usa **PostgreSQL** come esempio (in alternativa a SQLite, adatta la scelta secondo quanto deciso nel File 01, sezione 24.2).

### Comandi
```bash
mkdir -p /data/docker/postgres
cd /data/docker/postgres
```

Crea `docker-compose.yml`:
```yaml
version: "3.8"
services:
  postgres:
    image: postgres:16
    container_name: brainos-postgres
    environment:
      POSTGRES_USER: brainos
      POSTGRES_PASSWORD: CAMBIA_QUESTA_PASSWORD
      POSTGRES_DB: brainos
    volumes:
      - /data/brain/database/postgres:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    restart: unless-stopped
```

**Spiegazione riga per riga:**

| Riga | Significato |
|---|---|
| `image: postgres:16` | Versione 16 di PostgreSQL, immagine ufficiale |
| `POSTGRES_USER` / `POSTGRES_PASSWORD` / `POSTGRES_DB` | Credenziali e nome del database creato al primo avvio |
| `volumes` | I dati del database vengono salvati fuori dal container, in modo persistente |
| `ports: "5432:5432"` | Espone la porta standard di PostgreSQL |

> ⚠️ **Sostituisci sempre** `CAMBIA_QUESTA_PASSWORD` con una password robusta, e non condividerla mai in repository pubbliche.

Avvia il servizio:
```bash
docker compose up -d
```

### Configurazione
Verifica la connessione dall'interno del server:
```bash
docker exec -it brainos-postgres psql -U brainos -d brainos
```

Dentro la shell `psql`, esci con:
```sql
\q
```

### Verifica
```bash
docker ps
```
Dovresti vedere il container `brainos-postgres` con stato `Up`.

### Problemi comuni
| Problema | Causa |
|---|---|
| Il container si riavvia in loop | Password non valida o volume corrotto da un tentativo precedente |
| `port is already allocated` | Un altro servizio sta già usando la porta 5432 |

### Come risolverli
```bash
# Visualizza i log per capire l'errore esatto
docker logs brainos-postgres

# Se il volume è corrotto e non hai dati da salvare, puoi ripartire da zero
docker compose down
sudo rm -rf /data/brain/database/postgres
docker compose up -d
```

### Cosa fare dopo
Procedi alla Fase 8 per il database vettoriale.

---

## Fase 8 — Database vettoriale

### Obiettivo
Predisporre il motore di ricerca semantica per il Brain, come descritto in Architecture.md.

### Cosa fare
Installa **ChromaDB** come punto di partenza (in linea con la scelta consigliata nel File 01).

### Comandi
```bash
mkdir -p /data/docker/chromadb
cd /data/docker/chromadb
```

Crea `docker-compose.yml`:
```yaml
version: "3.8"
services:
  chromadb:
    image: chromadb/chroma:latest
    container_name: brainos-chromadb
    volumes:
      - /data/brain/vectors:/chroma/chroma
    ports:
      - "8001:8000"
    restart: unless-stopped
```

**Spiegazione riga per riga:**

| Riga | Significato |
|---|---|
| `volumes` | Gli embedding vengono salvati in modo persistente su `/data/brain/vectors` |
| `ports: "8001:8000"` | Espone ChromaDB sulla porta 8001 (per evitare conflitti con altri servizi sulla 8000) |

Avvia:
```bash
docker compose up -d
```

### Configurazione
Nessuna configurazione aggiuntiva è necessaria per iniziare: ChromaDB crea automaticamente il proprio storage al primo avvio.

### Verifica
```bash
curl http://localhost:8001/api/v1/heartbeat
```
Dovresti ricevere una risposta JSON con un timestamp, a conferma che il servizio è attivo.

### Problemi comuni
| Problema | Causa |
|---|---|
| `curl: (7) Failed to connect` | Il container non è ancora pronto, oppure la porta è sbagliata |

### Come risolverli
```bash
docker logs brainos-chromadb
docker ps   # verifica che lo stato sia "Up" e non "Restarting"
```

### Cosa fare dopo
Procedi alla Fase 9 per l'AI locale.

---

## Fase 9 — AI locale con Ollama

### Obiettivo
Installare il runtime AI locale, come descritto in README.md e BrainOsSystem.md, per poter eseguire modelli come Qwen, Llama, DeepSeek o Mistral senza dipendere dal cloud.

### Cosa fare
Installa Ollama direttamente sul sistema operativo host (non in Docker, per avere accesso più semplice a GPU se presente).

### Comandi
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Verifica l'installazione:
```bash
ollama --version
```

Scarica un primo modello:
```bash
ollama pull llama3.1:8b
```

Testa il modello:
```bash
ollama run llama3.1:8b "Rispondi con una sola parola: funzioni?"
```

### Configurazione
Per rendere Ollama raggiungibile anche da altri container Docker (es. Open WebUI) e da altri dispositivi della rete, modifica la configurazione del servizio per ascoltare su tutte le interfacce:

```bash
sudo systemctl edit ollama.service
```

Aggiungi (nel blocco che si apre):
```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
```

Riavvia il servizio:
```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

### Verifica
```bash
ollama list
```
Dovresti vedere il modello scaricato nell'elenco.

Dal PC principale (sulla rete locale):
```bash
curl http://192.168.1.50:11434/api/tags
```
Dovresti ricevere una risposta JSON con l'elenco dei modelli.

### Problemi comuni
| Problema | Causa |
|---|---|
| Il modello risponde molto lentamente | RAM insufficiente per il modello scelto, sistema che usa lo swap su disco |
| `ollama: command not found` dopo l'installazione | Il PATH della shell non è stato aggiornato, serve riaprire la sessione |
| Non raggiungibile da altri dispositivi | `OLLAMA_HOST` non configurato, o firewall che blocca la porta 11434 |

### Come risolverli
```bash
# Verifica la RAM disponibile
free -h

# Se necessario, passa a un modello più piccolo
ollama pull llama3.2:3b

# Apri la porta nel firewall se attivo
sudo ufw allow 11434/tcp
```

### Cosa fare dopo
Procedi alla Fase 10 per l'interfaccia web.

---

## Fase 10 — Interfaccia AI (Open WebUI)

### Obiettivo
Fornire un'interfaccia web comoda per interagire con i modelli AI, raggiungibile da qualsiasi dispositivo della rete (o, se configurato, anche da remoto tramite VPN).

### Cosa fare
Installa Open WebUI via Docker Compose, collegandolo a Ollama.

### Comandi
```bash
mkdir -p /data/docker/open-webui
cd /data/docker/open-webui
```

Crea `docker-compose.yml`:
```yaml
version: "3.8"
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: brainos-openwebui
    ports:
      - "3000:8080"
    volumes:
      - /data/brain/cache/open-webui:/app/backend/data
    environment:
      - OLLAMA_BASE_URL=http://host.docker.internal:11434
    extra_hosts:
      - "host.docker.internal:host-gateway"
    restart: unless-stopped
```

Avvia:
```bash
docker compose up -d
```

### Configurazione
Apri il browser (da qualsiasi PC della rete) su:
```
http://192.168.1.50:3000
```

Al primo accesso, crea l'account amministratore locale (questi dati restano solo nel database di Open WebUI, sul tuo server, non vengono inviati altrove).

### Verifica
- [ ] La pagina di login di Open WebUI si apre correttamente
- [ ] Dopo il login, il modello Ollama scaricato è visibile e selezionabile nel menu
- [ ] Una domanda di prova riceve una risposta coerente

### Problemi comuni
| Problema | Causa |
|---|---|
| Open WebUI non trova nessun modello | `OLLAMA_BASE_URL` errato, o Ollama non configurato per ascoltare su `0.0.0.0` (vedi Fase 9) |
| Pagina bianca o errore 502 | Il container ha impiegato più tempo del previsto per avviarsi |

### Come risolverli
```bash
docker logs brainos-openwebui

# Verifica che Ollama sia raggiungibile dal container
docker exec -it brainos-openwebui curl http://host.docker.internal:11434/api/tags
```

### Cosa fare dopo
Procedi alla Fase 11 per il vault Obsidian.

---

## Fase 11 — Vault Obsidian e sincronizzazione

### Obiettivo
Collegare Obsidian (lato client, sui tuoi dispositivi) al vault centralizzato sul server.

### Cosa fare
Scegli **uno o più** dei metodi descritti in `SpiegazioneAnalisi.md` per portare i file sul server: Git, Nextcloud, VPN diretta, o Obsidian Sync.

In questa guida usiamo **Git**, poiché è già stato inizializzato nella Fase 6 ed è il metodo più adatto a un profilo da sviluppatore.

### Comandi
Sul server, prepara un repository Git "nudo" che farà da punto di sincronizzazione:
```bash
mkdir -p /data/git/brain.git
cd /data/git/brain.git
git init --bare
```

Sul PC client (es. il tuo laptop), clona il vault tramite SSH:
```bash
git clone ssh://brainadmin@192.168.1.50/data/git/brain.git ~/BrainVault
```

Copia la struttura già creata nella Fase 6 dentro il repository appena clonato, oppure collega direttamente `/data/brain/vault` come remoto:

```bash
cd /data/brain/vault
git remote add origin /data/git/brain.git
git push origin main
```

Dal client, dopo eventuali modifiche:
```bash
cd ~/BrainVault
git add .
git commit -m "Aggiornamento appunti"
git push
```

### Configurazione
Apri Obsidian sul client e seleziona **"Apri cartella come vault"**, puntando a `~/BrainVault` (la copia locale clonata).

### Verifica
- [ ] Il vault è visibile e navigabile in Obsidian sul client
- [ ] Una modifica fatta sul client, dopo `git push`, è visibile facendo `git pull` sul server dentro `/data/brain/vault`

### Problemi comuni
| Problema | Causa |
|---|---|
| `git push` chiede la password ogni volta | Nessuna chiave SSH configurata per l'autenticazione |
| Conflitti di merge tra client e server | Modifiche fatte in parallelo su entrambi i lati senza sincronizzare prima |

### Come risolverli
```bash
# Genera una chiave SSH sul client, se non esiste già
ssh-keygen -t ed25519 -C "brain-client"

# Copia la chiave pubblica sul server
ssh-copy-id brainadmin@192.168.1.50
```

Per i conflitti di merge, esegui sempre `git pull` prima di iniziare a scrivere, e risolvi manualmente eventuali conflitti evidenziati da Git nei file interessati.

### Cosa fare dopo
Procedi alla Fase 12 se desideri accesso anche da fuori casa; altrimenti salta direttamente alla Fase 14 per i test.

---

## Fase 12 — Accesso remoto sicuro (VPN)

### Obiettivo
Permettere l'accesso al Brain anche fuori dalla rete domestica, senza esporre direttamente i servizi su Internet — principio di sicurezza ribadito più volte nella repository.

### Cosa fare
Installa **Tailscale**, la soluzione più semplice indicata nei file della repository.

### Comandi
Sul server:
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Il comando restituirà un link: apri quel link in un browser, autenticati con il tuo account Tailscale (o creane uno gratuito) e autorizza il dispositivo.

Sul PC/telefono client, installa l'app Tailscale corrispondente (disponibile per Windows, macOS, Linux, iOS, Android) e accedi con lo stesso account.

### Configurazione
Sul server, verifica l'IP assegnato da Tailscale:
```bash
tailscale ip -4
```

Questo IP (tipicamente nel formato `100.x.y.z`) sarà raggiungibile da qualsiasi dispositivo autenticato nella tua rete Tailscale, ovunque si trovi.

### Verifica
Da un dispositivo fuori dalla rete di casa (es. telefono con dati mobili, Tailscale attivo):
```
http://100.x.y.z:3000
```
Dovrebbe aprirsi Open WebUI, esattamente come in LAN.

### Problemi comuni
| Problema | Causa |
|---|---|
| Il dispositivo remoto non raggiunge il server | Tailscale non autenticato o disattivato su uno dei due dispositivi |
| Connessione lenta | Tailscale sta usando un relay invece di una connessione diretta (succede dietro NAT complessi) |

### Come risolverli
```bash
# Verifica lo stato della connessione
tailscale status

# Verifica se la connessione è diretta o relayed
tailscale ping <nome-dispositivo>
```

### Cosa fare dopo
Procedi alla Fase 13 (opzionale) o direttamente alla Fase 14 per i test.

---

## Fase 13 — Reverse proxy (opzionale)

### Obiettivo
Sostituire indirizzi tipo `192.168.1.50:3000` con nomi più comodi (es. `brain.local`), e centralizzare l'accesso a più servizi.

### Cosa fare
Installa **Caddy**, che gestisce automaticamente anche eventuali certificati HTTPS.

### Comandi
```bash
mkdir -p /data/docker/caddy
cd /data/docker/caddy
```

Crea `docker-compose.yml`:
```yaml
version: "3.8"
services:
  caddy:
    image: caddy:latest
    container_name: brainos-caddy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - ./caddy-data:/data
      - ./caddy-config:/config
    restart: unless-stopped
```

Crea il file `Caddyfile` nella stessa cartella:
```text
brain.local {
    reverse_proxy host.docker.internal:3000
}
```

Avvia:
```bash
docker compose up -d
```

### Configurazione
Se usi Technitium DNS (Fase 5), associa `brain.local` all'IP del server. In alternativa, per un test rapido, aggiungi una riga nel file `hosts` del PC client:

Linux/macOS: `/etc/hosts`
Windows: `C:\Windows\System32\drivers\etc\hosts`

```text
192.168.1.50   brain.local
```

### Verifica
Dal browser:
```
http://brain.local
```
Dovrebbe reindirizzare automaticamente a Open WebUI.

### Problemi comuni
| Problema | Causa |
|---|---|
| `brain.local` non si risolve | DNS locale non configurato, o file `hosts` non modificato correttamente |
| Errore 502 Bad Gateway | Il servizio di destinazione (Open WebUI) non è raggiungibile dal container Caddy |

### Come risolverli
```bash
docker logs brainos-caddy
```

Verifica che `host.docker.internal` funzioni anche per Caddy, aggiungendo se necessario lo stesso blocco `extra_hosts` visto nella Fase 10.

### Cosa fare dopo
Procedi alla Fase 14 per i test end-to-end.

---

## Fase 14 — Test end-to-end del sistema

### Obiettivo
Verificare che tutti i componenti installati finora lavorino correttamente insieme.

### Cosa fare
Esegui una serie di test manuali che ripercorrono il flusso descritto in `SpiegazioneAnalisi.md`.

### Comandi / Verifica
1. **Test Docker**: tutti i container devono essere `Up`.
```bash
docker ps
```

2. **Test PostgreSQL**:
```bash
docker exec -it brainos-postgres psql -U brainos -d brainos -c "SELECT version();"
```

3. **Test ChromaDB**:
```bash
curl http://localhost:8001/api/v1/heartbeat
```

4. **Test Ollama**:
```bash
ollama list
```

5. **Test Open WebUI**: apri il browser su `http://192.168.1.50:3000` (o `http://brain.local` se hai configurato il reverse proxy) e invia una domanda di prova al modello.

6. **Test vault Git**: sul client, modifica un file nel vault, fai `git push`, poi sul server verifica con `git log` che la modifica sia arrivata.

7. **Test accesso remoto** (se configurato Tailscale): da un dispositivo fuori casa, verifica di raggiungere Open WebUI tramite l'IP Tailscale.

### Configurazione
Nessuna configurazione aggiuntiva: questa fase è puramente di verifica.

### Problemi comuni
Vedi le tabelle "Problemi comuni" di ciascuna fase precedente: la maggior parte dei problemi in questa fase deriva da una fase precedente non completata correttamente.

### Come risolverli
Ripercorri la fase corrispondente al componente che sta fallendo il test, controllando i log con `docker logs <nome-container>`.

### Cosa fare dopo
Procedi alla Fase 15 per configurare il backup.

---

## Fase 15 — Backup

### Obiettivo
Proteggere il Brain applicando la "regola delle tre copie" descritta in `opzioneAumentoMemoriaUbuntu.md` e Idee.md.

### Cosa fare
1. Crea uno script di backup automatico per l'intera cartella `/data/brain`.
2. Pianifica l'esecuzione periodica con `cron`.
3. Configura una copia offsite (secondo disco o cloud cifrato).

### Comandi
Crea lo script:
```bash
sudo nano /data/docker/backup.sh
```

Contenuto:
```bash
#!/bin/bash
set -e

DATA=$(date +%Y-%m-%d_%H-%M)
SORGENTE=/data/brain
DESTINAZIONE=/data/backups/brain_backup_$DATA.tar.gz

tar -czf $DESTINAZIONE $SORGENTE

# Mantieni solo gli ultimi 14 backup
ls -1t /data/backups/brain_backup_*.tar.gz | tail -n +15 | xargs -r rm

echo "Backup completato: $DESTINAZIONE"
```

Rendilo eseguibile:
```bash
chmod +x /data/docker/backup.sh
```

### Configurazione
Pianifica l'esecuzione giornaliera con `cron` (es. ogni notte alle 3:00, come indicato in `ideaNetwork.md`):

```bash
crontab -e
```

Aggiungi la riga:
```text
0 3 * * * /data/docker/backup.sh >> /data/backups/backup.log 2>&1
```

**Spiegazione della riga cron:**

| Campo | Valore | Significato |
|---|---|---|
| Minuto | `0` | Al minuto 0 |
| Ora | `3` | Alle 3 del mattino |
| Giorno del mese | `*` | Ogni giorno |
| Mese | `*` | Ogni mese |
| Giorno della settimana | `*` | Ogni giorno della settimana |

### Verifica
Esegui manualmente lo script una prima volta per testarlo:
```bash
/data/docker/backup.sh
ls -la /data/backups/
```

### Problemi comuni
| Problema | Causa |
|---|---|
| Lo script cron non si esegue | Percorso dello script errato nel crontab, o permessi di esecuzione mancanti |
| Il backup cresce troppo nel tempo | Nessuna rotazione configurata |

### Come risolverli
```bash
# Verifica i log di cron
grep CRON /var/log/syslog

# Verifica che lo script sia eseguibile
ls -la /data/docker/backup.sh
```

La rotazione (mantenimento degli ultimi 14 backup) è già inclusa nello script sopra tramite `ls -1t ... | tail -n +15 | xargs -r rm`.

### Cosa fare dopo
Procedi alla Fase 16 per la manutenzione.

---

## Fase 16 — Manutenzione e aggiornamenti

### Obiettivo
Mantenere il sistema aggiornato e sano nel tempo.

### Cosa fare
Esegui periodicamente (consigliato: mensilmente) gli aggiornamenti di sistema, container e modelli AI.

### Comandi
Aggiornamento del sistema operativo:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt autoremove -y
```

Aggiornamento dei container Docker:
```bash
cd /data/docker/open-webui && docker compose pull && docker compose up -d
cd /data/docker/chromadb && docker compose pull && docker compose up -d
cd /data/docker/postgres && docker compose pull && docker compose up -d
```

Aggiornamento di Ollama e dei modelli:
```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama pull llama3.1:8b
```

### Configurazione
Puoi automatizzare l'aggiornamento dei container con uno script simile a quello del backup, pianificato via `cron` con cadenza settimanale o mensile (consigliato mensile, per poter verificare manualmente che nulla si rompa).

### Verifica
```bash
docker ps
docker images
```
Verifica che i container siano `Up` dopo ogni aggiornamento e che le versioni delle immagini siano quelle attese.

### Problemi comuni
| Problema | Causa |
|---|---|
| Un servizio non riparte dopo l'aggiornamento | Breaking change nella nuova versione dell'immagine |
| Spazio disco esaurito da immagini Docker vecchie | Nessuna pulizia periodica delle immagini inutilizzate |

### Come risolverli
```bash
# Torna alla versione precedente specificando il tag nella docker-compose.yml
# Esempio: image: chromadb/chroma:0.5.0 invece di :latest

# Pulisci immagini, container e volumi inutilizzati
docker system prune -a --volumes
```

> ⚠️ Usa `docker system prune --volumes` con cautela: cancella anche i volumi non collegati a nessun container attivo. Verifica sempre prima con `docker volume ls`.

### Cosa fare dopo
Procedi alla Fase 17 per le automazioni future.

---

## Fase 17 — Automazioni future (Brain Keeper)

### Obiettivo
Introdurre, quando il sistema base è stabile, le automazioni di manutenzione intelligente descritte in Idee.md e ideaNetwork.md.

### Cosa fare
Questa fase è **evolutiva** e va affrontata solo dopo che le Fasi 1-16 sono stabili e usate quotidianamente per un po' di tempo. Consiste nello sviluppo di uno script/servizio custom (in Python, ad esempio) che:

1. Scansiona periodicamente `/data/brain/vault`.
2. Genera embedding per i nuovi documenti tramite ChromaDB.
3. Individua documenti duplicati o simili tramite similarità coseno sugli embedding.
4. Individua documenti troppo grandi (es. oltre 3000 righe) e ne propone la suddivisione.
5. Genera un report giornaliero/settimanale.

### Comandi
Esempio concettuale di struttura del progetto Python (da sviluppare secondo le proprie esigenze):

```bash
mkdir -p /data/docker/brain-keeper
cd /data/docker/brain-keeper
python3 -m venv venv
source venv/bin/activate
pip install chromadb watchdog gitpython
```

### Configurazione
Uno scheduler (`cron`, oppure un loop interno con `APScheduler` in Python) esegue lo script ogni notte, salvando il report in `/data/brain/vault/13_Journal/`.

### Verifica
Dopo l'esecuzione, controlla che venga generato un file di report leggibile, ad esempio:
```text
/data/brain/vault/13_Journal/report_2026-07-16.md
```

### Problemi comuni
| Problema | Causa |
|---|---|
| Il Brain Keeper propone troppi falsi positivi (duplicati che non lo sono) | Soglia di similarità troppo bassa nell'algoritmo di confronto |

### Come risolverli
Aumenta la soglia di similarità richiesta prima di segnalare un possibile duplicato, e valuta sempre manualmente prima di unire o cancellare documenti (principio di **nessuna modifica automatica senza conferma**, ribadito più volte nella repository).

### Cosa fare dopo
Questo è il punto in cui il progetto entra nelle **Fasi 3-5 della roadmap** descritta nel README originale (RAG completo, memoria attiva, Brain autonomo). Da qui in avanti lo sviluppo diventa iterativo: si aggiungono funzionalità (router multi-modello, dashboard, API pubbliche, plugin per IDE) una alla volta, testando sempre in isolamento prima di integrarle nel sistema principale.

---

## Nota finale — parti della repository segnalate come da aggiornare o incomplete

| Argomento | Stato nella repository | Osservazione |
|---|---|---|
| Autenticazione/sicurezza degli accessi | Solo principi generali, nessun dettaglio implementativo | In questa guida si è scelto Tailscale + autenticazione base di Open WebUI come soluzione minima ragionevole |
| Proxmox / virtualizzazione | Assente | Non implementato in questa guida, che segue l'approccio "Ubuntu Server + Docker" della repository; resta un possibile miglioramento futuro (vedi File 01, sezione 20) |
| Orchestratore AI multi-modello (router) | Solo concettuale, nessun codice | Da sviluppare come step successivo (Fase 17) |
| Brain Keeper | Solo concettuale | Da sviluppare come step successivo (Fase 17) |
| CI/CD per il codice di BrainOS | Non trattato nella repository | Se BrainOS diventerà un software vero e proprio, valutare l'introduzione di test automatici e pipeline di deploy |

---

*Fine della guida. Con il completamento delle Fasi 0-16 si ottiene un sistema BrainOS funzionante, sicuro, con backup automatico e accesso sia locale che remoto via VPN. La Fase 17 rappresenta il punto di partenza per l'evoluzione verso le fasi più avanzate della roadmap originale (RAG completo, Brain autonomo).*
