# 02 — Homelab con Proxmox: Fondamenta (BrainOS + NAS)

> Questa è la versione **Proxmox** della guida domestica. Sostituisce la versione precedente basata su Ubuntu Server "nudo": qui **Proxmox VE è l'hypervisor dell'host**, e sia BrainOS sia il NAS vivono in VM/container separati sopra di esso. Questo perché hai indicato di voler far crescere l'homelab nel tempo (nuovi servizi, nuove VM) — Proxmox è pensato esattamente per questo.
>
> **Continuità con `Claude-02.md`:** tutta la parte Docker/BrainOS descritta in `Claude-02.md` (Fasi 4, 6, 7, 8, 9, 10, 15, ecc.) resta valida **parola per parola** — semplicemente non gira più sull'host fisico, ma dentro la VM dedicata creata alla Fase 4 di questa guida. Dove in `Claude-02.md` leggi "sul server" o "sull'host", intendi "dentro la VM BrainOS". I path (`/data/brain/...`, `/data/docker/...`) e i nomi dei container restano identici.

---

## Cosa costruiamo

```
Hardware fisico (il tuo PC/mini PC)
         │
     Proxmox VE (hypervisor)
         │
   ┌─────┼──────────────┬──────────────────┐
   │                     │                  │
VM "brainos"        LXC "nas"          (future VM/LXC:
Ubuntu Server        Samba/NFS          Home Assistant,
+ Docker             + SSD esterno       altri esperimenti,
+ stack BrainOS      passthrough         ecc.)
(Ollama, Open WebUI,
 vault, ecc.)
         │
   Tailscale (VPN) sull'host Proxmox
   → accesso remoto a VM e LXC da fuori casa
```

---

## Indice delle fasi

