# **Benchmark d'hyperviseurs**

# **1 Introduction**

Les benchmarks seront effectués via la solution de Phoronix : [Phoronix Test Suite](http://phoronix-test-suite.com) et les résultats uploadé sur le site affilié [OpenBenchmarking](https://openbenchmarking.org).

## 1.1 Matériel de test

* Carte mère : [AsrockRack X470D4U](https://www.asrockrack.com/general/productdetail.asp?Model=X470D4U#Specifications)
* CPU : [AMD Ryzen 5 3600 6-Core](https://www.amd.com/en/products/cpu/amd-ryzen-5-3600)
* Ram : 64 Go DDR4 ECC
* SSD : [Samsung 980 1Tb M.2](https://www.samsung.com/fr/memory-storage/nvme-ssd/980-1tb-nvme-pcie-gen-3-mz-v8v1t0bw/)
* Carte réseau : Intel I210AT 1Gb

## 1.2 Les hyperviseurs

* [XCP-NG 8.2](https://xcp-ng.org)
* [Proxmox Ve 7.0](https://www.proxmox.com)
* [ESXi 7](https://www.vmware.com )
* [Hyper-V Server 2019](https://www.microsoft.com/en-us/evalcenter/evaluate-hyper-v-server-2019)

  

## 1.3 OS testés

* [AlmaLinux](https://almalinux.org) 8.4
* [Debian](http://www.debian.org/) 11
* [FreeBSD](https://www.freebsd.org) 13
* [Ubuntu](https://ubuntu.com) 20.04 LTS
* [Windows Server](https://www.microsoft.com/fr-fr/windows-server) 2019
* Windows Server 2022

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



### 2.1.2 ESXi



### 2.1.2 Hyper-V

Utilisation de la version de Hyper-V 2019 Core disponible en téléchargement gratuit sur le site de Microsoft [Evaluation Center](https://www.microsoft.com/en-us/evalcenter/evaluate-hyper-v-server-2019) 

#### 2.1.2.1 Paramétrage de l'utilisation a distance

##### 2.1.2.1.1 Sur le serveur Hyper-V

Passage de l'interface réseau en Private

```powershell
Get-NetConnectionProfile
```

Capture d'écran

```powershell
Set-NetConnectionProfile -InterfaceIndex 4 -NetworkCategory Private
```

Capture d'écran

```powershell
Enable-PSRemoting
Enable-WSManCredSSP -Role Server
```

##### 2.1.2.1.1 Sur le client Windows 10

Installation de la console de gestion Hyper-V

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Tools-All -All
```

Contrôler que l'interfasse réseau du poste est en private

```powershell
Get-NetConnectionProfile
```

Capture d'écran

```powershell
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "fqdn-of-hyperv-host"

Enable-WSManCredSSP -Role Client -DelegateComputer "fqdn-of-hyperv-host"
```

Capture d'écran





Désactivation du firewall

#### 2.1.2.10 Désactivation de l'IPv6

Je désactive l'IPv6 car non utilisé dans mon infrastructure de test.
Pour cela lister les interfaces réseaux disponibles

```powershell
Get-NetAdapter
Get-NetAdapterBinding -Name "nom de l'interface qui nous intéresse"
```



```powershell
Disable-NetAdapterBinding -Name "Ethernet0 2" -ComponentID ms_tcpip6 -PassThru
```



## 2.2 VM

Les VM CE doivent être à jours avec les addons invités quand cela est possible et éventuellement le noyau cloud si disponible (Debian)

* vCPU : 4
* vRAM : 4 Go
* vDisk : 50 Go

### 2.2.1 AlmaLinux

```shell
dnf install php-cli php-xml php-json wget bash-completion expat expat-devel tar gcc autoconf automake cmake-data unzip bzip2 bzip2-devel gnupg2 expat flex libevent-devel libgpg-error-devel libgcrypt-devel libzip-devel ncurses-devel perl perl-Time-HiRes perl-utils openssl openssl-devel pcre-devel zlib-devel libgcc gcc-c++ tcl bison 
```

#### *2.2.1.1 Installation de la suite Phroronix test suite*

```shell
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
deb http://deb.debian.org/debian bullseye-backports main contrib non-free
deb-src http://deb.debian.org/debian bullseye-backports main contrib non-free
```

```shell
apt update && apt upgrade -y && apt install linux-image-5.10.0-0.bpo.8-cloud-amd64 wget qemu-guest-agent php-cli php-xml
```

#### 2.2.2.2 Ubuntu

```shell
apt update && apt upgrade -y && apt install wget curl git 7zip php-cli php-xml unzip curl git gnupg gnupg2
```

#### 2.2.2.2.1 Hyper-V

```shell
apt install -y linux-image-azure
```

#### 2.2.2.2.1 KVM

```shell
apt install -y linux-image-kvm
```



#### *2.2.2.3 Installation de la suite Phoronix test suite*

```shell
wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.4.0.tar.gz
tar xzvf phoronix-test-suite-10.4.0.tar.gz
cd phoronix-test-suite
./install-sh
```

### 2.2.3 FreeBSD

```shell
pkg install bash wget php74 php74-dom php74-zip php74-json php74-simplexml php74-openssl gcc sudo p7zip
```

#### *2.1.3.1 Installation de la suite Phoronix test suite*

```shell
wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.4.0.tar.gz
tar xzvf phoronix-test-suite-10.4.0.tar.gz
cd phoronix-test-suite
./install-sh
```

#### *2.2.3.2 XEN*

```
pkg install xen-guest-tools xe-guest-utilities 
freebsd-update fetch
freebsd-update install
```

#### *2.2.3.3 KVM*

```
pkg install -y qemu-guest-agent bash wget php74 php74-dom php74-zip php74-json php74-simplexml php74-openssl gcc sudo p7zip
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

cf KM : <https://kb.vmware.com/s/article/2149806>




### 2.2.4 Windows

#### *2.2.4.1 Installation de la suite Phoronix test suite*

```powershell
Invoke-WebRequest https://github.com/phoronix-test-suite/phoronix-test-suite/archive/v10.4.0.zip -OutFile c:\v10.4.0.zip
expand-Archive c:\v10.4.0.zip C:\
cd C:\phoronix-test-suite-10.4.0\
.\phoronix-test-suite.bat
```



#### *2.2.4.2 XEN*

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

#### 2.3.7.1 Apache

Apache HTTP Server 2.4.48:
    pts/apache-2.0.0
    System Test Configuration
        1: 1
        2: 20
        3: 100
        4: 200
        5: 500
        6: 1000
        7: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Concurrent Requests: **3**

#### *2.3.7.2 BlogBench 1.1*

> BlogBench 1.1:
>    pts/blogbench-1.1.0
>    Disk Test Configuration
>          1: Read
>          2: Write
>          3: Test All Options
>          \*\* Multiple items can be selected, delimit by a comma. \*\*
>          Test: **3**

#### *2.3.7.3 Iperf 3.7*

> iPerf 3.7:
>    pts/iperf-1.1.1
>    Network Test Configuration
>    Server Address [Use 'localhost' if wishing to benchmark the local system/server performance.]
> 
>    Enter Value: **192.168.1.27**
> 
>    Server Port [The default iperf3 server port is 5201.]
> 
>    Enter Positive Number: **5201**
> 
>    ​      1: 10 Seconds
>    ​      2: 30 Seconds
>    ​      3: 60 Seconds
>    ​      4: 360 Seconds
>    ​      5: Test All Options
>    ​      \*\* Multiple items can be selected, delimit by a comma. \*\*
>    ​      Duration: 4
> 
> ​      1: TCP
>    ​      2: UDP
>    ​      3: UDP - 100Mbit Objective
>    ​      4: UDP - 1000Mbit Objective
>    ​      5: Test All Options
>    ​      \*\* Multiple items can be selected, delimit by a comma. \*\*
>    ​      Test: **1**
>    
> 
> ​      1: 1
>    ​      2: 5
>    ​      3: 10
>    ​      4: 20
>    ​      5: 32
>    ​      6: 64
>    ​      7: Test All Options
>    ​      \*\* Multiple items can be selected, delimit by a comma. \*\*
>    ​      Parallel: **4**

#### *2.3.7.4 John The Ripper*

>           John The Ripper 1.9.0-jumbo-1:
>           pts/john-the-ripper-1.7.2
>           Processor Test Configuration
>                     1: MD5
>                     2: Blowfish
>                     3: Test All Options
>                     \*\* Multiple items can be selected, delimit by a comma. \*\*
>                     Test: **3**

#### 2.3.7.5 OpenSSL

OpenSSL 3.0:
    pts/openssl-3.0.1
    Processor Test Configuration
        1: RSA4096
        2: SHA256
        3: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Algorithm: **3**

#### *2.3.7.6 t-test1*

>           t-test1 2017-01-13:
>           pts/t-test1-1.0.1
>           Memory Test Configuration
>                     1: 1
>                     2: 2
>                     3: Test All Options
>                     \*\* Multiple items can be selected, delimit by a comma. \*\*
>                     Threads: **3**

#### 2.3.7.7 nginx

nginx 1.21.1:
    pts/nginx-2.0.0
    System Test Configuration
        1: 1
        2: 20
        3: 100
        4: 200
        5: 500
        6: 1000
        7: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Concurrent Requests: **3**

#### *2.3.7.8 mysqlslap*

> 

#### *2.3.7.9 pgbench* 

> PostgreSQL pgbench 13.0:
>     pts/pgbench-1.10.2
>     System Test Configuration
>         1: 1
>         2: 100
>         3: 1000
>         4: 10000
>         5: Test All Options
>         ** Multiple items can be selected, delimit by a comma. **
>         Scaling Factor: **1**
>
> ​        1: 1
> ​       2: 50
> ​       3: 100
> ​       4: 250
> ​       5: Test All Options
> ​       ** Multiple items can be selected, delimit by a comma. **
> ​       Clients: **3**
>
> ​       1: Read Write
> ​       2: Read Only
> ​       3: Test All Options
> ​       ** Multiple items can be selected, delimit by a comma. **
> ​       Mode: **3**

# **3 Résultats**


## 3.1 Liens vers les résultats

Date : du 9 septembre 2021 au ?
Description : 



## 3.1.1 FreeBSD 13

| Tests                         | Résultats sur OpenBenchmarking                             |
| :---------------------------- | ---------------------------------------------------------- |
| Physique UFS                  | https://openbenchmarking.org/result/2109170-IB-FREEBSDAS89 |
| Physique ZFS                  | https://openbenchmarking.org/result/2109123-IB-NIDOUILLE82 |
| ESXi 7u1 UFS                  | https://openbenchmarking.org/result/2109137-IB-NIDOUILLE37 |
| ESXi 7u1 ZFS                  |                                                            |
| Hyper-V Server 2019 gen 1 UFS | https://openbenchmarking.org/result/2109180-IB-FREEBSDHY81 |
| Hyper-V Server 2019 gen 2 UFS | https://openbenchmarking.org/result/2109176-IB-FREEBSD1798 |
| Hyper-V Server 2019 gen 1 ZFS | https://openbenchmarking.org/result/2109185-IB-FREEBSDHY02 |
| Hyper-V Server 2019 gen 2 ZFS |                                                            |
| Proxmox 7 UFS                 |                                                            |
| Proxmox 7                     |                                                            |
| XCP-ng 8.2 UFS                | https://openbenchmarking.org/result/2109143-IB-BSDUFSXCP55 |
| XCP-ng 8.2 ZFS                | https://openbenchmarking.org/result/2109147-IB-FREEBSDZF84 |



## 3.1.2 Debian 11

| Tests                     | Résultats sur OpenBenchmarking |
| ------------------------- | ------------------------------ |
| Physique                  |                                |
| ESXi 7u1                  |                                |
| Hyper-V Server 2019 gen 1 |                                |
| Hyper-V Server 2019 gen 2 |                                |
| Proxmox 7                 |                                |
| XCP-ng 8.2                |                                |



## 3.1.3 AlmaLinux 8.4

| Tests                     | Résultats sur OpenBenchmarking |
| ------------------------- | ------------------------------ |
| Physique                  |                                |
| ESXi 7u1                  |                                |
| Hyper-V Server 2019 gen 1 |                                |
| Hyper-V Server 2019 gen 2 |                                |
| Proxmox 7                 |                                |
| XCP-ng 8.2                |                                |



## 3.1.4 Ubuntu 20.04 LTS

| Tests                     | Résultats sur OpenBenchmarking |
| ------------------------- | ------------------------------ |
| Physique                  |                                |
| ESXi 7u1                  |                                |
| Hyper-V Server 2019 gen 1 |                                |
| Hyper-V Server 2019 gen 2 |                                |
| Proxmox 7                 |                                |
| XCP-ng 8.2                |                                |



## 3.1.5 Windows 2019 Server Core

| Tests                     | Résultats sur OpenBenchmarking |
| ------------------------- | ------------------------------ |
| Physique                  |                                |
| ESXi 7u1                  |                                |
| Hyper-V Server 2019 gen 1 |                                |
| Hyper-V Server 2019 gen 2 |                                |
| Proxmox 7                 |                                |
| XCP-ng 8.2                |                                |



## 3.1.6 Windows 2022 Server Core

| Tests                     | Résultats sur OpenBenchmarking |
| ------------------------- | ------------------------------ |
| Physique                  |                                |
| ESXi 7u1                  |                                |
| Hyper-V Server 2019 gen 1 |                                |
| Hyper-V Server 2019 gen 2 |                                |
| Proxmox 7                 |                                |
| XCP-ng 8.2                |                                |

