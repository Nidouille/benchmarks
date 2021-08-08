# **Benchmark d'hyperviseurs**

# 1 Introduction

Les benchmarks seront effectués via la solution de Phoronix : [Phoronix Test Suite](http://phoronix-test-suite.com) et les résultats uploadé sur le site affilié [OpenBenchmarking](https://openbenchmarking.org)

## 1.1 Matériel de test

* Carte mère : [AsrockRack X470D4U](https://www.asrockrack.com/general/productdetail.asp?Model=X470D4U#Specifications)
* CPU : AMD Ryzen 5 3600 6-Core
* Ram : 64 Go DDR4 ECC
* SSD : TCSUNBOW SSD 480GB en sata
* Carte réseau : Chelsio T320

## 1.2 Les hyperviseurs

* [XCP-NG 8.4](https://xcp-ng.org)
* [Proxmox Ve 7.0](https://www.proxmox.com)
* ESXi 7
* KVM from scratch
* Hyper-V 2019
* ThrueNAS avec bhyve

## 1.3 OS testés

* Debian 10
* Debian 11 (quand la sortie officiel sera faite)
* FreeBSD 13
* AlmaLinux (mauvaise connaissance du monde Red Hat)
* Windows Serer 2019 (soucis de méthodologie)

# 2 Méthodologie

## 2.1 Hyperviseurs

Les hyperviseurs ce doivent d'être à jours sans aucun réglage d'optimisation

### 2.1.1 Proxmox

Modifier le repository pour passer sur la version gratuite comme indiquer dans la [documentation officiel](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#sysadmin_package_repositories)

https://pve.proxmox.com/pve-docs/pve-admin-guide.html#sysadmin*package*repositories

```
apt update && apt full-update -y && apt autoremove -y
```

### 2.1.2 XCP-NG

```
yum update -y
```

## 2.2 VM

Les VM ce doivent être à jours avec les adons invités quand cela est possible et éventuellement le noyau cloud si disponible (Debian)

* vCPU : 4
* vRAM : 4 Go
* vDisk : 50 Go

### 2.2.1 Debian

L'installation de base est la plus minimal possible en partition ext4 dans un seul volume

![alt text](https://github.com/Nidouille/benchmarks/blob/main/pics/debian-1.png)

Installation des backports

```
deb http://deb.debian.org/debian buster-backports main contrib non-free
```

```
apt update && apt upgrade -y && apt install linux-image-5.10.0-0.bpo.8-cloud-amd64 wget qemu-guest-agent php-cli php-xml
wget http://phoronix-test-suite.com/releases/repo/pts.debian/files/phoronix-test-suite_10.4.0_all.deb
dpkg -i phoronix-test-suite_10.4.0_all.deb
```

### 2.2.2 FreeBSD

#### 2.2.2.1 XEN

```
pkg install xen-guest-tools xe-guest-utilities bash wget php74 php74-dom php74-zip php74-json php74-simplexml php74-openssl
freebsd-update fetch
freebsd-update install
wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.4.0.tar.gz
tar xzvf phoronix-test-suite-10.4.0.tar.gz
cd phoronix-test-suite
./install-sh
phoronix-test-suite install apache blogbench compress-7zip iperf john-the-ripper openssl sqlite-speedtest phpbench t-test1 gnupg mysqlslap nginx
phoronix-test-suite benchmark apache blogbench compress-7zip iperf john-the-ripper openssl sqlite-speedtest phpbench t-test1 gnupg mysqlslap nginx
```

#### 2.2.2.2 KVM

```
pkg install qemu-guest-agent bash wget php74 php74-dom php74-zip php74-json php74-simplexml php74-openssl
service qemu-guest-agent start
freebsd-update fetch
freebsd-update install
wget https://phoronix-test-suite.com/releases/phoronix-test-suite-10.4.0.tar.gz
tar xzvf phoronix-test-suite-10.4.0.tar.gz
cd phoronix-test-suite
./install-sh
phoronix-test-suite install apache blogbench compress-7zip iperf john-the-ripper openssl sqlite-speedtest phpbench t-test1 gnupg mysqlslap nginx
phoronix-test-suite benchmark apache blogbench compress-7zip iperf john-the-ripper openssl sqlite-speedtest phpbench t-test1 gnupg mysqlslap nginx
```
Ajouter les lignes suivante pour que l'agent qemu ce lance a chaque démarage dans : /etc/rc.conf file
```
qemu_guest_agent_enable="YES"
qemu_guest_agent_flags="-d -v -l /var/log/qemu-ga.log"
```

#### 2.2.2.3 ESXi/vSphere

```
pkg install open-vm-tools-nox11  
```
Et dans /etc/rc.conf
```
vmware-guestd_enable="YES
```
cf KM : https://kb.vmware.com/s/article/2149806

## 2.3 Tests à effectuer

[Phoronix Test Suite](http://phoronix-test-suite.com) permet de tester un large panel de paramètre qui vont des applications système, au CPU, la mémoire, le réseau, le stockage.

NB : sous Windows ou FreeBSD on a des tests qui ne peuvent être réalisé. Je fais au mieux pour obtenir quelque choses d'homogène et représentatif.

NB² : les benchmarks sous Windows ce font via cygwin et peuvent donc apporter un biais face aux Linux et BSD.

2\.3.1 Processeur

2\.3.2 Mémoire

2\.3.3 Réseau

2\.3.4 Stockage
