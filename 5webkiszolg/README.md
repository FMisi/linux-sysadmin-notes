## Webkiszolgáló

sudo dnf install httpd -y
sudo systemctl enable --now httpd

sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload

Teszt

Rocky-n:

curl localhost

Ha HTML jön → működik.

Debian kliensről

Browserben:

http://192.168.1.10

vagy:

http://rocky.lab.local

És meg kell jelennie az Apache test page-nek.


ha PHP modul is kell, akkor:

sudo dnf install php php-cli php-common -y
sudo systemctl restart httpd


Saját oldal:
sudo nano /var/www/html/index.html
VAGY PHP modullal:
sudo nano /var/www/html/index.php

Példa:
```php
<?php

$hostname = gethostname();

echo "<h1>$hostname webserver</h1><br><br>Szia!";

?>
```

Mentés után refresh.


fontos dolgok:

Apache troubleshooting:

systemctl status httpd
journalctl -u httpd
ss -tulpn

Config check:

apachectl configtest

SELinux issue esetén:

getenforce



Fontos:

Lehet még ott maradt:

/var/www/html/index.html

Az Apache általában azt preferálja előbb.

Szóval vagy:

töröld
nevezd át
sudo rm /var/www/html/index.html

Extra

Nagyon jó tudni:

php -v
apachectl -M | grep php

és logok:

journalctl -u httpd

