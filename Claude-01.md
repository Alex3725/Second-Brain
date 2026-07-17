# 02 — Second Brain Casalingo (Versione Semplificata)

> Questa guida è la **versione semplificata** di `Claude-02.md` (File 03 — Guida Completa all'Implementazione). Copre solo le Fasi necessarie per un uso puramente domestico: un solo utente, accesso solo da rete locale, nessuna VPN, nessun reverse proxy, nessun database applicativo/vettoriale (ancora).
>
> **Punto fondamentale:** questa guida usa **esattamente gli stessi path, gli stessi nomi dei container e la stessa numerazione delle Fasi** della guida completa. Non è una guida "diversa e più semplice" — è la guida completa con alcune Fasi saltate. Questo significa che il giorno in cui vorrai passare a `Claude-02.md` per aggiungere VPN, database, reverse proxy o ricerca semantica, **non dovrai spostare file, rinominare cartelle o rifare nulla**: basta riprendere dalla Fase successiva a quella in cui questa guida si ferma.

---

## Cosa include questa guida rispetto a Claude-02

| Fase | Titolo | In questa guida (01) | In Claude-02 (versione completa) |
|---|---|---|---|
| 0 | Pianificazione | ✅ Semplificata | ✅ Completa |
| 1 | Installazione sistema operativo | ✅ Uguale | ✅ Uguale |
| 2 | Accesso remoto SSH | ✅ Opzionale, comandi uguali | ✅ Uguale |
| 3 | Configurazione storage (`/data`) | ✅ Uguale | ✅ Uguale |
| 4 | Docker e Docker Compose | ✅ Uguale | ✅ Uguale |
| 5 | Rete locale / DNS | ✅ Solo riserva DHCP | ✅ Anche DNS locale (Technitium) |
| 6 | Struttura cartelle del Brain | ✅ Uguale (stessa struttura `Projects/` + `Knowledge/`) | ✅ Uguale |
| 7 | Database applicativo (Postgres) | ⏭️ Saltata | ✅ Presente |
| 8 | Database vettoriale (ChromaDB) | ⏭️ Saltata | ✅ Presente |
| 9 | AI locale (Ollama) | ✅ Uguale | ✅ Uguale |
| 10 | Interfaccia AI (Open WebUI) | ✅ Uguale | ✅ Uguale |
| 11 | Vault Obsidian | ✅ Versione locale semplice | ✅ Anche sync multi-dispositivo via Git |
| 12 | VPN (accesso da fuori casa) | ⏭️ Saltata | ✅ Presente |
| 13 | Reverse proxy | ⏭️ Saltata | ✅ Presente (opzionale anche lì) |
| 14 | Test end-to-end | ✅ Versione ridotta | ✅ Completa |
| 15 | Backup | ✅ Uguale | ✅ Uguale |
| 16 | Manutenzione | ✅ Cenni | ✅ Completa |
| 17 | Automazioni future | ✅ Cenni | ✅ Completa |

Le Fasi 7, 8, 12 e 13, quando vorrai attivarle, si trovano già pronte e invariate in `Claude-02.md`: puoi copiare i comandi da lì senza dover adattare nulla, perché i path (`/data/brain/...`, `/data/docker/...`) e i nomi dei container (`brainos-*`) sono identici.

---

## Scenario di riferimento

| Requisito | Valore |
|---|---|
| Utenti | Uno solo (tu) |
| Accesso da Internet | ❌ Nessuno (per ora — si aggiunge in Claude-02, Fase 12) |
| Dominio pubblico | ❌ Nessuno |
| Database applicativo/vettoriale | ❌ Non ancora (si aggiunge in Claude-02, Fasi 7-8) |
| Accesso dal PC principale | ✅ Sì |
| Accesso dagli altri PC/telefoni di casa (LAN) | ✅ Sì |

---

## Indice delle fasi

| Fase | Titolo |
|---|---|
| 0 | Pianificazione e scelte preliminari |
| 1 | Installazione del sistema operativo |
| 2 | Accesso remoto (SSH) — opzionale |
| 3 | Configurazione dello storage |
| 4 | Installazione di Docker e Docker Compose |
| 5 | Rete locale e nome host |
| 6 | Struttura delle cartelle del Brain |
| 9 | AI locale con Ollama |
| 10 | Interfaccia AI (Open WebUI) |
| 11 | Vault Obsidian (versione locale) |
| 14 | Test end-to-end (versione ridotta) |
| 15 | Backup |

> Nota: i numeri delle Fasi 7, 8, 12, 13, 16, 17 non compaiono in questa guida di proposito — sono riservati alle sezioni corrispondenti di `Claude-02.md`, per mantenere la numerazione identica tra le due guide.

---

## Fase 0 — Pianificazione e scelte preliminari

### Obiettivo
Definire con chiarezza cosa si sta costruendo, in versione domestica minima.

### Cosa fare
1. Decidi quale macchina userai: può essere il tuo PC principale (usato anche per altro) oppure un PC/mini PC dedicato, sempre acceso.
2. Prepara una chiavetta USB da almeno 8 GB per l'immagine di installazione, **solo se** installerai Ubuntu Server da zero. Se il PC ha già un sistema operativo che vuoi tenere, vedi la nota nella Fase 1.
3. Scarica l'immagine ISO di **Ubuntu Server 24.04 LTS** da `ubuntu.com/download/server`.

### Comandi
Nessun comando in questa fase: è puramente decisionale.

### Configurazione
Annota, prima di iniziare:
- Nome host del PC (es. `brainos-server`)
- Nome utente amministratore
- Se userai un disco esterno per i dati (consigliato, vedi Fase 3) o solo il disco interno

### Verifica
- [ ] Hai deciso quale PC userai
- [ ] Hai l'immagine ISO scaricata (se necessaria)

### Problemi comuni
| Problema | Causa |
|---|---|
| Non sai quale versione di Ubuntu scaricare | Usa sempre la versione LTS più recente |

### Come risolverli
Scarica esclusivamente da `ubuntu.com`, versione "Server" (non "Desktop").

### Cosa fare dopo
Procedi alla Fase 1.

---

## Fase 1 — Installazione del sistema operativo

### Obiettivo
Avere Ubuntu funzionante sul PC scelto.

### Cosa fare
1. Se stai installando da zero: scrivi l'ISO sulla chiavetta USB (Balena Etcher / Rufus su Windows, `dd` su Linux/macOS) e avvia il PC da USB.
2. Segui la procedura guidata: lingua, tastiera, rete (DHCP automatico va bene), disco, utente amministratore, nome host.
3. Nella schermata "Featured Server Snaps", abilita **OpenSSH Server** (utile anche solo per comodità, vedi Fase 2).
4. Completa l'installazione e riavvia.

> 💡 **Alternativa più semplice**: se preferisci non reinstallare il sistema operativo, puoi seguire questa guida anche su un PC con **Ubuntu Desktop, Windows o macOS** già installato, usando direttamente Docker Desktop invece di Docker Engine. In tal caso i comandi restano quasi identici, ma alcuni passaggi (es. `systemctl`, installazione via `apt`) non si applicano — usa l'interfaccia grafica di Docker Desktop al loro posto. Per la massima compatibilità futura con `Claude-02.md` (pensata per Ubuntu Server), l'opzione consigliata resta comunque Ubuntu.

### Comandi
```bash
# Scrittura ISO su USB da terminale Linux/macOS (sostituisci /dev/sdX con il device corretto)
sudo dd if=ubuntu-24.04-live-server-amd64.iso of=/dev/sdX bs=4M status=progress oflag=sync
```

> ⚠️ Presta **estrema attenzione** al device di destinazione: un errore può cancellare dati da un altro disco.

### Configurazione
- Nome host: es. `brainos-server`
- Nome utente: a tua scelta
- OpenSSH Server: consigliato attivo

### Verifica
Al riavvio, prompt di login testuale con il nome host scelto.

### Problemi comuni
| Problema | Causa probabile |
|---|---|
| Il PC non avvia dalla chiavetta USB | Ordine di boot nel BIOS/UEFI non corretto, o Secure Boot attivo |

### Come risolverli
Entra nel BIOS/UEFI (F2/Del all'accensione), imposta l'avvio da USB come priorità, disabilita temporaneamente Secure Boot se necessario.

### Cosa fare dopo
Procedi alla Fase 2 (opzionale) o direttamente alla Fase 3.

---

## Fase 2 — Accesso remoto (SSH) — opzionale

### Obiettivo
Poter amministrare il PC da un altro dispositivo senza monitor/tastiera collegati. Utile ma non obbligatorio se lavori sempre direttamente sul PC.

### Cosa fare
1. Trova l'IP del PC.
2. Connettiti via SSH da un altro dispositivo della rete.

### Comandi
Sul PC, per vedere l'IP:
```bash
ip addr show
```

Da un altro PC:
```bash
ssh brainadmin@192.168.1.50
```

### Configurazione
Alias comodo su `~/.ssh/config` (Linux/macOS):
```text
Host brainos
    HostName 192.168.1.50
    User brainadmin
```
Poi: `ssh brainos`

### Verifica
Dopo la connessione, vedi il prompt del PC (es. `brainadmin@brainos-server:~$`).

### Problemi comuni
| Problema | Causa |
|---|---|
| `Connection refused` | Servizio SSH non attivo |
| `Connection timed out` | Firewall blocca la porta 22, o IP errato |

### Come risolverli
```bash
sudo systemctl status ssh
sudo systemctl enable --now ssh
sudo ufw allow OpenSSH
```

### Cosa fare dopo
Procedi alla Fase 3.

---

## Fase 3 — Configurazione dello storage

### Obiettivo
Separare **sistema** e **dati**, così da poter reinstallare il sistema operativo in futuro senza perdere il Brain. Stessa identica struttura di `Claude-02.md`.

### Cosa fare
1. Collega un SSD esterno USB (o un secondo disco interno).
2. Identificalo, formattalo e montalo in modo persistente usando l'UUID.

### Comandi
```bash
# Elenca tutti i dischi collegati
lsblk

# Supponendo che il nuovo disco sia /dev/sdb, crea una partizione
sudo fdisk /dev/sdb
# All'interno di fdisk: n -> invio per i default -> w

# Formatta in ext4
sudo mkfs.ext4 /dev/sdb1

# Crea il punto di mount
sudo mkdir -p /data

# Trova l'UUID
sudo blkid /dev/sdb1
```

### Configurazione
Apri `/etc/fstab`:
```bash
sudo nano /etc/fstab
```
Aggiungi (sostituendo `UUID-DEL-TUO-DISCO`):
```text
UUID=UUID-DEL-TUO-DISCO   /data   ext4   defaults   0   2
```
Monta subito senza riavviare:
```bash
sudo mount -a
```

Crea la struttura dati (**identica a Claude-02**, incluse le cartelle che per ora resteranno vuote — `vectors` e `database` verranno usate quando attiverai le Fasi 7-8):
```bash
sudo mkdir -p /data/brain/vault /data/brain/vectors /data/brain/database /data/brain/cache
sudo mkdir -p /data/git /data/backups /data/docker
sudo chown -R $USER:$USER /data
```

### Verifica
```bash
df -h /data
ls -la /data
```
Dovresti vedere le cartelle `brain`, `git`, `backups`, `docker`.

### Problemi comuni
| Problema | Causa |
|---|---|
| Il sistema non si avvia dopo aver modificato `fstab` | Errore di sintassi o UUID sbagliato |
| `mount -a` restituisce errore | UUID errato o filesystem non corrispondente |

### Come risolverli
Se il sistema non si avvia, usa la shell di emergenza offerta da Ubuntu (Ctrl+D o modalità recovery) e correggi `/etc/fstab`.

> 💡 Testa sempre con `sudo mount -a` prima di riavviare dopo una modifica a `fstab`.

### Cosa fare dopo
Procedi alla Fase 4.

---

## Fase 4 — Installazione di Docker e Docker Compose

### Obiettivo
Installare il motore che ospiterà i servizi in container.

### Cosa fare
Installa Docker Engine e il plugin Docker Compose (procedura ufficiale Ubuntu — identica a Claude-02).

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

Disconnettiti e riconnettiti perché il gruppo abbia effetto:
```bash
exit
ssh brainos
```
(oppure, se lavori direttamente sul PC senza SSH, disconnetti/riconnetti la sessione utente o riavvia)

### Configurazione
```bash
sudo systemctl enable docker
```

### Verifica
```bash
docker --version
docker compose version
docker run hello-world
```

### Problemi comuni
| Problema | Causa |
|---|---|
| `permission denied` eseguendo `docker` | L'utente non è (ancora) nel gruppo `docker`, manca logout/login |
| `docker: command not found` | Installazione non completata |

### Come risolverli
```bash
groups
sudo usermod -aG docker $USER
```

### Cosa fare dopo
Procedi alla Fase 5.

---

## Fase 5 — Rete locale e nome host

### Obiettivo
Rendere il PC sempre raggiungibile con lo stesso indirizzo IP.

### Cosa fare
Configura una **riserva DHCP** sul router per il PC.

> Il DNS locale (Technitium, nomi tipo `brain.local`) è **facoltativo** ed è descritto in `Claude-02.md`, Fase 5: puoi aggiungerlo in qualsiasi momento senza toccare nulla di quanto fatto qui.

### Comandi
Trova l'indirizzo MAC:
```bash
ip link show
```

### Configurazione
1. Accedi al pannello del router (es. `http://192.168.1.1`).
2. Cerca "DHCP Reservation" o "IP statico via DHCP".
3. Associa il MAC address del PC a un IP fisso (es. `192.168.1.50`).
4. Salva.

### Verifica
```bash
ping 192.168.1.50
```
L'IP deve restare lo stesso anche dopo un riavvio del PC.

### Problemi comuni
| Problema | Causa |
|---|---|
| L'IP cambia comunque | Riserva DHCP non salvata correttamente, o router non supportato |

### Come risolverli
Verifica nel pannello del router che la riserva sia effettivamente elencata e attiva.

### Cosa fare dopo
Procedi alla Fase 6.

---

## Fase 6 — Struttura delle cartelle del Brain

### Obiettivo
Preparare la struttura dati completa del vault — **identica a Claude-02**. La logica alla base è separare due tipi di memoria:

- **`Projects/`** → memoria di progetto: documentazione, decisioni, bug e roadmap che appartengono a un progetto specifico e, se apri un nuovo progetto, probabilmente non ti interessano.
- **`Knowledge/`** → memoria personale riutilizzabile: appunti di programmazione, pattern, snippet, convenzioni. Cresce nel tempo e vale per qualsiasi progetto.

Le vecchie cartelle globali `08_Decisions` e `09_Bugs` non esistono più come cartelle a sé: decisioni e bug vivono dentro `Projects/<NomeProgetto>/`, perché appartengono sempre a qualcosa di specifico. Quando una decisione o un pattern nato in un progetto si rivela riutilizzabile ovunque, lo **promuovi** spostandolo in `Knowledge/` e lasci un collegamento nel progetto originale.

### Cosa fare
Crea la struttura completa dentro `/data`, già montata nella Fase 3.

### Comandi
```bash
mkdir -p /data/brain/vault/{System,Identity,Projects,Prompts,Templates,Resources,Journal,Inbox,Archive}
mkdir -p /data/brain/vault/Knowledge/{Programming,DevOps,AI,Patterns,Snippets}

touch /data/brain/vault/AGENTS.md

mkdir -p /data/brain/vectors
mkdir -p /data/brain/database
mkdir -p /data/brain/cache
mkdir -p /data/git
mkdir -p /data/backups
mkdir -p /data/docker
```

### Configurazione
Popola `AGENTS.md`:
```bash
cat > /data/brain/vault/AGENTS.md << 'EOF'
# AGENTS.md

Punto di ingresso per qualsiasi agente AI collegato a questo Brain.

## Struttura del vault
- `Projects/` — memoria di progetto: documentazione, decisioni, bug e roadmap
  specifici di ciascun progetto (in `Projects/<NomeProgetto>/`).
- `Knowledge/` — memoria personale riutilizzabile: programmazione, DevOps, AI,
  pattern architetturali (`Knowledge/Patterns/`), snippet di codice
  (`Knowledge/Snippets/`).
- `Prompts/` — prompt riutilizzabili, non legati a un progetto specifico.
- `Templates/` — scheletri di progetto e template di documenti.
- `Resources/` — documentazione esterna, PDF, libri, datasheet.
- `Journal/` — note giornaliere e idee.
- `Inbox/` — appunti veloci da classificare.
- `Archive/` — materiale non più attivo.
- `System/` — standard e convenzioni per gli agenti AI.
- `Identity/` — contesto personale, preferenze.

## Priorità delle fonti
1. Progetto corrente (`Projects/<NomeProgetto>/`)
2. Conoscenza generale (`Knowledge/`)
3. Altri progetti (`Projects/`), per analogie o problemi già affrontati altrove
4. Documentazione e codice del progetto specifico (nel repository del progetto,
   fuori dal vault)
5. Internet (solo se le fonti precedenti non bastano)

## Prima di rispondere, l'agente deve:
1. Comprendere il contesto della richiesta.
2. Identificare il progetto pertinente in `Projects/`.
3. Recuperare decisioni e bug pertinenti (`Projects/<NomeProgetto>/Decisions.md`,
   `Bugs.md`).
4. Cercare in `Knowledge/` pattern, snippet o appunti riutilizzabili.
5. Se necessario, verificare se altri progetti hanno affrontato un problema simile.
6. Leggere gli standard in `System/`.
7. Produrre la risposta, segnalando se un'informazione andrebbe "promossa" da un
   progetto a `Knowledge/` perché riutilizzabile altrove.
EOF
```

Inizializza Git nel vault:
```bash
cd /data/brain/vault
git init
git add .
git commit -m "Inizializzazione struttura Brain"
```

> Nota: `Projects/` parte vuota. Ogni volta che avvii un nuovo progetto, crea al suo interno una sottocartella con `README.md`, `Architecture.md`, `Decisions.md`, `Bugs.md` e `Roadmap.md` (vedi il file di riferimento sulla logica del vault per un esempio completo).

### Verifica
```bash
find /data/brain/vault -maxdepth 2 -type d
cd /data/brain/vault && git log --oneline
```

### Problemi comuni
| Problema | Causa |
|---|---|
| `git commit` fallisce chiedendo nome/email | Git non configurato globalmente |

### Come risolverli
```bash
git config --global user.name "Il Tuo Nome"
git config --global user.email "tua-email@esempio.com"
```

### Cosa fare dopo
Procedi alla Fase 9 per l'AI locale. (Le Fasi 7 e 8 — database applicativo e vettoriale — sono facoltative in questa versione domestica: le trovi pronte, con gli stessi path, in `Claude-02.md`.)

---

## Fase 9 — AI locale con Ollama

### Obiettivo
Installare il runtime AI locale.

### Cosa fare
Installa Ollama direttamente sul sistema host.

### Comandi
```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama --version
ollama pull llama3.1:8b
ollama run llama3.1:8b "Rispondi con una sola parola: funzioni?"
```

### Configurazione
Per renderlo raggiungibile da Open WebUI (Fase 10) e da altri dispositivi della LAN:
```bash
sudo systemctl edit ollama.service
```
Aggiungi:
```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
```
Riavvia:
```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

### Verifica
```bash
ollama list
```
Dal PC principale (rete locale):
```bash
curl http://192.168.1.50:11434/api/tags
```

### Problemi comuni
| Problema | Causa |
|---|---|
| Risposte molto lente | RAM insufficiente per il modello scelto (vedi tabella modelli sotto) |
| `ollama: command not found` | PATH non aggiornato, riapri la sessione |
| Non raggiungibile da altri dispositivi | `OLLAMA_HOST` non configurato, o firewall blocca la porta 11434 |

### Come risolverli
```bash
free -h
ollama pull llama3.2:3b   # modello più leggero
sudo ufw allow 11434/tcp
```

### Modelli consigliati in base alla RAM

| RAM disponibile | Modello consigliato | Comando |
|---|---|---|
| 8 GB | `llama3.2:3b` o `phi3:mini` | `ollama pull llama3.2:3b` |
| 16 GB | `llama3.1:8b` o `mistral:7b` | `ollama pull llama3.1:8b` |
| 32 GB (senza GPU dedicata) | `qwen2.5:14b` | `ollama pull qwen2.5:14b` |
| 32 GB + GPU 12GB+ VRAM | `qwen2.5:32b` | `ollama pull qwen2.5:32b` |

### Cosa fare dopo
Procedi alla Fase 10.

---

## Fase 10 — Interfaccia AI (Open WebUI)

### Obiettivo
Interfaccia web per parlare con i modelli AI, raggiungibile da qualsiasi dispositivo della rete locale.

### Cosa fare
Installa Open WebUI via Docker Compose, collegato a Ollama.

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
Crea l'account amministratore locale (resta solo sul tuo PC).

### Verifica
- [ ] La pagina di login di Open WebUI si apre correttamente
- [ ] Il modello Ollama scaricato è visibile e selezionabile
- [ ] Una domanda di prova riceve una risposta coerente
- [ ] Accesso verificato anche da un secondo dispositivo (altro PC o telefono) sulla stessa rete Wi-Fi

### Problemi comuni
| Problema | Causa |
|---|---|
| Open WebUI non trova nessun modello | `OLLAMA_BASE_URL` errato, o Ollama non configurato su `0.0.0.0` (Fase 9) |
| Pagina bianca o errore 502 | Il container ha impiegato più tempo del previsto ad avviarsi |

### Come risolverli
```bash
docker logs brainos-openwebui
docker exec -it brainos-openwebui curl http://host.docker.internal:11434/api/tags
```

### Cosa fare dopo
Procedi alla Fase 11.

---

## Fase 11 — Vault Obsidian (versione locale)

### Obiettivo
Collegare Obsidian al vault. In questa versione domestica, senza sincronizzazione multi-dispositivo avanzata: il vault è già versionato in Git dalla Fase 6, il che è sufficiente per iniziare.

### Cosa fare
Scegli in base a dove si trova Obsidian rispetto al vault:

- **Stesso PC**: apri Obsidian direttamente sulla cartella `/data/brain/vault`.
- **Altro PC della rete**: condividi `/data/brain/vault` come cartella di rete (SMB/Samba), oppure clona il repository Git locale su quel PC.

### Comandi
Se usi un altro PC via Git (metodo consigliato, identico a quello usato in `Claude-02.md`, Fase 11):
```bash
mkdir -p /data/git/brain.git
cd /data/git/brain.git
git init --bare
```
Sul PC client:
```bash
git clone ssh://brainadmin@192.168.1.50/data/git/brain.git ~/BrainVault
```
Collega il vault del server come remoto:
```bash
cd /data/brain/vault
git remote add origin /data/git/brain.git
git push origin main
```

### Configurazione
Apri Obsidian e scegli **"Apri cartella come vault"**, puntando a `/data/brain/vault` (stesso PC) oppure a `~/BrainVault` (PC client).

### Verifica
- [ ] Il vault è visibile e navigabile in Obsidian
- [ ] Se usi più dispositivi: una modifica fatta su un client, dopo `git push`, è visibile con `git pull` sul server

### Problemi comuni
| Problema | Causa |
|---|---|
| `git push` chiede la password ogni volta | Nessuna chiave SSH configurata |
| Conflitti di merge tra client e server | Modifiche parallele senza sincronizzare prima |

### Come risolverli
```bash
ssh-keygen -t ed25519 -C "brain-client"
ssh-copy-id brainadmin@192.168.1.50
```
Fai sempre `git pull` prima di iniziare a scrivere.

> Se in futuro vorrai sincronizzare **più di due dispositivi** in modo robusto, la sezione completa (con Nextcloud/Obsidian Sync come alternative) è già pronta in `Claude-02.md`, Fase 11 — stessi path, nessuna modifica necessaria.

### Cosa fare dopo
Procedi alla Fase 14 per i test.

---

## Fase 14 — Test end-to-end (versione ridotta)

### Obiettivo
Verificare che i componenti installati in questa versione domestica funzionino correttamente insieme.

### Comandi / Verifica
1. **Test Docker**:
```bash
docker ps
```
2. **Test Ollama**:
```bash
ollama list
```
3. **Test Open WebUI**: apri `http://192.168.1.50:3000` e invia una domanda di prova.
4. **Test vault Git** (se usi più dispositivi): modifica un file, `git push`, poi verifica con `git log` sul server.

> I test relativi a PostgreSQL, ChromaDB, VPN e reverse proxy non si applicano finché non attivi le Fasi 7, 8, 12 e 13 da `Claude-02.md`.

### Problemi comuni
Vedi le tabelle "Problemi comuni" di ciascuna Fase precedente.

### Come risolverli
Ripercorri la Fase corrispondente al componente che fallisce, controllando i log con `docker logs <nome-container>`.

### Cosa fare dopo
Procedi alla Fase 15 per il backup.

---

## Fase 15 — Backup

### Obiettivo
Proteggere il Brain — questa parte **non va mai saltata**, indipendentemente dalla scala del sistema. Identica a `Claude-02.md`.

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
Pianifica l'esecuzione giornaliera con `cron` (es. ogni notte alle 3:00):
```bash
crontab -e
```
Aggiungi:
```text
0 3 * * * /data/docker/backup.sh >> /data/backups/backup.log 2>&1
```

### Verifica
```bash
/data/docker/backup.sh
ls -la /data/backups/
```

### Problemi comuni
| Problema | Causa |
|---|---|
| Lo script cron non si esegue | Percorso errato nel crontab, o permessi di esecuzione mancanti |
| Il backup cresce troppo nel tempo | Nessuna rotazione configurata (già inclusa nello script sopra) |

### Come risolverli
```bash
grep CRON /var/log/syslog
ls -la /data/docker/backup.sh
```

12.3. **Copia periodicamente** la cartella `/data/backups` anche su una chiavetta USB o un hard disk esterno, per avere una seconda copia fisica separata dal PC (regola delle tre copie).

### Cosa fare dopo
A questo punto hai un Second Brain domestico funzionante. Per i prossimi passi, vai direttamente in `Claude-02.md` alle Fasi che ti interessano:

- **Vuoi accedere anche da fuori casa?** → Fase 12 (VPN con Tailscale)
- **Vuoi ricerca semantica sul vault?** → Fasi 7-8 (Postgres + ChromaDB)
- **Vuoi nomi tipo `brain.local` invece dell'IP?** → Fase 5 (DNS locale) e Fase 13 (reverse proxy)
- **Vuoi mantenere il sistema aggiornato nel tempo?** → Fase 16
- **Vuoi automazioni intelligenti (Brain Keeper)?** → Fase 17

In tutti i casi, i path (`/data/brain/...`, `/data/docker/...`) e i nomi dei container (`brainos-*`) sono già gli stessi: non serve alcuna migrazione, solo continuare da dove questa guida si è fermata.

---

## Checklist finale

- [ ] Ubuntu installato e funzionante (Fase 1)
- [ ] `/data` montato in modo persistente, struttura cartelle creata (Fase 3)
- [ ] Docker installato e funzionante (Fase 4)
- [ ] IP del PC reso stabile tramite riserva DHCP (Fase 5)
- [ ] Struttura completa del vault creata in `/data/brain/vault` (`Projects/`, `Knowledge/`, ecc.), con `AGENTS.md` e Git inizializzato (Fase 6)
- [ ] Ollama installato e almeno un modello scaricato (Fase 9)
- [ ] Open WebUI avviato e raggiungibile su `http://<ip-server>:3000` (Fase 10)
- [ ] Obsidian collegato al vault (Fase 11)
- [ ] Test end-to-end superati (Fase 14)
- [ ] Script di backup creato, testato e pianificato via cron (Fase 15)
- [ ] Copia di backup salvata anche su un supporto esterno

---

> 🎯 **Risultato finale di questa guida**: un Second Brain funzionante, interamente locale, con AI tramite Ollama e Open WebUI, vault Obsidian versionato in Git e backup automatico — costruito sulla stessa identica base (`/data`, Docker, nomi dei container) della versione completa in `Claude-02.md`, pronto per essere esteso senza modifiche quando vorrai aggiungere VPN, database o ricerca semantica.
