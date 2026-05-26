## DHCP

ip a

## statikus IP állítása

sudo nmcli con show<br>
sudo nmcli con mod "1. vezetékes kapcsolat" ipv4.addresses 192.168.1.10/24<br>
sudo nmcli con mod "Wired connection 1" ipv4.method manual<br>
sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1<br>
sudo nmcli con up "Wired connection 1"<br>
`A kapcsolat sikeresen aktiválva[...]`<br>

## DHCP beállítás

Rocky <10:<br>
sudo dnf install dhcp-server -y<br>
sudo nano /etc/dhcp/dhcpd.conf<br>

csak ez legyen:
```bash
option domain-name "lab.local";
option domain-name-servers 8.8.8.8;

default-lease-time 600;
max-lease-time 7200;

authoritative;

subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option routers 192.168.1.1;
    option broadcast-address 192.168.1.255;
}
```

sudo nano /etc/sysconfig/dhcpd<br>
```bash
DHCPDARGS=enp0s8    
```
(megnézni ip a -val)<br><br>

sudo systemctl enable --now dhcpd<br><br>

systemctl status dhcpd<br><br>

journalctl -u dhcpd<br><br>


Rocky >=10:<br><br>

sudo dnf install kea -y<br>
sudo nano /etc/kea/kea-dhcp4.conf<br><br>

{
  "Dhcp4": {
    "interfaces-config": {
      "interfaces": [ "enp0s8" ]
    },

    "lease-database": {
      "type": "memfile",
      "persist": true,
      "name": "/var/lib/kea/dhcp4.leases"
    },

    "subnet4": [
      {
        "subnet": "192.168.1.0/24",
        "pools": [
          {
            "pool": "192.168.1.100 - 192.168.1.200"
          }
        ],

        "option-data": [
          {
            "name": "routers",
            "data": "192.168.1.1"
          },
          {
            "name": "domain-name-servers",
            "data": "8.8.8.8"
          }
        ]
      }
    ]
  }
}

systemctl enable --now kea-dhcp4<br><br>

systemctl status kea-dhcp4<br><br>

journalctl -u kea-dhcp4<br><br>


## Debian kliens

sudo apt install isc-dhcp-client -y<br>
sudo dhclient<br>
ip a<br>

