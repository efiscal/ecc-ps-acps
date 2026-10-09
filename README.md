# MANUAL DE INSTALARE A SISTEMULUI ECC S/P v1.2

---

## Cerințe de Sistem

| Cerinta | Descriere |
| --- | --- |
| Sistem de Operare | RockyLinux min. 9 / RHEL min. 9 / CentOS min. 9 / Ubuntu min. 22.04 sau alt tip/versiune ce suporta versiunea minima Docker |
| Docker Engine | Versiunea minima: **27.5.1** |
| Pachete necesare | `zip`, `git`, `jq` |
| Parametrii Tehnici | CPU (AMD64/ARM64 min. 6 Core-uri / 3000 MHz), RAM 16 GB, Disk Storage: min. 300 GB, Port 8443/TCP & 8443/UDP |
| Experienta | Minima: System Administrator |

---

## Sumele de Control (Checksums) SHA-256

| Arhitectură  | SHA-256                                                            |
| ------------ | ------------------------------------------------------------------ |
| LINUX/AMD64  | `8833dbb9358d8750e61502d19efe1dae2ea45b4a75052441e4dec07db24ae7f0` |
| LINUX/ARM64  | `04c645cccf31f79aeab30bf468faa7016a3850ffd2e79d2b8b2884e88753be1a` |

---

## Pregătirea Sistemului de Operare

OS-ul poate fi lansat prin mai multe modalități:

- Instalând sistemul operațional Linux propriu-zis pe hardware.
- Rulând un **WSL** (Windows Subsystem for Linux) cum ar fi Ubuntu sau RockyLinux în cadrul sistemului Windows (îl puteți instala de pe Microsoft Store).
- Virtualizând sistemul Linux în cadrul unui hypervizor.

---

## Pașii de Instalare și Lansare

### 1. Instalare Docker Engine

Instalați Docker Engine de pe site-ul oficial (versiunea minimă: **27.5.1**):

