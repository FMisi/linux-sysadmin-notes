# Routing — RHEL / Rocky Linux jegyzet

A routing célja:

* hálózatok összekötése
* csomagok továbbítása
* internetmegosztás
* gateway működés

---

# Fontos fogalmak

| Fogalom       | Jelentés                            |
| ------------- | ----------------------------------- |
| Router        | hálózatok között forgalmat továbbít |
| Gateway       | kijárat más hálózat felé            |
| NAT           | címfordítás                         |
| IP forwarding | csomagtovábbítás                    |

---

# Hálózati interfészek

```bash
ip addr
```

Vagy:

```bash
nmcli device status
```

---

# Routing tábla

```bash
ip route
```

Példa:

```text
default via 10.0.2.2 dev enp0s3
192.168.1.0/24 dev enp0s8
```

---

# Ping teszt

IP:

```bash
ping 8.8.8.8
```

DNS:

```bash
ping google.com
```

---

# IP forwarding

Bekapcsolás:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Ellenőrzés:

```bash
cat /proc/sys/net/ipv4/ip_forward
```

Tartósan:

```bash
sudo nano /etc/sysctl.conf
```

```conf
net.ipv4.ip_forward = 1
```

Betöltés:

```bash
sudo sysctl -p
```

---

# NAT / Masquerade

Bekapcsolás:

```bash
sudo firewall-cmd --add-masquerade --permanent
```

Reload:

```bash
sudo firewall-cmd --reload
```

Ellenőrzés:

```bash
sudo firewall-cmd --query-masquerade
```

---

# Static route

```bash
sudo ip route add 192.168.2.0/24 via 192.168.1.1
```

Törlés:

```bash
sudo ip route del 192.168.2.0/24
```

---

# nmcli

Kapcsolatok:

```bash
nmcli connection show
```

IP cím:

```bash
sudo nmcli connection modify enp0s8 \
ipv4.addresses 192.168.1.10/24 \
ipv4.method manual
```

DNS:

```bash
sudo nmcli connection modify enp0s8 \
ipv4.dns "8.8.8.8"
```

Kapcsolat újraindítása:

```bash
sudo nmcli connection up enp0s8
```

---

# Fontos troubleshooting

Routing:

```bash
ip route
```

Interfészek:

```bash
ip addr
```

Portok:

```bash
ss -tulpn
```

Firewall:

```bash
firewall-cmd --list-all
```

---

# tcpdump

DNS figyelés:

```bash
sudo tcpdump port 53
```

DHCP figyelés:

```bash
sudo tcpdump port 67
```

---

# Gyakori hibák

| Hiba               | Ok                           |
| ------------------ | ---------------------------- |
| nincs internet     | forwarding nincs bekapcsolva |
| ping megy, DNS nem | rossz DNS                    |
| connection refused | firewall                     |
| timeout            | routing vagy NAT hiba        |