| Fase | Titolo |
|---|---|
| 0 | Pianificazione e decisioni |
| 1 | Installazione di Proxmox VE |
| 2 | Accesso alla Web UI e SSH dell'host |
| 3 | Storage: disco di sistema e SSD esterno per il NAS |
| 4 | Decisione e creazione: dove gira BrainOS (VM vs LXC) |
| 5 | Rete: bridge Proxmox e IP statici |
| 6 | Struttura del Brain dentro la VM (rimando a Claude-02) |
| 7 | NAS: LXC dedicato con SSD esterno per foto/video |
| 8 | VPN centralizzata (Tailscale sull'host Proxmox) |
| 9 | Backup (VM/LXC + dati NAS) |

---

## Fase 0 — Pianificazione e decisioni

### Obiettivo
Decidere la struttura prima di installare qualunque cosa, per evitare di dover smontare macchine virtuali già in uso.

### Cosa fare
1. Conferma l'hardware che diventerà l'host Proxmox (PC/mini PC sempre acceso).
2. Prevedi almeno **due dischi**: uno interno per Proxmox + le VM (idealmente SSD, 128 GB+), e un **SSD esterno USB dedicato al NAS** (foto, video, backup) — quello che vuoi usare per lo spazio NAS.
3. Decidi la RAM da riservare: Proxmox stesso ha bisogno di poca RAM (1-2 GB), il resto va diviso tra la VM BrainOS e l'LXC NAS. Con 16 GB totali, un'allocazione ragionevole è: Proxmox host ~2 GB, VM BrainOS 6-8 GB, LXC NAS 1-2 GB, margine libero per altre VM future.

### Configurazione
Annota prima di iniziare:
- Nome host dell'host Proxmox (es. `pve-home`)
- IP che vuoi assegnare all'host Proxmox (es. `192.168.1.10`)
- Quale disco farà da storage NAS (l'SSD esterno)

### Verifica
- [ ] Hai chiaro quale disco è "sistema" e quale è "dati NAS"
- [ ] Hai un piano di allocazione RAM approssimativo

### Cosa fare dopo
Procedi alla Fase 1.

---

## Fase 1 — Installazione di Proxmox VE

### Obiettivo
Installare Proxmox VE come sistema operativo dell'host, al posto di Ubuntu Server.

### Cosa fare
1. Scarica l'ISO di **Proxmox VE** (ultima versione stabile) da `proxmox.com/downloads`.
2. Scrivi l'ISO su una chiavetta USB (Balena Etcher/Rufus su Windows, `dd` su Linux/macOS).
3. Avvia il PC da USB e segui l'installer grafico di Proxmox:
   - Disco di destinazione: **solo il disco interno** (non l'SSD esterno che riservi al NAS)
   - Paese/fuso orario/tastiera
   - Password di root e email amministrativa
   - Configurazione di rete: IP statico (usa quello annotato alla Fase 0), gateway = IP del router, DNS = IP del router o `1.1.1.1`
4. Completa l'installazione e riavvia, rimuovendo la chiavetta.

### Comandi
```bash
# Scrittura ISO su USB da terminale Linux/macOS (sostituisci /dev/sdX con il device corretto)
sudo dd if=proxmox-ve_8.x.iso of=/dev/sdX bs=4M status=progress oflag=sync
```

> ⚠️ Presta attenzione al device di destinazione: un errore può cancellare dati da un altro disco. **Non collegare ancora l'SSD esterno del NAS durante l'installazione**, per evitare di selezionarlo per errore come disco di sistema.

### Verifica
Al riavvio, la console mostra l'URL della Web UI, del tipo:
```
https://192.168.1.10:8006
```

### Problemi comuni
| Problema | Causa probabile |
|---|---|
| Il PC non avvia dalla USB | Ordine di boot nel BIOS/UEFI non corretto, o Secure Boot attivo |
| Proxmox non vede il disco giusto | Più dischi collegati contemporaneamente durante l'installazione |

### Come risolverli
Entra nel BIOS/UEFI, imposta l'avvio da USB, disabilita Secure Boot se necessario. Se hai selezionato il disco sbagliato, reinstalla dopo aver scollegato l'SSD del NAS.

### Cosa fare dopo
Procedi alla Fase 2.

---

## Fase 2 — Accesso alla Web UI e SSH dell'host

### Obiettivo
Amministrare Proxmox comodamente da un browser e, se serve, da terminale.

### Cosa fare
1. Da un PC della stessa rete, apri il browser su `https://192.168.1.10:8006` (il certificato è self-signed: accetta l'avviso di sicurezza).
2. Accedi con utente `root` e la password scelta in Fase 1.
3. (Opzionale) Connettiti via SSH per operazioni da riga di comando.

### Comandi
```bash
ssh root@192.168.1.10
```

### Verifica
- [ ] La Web UI di Proxmox si apre e mostra il nodo host nel pannello di sinistra
- [ ] L'accesso SSH funziona

### Problemi comuni
| Problema | Causa |
|---|---|
| "La tua connessione non è privata" nel browser | Normale: Proxmox usa un certificato self-signed. Puoi procedere in sicurezza in rete locale, o sostituirlo in seguito con Let's Encrypt |

### Cosa fare dopo
Procedi alla Fase 3.

---

## Fase 3 — Storage: disco di sistema e SSD esterno per il NAS

### Obiettivo
Preparare in Proxmox sia lo storage per le VM/LXC sia lo storage dedicato al NAS, tenendoli separati.

### Cosa fare
1. Collega ora l'SSD esterno che userai per il NAS.
2. In Proxmox, aggiungilo come storage dedicato, separato da quello di sistema.

### Comandi
Identifica il disco appena collegato:
```bash
lsblk
```

Se vuoi passarlo come disco intero a un LXC/VM (consigliato per semplicità), non serve formattarlo da Proxmox: lo formatterai dentro il container/VM che lo userà (vedi Fase 7). Se invece preferisci gestirlo direttamente dall'host come directory storage:

```bash
# Formatta in ext4 (solo se il disco è nuovo/vuoto)
sudo mkfs.ext4 /dev/sdb1

# Crea il punto di mount
sudo mkdir -p /mnt/nas-ssd

# Trova l'UUID
sudo blkid /dev/sdb1
```

Aggiungi a `/etc/fstab` (host Proxmox):
```text
UUID=UUID-DEL-TUO-DISCO   /mnt/nas-ssd   ext4   defaults   0   2
```
```bash
sudo mount -a
```

Registra la cartella come storage Proxmox (Web UI): **Datacenter → Storage → Add → Directory**, puntando a `/mnt/nas-ssd`. In questo modo diventa selezionabile anche per backup delle VM, non solo per il NAS.

### Verifica
```bash
df -h /mnt/nas-ssd
```
E nella Web UI: **Datacenter → Storage** deve mostrare il nuovo storage con lo spazio disponibile corretto.

### Problemi comuni
| Problema | Causa |
|---|---|
| L'SSD esterno non è riconosciuto | Cavo/porta USB difettosi, o disco con filesystem non supportato |
| Lo storage non appare nella Web UI dopo il mount | Manca la registrazione manuale in Datacenter → Storage |

### Cosa fare dopo
Procedi alla Fase 4.

---

## Fase 4 — Decisione e creazione: dove gira BrainOS

### Obiettivo
Scegliere consapevolmente come isolare lo stack BrainOS (Docker + Ollama + Open WebUI + vault), non solo "farlo partire".

### Le opzioni

| Opzione | Vantaggi | Svantaggi | Quando conviene |
|---|---|---|---|
| **VM dedicata (Ubuntu Server 24.04) con Docker dentro** — consigliata | Isolamento completo dal kernel host, snapshot Proxmox a livello di intera VM, nessun problema di compatibilità Docker (Docker richiede funzionalità del kernel che in LXC non privilegiati spesso mancano o vanno forzate), puoi spostarla su un altro nodo Proxmox in futuro | Overhead di RAM/CPU per il proprio kernel (qualche centinaio di MB in più rispetto a un LXC) | Scelta di default per un homelab che vuoi far crescere in sicurezza — è anche quella su cui è scritta tutta `Claude-02.md` |
| **LXC privilegiato con Docker dentro** | Più leggero, avvio quasi istantaneo, meno RAM sprecata | Richiede LXC *privilegiato* con nesting abilitato (perdi parte dell'isolamento che rende gli LXC sicuri), alcuni plugin di rete Docker possono dare problemi dentro LXC | Solo se l'hardware è davvero limitato (es. 8 GB RAM totali) e accetti il compromesso |
| **Bare metal sull'host Proxmox stesso** | Zero overhead | Mischi il ruolo di hypervisor con quello di servizio applicativo: un crash di un container Docker può impattare la stabilità dell'host che gestisce tutte le altre VM. Sconsigliato in un homelab pensato per espandersi | Da evitare |

**Raccomandazione:** VM dedicata. È la scelta più solida per un homelab che vuoi espandere (come hai detto tu stesso), permette snapshot/rollback di tutta la VM da Proxmox stesso, e ti consente di riprendere `Claude-02.md` senza alcun adattamento: quella guida presuppone proprio Ubuntu Server + Docker su una macchina dedicata.

### Cosa fare
Crea una VM Ubuntu Server 24.04 dedicata a BrainOS.

### Comandi / Configurazione (Web UI Proxmox)
1. **Create VM** in alto a destra.
2. **General**: Nome `brainos`.
3. **OS**: carica l'ISO di Ubuntu Server 24.04 (caricala prima in Datacenter → Storage locale → ISO Images, scaricandola da `ubuntu.com/download/server`).
4. **System**: lascia i default (Machine: q35, BIOS: OVMF se vuoi UEFI, altrimenti SeaBIOS va bene).
5. **Disks**: assegna almeno 32 GB sullo storage di sistema (non sull'SSD del NAS).
6. **CPU**: 2-4 core.
7. **Memory**: 6-8 GB (in base al piano fatto in Fase 0).
8. **Network**: bridge `vmbr0` (la rete di default, ponte verso la tua LAN).
9. Conferma e avvia la VM, poi installa Ubuntu Server come già descritto nelle guide precedenti (utente, password, OpenSSH abilitato).

### Verifica
- [ ] La VM `brainos` è visibile e in stato "Running" nella Web UI di Proxmox
- [ ] Riesci a fare SSH dentro la VM dal tuo PC principale, con un IP nella stessa LAN (es. `192.168.1.51`)

### Problemi comuni
| Problema | Causa |
|---|---|
| La VM non parte / errore "KVM not supported" | Virtualizzazione (VT-x/AMD-V) non abilitata nel BIOS dell'host |
| Nessuna rete dentro la VM | Bridge di rete sbagliato selezionato in fase di creazione |

### Come risolverli
Abilita VT-x/AMD-V nel BIOS/UEFI dell'host. Verifica in Proxmox che l'interfaccia della VM sia collegata a `vmbr0`.

### Cosa fare dopo
Procedi alla Fase 5. Da qui in poi, **tutto quello che riguarda Docker, Ollama, Open WebUI, la struttura del vault e il backup del Brain va eseguito dentro la VM `brainos`**, seguendo `Claude-02.md` dalla Fase 3 in poi (storage interno alla VM) — oppure, se vuoi la sequenza già ridotta per uso domestico, la versione semplificata che avevamo preparato prima, sempre eseguita dentro questa VM invece che sull'host fisico.

---

## Fase 5 — Rete: bridge Proxmox e IP statici

### Obiettivo
Avere IP prevedibili sia per l'host Proxmox sia per la VM BrainOS e il futuro LXC NAS.

### Cosa fare
1. Assicurati che host, VM `brainos` e (a breve) LXC `nas` abbiano tutti IP statici o riservati via DHCP, sulla stessa LAN.

### Configurazione
Nel router, aggiungi una riserva DHCP per ciascun MAC address (host Proxmox, VM brainos, LXC nas), oppure assegna IP statici direttamente dentro Ubuntu Server (VM) e dentro l'LXC.

Esempio di piano IP:
| Dispositivo | IP |
|---|---|
| Host Proxmox | `192.168.1.10` |
| VM `brainos` | `192.168.1.51` |
| LXC `nas` | `192.168.1.52` |

### Verifica
```bash
ping 192.168.1.10
ping 192.168.1.51
```
Entrambi devono rispondere in modo stabile anche dopo un riavvio.

### Cosa fare dopo
Procedi alla Fase 6.

---

## Fase 6 — Struttura del Brain dentro la VM

### Obiettivo
Non duplicare contenuto: questa fase è **identica** a quanto già descritto in `Claude-02.md` (Fase 6) o nella guida domestica semplificata, con l'unica differenza che i comandi vanno eseguiti dentro la VM `brainos` invece che sull'host fisico.

### Cosa fare
1. Fai SSH dentro la VM: `ssh brainadmin@192.168.1.51`
2. Segui da qui `Claude-02.md`, Fasi 3 (storage interno alla VM, es. `/data`), 4 (Docker), 6 (struttura cartelle), 9 (Ollama), 10 (Open WebUI), 15 (backup) — parola per parola, nessun path cambia.

### Cosa fare dopo
Una volta che BrainOS gira correttamente dentro la VM, procedi alla Fase 7 per il NAS.

---

## Fase 7 — NAS: LXC dedicato con SSD esterno per foto/video

### Obiettivo
Creare un piccolo spazio di rete per salvare foto, video e altri file personali, separato da BrainOS, usando l'SSD esterno preparato in Fase 3.

### Cosa fare
Crea un container LXC leggero con Samba, e passa (o monta) l'SSD esterno al suo interno.

### Comandi / Configurazione (Web UI Proxmox)
1. **Create CT** (container LXC).
2. **General**: Nome `nas`, imposta una password.
3. **Template**: Debian 12 (leggero, ottimo per questo scopo).
4. **Disks**: 4-8 GB bastano per il sistema dell'LXC (i dati veri stanno sull'SSD esterno, montato come bind mount, vedi sotto).
5. **CPU**: 1 core. **Memory**: 512 MB - 1 GB.
6. **Network**: bridge `vmbr0`, IP statico (es. `192.168.1.52`, vedi Fase 5).

Dopo la creazione, per **passare l'SSD esterno all'LXC come bind mount** (se in Fase 3 l'hai montato su `/mnt/nas-ssd` dell'host), modifica la configurazione del container dall'host Proxmox:
```bash
nano /etc/pve/lxc/<ID-CONTAINER>.conf
```
Aggiungi una riga:
```text
mp0: /mnt/nas-ssd,mp=/mnt/nas-data
```
Riavvia l'LXC dalla Web UI.

Dentro l'LXC, installa Samba:
```bash
apt update
apt install -y samba
```

Configura la condivisione:
```bash
nano /etc/samba/smb.conf
```
Aggiungi in fondo:
```ini
[Foto-Video]
   path = /mnt/nas-data
   browseable = yes
   writable = yes
   guest ok = no
   valid users = tuoutente
```

Crea l'utente Samba e riavvia il servizio:
```bash
useradd -m tuoutente
smbpasswd -a tuoutente
systemctl restart smbd
```

### Verifica
Da un PC/telefono sulla stessa rete, apri Esplora File (Windows) o Finder (macOS) e cerca la condivisione:
```
\\192.168.1.52\Foto-Video     (Windows)
smb://192.168.1.52/Foto-Video (macOS/Linux)
```
Inserisci le credenziali create sopra e verifica di poter copiare un file di prova.

### Problemi comuni
| Problema | Causa |
|---|---|
| La cartella condivisa non appare | Servizio Samba non attivo, o firewall dell'LXC blocca le porte 445/139 |
| I file scritti non persistono dopo un riavvio dell'LXC | Il bind mount non è stato configurato correttamente nel file `.conf` del container |

### Come risolverli
```bash
systemctl status smbd
```
Verifica che `mp0` compaia nella configurazione del container (`cat /etc/pve/lxc/<ID>.conf` dall'host) e che `df -h /mnt/nas-data` dentro l'LXC mostri lo spazio reale dell'SSD esterno, non lo spazio del disco di sistema dell'LXC.

> 💡 Se in futuro vorrai funzionalità NAS più serie (RAID, snapshot ZFS, ridondanza contro dischi guasti), questo LXC leggero può essere sostituito con una VM dedicata **TrueNAS Scale**, mantenendo lo stesso ruolo nella rete (stesso IP, stessa condivisione) — è un aggiornamento indipendente da tutto il resto dell'homelab.

### Cosa fare dopo
Procedi alla Fase 8 per l'accesso da remoto.

---

## Fase 8 — VPN centralizzata (Tailscale sull'host Proxmox)

### Obiettivo
Poter raggiungere sia BrainOS (VM) sia il NAS (LXC) da fuori casa, con un'unica VPN, senza dover configurare Tailscale separatamente su ogni VM/LXC.

### Cosa fare
Installa Tailscale **sull'host Proxmox** e attivalo come "subnet router": in questo modo un solo punto di ingresso VPN dà accesso a tutta la rete interna (`192.168.1.0/24`), comprese le VM e gli LXC attuali e quelli che aggiungerai in futuro.

### Comandi
Sull'host Proxmox:
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --advertise-routes=192.168.1.0/24
```
Il comando restituisce un link: aprilo in un browser, autenticati con il tuo account Tailscale e autorizza il dispositivo.

### Configurazione
1. Sul pannello di amministrazione di Tailscale (`login.tailscale.com/admin/machines`), trova la macchina `pve-home` e **approva la route annunciata** (`192.168.1.0/24`) — è un passaggio manuale di sicurezza, non avviene automaticamente.
2. Sui dispositivi client (telefono, laptop), installa l'app Tailscale e accedi con lo stesso account.
3. (Opzionale ma consigliato) Attiva "Use Tailscale subnets" nelle impostazioni del client, se non è già automatico.

### Verifica
Da un dispositivo **fuori dalla rete di casa** (es. telefono con dati mobili, Tailscale attivo):
```
http://192.168.1.51:3000        → Open WebUI (BrainOS)
smb://192.168.1.52/Foto-Video   → NAS
```
Devono essere raggiungibili esattamente come se fossi in LAN, grazie al subnet routing.

### Problemi comuni
| Problema | Causa |
|---|---|
| Il dispositivo remoto non raggiunge le VM/LXC, solo l'host Proxmox | La route `192.168.1.0/24` non è stata approvata nel pannello admin di Tailscale |
| Connessione lenta | Tailscale sta passando da un relay invece che diretta (succede dietro NAT complessi) |

### Come risolverli
```bash
tailscale status
tailscale ping 192.168.1.51
```
Controlla nel pannello admin che la route sia contrassegnata come "Approved", non solo "Advertised".

> 💡 In alternativa, potresti installare Tailscale separatamente dentro la VM `brainos` e l'LXC `nas` invece che sull'host — più isolato (l'host Proxmox stesso non è raggiungibile da VPN) ma richiede configurarlo più volte. Il subnet routing sull'host resta la soluzione più comoda per un homelab in crescita, perché ogni nuova VM/LXC futura sarà automaticamente raggiungibile senza toccare di nuovo la configurazione VPN.

### Cosa fare dopo
Procedi alla Fase 9 per il backup.

---

## Fase 9 — Backup (VM/LXC + dati NAS)

### Obiettivo
Proteggere sia BrainOS sia i dati del NAS, sfruttando le funzionalità di backup native di Proxmox oltre allo script già usato dentro la VM.

### Cosa fare
1. Configura backup automatici **a livello di VM/LXC** direttamente da Proxmox (snapshot dell'intera macchina, non solo dei file).
2. Mantieni comunque il backup interno del vault (`Claude-02.md`, Fase 15), eseguito dentro la VM: sono due livelli complementari, non alternativi.

### Configurazione (Web UI Proxmox)
1. **Datacenter → Backup → Add**.
2. Storage di destinazione: lo storage `/mnt/nas-ssd` registrato in Fase 3 (o, meglio, un secondo disco esterno dedicato solo ai backup, se ne hai uno — non conviene backuppare le VM sullo stesso SSD dove giri anche il NAS in produzione).
3. Seleziona le VM/LXC da includere: `brainos` e `nas`.
4. Pianificazione: es. ogni notte alle 3:00.
5. Retention: mantieni, ad esempio, gli ultimi 7 backup giornalieri.

### Verifica
Dopo la prima esecuzione pianificata (o forzandola manualmente da **Backup → Run now**):
```bash
ls -la /mnt/nas-ssd/dump/
```
Dovresti vedere i file di backup delle VM/LXC.

### Problemi comuni
| Problema | Causa |
|---|---|
| Il backup fallisce per spazio esaurito | Lo storage di destinazione è troppo piccolo per contenere backup di VM + dati NAS insieme |
| Il backup è molto lento | Backup "full" ogni volta invece che incrementale; valuta il formato `zstd` con compressione e, se disponibile, backup mode "snapshot" invece di "stop" |

### Come risolverli
Se possibile, usa un disco separato solo per i backup Proxmox, distinto sia dal disco di sistema sia dall'SSD dati del NAS — è la vera "regola delle tre copie" applicata a un homelab con Proxmox: dati live, backup locale su disco separato, e idealmente una terza copia offsite (cloud cifrato o disco portato fisicamente altrove).

### Cosa fare dopo
A questo punto hai: Proxmox come base, BrainOS isolato in una VM dedicata (pronta per tutte le Fasi avanzate di `Claude-02.md`), un NAS leggero per foto/video su SSD esterno, accesso VPN unificato a tutto tramite Tailscale, e backup sia a livello di VM sia a livello di vault. Da qui puoi aggiungere altre VM/LXC in futuro (Home Assistant, altri esperimenti) senza toccare nulla di quanto già costruito.

---

## Checklist finale

- [ ] Proxmox VE installato e Web UI raggiungibile
- [ ] SSD esterno collegato e riconosciuto come storage separato
- [ ] Deciso e creato: VM dedicata `brainos` (Ubuntu Server + Docker)
- [ ] Dentro la VM, seguite le Fasi rilevanti di `Claude-02.md` (storage, Docker, vault, Ollama, Open WebUI)
- [ ] IP statici assegnati a host, VM brainos e LXC nas
- [ ] LXC `nas` creato, SSD esterno montato via bind mount, condivisione Samba funzionante
- [ ] Tailscale installato sull'host Proxmox, route `192.168.1.0/24` approvata
- [ ] Accesso verificato da un dispositivo fuori casa (VPN) sia a Open WebUI sia al NAS
- [ ] Backup Proxmox configurato per VM e LXC, su storage separato da quello di produzione
- [ ] Backup interno del vault (script + cron dentro la VM) ancora attivo

---

> 🎯 **Risultato di questa guida**: un homelab basato su Proxmox, pronto a crescere: BrainOS isolato in una VM dedicata (compatibile al 100% con `Claude-02.md`), un piccolo NAS su SSD esterno per foto e video, e accesso remoto unificato via Tailscale a tutti i servizi presenti e futuri.