> [Instalare Docker Engine](https://docs.docker.com/engine/install/)

### 2. Activare AutoStart + Reboot

După instalare, este recomandat să activați AutoStart-ul și să faceți un reboot la OS pentru a fi aplicate regulile Firewall (iptables) corect.

```sh
systemctl enable docker --now
```

### 3. Verificare Docker

După reboot, asigurați-vă că serviciul Docker funcționează corect:

```sh
systemctl status docker
```

### 4. Instalare instrumente necesare

**RockyLinux / RHEL / AlmaLinux / CentOS / Fedora:**

```sh
dnf install git zip jq -y
```

sau

```sh
yum install git zip jq -y
```

**Ubuntu:**

```sh
apt update && apt install git zip jq -y
```

### 5. Clonare proiect

Clonați proiectul de pe GitHub sau îl puteți descărca accesând același link prin Browser:

```sh
git clone --depth 1 -b v1.1 https://github.com/efiscal/servicii-si-produse.git ecc-sp
```

### 6. Accesare folder

```sh
cd ecc-sp
```

### 7. Comenzi disponibile

Aflându-vă în cadrul folderului clonat, puteți executa următoarele comenzi:

| Acțiune                                        | Comandă                 |
| ---------------------------------------------- | ----------------------- |
| Creare și lansare ECC (background)             | `./bin/start.sh`        |
| Oprire ECC (fără eliminarea resurselor)        | `./bin/stop.sh`         |
| Restartare ECC (fără eliminarea resurselor)    | `./bin/restart.sh`      |
| Distrugere completă a resurselor ECC           | `./bin/destroy.sh`      |
| Distrugere completă ECC + volume cu informații | `./bin/destroy-data.sh` |
| Backup ECC (date + configurație)               | `./bin/backup.sh`       |
| Restaurare ECC dintr-un backup                 | `./bin/restore.sh`      |
| Generare checksum SHA-256                      | `./checksum.sh`         |
| Creare arhivă ECC                              | `./archive.sh`          |

### 8. Instalare licență

După lansarea sistemului pentru prima dată folosind `./bin/start.sh`, este nevoie de instalat licența care v-a fost emisă de către **Fiscal Partner**. Aveți licența pe disk-ul local, apoi încărcați-o prin Browser:

```text
https://localhost:8443
```

### 9. Backup și restaurare

Scripturile `./bin/backup.sh` și `./bin/restore.sh` salvează și restaurează volumele Docker cu datele sistemului (`app` și `db`), împreună cu configurația (`docker-compose.yml`, `.env` și folderul `docker/`).

> **Atenție:** ECC este oprit pe durata backup-ului și a restaurării volumelor, pentru ca baza de date să fie salvată într-o stare consistentă, și este repornit automat la final. Planificați aceste operațiuni în afara orelor de lucru.

Scripturile folosesc imaginea Docker `alpine`, care este descărcată automat de pe Docker Hub la prima rulare.

#### 9.1. Backup

```sh
./bin/backup.sh
```

Implicit, backup-ul este salvat în folderul `./backups` din proiect. Pentru a-l salva în alt loc (de exemplu, pe un disc extern), folosiți opțiunea `-d` / `--dir` sau variabila `BACKUP_DIR`. Căile relative sunt calculate față de folderul proiectului.

```sh
./bin/backup.sh -d /mnt/backup/ecc
```

sau

```sh
BACKUP_DIR=/mnt/backup/ecc ./bin/backup.sh
```

Ce face scriptul:

1. Verifică dacă există suficient spațiu liber pe disc. Dacă nu există, se oprește **înainte** de a opri ECC.
2. Salvează configurația curentă: `docker-compose.yml`, `.env`, folderul `docker/` și versiunile imaginilor care rulează.
3. Oprește ECC, arhivează volumele `app` și `db` în fișiere `.tar.gz` și verifică integritatea fiecărei arhive.
4. Pornește din nou ECC. Dacă apare o eroare după oprire, încearcă să îl repornească automat.

Fiecare backup este salvat într-un subfolder separat, denumit după data și ora la care a fost făcut (`AAAALLZZ-HHMMSS`):

```text
backups/
└── 20261009-143000/
    ├── app-20261009-143000.tar.gz
    ├── db-20261009-143000.tar.gz
    └── config/
        ├── docker-compose.yml
        ├── .env
        ├── docker/
        ├── containers-20261009-143000.json
        └── compose-config-20261009-143000.json
```

Backup-urile vechi nu sunt șterse automat. Eliminați manual subfolderele de care nu mai aveți nevoie.

> **Securitate:** backup-ul conține toate datele sistemului și fișierul `.env`. Păstrați-l într-o locație sigură, cu acces restricționat.

#### 9.2. Restaurare

```sh
./bin/restore.sh
```

Fără argumente, scriptul afișează backup-urile disponibile (cel mai recent primul) și vă cere să alegeți unul. Puteți indica direct backup-ul dorit și, opțional, folderul în care se află:

```sh
./bin/restore.sh 20261009-143000
```

```sh
./bin/restore.sh -d /mnt/backup/ecc 20261009-143000
```

Restaurarea este interactivă:

1. Scriptul propune un backup al stării curente înainte de restaurare (implicit **DA**, apăsați `Enter`). Vă recomandăm să îl acceptați: dacă ați ales un backup greșit, puteți reveni la starea anterioară.
2. Scriptul cere confirmare separat pentru fiecare element: volumul `app`, volumul `db`, `docker-compose.yml`, `.env` și folderul `docker/`. Implicit răspunsul este **NU**; tastați `y` pentru a restaura elementul respectiv.
3. Arhivele selectate sunt verificate înainte de orice modificare. Dacă o arhivă este coruptă, restaurarea se oprește fără a modifica nimic.
4. Pentru volume: ECC este oprit, conținutul volumului este **înlocuit complet** cu cel din backup, apoi ECC este repornit.
5. Pentru fișierele de configurare: versiunea curentă este păstrată alături, cu sufixul `.bak-<backup>` (de exemplu `.env.bak-20261009-143000`), înainte de a fi suprascrisă.

Dacă ați restaurat fișiere de configurare, containerele care rulează folosesc încă vechea configurație. Aplicați configurația restaurată cu:

```sh
docker compose up -d
```

> Această comandă poate recrea containerele cu versiunile de imagini din configurația restaurată.

Pentru lista completă de opțiuni:

```sh
./bin/backup.sh --help
./bin/restore.sh --help
```

### 10. Informații suplimentare

Pentru mai multe detalii, vă rugăm să citiți **manualul oficial de instalare** care v-a fost transmis de Fiscal Partner.
