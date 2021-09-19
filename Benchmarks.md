# **Ne plus en tenir compte !**

# **Benchmark d'hyperviseurs**

# **1 Introduction**

Les benchmarks seront effectués via la solution de Phoronix : [Phoronix Test Suite](http://phoronix-test-suite.com) et les résultats uploadé sur le site affilié [OpenBenchmarking](https://openbenchmarking.org).

## 1.1 Matériel de test

* Carte mère : [AsrockRack X470D4U](https://www.asrockrack.com/general/productdetail.asp?Model=X470D4U#Specifications)
* CPU : AMD Ryzen 5 3600 6-Core
* Ram : 64 Go DDR4 ECC
* SSD : TCSUNBOW SSD 480GB en sata
* Carte réseau : Chelsio T320

## 1.2 Les hyperviseurs

* [XCP-NG 8.2](https://xcp-ng.org)
* [Proxmox Ve 7.0](https://www.proxmox.com)
* ESXi 7
* KVM from scratch
* Hyper-V 2019
* TrueNAS avec bhyve

## 1.3 OS testés

* AlmaLinux 8.4
* Debian 10
* Debian 11 (quand la sortie officielle sera faite)
* FreeBSD 13
* Ubuntu 20.04 LTS
* Windows Server 2019 (soucis de méthodologie)

# **2 Méthodologie**

## 2.1 Hyperviseurs

Les hyperviseurs ce doivent d'être à jours sans aucun réglage d'optimisation

### 2.1.1 Proxmox

Modifier le repository pour passer sur la version gratuite comme indiqué dans la [documentation officiel](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#sysadmin_package_repositories)

https://pve.proxmox.com/pve-docs/pve-admin-guide.html#sysadmin*package*repositories

```
apt update && apt full-update -y && apt autoremove -y
```

### 2.1.2 XCP-NG

```
yum update -y
```

## 2.2 VM

Les VM CE doivent être à jours avec les addons invités quand cela est possible et éventuellement le noyau cloud si disponible (Debian)

* vCPU : 4
* vRAM : 4 Go
* vDisk : 50 Go

### 2.2.1 AlmaLinux

```
dnf install php-cli php-xml php-json wget bash-completion expat expat-devel tar gcc autoconf automake cmake-data unzip bzip2 bzip2-devel gnupg2 expat flex libevent-devel libgpg-error-devel libgcrypt-devel libzip-devel ncurses-devel perl perl-Time-HiRes perl-utils openssl openssl-devel pcre-devel zlib-devel libgcc gcc-c++ tcl bison 
```

#### *2.2.1.1 Installation de la suite Phroronix test suite*

```
wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.4.0.tar.gz
tar xzvf phoronix-test-suite-10.4.0.tar.gz
cd phoronix-test-suite
./install-sh
```

### 2.2.2 Debian/Ubuntu

#### *2.2.2.1 Debian*

L'installation de base est la plus minimale possible en partition ext4 dans un seul volume
![alt text](https://github.com/Nidouille/benchmarks/blob/main/pics/debian-1.png)

Installation des backports

```
deb http://deb.debian.org/debian buster-backports main contrib non-free
```

```
apt update && apt upgrade -y && apt install linux-image-5.10.0-0.bpo.8-cloud-amd64 wget qemu-guest-agent php-cli php-xml
```

#### *2.2.2.2 Installation de la suite Phoronix test suite*

```
wget http://phoronix-test-suite.com/releases/repo/pts.debian/files/phoronix-test-suite_10.4.0_all.deb
dpkg -i phoronix-test-suite_10.4.0_all.deb
```

### 2.2.3 FreeBSD

```
pkg install bash wget php74 php74-dom php74-zip php74-json php74-simplexml php74-openssl gcc benchmarks/phoronix-test-suite
```

#### *2.1.3.1 Installation de la suite Phoronix test suite*

```
pkg install benchmarks/phoronix-test-suite
```

#### *2.2.3.2 XEN*

```
pkg install xen-guest-tools xe-guest-utilities 
freebsd-update fetch
freebsd-update install
```

#### *2.2.3.3 KVM*

```
pkg install qemu-guest-agent bash wget php74 php74-dom php74-zip php74-json php74-simplexml php74-openssl gcc benchmarks/phoronix-test-suite
service qemu-guest-agent start
freebsd-update fetch
freebsd-update install
```

Ajouter les lignes suivantes pour que l'agent qemu ce lance a chaque démarage dans : /etc/rc.conf file

```
qemu_guest_agent_enable="YES"
qemu_guest_agent_flags="-d -v -l /var/log/qemu-ga.log"
```

#### *2.2.3.4 ESXi*

```
pkg install open-vm-tools-nox11  
```

Et dans /etc/rc.conf

```
vmware-guestd_enable="YES
```

cf KM : <https://kb.vmware.com/s/article/2149806>


### 2.2.4 Windows

#### *2.2.4.1 Installation de la suite Phoronix test suite*

#### *2.2.4.2 XREN*

#### *2.2.4.3 KVM*

https://github.com/virtio-win/virtio-win-pkg-scripts

## 2.3 Benchmarks

[Phoronix Test Suite](http://phoronix-test-suite.com) permet de tester un large panel de paramètre qui vont des applications système, au CPU, la mémoire, le réseau, le stockage.

NB : sous Windows ou FreeBSD on a des tests qui ne peuvent être réalisés. Je fais au mieux pour obtenir quelque choses d'homogène et représentatif.

NB² : les benchmarks sous Windows se font via cygwin et peuvent donc apporter un biais face aux Linux et BSD.

### 2.3.1 Processeur

* pts/compress-7zip : 7-Zip Compression
* pts/john-the-ripper : John The Ripper
* pts/openssl : OpenSSL

### 2.3.2 Mémoire

* pts/t-test1 : t-test1

### 2.3.3 Réseau

* pts/iperf iPerf

### 2.3.4 Stockage

* pts/blogbench : BlogBench
* pts/sqlite : SQLite

### 2.3.5 System

* pts/apache : Apache Benchmark
* pts/gnupg : GnuPG
* pts/mysqlslap : MariaDB
* pts/nginx : NGINX Benchmark
* pts/pgbench : PostgreSQL pgbench
* pts/phpbench : PHPBench
* sqlite-speedtest : SQLite Speedtest

### 2.3.6 Commande pour installer les tests

```
phoronix-test-suite install apache blogbench compress-7zip iperf john-the-ripper openssl sqlite-speedtest phpbench t-test1 gnupg mysqlslap nginx pgbench
```

### 2.3.7 Commande pour lancer les tests

```
phoronix-test-suite benchmark apache blogbench compress-7zip iperf john-the-ripper openssl sqlite-speedtest phpbench t-test1 gnupg mysqlslap nginx pgbench
```

Il sera demandé des informations sur certains benchmarks, voici ceux que j'utilise.

#### *2.3.7.1 BlogBench 1.1*

> BlogBench 1.1:\
>       pts/blogbench-1.1.0\
>       Disk Test Configuration\
>             1: Read\
>             2: Write\
>             3: Test All Options\
>             \*\* Multiple items can be selected, delimit by a comma. \*\*\
>             Test: **3**

#### *2.3.7.2 Iperf 3.7*

> iPerf 3.7:\
>       pts/iperf-1.1.1\
>       Network Test Configuration\
>       Server Address [Use 'localhost' if wishing to benchmark the local system/server performance.]\
> \
>       Enter Value: **192.168.1.27**\
> \
>       Server Port [The default iperf3 server port is 5201.]\
> \
>       Enter Positive Number: **5201**\
> \
>             1: 10 Seconds\
>             2: 30 Seconds\
>             3: 60 Seconds\
>             4: 360 Seconds\
>             5: Test All Options\
>             \*\* Multiple items can be selected, delimit by a comma. \*\*\
>             Duration: 4\
> \
> \
>             1: TCP\
>             2: UDP\
>             3: UDP - 100Mbit Objective\
>             4: UDP - 1000Mbit Objective\
>             5: Test All Options\
>             \*\* Multiple items can be selected, delimit by a comma. \*\*\
>             Test: **1**\
> \
> \
>             1: 1\
>             2: 5\
>             3: 10\
>             4: 20\
>             5: 32\
>             6: 64\
>             7: Test All Options\
>             \*\* Multiple items can be selected, delimit by a comma. \*\*\
>             Parallel: **4**

#### *2.3.7.3 John The Ripper*

>           John The Ripper 1.9.0-jumbo-1:\
>           pts/john-the-ripper-1.7.2\
>           Processor Test Configuration\
>                     1: MD5\
>                     2: Blowfish\
>                     3: Test All Options\
>                     \*\* Multiple items can be selected, delimit by a comma. \*\*\
>                     Test: **3**

#### *2.3.7.4 t-test1*

>           t-test1 2017-01-13:\
>           pts/t-test1-1.0.1\
>           Memory Test Configuration\
>                     1: 1\
>                     2: 2\
>                     3: Test All Options\
>                     \*\* Multiple items can be selected, delimit by a comma. \*\*\
>                     Threads: **3**

#### *2.3.7.5 mysqlslap*

> 

#### *2.3.7.5 pgbench* 

> 

# **3 Résultats**

## 3.1 Première campagne de benchmark

Date : du 6 ou 10 aout 2021
Description : Cette première étape est pour prendre en mains et rectifier ma méthodologie.
Note : Les tests sous Debian ont pris une tournure non prévue sur twitter. Les résultats sous Proxmox 7.0 montraient un effondrement des performances sous MariaDB. Cela a affecté la méthodologie initialement mise en place et a évolué, c'est pour cela que ça ne respecte pas le plan décrit plus haut.

### 3.1.2 Almalinux

Un seul résultat : https://openbenchmarking.org/result/2108078-IB-XCPNGALMA47


### 3.1.2 Debian

Lien des résultats : https://openbenchmarking.org/result/2108096-IB-NIDOUILLE92,2108103-IB-NIDOUILLE13,2108073-IB-XCPNGDEBI13

![alt text](https://github.com/Nidouille/benchmarks/blob/main/pics/Resultats/Bench-set_1-Debian.png)

### 3.1.3 FreeBSD

Lien des résultats : https://openbenchmarking.org/result/2108108-IB-NIDOUILLE53,2108096-IB-NIDOUILLE44,2108088-IB-PROXMOXFR29,2108078-IB-XCPNGFEEB00

![alt text](https://github.com/Nidouille/benchmarks/blob/main/pics/Resultats/Bench-set_1-FreeBSD.png)


### 3.1.4 Conclusion

Comme indiqué plus haut, les benchmarks ne ce sont pas déroulé comme prévu et je suis dans l'obligation de refaire une seconde campagne de mesure.
Il en ressort tout de même deux choses intéressantes :

* XCP-NG 8.2 qui se trouve très proche de la solution de VMware ESXi
* Debian 10 sous Proxmox 6.4 et 7.0 a des résultats excessivement bas par rapport aux autres hypersiseurs.


## 3.1 Deuxieme campagne de benchmark

