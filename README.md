# **Benchmark d'hyperviseurs**

# **1 Introduction**

Les benchmarks seront effectués via la solution de Phoronix : [Phoronix Test Suite](http://phoronix-test-suite.com) et les résultats uploadés sur le site affilié [OpenBenchmarking](https://openbenchmarking.org).

## 1.1 Matériel de test

### 1.1.1 Serveur de virtualisation

* Carte mère : [AsrockRack X470D4U](https://www.asrockrack.com/general/productdetail.asp?Model=X470D4U#Specifications)
* CPU : [AMD Ryzen 5 3600 6-Core](https://www.amd.com/en/products/cpu/amd-ryzen-5-3600)
* Ram : 64 Go DDR4 ECC
* SSD : [Samsung 980 1Tb M.2](https://www.samsung.com/fr/memory-storage/nvme-ssd/980-1tb-nvme-pcie-gen-3-mz-v8v1t0bw/)
* Carte réseau : Intel I210AT 1Gb

### 1.1.2 Réseau

- [Zyxell ‎XGS1210-12](https://www.zyxel.com/fr/fr/products_services/12-Port-Web-Managed-Multi-Gigabit-Switch-with-2-Port-2-5G-and-2-Port-10G-SFP--XGS1210-12/)

### 1.1.3 Serveur ou est hébergé iperf

* Carte mère : MSI MS-S0891
* CPU : Intel E3-1220L v3
* Ram : 4 Go DDR3 ECC
* SSD : Kingstow SSDNow V+200
* Carte réseau : Chelsio  T320 10GbE Dual Port Adapter
* OS : Debian 10

## 1.2 Les hyperviseurs

* [XCP-NG 8.2](https://xcp-ng.org)

* [Proxmox Ve 7.0](https://www.proxmox.com)

* [ESXi 7](https://www.vmware.com )

* [Hyper-V Server 2019](https://www.microsoft.com/en-us/evalcenter/evaluate-hyper-v-server-2019)

* [Hyper-V Server 2022](https://www.microsoft.com/fr-fr/evalcenter/evaluate-windows-server-2022)

  

## 1.3 OS testés

* [AlmaLinux](https://almalinux.org) 8.4
* [Debian](http://www.debian.org/) 11
* [FreeBSD](https://www.freebsd.org) 13
* [Ubuntu](https://ubuntu.com) 20.04 LTS
* [Windows Server](https://www.microsoft.com/fr-fr/windows-server) 2019
* [Windows Server 2022](https://www.microsoft.com/fr-fr/evalcenter/evaluate-windows-server-2022)

# **2 Méthodologie**

## 2.1 Hyperviseurs

Les hyperviseurs ce doivent d'être à jours sans aucun réglage d'optimisation.

### 2.1.1 Proxmox

#### 2.1.1.1 Configuration du repository communautaire 

Modifier le repository pour passer sur la version gratuite comme indiqué dans la [documentation officiel](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#sysadmin_package_repositories)

https://pve.proxmox.com/pve-docs/pve-admin-guide.html#sysadmin*package*repositories

```shell
apt update && apt full-update -y && apt autoremove -y
```

#### 2.1.1.2 Création d'une VM

Comme on est en installation pas défaut, les VM sont créées au format RAW dans un volume LVM.

Si vous clonez une VM, celle ci passera au format .qcow2 légèrement moins performante.

Mes réglages habituels et par réflexe sont le passage du contrôleur de stockage en **VirtIO Block** et de laisser le type de CPU en **kvm64**.

![](https://github.com/Nidouille/benchmarks/blob/main/pics/Proxmox/Proxmox-vm-1.png)
![](https://github.com/Nidouille/benchmarks/blob/main/pics/Proxmox/Proxmox-vm-2.png)
![](https://github.com/Nidouille/benchmarks/blob/main/pics/Proxmox/Proxmox-vm-3.png)
![](https://github.com/Nidouille/benchmarks/blob/main/pics/Proxmox/Proxmox-vm-4.png)
![](https://github.com/Nidouille/benchmarks/blob/main/pics/Proxmox/Proxmox-vm-5.png)
![](https://github.com/Nidouille/benchmarks/blob/main/pics/Proxmox/Proxmox-vm-6.png)
![](https://github.com/Nidouille/benchmarks/blob/main/pics/Proxmox/Proxmox-vm-7.png)
![](https://github.com/Nidouille/benchmarks/blob/main/pics/Proxmox/Proxmox-vm-8.png)

### 2.1.2 XCP-NG

```shell
yum update -y
```



### 2.1.2 ESXi



### 2.1.2 Hyper-V

Utilisation de la version de Hyper-V Server 2019 disponible en téléchargement gratuit sur le site de Microsoft [Evaluation Center](https://www.microsoft.com/en-us/evalcenter/evaluate-hyper-v-server-2019) 

#### 2.1.2.1 Paramétrage de l'utilisation a distance

source : [Remotely manage Hyper-V hosts with Hyper-V Manager](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/manage/remotely-manage-hyper-v-hosts)

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

Contrôler que l’interface réseau du poste est en private

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

### 2.1.2.2 Linux/FreeBSD

Microsoft a intégré le support d'Hyper-V dans certains OS comme FreeBSD et Ubuntu, ce qui permet l'utilisation de ces OS en mode full virtualisation dit generation 2.



## 2.2 VM

Les VM CE doivent être à jours avec les addons invités quand cela est possible et éventuellement le noyau cloud si disponible (Debian)

* vCPU : 4
* vRAM : 4 Go
* vDisk : 50 Go

### 2.2.1 AlmaLinux

#### 2.2.1.1 Désactiver Kdump

Cette opération se fait lors de installation de l'OS

![](https://github.com/Nidouille/benchmarks/blob/main/pics/Almalinix-Install-1.png)


#### 2.2.1.2 Pré requis

Installation du dépôt EPEL

```shell
dnf install -y epel-release && dnf update -y
```

Les dépendances

```shell
dnf install -y php-cli php-xml php-json wget bash-completion expat expat-devel tar gcc autoconf automake cmake-data unzip bzip2 bzip2-devel gnupg1 gnupg2 expat flex libevent-devel libgpg-error-devel libgcrypt-devel libzip-devel ncurses-devel perl perl-Time-HiRes perl-utils openssl openssl-devel pcre-devel zlib-devel libgcc gcc-c++ tcl bison golang npth libksba libassuan
```

#### 2.2.1.3 Hyper-V

```shell
dnf install -y hyperv-daemons hyperv-tools
```



#### *2.2.1.5 Installation de la suite Phroronix test suite*

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

##### 2.2.2.1.1 Installation des backports (facultatif)

```
deb http://deb.debian.org/debian bullseye-backports main contrib non-free
deb-src http://deb.debian.org/debian bullseye-backports main contrib non-free
```



##### 2.2.2.1.2 Prérequis

```shell
apt update && apt upgrade -y && apt install -y linux-image-cloud-amd64 wget unzip curl git gnupg gnupg2 p7zip qemu-guest-agent php-cli php-xml bash-completion cmake autoconf golang build-essential apt-file openssh-server
```

##### 2.2.2.1.3 Hyper-V

```shell
apt install -y hyperv-daemons
```

Source : [Supported Debian virtual machines on Hyper-V](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/supported-debian-virtual-machines-on-hyper-v) && [hyperv-daemons](https://packages.debian.org/fr/bullseye/hyperv-daemons) 

##### 2.2.2.1.4 KVM

```shell
apt install -y qemu-guest-agent
```

##### 2.2.2.1.5 XCP-ng

```shell
mount /dev/cdrom /mnt
/mnt/Linux/install.sh
```



#### 2.2.2.2 Ubuntu

```shell
apt update && apt upgrade -y && apt install wget curl git p7zip php-cli php-xml unzip curl git gnupg gnupg1 gnupg2 autoconf golang cmake build-essential libssl-dev apt-file
```

##### 2.2.2.2.1 Hyper-V

```shell
apt install -y linux-image-azure
```

Source : [Supported Ubuntu virtual machines on Hyper-V](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/supported-ubuntu-virtual-machines-on-hyper-v) 

##### 2.2.2.2.1 KVM

```shell
apt install -y linux-image-kvm qemu-guest-agent
```



#### *2.2.2.3 Installation de la suite Phoronix test suite*

```shell
wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.4.0.tar.gz
tar xzvf phoronix-test-suite-10.4.0.tar.gz
cd phoronix-test-suite
./install-sh
```

### 2.2.3 FreeBSD

#### 2.1.3.1 Procédure installation 

[Installation type de FreeBSD en net install](https://github.com/Nidouille/benchmarks/blob/main/VM-Instalation-FreeBSD.md)

#### 2.1.3.2 Pré requis

```shell
pkg install -y bash wget php74 php74-dom php74-zip php74-json php74-simplexml php74-openssl gcc sudo p7zip htop tmux
freebsd-update fetch
freebsd-update install
```

#### *2.1.3.3 Installation de la suite Phoronix test suite*

```shell
wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.4.0.tar.gz
tar xzvf phoronix-test-suite-10.4.0.tar.gz
cd phoronix-test-suite
./install-sh
```

#### *2.2.3.4 XEN*

```shell
pkg install xen-guest-tools xe-guest-utilities 
```

#### *2.2.3.5 KVM*

```shell
pkg install -y qemu-guest-agent
service qemu-guest-agent start
```

Ajouter les lignes suivantes pour que l'agent qemu se lance à chaque démarrage dans le fichier : /etc/rc.conf

```properties
qemu_guest_agent_enable="YES"
qemu_guest_agent_flags="-d -v -l /var/log/qemu-ga.log"
```

#### *2.2.3.6 ESXi*

```shell
pkg install open-vm-tools-nox11  
```

Et dans /etc/rc.conf

```properties
vmware-guestd_enable="YES"
```

cf KM : <https://kb.vmware.com/s/article/2149806>

#### 2.2.3.7 Hyper-V

Intégré dans le kernel .


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

[Phoronix Test Suite](http://phoronix-test-suite.com) permet de tester un large panel de paramètres qui vont des applications système, au CPU, la mémoire, le réseau, le stockage.

NB : sous Windows ou FreeBSD, on a des tests qui ne peuvent être réalisés. Je fais au mieux pour obtenir quelque chose d'homogène et représentatif.

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

### 2.3.5 Système

* pts/apache : Apache Benchmark
* pts/gnupg : GnuPG
* pts/mysqlslap : MariaDB
* pts/nginx : NGINX Benchmark
* pts/pgbench : PostgreSQL pgbench
* pts/phpbench : PHPBench
* sqlite-speedtest : SQLite Speedtest

### 2.3.6 Commande pour installer les tests

```shell
phoronix-test-suite install apache blogbench compress-7zip iperf john-the-ripper openssl sqlite-speedtest phpbench t-test1 gnupg mysqlslap nginx pgbench
```

### 2.3.7 Commande pour lancer les tests

```shell
phoronix-test-suite benchmark apache blogbench compress-7zip iperf john-the-ripper openssl sqlite-speedtest phpbench t-test1 gnupg mysqlslap nginx pgbench
```

Il sera demandé des informations sur certains benchmarks, voici ceux que j'utilise.

#### 2.3.7.1 Apache

```shell
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
```



#### *2.3.7.2 BlogBench 1.1*

```shell
BlogBench 1.1:
pts/blogbench-1.1.0
Disk Test Configuration
      1: Read
      2: Write
      3: Test All Options
      \*\* Multiple items can be selected, delimit by a comma. \*\*
      Test: **3**
```



#### *2.3.7.3 Iperf 3.7*

```shell
iPerf 3.7:
pts/iperf-1.1.1
Network Test Configuration
Server Address [Use 'localhost' if wishing to benchmark the local system/server performance.]

Enter Value: **192.168.1.27**

Server Port [The default iperf3 server port is 5201.]

Enter Positive Number: **5201**

       1: 10 Seconds
       2: 30 Seconds
       3: 60 Seconds
       4: 360 Seconds
       5: Test All Options
       \*\* Multiple items can be selected, delimit by a comma. \*\*
       Duration: 4

       1: TCP
       2: UDP
       3: UDP - 100Mbit Objective
       4: UDP - 1000Mbit Objective
       5: Test All Options
       \*\* Multiple items can be selected, delimit by a comma. \*\*
       Test: **1**


       1: 1
       2: 5
       3: 10
       4: 20
       5: 32
       6: 64
       7: Test All Options
       \*\* Multiple items can be selected, delimit by a comma. \*\*
       Parallel: **4**
```




#### *2.3.7.4 John The Ripper*

```shell
John The Ripper 1.9.0-jumbo-1:
pts/john-the-ripper-1.7.2
Processor Test Configuration
          1: MD5
          2: Blowfish
          3: Test All Options
          \*\* Multiple items can be selected, delimit by a comma. \*\*
          Test: **3**
```



#### 2.3.7.5 OpenSSL

```shell
OpenSSL 3.0:
    pts/openssl-3.0.1
    Processor Test Configuration
        1: RSA4096
        2: SHA256
        3: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Algorithm: **3**
```



#### *2.3.7.6 t-test1*

```shell
t-test1 2017-01-13:
pts/t-test1-1.0.1
Memory Test Configuration
          1: 1
          2: 2
          3: Test All Options
          \*\* Multiple items can be selected, delimit by a comma. \*\*
          Threads: **3**
```



#### 2.3.7.7 nginx

```shell
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
```



#### *2.3.7.8 mysqlslap*

```shell
MariaDB 10.6.4:
    pts/mysqlslap-1.2.0
    System Test Configuration
        1:  1
        2:  8
        3:  16
        4:  32
        5:  64
        6:  128
        7:  256
        8:  512
        9:  1024
        10: 2048
        11: 4096
        12: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Clients: **3**
```



#### *2.3.7.9 pgbench* 

```shell
PostgreSQL pgbench 13.0:
    pts/pgbench-1.10.2
    System Test Configuration
        1: 1
        2: 100
        3: 1000
        4: 10000
        5: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Scaling Factor: **1**

        1: 1
        2: 50
        3: 100
        4: 250
        5: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Clients: **3**

        1: Read Write
        2: Read Only
        3: Test All Options
        ** Multiple items can be selected, delimit by a comma. **
        Mode: **3**
```



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
| Hyper-V Server 2019 gen 1 UFS | https://openbenchmarking.org/result/2109202-IB-FREEBSDHY89 |
| Hyper-V Server 2019 gen 2 UFS | https://openbenchmarking.org/result/2109209-IB-FREEBSDHY16 |
| Hyper-V Server 2019 gen 1 ZFS | https://openbenchmarking.org/result/2109206-IB-FREEBSDHY29 |
| Hyper-V Server 2019 gen 2 ZFS | https://openbenchmarking.org/result/2109218-IB-FREEBSDHY04 |
| Hyper-V Server 2022 gen 1 UFS | https://openbenchmarking.org/result/2109285-IB-FREEBSDHY45 |
| Hyper-V Server 2022 gen 2 UFS |                                                            |
| Proxmox 7 UFS                 | https://openbenchmarking.org/result/2109263-IB-FREEBSDUF65 |
| Proxmox 7 ZFS                 | https://openbenchmarking.org/result/2109257-IB-FREEBSDPR86 |
| XCP-ng 8.2 UFS                | https://openbenchmarking.org/result/2109143-IB-BSDUFSXCP55 |
| XCP-ng 8.2 ZFS                | https://openbenchmarking.org/result/2109147-IB-FREEBSDZF84 |

Preview result :

UFS : https://openbenchmarking.org/result/2109269-IB-FREEBSD1385 & https://openbenchmarking.org/result/2109287-IB-FREEBSDHY41

Physical > ESXi > XCP-ng > Proxmox > Hyper-V 2022 > Hyper-V 2019

## 3.1.2 Debian 11

| Tests                     | Résultats sur OpenBenchmarking                             |
| ------------------------- | ---------------------------------------------------------- |
| Physique                  |                                                            |
| ESXi 7u1                  |                                                            |
| Hyper-V Server 2019 gen 1 | https://openbenchmarking.org/result/2109219-IB-DEBIANHYP47 |
| Hyper-V Server 2019 gen 2 | https://openbenchmarking.org/result/2109211-IB-DEBIANHYP70 |
| Hyper-V Server 2022 gen 1 | https://openbenchmarking.org/result/2109279-IB-DEBIANHYP63 |
| Hyper-V Server 2022 gen 2 | https://openbenchmarking.org/result/2109289-IB-DEBIENHYP30 |
| Proxmox 7                 | https://openbenchmarking.org/result/2109255-IB-DEBIAN11P85 |
| XCP-ng 8.2                |                                                            |



## 3.1.3 AlmaLinux 8.4

| Tests                     | Résultats sur OpenBenchmarking                             |
| ------------------------- | ---------------------------------------------------------- |
| Physique                  |                                                            |
| ESXi 7u1                  |                                                            |
| Hyper-V Server 2019 gen 1 | https://openbenchmarking.org/result/2109232-IB-ALMALINUX25 |
| Hyper-V Server 2019 gen 2 | https://openbenchmarking.org/result/2109239-IB-ALMALINUX35 |
| Hyper-V Server 2022 gen 1 | https://openbenchmarking.org/result/2109289-IB-ALMALINUX24 |
| Hyper-V Server 2022 gen 2 | https://openbenchmarking.org/result/2109295-IB-ALMALINUX40 |
| Proxmox 7                 | https://openbenchmarking.org/result/2109259-IB-ALMALINUX18 |
| XCP-ng 8.2                |                                                            |



## 3.1.4 Ubuntu 20.04 LTS

| Tests                     | Résultats sur OpenBenchmarking                             |
| ------------------------- | ---------------------------------------------------------- |
| Physique                  |                                                            |
| ESXi 7u1                  |                                                            |
| Hyper-V Server 2019 gen 1 | https://openbenchmarking.org/result/2109243-IB-UBUNTU20026 |
| Hyper-V Server 2019 gen 2 | https://openbenchmarking.org/result/2109231-IB-UBUNTU20067 |
| Hyper-V Server 2022 gen 2 |                                                            |
| Proxmox 7                 | https://openbenchmarking.org/result/2109254-IB-UBUNTUPRO72 |
| XCP-ng 8.2                |                                                            |



## 3.1.5 Windows 2019 Server Core

| Tests                     | Résultats sur OpenBenchmarking                             |
| ------------------------- | ---------------------------------------------------------- |
| Physique                  | https://openbenchmarking.org/result/2109266-IB-WINDOWS2048 |
| ESXi 7u1                  |                                                            |
| Hyper-V Server 2019 gen 1 | https://openbenchmarking.org/result/2109229-IB-WINDOWS2039 |
| Hyper-V Server 2019 gen 2 | https://openbenchmarking.org/result/2109199-IB-WIN2009SE38 |
| Hyper-V Server 2022 gen 1 |                                                            |
| Hyper-V Server 2022 gen 2 |                                                            |
| Proxmox 7                 | https://openbenchmarking.org/result/2109257-IB-WINDOWS2030 |
| XCP-ng 8.2                |                                                            |



## 3.1.6 Windows 2022 Server Core

| Tests                     | Résultats sur OpenBenchmarking                             |
| ------------------------- | ---------------------------------------------------------- |
| Physique                  | https://openbenchmarking.org/result/2109273-IB-WINDOWS2048 |
| ESXi 7u1                  |                                                            |
| Hyper-V Server 2019 gen 1 | https://openbenchmarking.org/result/2109243-IB-WINDOWS2099 |
| Hyper-V Server 2019 gen 2 | https://openbenchmarking.org/result/2109225-IB-WINDOWS2007 |
| Hyper-V Server 2022 gen 1 |                                                            |
| Hyper-V Server 2022 gen 2 |                                                            |
| Proxmox 7                 | Soucis d'adon invité                                       |
| XCP-ng 8.2                |                                                            |

