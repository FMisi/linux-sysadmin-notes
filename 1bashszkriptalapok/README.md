# Bash script alapok

A Bash egy Unix rendszerhéj (shell), amely a GNU projekt részeként készült.
A legtöbb Linux rendszeren alapértelmezett shell.

Mire jó?

* automatizálás
* karbantartási feladatok
* backupok
* service ellenőrzés
* rendszeradminisztráció

A Bash script fájlok kiterjesztése általában:

```bash
.sh
```

---

# Első program

Fájl létrehozása:

```bash
nano elso.sh
```

Tartalma:

```bash
#!/bin/bash

echo "Hello World, ez az első bash programom!"
```

Futtatás:

```bash
bash elso.sh
```

Vagy futtatási joggal:

```bash
chmod +x elso.sh
./elso.sh
```

---

# Második program

Hozzunk létre egy változót, ami két szám összegét tárolja, majd írassuk ki a tartalmát.

```bash
nano masodik.sh
```

```bash
#!/bin/bash

# Két szám összeadása
((osszeg=25+35))

# Eredmény kiíratása
echo $osszeg
```

Futtatás:

```bash
bash masodik.sh
```

---

# While ciklus

Számoljunk el 5-ig while ciklussal.

```bash
#!/bin/bash

szamlalo=1

while [ $szamlalo -le 5 ]
do
    echo $szamlalo
    ((szamlalo++))
done
```

---

# FOR ciklus

## Számolás 1-től 5-ig

```bash
#!/bin/bash

for (( szamlalo=1; szamlalo<=5; szamlalo++ ))
do
    echo -n "$szamlalo "
done

printf "\n"
```

---

## Számolás 10-től 1-ig

```bash
#!/bin/bash

for (( szamlalo=10; szamlalo>0; szamlalo-- ))
do
    echo -n "$szamlalo "
done

printf "\n"
```

---

# Adatok bekérése — read

## Név bekérése

```bash
#!/bin/bash

echo "Enter your name:"
read name

echo "Welcome $name"
```

---

## Téglalap kerülete és területe

```bash
#!/bin/bash

echo "A téglalap a oldala:"
read a

echo "A téglalap b oldala:"
read b

((terulet=a*b))
((kerulet=(a+b)*2))

echo "A téglalap területe: $terulet"
echo "A téglalap kerülete: $kerulet"
```

---

# IF és ELSE

## Számjegyek vizsgálata

```bash
#!/bin/bash

echo "Adj meg egy számot 0 és 100 között!"
read n

if [ $n -lt 10 ]
then
    echo "Az adott szám egy számjegyből áll."
else
    echo "Az adott szám több számjegyből áll."
fi
```

---

# ELIF használata

```bash
#!/bin/bash

n=107

if (( n < 10 ))
then
    echo "egy számjegyű"
elif (( n <= 99 ))
then
    echo "két számjegyű"
else
    echo "több mint 2 számjegyű"
fi
```

---

# Szöveges adatok vizsgálata

```bash
#!/bin/bash

echo "Enter username:"
read username

echo "Enter password:"
read password

if [[ $username == "admin" && $password == "secret" ]]
then
    echo "valid user"
else
    echo "invalid user"
fi
```

---

# Érdemjegyek — elif

```bash
#!/bin/bash

echo "Add meg az érdemjegyet:"
read n

if [ $n -eq 1 ]
then
    echo "elégtelen"
elif [ $n -eq 2 ]
then
    echo "elégséges"
elif [ $n -eq 3 ]
then
    echo "közepes"
elif [ $n -eq 4 ]
then
    echo "jó"
elif [ $n -eq 5 ]
then
    echo "jeles"
else
    echo "Csak 1-től 5-ig vannak az osztályzatok!"
fi
```

---

# Könyvtár létrehozása

```bash
#!/bin/bash

echo "Az új könyvtár neve:"
read ndir

if [ -d "$ndir" ]
then
    echo "Már létezik ilyen könyvtár."
else
    mkdir "$ndir"
    echo "Könyvtár létrehozva."
fi
```

---

# Fájl létezésének vizsgálata

```bash
#!/bin/bash

filename=$1

if [ -f "$filename" ]
then
    echo "Igen, létezik."
else
    echo "Nincs ilyen fájl."
fi
```

Futtatás:

```bash
bash script.sh fajlnev.txt
```

---

# CASE használata

```bash
#!/bin/bash

echo "Add meg az érdemjegyet:"
read n

case $n in
    1)
        echo "Elégtelen"
        ;;
    2)
        echo "Elégséges"
        ;;
    3)
        echo "Közepes"
        ;;
    4)
        echo "Jó"
        ;;
    5)
        echo "Jeles"
        ;;
    *)
        echo "1-5 közötti számot kérek!"
        ;;
esac
```

---

# Függvények

## Egyszerű függvény

```bash
#!/bin/bash

function Fuggvenyem() {
    echo "Szeretem a bash programozást!"
}

Fuggvenyem
```

---

# Függvény paraméterekkel

```bash
#!/bin/bash

teglalap_terulete() {
    terulet=$(($1 * $2))
    echo "A téglalap területe: $terulet"
}

teglalap_terulete 10 20
```

---

# Üdvözlő függvény

```bash
#!/bin/bash

udvozles() {
    echo "Üdv, $1!"
}

echo "Írd be a neved:"
read nev

udvozles "$nev"
```

Futtatás:

```bash
bash udvozlo_fuggveny.sh
```
