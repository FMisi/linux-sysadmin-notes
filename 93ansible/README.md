# Ansible — RHEL / Rocky Linux jegyzet

Az Ansible egy automation és configuration management tool.

Mire jó?

* szerverek automatizálása
* package install
* service management
* config deploy
* user kezelés
* több szerver egyszerre kezelése

Nagyon gyakori:

* Linux admin
* DevOps
* cloud
* enterprise infra

---

# Telepítés

Rocky / RHEL:

```bash
sudo dnf install epel-release -y
sudo dnf install ansible -y
```

Verzió:

```bash
ansible --version
```

---

# Alap fogalmak

| Fogalom   | Jelentés                    |
| --------- | --------------------------- |
| Inventory | host lista                  |
| Playbook  | YAML automation script      |
| Module    | konkrét művelet             |
| Task      | egy lépés                   |
| Role      | újrahasznosítható struktúra |

---

# Inventory

Fájl:

```bash
sudo nano /etc/ansible/hosts
```

Példa:

```ini
[linux]
192.168.1.10
192.168.1.20
```

---

# Ping teszt

```bash
ansible all -m ping
```

---

# SSH kapcsolat

Ansible SSH-t használ.

Ajánlott:

* SSH key
* PasswordAuthentication no

Kulcs generálás:

```bash
ssh-keygen
```

Kulcs másolás:

```bash
ssh-copy-id user@192.168.1.10
```

---

# Ad-hoc parancsok

Hostname:

```bash
ansible all -a "hostname"
```

Disk usage:

```bash
ansible all -a "df -h"
```

Uptime:

```bash
ansible all -a "uptime"
```

---

# Package install

Apache:

```bash
ansible all -m dnf -a "name=httpd state=present"
```

---

# Service kezelés

Apache indítás:

```bash
ansible all -m service -a "name=httpd state=started enabled=yes"
```

---

# User létrehozás

```bash
ansible all -m user -a "name=testuser state=present"
```

---

# File másolás

```bash
ansible all -m copy -a "src=index.html dest=/var/www/html/index.html"
```

---

# Playbook

Fájl:

```bash
nano apache.yml
```

Példa:

```yaml
---
- name: Apache install
  hosts: all
  become: true

  tasks:

    - name: Apache install
      dnf:
        name: httpd
        state: present

    - name: Apache start
      service:
        name: httpd
        state: started
        enabled: yes
```

Futtatás:

```bash
ansible-playbook apache.yml
```

---

# YAML alapok

Behúzás nagyon fontos:

```yaml
name: value
```

Space kell:

* tab nem ajánlott

---

# become

Root jog:

```yaml
become: true
```

---

# Gather facts

Ansible rendszerinfó gyűjtés:

```bash
ansible all -m setup
```

---

# Variables

```yaml
vars:
  package_name: httpd
```

Használat:

```yaml
name: "{{ package_name }}"
```

---

# Templates

Jinja2 template:

```bash
templates/index.j2
```

Példa:

```html
<h1>{{ ansible_hostname }}</h1>
```

Deploy:

```yaml
- name: template copy
  template:
    src: index.j2
    dest: /var/www/html/index.html
```

---

# Handlers

Service restart csak változáskor:

```yaml
handlers:

  - name: restart apache
    service:
      name: httpd
      state: restarted
```

---

# Firewall automation

```yaml
- name: open http
  firewalld:
    service: http
    permanent: true
    state: enabled
```

---

# SELinux automation

```yaml
- name: SELinux context
  sefcontext:
    target: '/web(/.*)?'
    setype: httpd_sys_content_t
    state: present
```

---

# Role-ok

Role létrehozás:

```bash
ansible-galaxy init apache
```

Struktúra:

* tasks
* templates
* handlers
* vars

---

# Inventory groupok

```ini
[web]
192.168.1.10

[db]
192.168.1.20
```

---

# Limit

Csak egy host:

```bash
ansible-playbook apache.yml --limit 192.168.1.10
```

---

# Dry run

```bash
ansible-playbook apache.yml --check
```

---

# Verbose mode

```bash
ansible-playbook apache.yml -vvv
```

---

# Fontos troubleshooting

Inventory:

```bash
ansible all --list-hosts
```

Ping:

```bash
ansible all -m ping
```

SSH debug:

```bash
ssh user@host
```

---

# Hasznos module-ok

| Module    | Mire jó         |
| --------- | --------------- |
| dnf       | package kezelés |
| service   | service kezelés |
| copy      | fájl másolás    |
| template  | template deploy |
| user      | user kezelés    |
| firewalld | firewall        |
| selinux   | SELinux         |
| command   | command run     |
| shell     | shell command   |

---

# Ansible best practice

* idempotent playbook
* SSH key auth
* role használat
* inventory groupok
* template-ek
* variables
* secrets külön kezelése

---

# Enterprise szempontból fontos

Az Ansible:

* configuration management
* automation
* infrastructure as code
* provisioning
* deployment

alap tool.

Nagyon gyakori:

* Linux admin
* DevOps
* cloud
* enterprise infra

munkákban.

---

# fontos témák

* inventory
* playbook
* YAML
* idempotency
* modules
* SSH auth
* roles
* variables
* templates
* automation

---

# Gyakori hibák

| Hiba                   | Ok                |
| ---------------------- | ----------------- |
| UNREACHABLE            | SSH probléma      |
| permission denied      | sudo vagy SSH key |
| YAML syntax error      | rossz indent      |
| service nem indul      | config hiba       |
| SELinux blocked        | context probléma  |
| playbook nem változtat | idempotent state  |
