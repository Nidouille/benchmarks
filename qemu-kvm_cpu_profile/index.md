Quand on créait une VM dans Proxmox ou autre hyperviseur basé sur qemu/kvm (OpenNebula, oVirt, OpenStack, etc.), il faut choisir le type de processeur en choisissant un modèle.

Sur Proxmox 7, c'était le profil kvm64 et depuis Proxmox 8 c'est le profil x86-64-v2-AES.



![image-20240120183823583](C:\Users\nidal\AppData\Roaming\Typora\typora-user-images\image-20240120183823583.png)

Mais a quoi cela correspond t'il ?



# Modèle ou architecture de CPU

## Introduction

Je ne compte pas faire un historique sur les CPU 32 bits vu qu'ils ne sont plus en vente et intérêt me semble limité vu que les CPU sont 64 bits depuis environ 20 ans. C'est AMD qui a introduit le 64 bits dans le monde x86. Et voici un bref aperçu de l'introduction du 64 bits

AMD introduit le 64 bit en 2003 avec les Atlon64 (architecture K8)

Intel passe partiellement au 64 bit en 2004 avec les Pentium 4 Prescott ayant la lettre F dans leur nomenclature (architecture NetBurst + Intel 64). Puis en 2006 tout ses processeurs sont 64  bit avec Core2 (architecture Core)

VIA Technologies sort encore des processeurs, son premier processeur 64 bits c'est le VIA Nano (architecture Isaiah) en 2008. Petit aparté, cela fait bien longtemps que l'on ne voit plus rien en Europe niveau CPU VIA ou provenant du fabriquant chinois Zhaoxin (joint venture avec Shanghai Municipal Government). 



## qemu/kvm

Pour faire simple et concis sur le sujet, une infographie pour comprendre le fonctionnement.

Ceux qui veulent aller plus loin, les pages Wikipédia française et anglaise sont une base.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Kernel-based_Virtual_Machine.svg/1280px-Kernel-based_Virtual_Machine.svg.png" alt="undefined" style="zoom: 33%;" />



## Evolutions

Depuis l'introduction du 64 bits dans le CPU x86, l'architecture a évolué petit à a petit chez les 2 principaux fournisseurs de processeurs X86 : Intel & AMD. 

J'ai fait un petit tableau très sommaire sur l'évolution de l'architecture x86-64

| Architecture | Instructions (non exhaustive)     | CPU                                     |
| ------------ | --------------------------------- | --------------------------------------- |
| x86-64-v1    | MMX, SSE, SSE2, etc.              | tout les CPU x86-64                     |
| x86-64-v2    | SSE3, SSE4, etc.                  | Intel Nehalem et AMD Jaguar             |
| x86-64-v3    | AVX, AVX2, etc.                   | Intel Haswell et AMD Excavator          |
| x86-64-v4    | AVX512F, AVX512BW, AVX512CD, etc. | Intel Skylake-X, Skylake-SP et AMD ZEN4 |

Pour les personnes qui sont avec des CPU serveurs neuf dans le cadre profetionel, pas de soucis à ce faire avec le profil par défaut, mais vous avez intérêt a changer pour une version plus à jours que le profil par défaut avec l'x86-64-v2-AES.

Ceux qui font du homelab ou du professionnel en utilisant du matériel de seconde main ou ancien, le profil par défaut peut être le mieux adapté. La probalité que vous n'avez pas de support de l'AES dans votre processeur est très faible, ca voudrait dire que vous avez du matériel de plus de 15 ans.

## AES

Depuis le début de l'article, je cite AES dans les processeurs, mais dans les fait je devrais dire jeux d'instruction AES.

Le jeux d'instruction vous l'aurez parfaitement compris permet accélérer matériellement les opération de chiffrement et déchiffrement AES.

Intel a introduit le jeux d'instruction AES en 2010 via l'architecture Westmere, c'est les CPU Core (I3/I5/I7) de première génération ou les XEON (E56xx,L56xx,X56xx)

AMD à supporté le jeux d'instruction AES en 2011 via les processeur sous architecture Bulldozer via les CPU AMD FX (FX-41xx,FX-61xx,FX-81xx) et les Opteron (42xx, 62xx).

Cyrix, a introduit aussi par ses CPU Nano (architecture  VIA Isaiah) en 2009.

## AES-NI

Je fais une digression sur l'AES-NI qui est l'évolution du jeux d'instruction AES.

Evolution est un bien grand mot, pour les calcul lié au chiffrement et déchiffrement de l'AES, une nouvelle méthode a vu le jours via l'utilisation du jeux d'instruction AVX512

Si votre VM effectue beaucoup de calcul lié a l'AES (tunnel OpenSSL) et que les développeurs et que les mainteneurs de votre distribution Linux ont correctement fait leur travail (toussote) en supportant l'AVX, vous obtiendrez de meilleur performance en utilisant sous Proxmox x86-64-v3.

## Autres instructions CPU

Il existe des jeux d'instruction connue et d'autre bien plus obscure mais ayant un impact sur les performances des VM.

Voici quelques une qui me semble intéressante d'évoquer succinctement.

+ [MMX](https://en.wikipedia.org/wiki/MMX_(instruction_set)) : 1997, orienté tache d'accélération sur le traitement d'image ou le son.
+ [SSE](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions) : 1999, calcul en virgule flottante 
+ [SSE2](https://en.wikipedia.org/wiki/SSE2) : 2000
+ [SSE3](https://en.wikipedia.org/wiki/SSE3) : 2005
+ [SSE4](https://en.wikipedia.org/wiki/SSE4) : 2006
+ [AVX](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions) : 2008, calcul vectoriel
+ [AVX2](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions) : 2013, permet une amélioration de l'AVX en passant de 128 à 256 bits et de nouvelles instructions
+ [AVX512](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions) : 2013, on est sur du 512bits
+ [RDRAND](https://en.wikipedia.org/wiki/RDRAND) : 2012, envoie des nombre aléatoire pour les générateurs de nombre aléatoire matériel
+ [FSGSBASE](https://www.phoronix.com/news/Linux-5.3-FSGSBASE) : 2012, registre de contrôle
+ [IBPB](https://www.intel.com/content/www/us/en/developer/articles/technical/software-security-guidance/technical-documentation/indirect-branch-predictor-barrier.html) : atténuation de la baisse de performance lié aux correctif Meltdown et Spectre (2017)

## OS

Certains système d'exploitation demande une version spécifique du jeux d'instruction x86-64 comme par exemple Red Hat Enterprise Linux 9, CentoS 9 requièrent x86-64-v2 et il est question que pour la version 10 ca soit x86-64-v3.
Chez Canonical, il y a une version expérimental de Ubuntu compilé avec le jeux d'instruction x86-64-v3.

Je vous passe le monde BSD ou Gentoo ou l'on peut construire sont OS avec une architecture spécifique
