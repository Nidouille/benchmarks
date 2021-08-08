# **Benchmark d'hyperviseurs**

# 1 Etat des lieux

## 1.1 Matériel de test

* Carte mère : [AsrockRack X470D4U](https://www.asrockrack.com/general/productdetail.asp?Model=X470D4U#Specifications)
* CPU : AMD Ryzen 5 3600 6-Core
* Ram : 64 Go DDR4 ECC
* SSD : TCSUNBOW SSD 480GB en sata
* Carte réseau : Chelsio 

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

# 2 Pré requis

## 2.1 Hyperviseurs

Les hyperviseurs ce doivent d'être à jours sans aucun réglage d'optimisation

## 2.2 les VM

Les VM ce doivent être à jours avec les adons invité quand cela est possible et éventuellement le noyau cloud si disponible (Debian)

* vCPU : 4
* vRAM : 4 Go
* vDisk : 50 Go