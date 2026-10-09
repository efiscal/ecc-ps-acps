# MANUAL DE INSTALARE A SISTEMULUI ECC S/P v1.2

---

## Cerințe de Sistem

| Cerinta | Descriere |
| --- | --- |
| Sistem de Operare | RockyLinux min. 9 / RHEL min. 9 / CentOS min. 9 / Ubuntu min. 22.04 sau alt tip/versiune ce suporta versiunea minima Docker |
| Docker Engine | Versiunea minima: **27.5.1** |
| Pachete necesare | `zip`, `git` |
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
dnf install git zip -y
```

sau

```sh
yum install git zip -y
```

**Ubuntu:**

```sh
apt update && apt install git zip -y
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
| Generare checksum SHA-256                      | `./checksum.sh`         |
| Creare arhivă ECC                              | `./archive.sh`          |

### 8. Instalare licență

După lansarea sistemului pentru prima dată folosind `./bin/start.sh`, este nevoie de instalat licența care v-a fost emisă de către **Fiscal Partner**. Aveți licența pe disk-ul local, apoi încărcați-o prin Browser:

```text
https://localhost:8443
```

### 9. Informații suplimentare

Pentru mai multe detalii, vă rugăm să citiți **manualul oficial de instalare** care v-a fost transmis de Fiscal Partner.
