# Podman — RHEL / Rocky Linux jegyzet

A Podman egy modern container engine.

RHEL / Rocky világban gyakran:

* Docker helyett használják
* daemon nélkül működik
* rootless módot támogat
* Red Hat által támogatott

---

# Telepítés

```bash
sudo dnf install podman -y
```

Verzió:

```bash
podman --version
```

---

# Alap fogalmak

| Fogalom      | Jelentés              |
| ------------ | --------------------- |
| Image        | container template    |
| Container    | futó instance         |
| Registry     | image repository      |
| Volume       | persistent storage    |
| Port mapping | host ↔ container port |

---

# Image keresés

```bash
podman search nginx
```

---

# Image letöltése

```bash
podman pull nginx
```

Image-ek listázása:

```bash
podman images
```

---

# Container indítása

```bash
podman run nginx
```

Háttérben:

```bash
podman run -d nginx
```

---

# Port mapping

Host 8080 → container 80:

```bash
podman run -d -p 8080:80 nginx
```

Teszt:

```bash
curl localhost:8080
```

---

# Container lista

Futó:

```bash
podman ps
```

Összes:

```bash
podman ps -a
```

---

# Container leállítása

```bash
podman stop CONTAINER_ID
```

Törlés:

```bash
podman rm CONTAINER_ID
```

---

# Container shell

```bash
podman exec -it CONTAINER_ID bash
```

---

# Logok

```bash
podman logs CONTAINER_ID
```

Follow:

```bash
podman logs -f CONTAINER_ID
```

---

# Volume-ok

Volume létrehozása:

```bash
podman volume create nginx-data
```

Mount:

```bash
podman run -d \
-v nginx-data:/usr/share/nginx/html \
-p 8080:80 nginx
```

---

# Bind mount

Host könyvtár mountolása:

```bash
podman run -d \
-v /home/user/web:/usr/share/nginx/html:Z \
-p 8080:80 nginx
```

A `:Z` fontos SELinux miatt.

---

# Környezeti változók

```bash
podman run -e MYSQL_ROOT_PASSWORD=secret mysql
```

---

# Container törlés

```bash
podman rm -f CONTAINER_ID
```

---

# Rootless Podman

Normál userként:

```bash
podman run -d nginx
```

Ez nagy enterprise előny:

* kisebb attack surface
* nincs root daemon

---

# Podman generate systemd

Containerből systemd service:

```bash
podman generate systemd --name CONTAINER_ID
```

Nagyon RHEL-es workflow.

---

# Podman Compose

Docker-compose alternatíva:

```bash
sudo dnf install podman-compose -y
```

Indítás:

```bash
podman-compose up -d
```

---

# Registry login

```bash
podman login
```

---

# Saját image build

Dockerfile build:

```bash
podman build -t sajatapp .
```

---

# Container inspect

```bash
podman inspect CONTAINER_ID
```

---

# Networking

Hálózatok:

```bash
podman network ls
```

Új network:

```bash
podman network create sajathalo
```

---

# Resource limitek

CPU / RAM limit:

```bash
podman run \
--memory=512m \
--cpus=1 \
nginx
```

---

# SELinux és Podman

SELinux nagyon fontos Podmannál.

Container context:

```bash
ls -Z
```

Bind mountnál gyakran kell:

```text
:Z
```

különben:

```text
permission denied
```


---

# Fontos troubleshooting

Container lista:

```bash
podman ps -a
```

Logok:

```bash
podman logs CONTAINER_ID
```

Portok:

```bash
ss -tulpn
```

SELinux:

```bash
sudo ausearch -m AVC
```

---

# Hasznos cleanup

Nem használt container/image törlés:

```bash
podman system prune
```

---

# Podman vs Docker

| Podman           | Docker       |
| ---------------- | ------------ |
| daemonless       | daemon alapú |
| rootless         | gyakran root |
| SELinux friendly | kevésbé      |
| RHEL-native      | általánosabb |

---

# Enterprise szempontból fontos

A Podman:

* modern RHEL container stack
* DevOps alap
* CI/CD alap
* microservice alap

Nagyon gyakori:

* web app
* reverse proxy
* monitoring
* automation
* internal tools

containerizálása.

---

# Interjús fontos témák

* image vs container
* volume
* bind mount
* networking
* rootless container
* port mapping
* logs
* SELinux + Podman
* container troubleshooting

---

# Gyakori hibák

| Hiba                        | Ok                    |
| --------------------------- | --------------------- |
| permission denied           | SELinux               |
| port already in use         | port conflict         |
| container exits immediately | app crash             |
| nincs weboldal              | port mapping hiányzik |
| bind mount nem működik      | `:Z` hiányzik         |
