# Gitlab PPProd scripts

## Introduction

Bienvenue sur le gitlab de l'[association PPProd](https://drive.google.com/file/d/1jZzIhDD1UfE6J6BTcf3pvz8nwBP4SeKP/view?usp=sharing) (PéPé PRODuction, loi 1901) ([règlement intérieur](https://drive.google.com/file/d/1FUASETBoaTokUenv5XXdv8vFmuOtbSHa/view?usp=sharing)).

Si vous avez accès à ce dépot, c'est que vous avez reçu votre login et password par mail. Conservez précieusement ces informations.

Le dépot scripts contient un ensemble de scripts shell et fichiers de configuration. Leurs buts est de rapidement déployer une infrastructure qui permet d'exploiter des services.

Ces services se découpent en 3 grosses familles :
- les services d'INFrastructures pures, comme un serveur [gitlab](https://fr.wikipedia.org/wiki/GitLab), un serveur [ldap](https://fr.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol), un serveur [tor](https://fr.wikipedia.org/wiki/Tor_(r%C3%A9seau)), ... (certains services, comme le [vpn](https://fr.wikipedia.org/wiki/R%C3%A9seau_priv%C3%A9_virtuel) sur [lokinet](https://loki.network/), sont à cheval entre les services infra et crypto)
- les services de CRYPTO-monnaie, comme un [bot de trading](https://www.ppprod.biz/gitlab/ppprod/ab4c), un serveur [mynode](https://mynodebtc.com/), des [nodes](https://academy.binance.com/fr/articles/what-are-nodes) de [staking](https://academy.binance.com/fr/articles/what-is-staking) ou le [DEX](https://en.wikipedia.org/wiki/Decentralized_exchange) [blocknet](https://docs.blocknet.co/),
- les services Multi-Média, comme une [seedbox](https://fr.wikipedia.org/wiki/Seedbox) ou un serveur [plex](https://www.plex.tv/fr/).

Les services de crypto sont décrits plus préciséments [ici](trading/README.md). 

## Licence

En devenant membre de l'association PPProd, vous avez accepté et signé 
- les termes de [la licence de ce projet](license.md) ([licence de l'association](https://drive.google.com/file/d/1RIolXZQcsCAHuJQn9r3PQD_NZvdJHOVy/view?usp=sharing), appliqué à tous les projets de l'[association PPProd](https://drive.google.com/file/d/1jZzIhDD1UfE6J6BTcf3pvz8nwBP4SeKP/view?usp=sharing))
- [la politique de contribution](contribution.md) ([politique de contribution de l'association](https://drive.google.com/file/d/1RIolXZQcsCAHuJQn9r3PQD_NZvdJHOVy/view?usp=sharing), appliqué à tous les projets de l'[association PPProd](https://drive.google.com/file/d/1jZzIhDD1UfE6J6BTcf3pvz8nwBP4SeKP/view?usp=sharing))

Dans les grandes lignes, cela signifie que tout ce qui est dans le [gitlab de l'association](https://www.ppprod.biz/gitlab/) appartient à l'association (donc à vous tant que vous êtes membre), et que tout ce que vous modifiez/écrivez doit être donné à l'association (donc à vous et aux autres membres).

## Organistation du dépot

Ce dépot contient :
- à la racine, des scripts et des fichiers de configuration communs à plusieurs machines ;
- dans les répertoires, par machines, les scripts d'installation et de configuration des services.

Avant de définir les machines, une brève présentation des sous-réseaux:
- *local*, qui porte le nom du domaine de votre box (/etc/localdomain sur manager),
- freenet, le sous-réseau construit à partir du fap (Free Access Point),
- hiddennet, le sous-réseau construit à partir du vpn (Virtual Private Network).

C'est 3 réseaux sont idéalement à séparer sur 3 switchs distincts.

Les machines de votre infrastructure locale doivent avoir des IPs prédéfinies, voici la liste :

| hostname                  | IP1/domain      | IP2                       | groupe
| ------------------------- | --------------- | ------------------------- | ------
| _votre box_               | 192.168.1.1     |                           | 
| [manager](#manager)       | 192.168.1.2     | 192.168.128.2/hidden      | [**INF0**](#inf0)
| [fap](#fap)               | 192.168.1.3     | 192.168.2.1/free          | [**INF3**](#inf3)
| [nut](#nut)               | 192.168.1.4     |                           | [**INF2**](#inf2)
| [gitlab](#gitlab)         | 192.168.1.5     |                           | [**MASTER**](#master)
| [grunner](#gitlab)        | 192.168.1.6     |                           | [**MASTER**](#master)
| [searcheng](#searcheng)   | 192.168.1.7     |                           | [**MASTER**](#master)
| [trade](#trade)           | 192.168.1.10    |                           | [**CRYP0**](#cryp0)
| [bigtrade](#trade)        | 192.168.1.10    |                           | [**CRYP0**](#cryp0)
| [btcnode](#btcnode)       | 192.168.1.11    |                           | [**CRYP3**](#cryp3), [**CRYP1**](#cryp1)
| [blocknode](#blocknode)   | 192.168.1.12    |                           | [**CRYP2**](#cryp2), [**CRYP3**](#cryp3)
| [litenode](#litenode)     | 192.168.1.13    |                           | [**CRYP2**](#cryp2), [**CRYP3**](#cryp3)
| [pivxnode](#pivxnode)     | 192.168.1.14    |                           | [**CRYP2**](#cryp2), [**CRYP3**](#cryp3)
| [dogenode](#dogenode)     | 192.168.1.15    |                           | [**CRYP3**](#cryp3)
| [sysnode](#sysnode)       | 192.168.1.16    |                           | [**CRYP3**](#cryp3)
| *RESERVED*                | 192.168.1.17    |                           | [**CRYP3**](#cryp3)
| [ethnode](#ethnode)       | 192.168.2.18    | 192.168.1.18              | [**CRYP4**](#cryp4)
| [hydranode](#hydranode)   | 192.168.1.19    | 192.168.2.19              | [**CRYP4**](#cryp4)
| [vpn](#vpn)               | 192.168.2.2/free  | 192.168.128.1/hidden    | [**INF4**](#inf4)
| [openwrt](#openwrt)       | 192.168.2.3/free  |                         | [**INF4**](#inf4)
| [pms](#pms)               | 192.168.1.20    | 192.168.129.1/hidden       | [**MM0**](#mm0)
| [seedbox](#seedbox)       | 192.168.128.21/hidden  | 192.168.129.2/hidden  | [**MM1**](#mm1)
| [lokivpn](#lokivpn)       | 192.168.2.30    | 192.168.1.30              | [**INF5**](#inf5)
| [sentvpn](#sentvpn)       | 192.168.2.31    | 192.168.1.31  | [**INF5**](#inf5)
| [mystvpn](#mystvpn)       | 192.168.2.32    | 192.168.1.32              | [**INF5**](#inf5)
| [xmrnode](#xmrnode)       | 192.168.2.39    | 192.168.1.39              | [**CRYP5**](#cryp5)
| [btcpays](#btcpays)       | 192.168.128.40  | 192.168.1.40              | [**CRYP5**](#cryp5)
| [btcnode](#mynode)        | 192.168.1.41    |                           | [**CRYP3**](#cryp3)
| [ethnode](#ethnode)       | 192.168.1.42    |                           | [**CRYP3**](#cryp3)
| bigtrade                  | 192.168.1.100   |                           |
| [nextcloud](#nextcloud)   | 192.168.1.120   |                           | [**MM0**](#mm0)
| jeedom                    | 192.168.1.140   |                           |
| zwavegwt                  | 192.168.1.141   |                           |
| borne wifi1               | 192.168.1.151   |                           |
| borne wifi2               | 192.168.1.152   |                           |
| borne wifi3               | 192.168.1.153   |                           |

*IP1 correspond à la prise réseau disponible sur la carte*


Pour les machines avec plusieurs interfaces réseau, je conseille ces [adapteurs USB3 ETH](https://www.amazon.fr/UGREEN-Adaptateur-Ethernet-Supporte-Supérieur/dp/B00MYTSN18/) ou [ceux-ci](https://www.amazon.fr/UGREEN-Adaptateur-Ethernet-100Mbps-Supporte/dp/B00MYT481C/).

Pensez aussi aux [doubleurs d'alimentation](https://www.amazon.fr/Logilink-CU0070-Câble-Mâle-Femelle/dp/B00DI6ZGAM/), il vous en faudra quelques uns (euphémisme...).

__les machines *Big*__

Il existe 2 types de machines *Big* : 
- *bignode*, qui vous permet de mutualiser des noeuds de la famille [**CRYP2**](#cryp2) et/ou [**CRYP3**](#cryp3) ( prévoir un 4, 8G ou 16G de mémoire **selon le type et le nombre de noeuds**)
- *bigvpn*, qui vous permet de mutualiser [lokivpn](#lokivpn) et [sentvpn](#sentvpn) (prévoir du **CPU** et minimum **4G** de mémoire,la famille des Raspberry n'ira pas, partir vers un Pine64 Rock64Pro ou un Pine64 Quartz, ou un ODroid XU4 de récup)
- *bigtrade*, qui est une machine *trade* configurée pour faire du multi-account.

*exemples :*
- pour co-locatiser block, pivx et litecoin, prévoir un Raspberry Pi4 8G,
- pour co-locatiser btc, block et litecoin, prévoir un Pine64 Quartz A ou B 8G,
- pour co-locatiser btc et eth, prévoir un Pine64 Quartz A 8G,
- pour co-locatiser btc, eth, pivx et litecoin, prévoir un Pine64 Quartz Pro 16G,

### Groupes

**Les groupes sont des sous-ensembles de machines qui forment un ensemble fonctionnel.**

Vous pouvez piocher _"vos services"_ selon vos envies/besoins.

groupe               | description
-------------------- | -----------
[**INF0**](#inf0)    | le point d'entrée sur votre infrastructure locale
[**INF2**](#inf2)    | la gestion d'un onduleur
[**INF3**](#inf3)    | délester le réseau local
[**INF4**](#inf4)    | completer le réseau avec un accès VPN (utile pour [MM1](#MM1) )
[**INF5**](#inf5)    | completer le réseau avec(des) VPN(s) crypto rémunéré(s) avec staking (noeud [lokinet](https://loki.network/) ( remplacant de [I2P](https://fr.wikipedia.org/wiki/I2P) ) avec [darkweb](https://fr.wikipedia.org/wiki/Darknet), noeud [sentinel](https://sentinel.co/) et neud [myst](#https://www.mysterium.network/mysteriumvpn))
[**MM0**](#mm0)      | serveur multimedia de streaming
[**MM1**](#mm1)      | système de téléchargement
[**CRYP0**](#cryp0)  | trading bot
[**CRYP1**](#cryp1)  | noeud [bitcoin](https://fr.wikipedia.org/wiki/Bitcoin) pour héberger son portefeuille ( [wallet](https://fr.wikipedia.org/wiki/Bitcoin#Wallets) ) 
[**CRYP2**](#cryp2)  | noeuds de staking
[**CRYP3**](#cryp3)  | [DEX](https://en.wikipedia.org/wiki/Decentralized_exchange) [blocknet](https://docs.blocknet.co/) avec noeuds locaux
[**CRYP4**](#cryp4)  | [noeud de service blocknet](https://docs.blocknet.co/service-nodes/introduction/) avec [hydra](https://twitter.com/HydraByBlocknet) (rémunéré avec staking), connecté sur ethereum 
[**CRYP5**](#cryp5)  | [BTC Pay Server](https://btcpayserver.org/) et connexion sur [xmrnode](#xmrnode) qui est un [PiNode-XMR](https://github.com/monero-ecosystem/PiNode-XMR) 

### Comment commencer

```
J'ai eu la question, alors je vous fait part de la réponse

Q : Pourquoi il n'y a pas de mot de passe root ?
R : C'est une grosse compromission du système, et interdit par tous les scripts de hardening.
    En effet, si root peut se logguer, cela signifie que l'on perd la traçabilité de quel utilisateur a fait la manipulation.
	Donc, dans tout système 'honnête', le root n'a pas de mot de passe et l'on passe obligatoirement par un 'sudo -sH'
	On peut alors consulter le fichier /var/log/auth.log et identifier l'acteur

/var/log/auth.log :
Dec 25 19:18:45 trade sshd[68677]: pam_unix(sshd:session): session opened for user ubuntu by (uid=0)
Dec 25 19:18:45 trade systemd-logind[1943]: New session 2024 of user ubuntu.
Dec 25 19:18:52 trade sudo:   ubuntu : TTY=pts/1 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/bin/bash
Dec 25 19:18:52 trade sudo: pam_unix(sudo:session): session opened for user root by ubuntu(uid=0)

    Ici, je peux voir que c'est l'utilisateur ubuntu qui est passé root, 
	je saurais alors que tout ce qui est fait dans le TTY=pts/1 est l'oeuvre de ubuntu, 
	jusqu'à ce qu'il close sa session root.

	Bien sur, c'est pour l'exemple : l'utilisateur générique ubuntu doit lui aussi toujours être supprimé du système.

```

Définissez vos besoins, vérifiez la cohérence de l'ensemble (est-ce un groupe existant ?), achetez le matériel, puis ...

**Attention**, pensez bien à utiliser une alimention USB-C ou un [doubleur d'alimentation](https://www.amazon.fr/Logilink-CU0070-Câble-Mâle-Femelle/dp/B00DI6ZGAM/) pour alimenter votre carte.

**Attention**, vérifiez bien la puissance de votre alimentation. Une alimentation de téléphone portable n'est pas adaptée, utilisez plutôt [celle-ci](https://www.amazon.fr/AmazonBasics-Chargeur-secteur-double-Amp/dp/B0773JCTRD/) (pour 1 carte, ou max 2 cartes sans HD) ou [celle-la](https://www.amazon.fr/Anker-PowerPort-Chargeur-60W-Ports/dp/B00YTJ45HM/) si vous voulez n'avoir qu'un bloc d'alimentation.

Masterisez vos machines, en utilisant soit une [pure ubuntu](https://ubuntu.com/download/raspberry-pi) (donc un Rasberry Pi 3 ou 4), ou un [armbian](https://www.armbian.com/), **attention**, seule la distribution **ubuntu focal 20.04 serveur 64 bits** (sauf si c'est explicitement précisé) est supportée par les scripts. 
Choisir un ubuntu fonctionnera toujours, les debian (comme bulleyes) sont généralement supportées.

Sur chacune de vos machines :
- loggez vous, passez root ( *sudo -sH* ),
- faites *cd /root*, *mkdir ppprod* puis *git clone https://www.ppprod.biz/gitlab/scripts.git*,
- faite un *cd scripts*, puis éditez .git/config avec *vi .git/config*
- dans /root/ppprod/scripts/.git/config, ajouter votre *login* et *password* git (envoyé par mail) sur la ligne remote origin
```
[remote "origin"]
	url = https://<login>:<password>@www.ppprod.biz/gitlab/ppprod/scripts.git
```

**La notion de domaine (domain, masterdomain et localdomain)**

vous trouverez généralement ces 3 fichiers dans /etc. Il s'agit des informations qui lient les infrastructures.

Le *domain* est celui que vous avez acheté, qui pointe directement chez vous (pensez à configurer le dyndns de votre box). Le *masterdomain* est votre domaine de référence (là ou vous allez cherché certain éléments, comme l'annuaire LDAP par exemple). Enfin, le *localdomain* est le domaine associé à votre box (il est définit directement sur/par la box).

**Aller ensuite sur le paragraphe dédié à la machine dans ce readme pour avoir plus d'informations.**

#### INF0

##### Machines constituant ce groupe

- [manager](#manager)

##### Services

* [protection contre les attaques exterieures](#protection-contre-les-attaques-exterieures)
* [se cacher ou presque](#se-cacher-ou-presque)
* utilisation d'un DNS non filtré (non gourverné é-ta-thiquement)

#### INF1 ( inclus [INF0](#inf0) )

##### Machines constituant ce groupe

- [manager](#manager)
- [nut](#nut)

##### Services

* [protection contre les attaques exterieures](#protection-contre-les-attaques-exterieures)
* [protection contre les coupures électriques](#protection-contre-les-coupures-electriques)
* monitoring (par l'intermédiare des prise [awox bluetooth](#https://www.manomano.fr/p/prise-connectee-bluetooth-version-sch-25175885))

#### INF3 ( inclus [INF0](#inf0) )

##### Machines constituant ce groupe

- [manager](#manager)
- [fap](#fap)

##### Services

* [protection contre les attaques exterieures](#protection-contre-les-attaques-exterieures)
* [limiter l'usage de la bande passante locale](#limiter-l-usage-de-la-bande-passante-locale)
* (commencer à) se cacher, en déléguant les résolutions DNS au réseau de votre voisin. 

#### INF4 ( inclus [INF3](#inf3) )

##### Machines constituant ce groupe

- [manager](#manager)
- [fap](#fap)
- [vpn](#vpn)

##### Services

* [protection contre les attaques exterieures](#protection-contre-les-attaques-exterieures)
* [limiter l'usage de la bande passante locale](#limiter-l-usage-de-la-bande-passante-locale)
* [se cacher ou presque](#se-cacher-ou-presque)

#### MASTER

##### Machines constituant ce groupe

- [manager](#manager)
- [fap](#fap)
- [nut](#nut)
- [vpn](#vpn)
- [gitlab](#gitlab)
- [grunner](#gitlab)
- [searcheng](#searcheng)
- [lokivpn](#lokivpn) et/ou [mystvpn](#mystvpn) et/ou [sentvpn](#sentvpn) 
- pluieurs noeuds cryptos (minimum [btcnode](#btcnode) et [ethnode](#ethnode) en mode *archive* (donc 20T de ssd à ce jour))

*Si vous envisagez de construire votre sous-réseau en tant que master, c'est que vous n'avez plus besoin de lire ce README, vous utilisez et développez sur l'infra ppprod depuis des années !*

#### INF5 ( inclus [INF3](#inf3) )

##### Machines constituant ce groupe

- [manager](#manager)
- [fap](#fap)
- [vpn](#vpn) *optionnel mais FORTEMENT conseillé*
- [lokivpn](#lokivpn) et/ou [mystvpn](#mystvpn) et/ou [sentvpn](#sentvpn) 

##### Services

* [protection contre les attaques exterieures](#protection-contre-les-attaques-exterieures)
* [limiter l'usage de la bande passante locale](#limiter-l-usage-de-la-bande-passante-locale)
* [se cacher ou presque](#se-cacher-ou-presque)
* [gagner des crypto-ronds](#gagner-des-crypto-ronds)


#### CRYP0

[trading bot](#https://www.ppprod.biz/gitlab/ppprod/ab4c)

##### Machines constituant ce groupe

- [trade](#trade), ou sa version "big"

Une version "big" est la même machine, mais avec un déploiement plus complexe pour permettre d'héberger les bot de vos potes.

Votre propre noeud blocknet (sur [blocknode](#blocknode)) est recommandé.

##### Services

[Voir la page dédiée au bot](#https://www.ppprod.biz/gitlab/ppprod/ab4c/-/blob/master/README.md#introduction)

#### CRYP1

noeud [bitcoin](https://fr.wikipedia.org/wiki/Bitcoin) pour héberger son portefeuille ( [wallet](https://fr.wikipedia.org/wiki/Bitcoin#Wallets) )

##### Machines constituant ce groupe

- [myNode](#mynode) ou [btcnode](#btcnode)

##### Services

[Voir la page dédiée](#https://www.ppprod.biz/gitlab/ppprod/ab4c/-/blob/master/trading/README.md/#mynode)

#### CRYP2

noeuds de staking

##### Machines constituant ce groupe

- [blocknode](#blocknode)
- [litenode](#litenode)
- [pivxnode](#pivxnode)
- [dogenode](#dogenode)
- [sysnode](#sysnode)

##### Services

[Voir la page dédiée](#https://www.ppprod.biz/gitlab/ppprod/ab4c/-/blob/master/trading/README.md/#noeuds)

#### CRYP3 ( inclus [CRYP2](#cryp2) )

[DEX](https://en.wikipedia.org/wiki/Decentralized_exchange) [blocknet](https://docs.blocknet.co/) avec noeuds locaux ou mutualisés.

La machine [blocknode](#blocknode) est indispensable.

Chaque noeud que vous allez ajouter va vous permettre de trader sur cette crypto.

Les noeuds :
- [myNode](#mynode) et/ou [btcnode](#btcnode)
- [litenode](#litenode)
- [pivxnode](#pivxnode)
- [dogenode](#dogenode)
sont  fortement conseillés.

**Attention**, ce service est incompatible avec du staking.

Il est cependant possible d'utiliser un noeud sans staking sur le masterdomain avec un portefeuille dédié (en utilisant __yggdrasil__), puis de faire le transfert sur votre propre noeud local (que vous aurez mis en staking).

L'ip *17* est réservée volontairement a n'importe quelle crypto dont vous êtes fan.

##### Machines constituant ce groupe

- [blocknode](#blocknode)
- [btcnode](#btcnode) (peut fonctionner avec un [myNode](#mynode))
- [litenode](#litenode)
- [pivxnode](#pivxnode)
- [dogenode](#dogenode)
- [sysnode](#sysnode)
- ...

##### Services

TODO

#### CRYP4 ( inclus [CRYP2](#cryp2) et [CRYP3](#cryp3) )

[noeud de service blocknet](https://docs.blocknet.co/service-nodes/introduction/) avec [hydra](https://twitter.com/HydraByBlocknet) (rémunéré avec staking), connecté sur ethereum.

La machine [blocknode](#blocknode) est indispensable, elle est mis en collateral.

Les noeuds :
- [btcnode](#btcnode)
- [litenode](#litenode)
- [pivxnode](#pivxnode)
- [dogenode](#dogenode)
- [sysnode](#sysnode)
- [ethnode](#ethnode) ( avec/sans [dappnode](#dappnode) )
sont optionnels, mais fortement conseillés.

**Attention**, ce service est incompatible avec du staking.

**Attention**, il s'agit d'un déploiement "big" uniquement, sauf pour ethnode qui peut être indépendant.

Donc, si vous voulez tout faire, un PC ou un Pine64 QuartzPro 16G + disques en RAID seront indispensables pour héberger btc,lite, pivx, doge + sys.

##### Machines constituant ce groupe

- [hydranode](#hydranode) (le "big")
- [ethnode](#ethnode)

##### Services

TODO

#### CRYP5 ( inclus [CRYP1](#cryp1) )

[BTC Pay Server](https://btcpayserver.org/) et connexion sur [PiNode-XMR](https://github.com/monero-ecosystem/PiNode-XMR)

##### Machines constituant ce groupe

- [btcpays](#btcpays)
- [xmrnode](#xmrnode)
- (OPTIONNEL) [btcnode](#btcnode) (peut fonctionner avec un [myNode](#mynode))

**Attention**, n'oubliez pas que le bitcoin n'est pas anonyme, seuls Monero (xmr) et pivx le sont. Donc, selon le service que vous voulez fournir, ...

##### Services

TODO


#### manager

| OS supporté                              | machines supportées                                | matériel nécessaire  |
| ---------------------------------------- | -------------------------------------------------- | -------------------- |
| ubuntu focal 20.04 serveur 32 ou 64 bits, ou debian bulleyes 64 | RasPi2, RasPi3 ou RasPi4 (2/4/8G), CubieTruck, OdroidC2/C4, RockPi4C+... | un HD ou 1 clé USB   |

En fait, comme machine, tout ce qui supporte un ubuntu focal 20.04 et bulleyes (pure ou [armbian](https://www.armbian.com/)) est fonctionnel. La version 32 bits est bien moins fonctionnelle, attention.

Pour l'ensemble des images à monter sur les Rapsberry, on peur utiliser l'outil windows Rapsberry PI Imager sur lequel on peut directement choisir l'OS a installer.

Sur linux, on peut utiliser [Balena Etcher](https://www.balena.io/etcher/) en ayant préalablement téléchargé les images sur Ubuntu ou sur armbian.

**Attention**, si vous êtes un *domainmaster*, seul **un HD est conseillé**. En effet, une clé USB ou une [carte SD](https://www.amazon.fr/SanDisk-Extreme-microSDXC-Adapter-Performance/dp/B07FCMWCVB/) sur [un adaptateur](https://www.amazon.fr/Beikell-Lecteur-Mémoire-Vitesse-Windows/dp/B07L9VT8YY/) vont être corrompus en 1 mois. 

**Attention**, si vous êtes sur un réseau fortement perturbé (par exemple, chez orange - qui, puisqu'il connecte les pros - est extrement sujet aux attaques), soit mettez vos logs sur un drives dédié (clé USB dédiée) ou utilisez un disque dur. Les logs d'attaques (analysés par psad) vont corrompre votre système rapidement.

##### Règlages de votre box

Vous devez installer et utiliser ddclient (mise a jour d'un serveur dyndns). C'est la première chose a faire.

Ensuite, ajouter la redirection vers 192.168.1.2 dans la configuration de votre DMZ.

**Attention**, si vous utilisez 1 ou 2 dappnode (pour ethereum, rotki - sur 192.168.1.18 - et mysterium - sur 192.168.1.32 -), vous devez absoluement modifier les ports de vpn (1194 par défaut), et mettre, par exemple 1195 et 1196 (se configure directement avec l'interface web de dappnode).

##### Protection contre les attaques exterieures

En mettant cette machine comme [DMZ](https://fr.wikipedia.org/wiki/Zone_d%C3%A9militaris%C3%A9e_(informatique)), vous forcez tout traffic entrant à être rerouté vers cette machine.

Voir [dans confidentialité](privacy.md) pour plus d'informations.

##### Installation

Vous devez dans un premier temps [acheter un domaine](https://www.ovh.com/fr/domaines/), et configurer votre box en [DDNS (DNS Dynamique)](https://docs.ovh.com/fr/domains/utilisation-dynhost/). Je conseille OVH, mais n'importe lequel, compatible avec votre box, fera l'affaire.

Come dit plus haut, vous devez installer et utiliser ddclient (mise a jour d'un serveur dyndns). C'est la première chose a faire. La configuration de votre box n'est pas suffisante : En effet, lorsque votre fournisseur va voir des services mis à disposition, il va "rincer" la configuration à distance. Ca m'est arrivé plusieurs fois, jusqu'à ce que je comprenne que cela ne enais pas de moi, et que je ne pouvais faire confiance à la box.


* Vous devez identifier votre *domain*, imaginons que vous ayez acheté "ppprod.ovh", ce sera votre *domain*.
* Vous devez ensuite identifier votre *masterdomain*, si vous ne savez pas ce que c'est, et bien c'est que vous devez utiliser la valeur par défaut : *ppprod.biz*.
* Faites le réglage du DDNS et de la DMZ dans sur votre box.
* Branchez le HD (ou la clé USB, ou l'adapteur SD avec une SD) sur un port USB3 de préférence, en direct (pas de hub).
* Branchez le HD sur un port USB3 de préférence.
* Loggez vous sur la machine, passez root (avec *sudo -sH*)
* Configurez votre [LVM](https://www.digitalocean.com/community/tutorials/an-introduction-to-lvm-concepts-terminology-and-operations)
  * trouvez le device (pour vérifier qu'il est connecté, utilisez *lsusb*), c'est souvent /dev/sda,
  * créez une partition sur le device avec *fdisk* (si une ancienne partition existe, supprimez la en premier), utilisez le type *8e*(Linux LVM) pour cette nouvelle partition,
  * faites un *pvcreate /dev/sda1*,
  * si c'est un HD (Hard Drive ou disque dur), faites un *vgcreate DATA /dev/sda1*,
  * si c'est une clé USB ou une SD sur adaptateur, faites un *vgcreate USBKEY /dev/sda1*
* Faites un *mkdir -p /var/www/html*
* Faites un *vi /var/www/html/domain* et saisissez le *domain* (exemple : *ppprod.ovh*)
* Faites un *vi /var/www/html/masterdomain* et saisissez le *masterdomain* (sauf exception, *ppprod.biz*)
* Faites un *vi /var/www/html/git_user* et saisissez les informations suivantes :
  * USER_EMAIL=*<le mail donnée à l'association, celui sur lequel vous recevez les courriers de l'association>*
  * USER_NAME=*<le login de votre compte git sur le gitlab de l'association>*
* Faites un *cd /root*, puis *mkdir ppprod && cd ppprod*,
* Faites un *git clone 'https://www.ppprod.biz/gitlab/ppprod/scripts.git'*
* Faites un *cd scripts*, puis un *vi .git/config*
  * completez la ligne *url = https://www.ppprod.biz/gitlab/ppprod/scripts.git* en ajoutant *le login de votre compte git sur le gitlab de l'association* et *le mot de passe du gitlab*, ce qui doit donner la ligne *url = https://*le login*:*le mot de passe*@www.ppprod.biz/gitlab/ppprod/scripts.git*
* Faites un *git pull*
* Faites un *cd manager*
* Lancez *./install.sh* et regardez que tout se passe bien
  * en cas de problème, utilisez le *chat telegram PpProd_scripts* pour avoir des informations  

Remarque : A la question autoriser les http tunnel via apt catcher NG, répondre oui.

#### fap

fap est l'acronyme pour "Free Acces Point".

| OS supporté                | machines supportées         | matériel nécessaire                               |
|----------------------------|-----------------------------|---------------------------------------------------|
| ubuntu focal 20.04 64 bits | **Raspi3b,3b+ ou RockPi3B** | 1 clé USB, 1 antenne wifi, 1 lecteur de carte SIM |

**Pour les RockPi3B, la clé USB wifi n'est pas nécessaire, le WIFI de la carte est utilisé.**
**Attention, en USB, seules les cartes WiFi avec chipset RealTek 88XXau sont supportées.**

Un __lsusb__ doit vous ressortir la ligne :

```
0bda:8812 Realtek Semiconductor Corp. RTL8812AU 802.11a/b/g/n/ac 2T2R DB WLAN Adapter
```

##### Règlages de votre box

Redirection(s) :

Nom | Protocole | Type | Ports externes | IP de destination | Ports de destination
--- | --------- | ---- | -------------- | ----------------- | --------------------
fap | TCP       | Port | 24             | 192.168.1.3       | 22

##### Limiter l'usage de la bande passante locale

Cette machine a 2 raisons principales :
- héberger des services qui "déplairaient" à votre fournisseur d'accès,
- délester votre connexion principale.

L'usage typique peur être le(s) suivant(s) :
- vous voulez héberger un serveur tor, mais votre fournisseur vous bloque ce service,
- vous utilisez une connexion 4G pour votre réseau local, mais vous souhaitez rerouter molotov.tv et la mise a jour des machines sur un réseau bas débit.

Voir [dans confidentialité](privacy.md) pour plus d'informations.

##### Installation

- fap ne doit pas fonctionner avec un HD, mais uniquement avec une clé USB (ou un adapteur SD).
- fap doit obligatoirement être branché en USB avec une carte wifi compatible (sauf RockPi3B).
- Avant de commencer votre installation, vous devez avoir une carte SIM Free avec un forfait 2 Euros activée, branché sur un adaptateur USB. 

Sur un Raspberry Pi3b(+), la clé USB, la carte wifi et la carte SIM doivent être banchés en direct. Tout doit être branché avant de commencer l'installation. 

* Vous devez identifier votre *domain*, imaginons que vous ayez acheté "ppprod.ovh", ce sera votre *domain*.
* Vous devez ensuite identifier votre *masterdomain*, si vous ne savez pas ce que c'est, et bien c'est que vous devez utiliser la valeur par défaut : *ppprod.biz*.
* Loggez vous sur la machine, passez root (avec *sudo -sH*)
* Configurez votre [LVM](https://www.digitalocean.com/community/tutorials/an-introduction-to-lvm-concepts-terminology-and-operations)
  * trouvez le device, c'est souvent /dev/sda,
  * créez une partition sur le device avec *fdisk* (si une ancienne partition existe, supprimez la en premier), utiliser le type *8e*(Linux LVM) pour cette nouvelle partition,
  * faites un *pvcreate /dev/sda1*,
  * faites un *vgcreate USBKEY /dev/sda1*
* Faites un *vi /etc/domain* et saisissez le *domain* (exemple : *ppprod.ovh*)
* Faites un *vi /etc/masterdomain* et saisissez le *masterdomain* (sauf exception, *ppprod.biz*)
* Faites un *vi /etc/git_user* et saisissez les informations suivantes :
  * USER_EMAIL=*<le mail donnée à l'association, celui sur lequel vous recevez les courriers de l'association>*
  * USER_NAME=*<le login de votre compte git sur le gitlab de l'association>*
* Faites un *cd /root*, puis *mkdir ppprod && cd ppprod*,
* Faites un *git clone 'https://www.ppprod.biz/gitlab/ppprod/scripts.git'*
* Faites un *cd scripts*, puis un *vi .git/config*
  * completez la ligne *url = https://www.ppprod.biz/gitlab/ppprod/scripts.git* en ajoutant *le login de votre compte git sur le gitlab de l'association* et *le mot de passe du gitlab*, ce qui doit donner la ligne *url = https://**le login**:**le mot de passe**@www.ppprod.biz/gitlab/ppprod/scripts.git*
* Faites un *git pull*
* Faites un *cd fap*
* Lancez *./install.sh* et regardez que tout se passe bien
  * en cas de problème, utilisez le *chat telegram PpProd_scripts* pour avoir des informations (l'invitation vous a été envoyé lors de votre souscription à l'association).  

##### Les services en détail

Voir dans dans [privacy.md](https://www.ppprod.biz/gitlab/ppprod/scripts/-/blob/master/privacy.md?ref_type=heads#fap).

#### nut

| OS supporté                    | machines supportées             | matériel nécessaire         |
| ------------------------------ | ------------------------------- | --------------------------- |
| **seul raspbian est supporté** | **n'importe quel Raspberry Pi** | un onduleur compatible nut  |

**Aujourd'hui, les onduleurs génériques LDLC et les onduleurs APC700usb sont configurés (la conf est dans le git)**. Si vous utilisez un autre onduleur, vous devrez le rajouter vous-même au git (mais si vous êtes membre, vous *devez* contribuer ;) ).

##### Règlages de votre box

Rien à faire.

##### Protection contre les coupures électriques

Cette machine doit être connectée à un onduleur. Les machines du reste de votre infrastructure vont alors se connecter à ce service ( [nut](https://doc.ubuntu-fr.org/nut) ) et s'éteindre proprement sur coupure électrique.

Cette machine va aussi servir de monitoring, à la condition d'avoir des prise bluethoot awox.

Ce service est très utile, voire indispensable, pour les nodes ( [CRYP2](#cryp2) ).

##### Installation

* Vous devez identifier votre *domain*, imaginons que vous ayez acheté "ppprod.ovh", ce sera votre *domain*.
* Vous devez ensuite identifier votre *masterdomain*, si vous ne savez pas ce que c'est, et bien c'est que vous devez utiliser la valeur par défaut : *ppprod.biz*.
* Loggez vous sur la machine, passez root (avec *sudo -sH*)
* Faites un *vi /etc/domain* et saisissez le *domain* (exemple : *ppprod.ovh*)
* Faites un *vi /etc/masterdomain* et saisissez le *masterdomain* (sauf exception, *ppprod.biz*)
* Faites un *vi /etc/git_user* et saisissez les informations suivantes :
  * USER_EMAIL=*<le mail donnée à l'association, celui sur lequel vous recevez les courriers de l'association>*
  * USER_NAME=*<le login de votre compte git sur le gitlab de l'association>*
* configurez les machines connectées à votre nut, voir [dans nut](nut/README.md#configuration)
* Faites un *cd /root*, puis *mkdir ppprod && cd ppprod*,
* Faites un *git clone 'http://www.ppprod.biz/gitlab/ppprod/scripts.git'*
* Faites un *cd scripts*, puis un *vi .git/config*
  * completez la ligne *url = http://www.ppprod.biz/gitlab/ppprod/scripts.git* en ajoutant *le login de votre compte git sur le gitlab de l'association* et *le mot de passe du gitlab*, ce qui doit donner la ligne *url = http://**le login**:**le mot de passe**@www.ppprod.biz/gitlab/ppprod/scripts.git*
* Faites un *git pull*
* Faites un *cd nut*
* Lancez *./install.sh* et regardez que tout se passe bien
  * en cas de problème, utilisez le *chat telegram PpProd_scripts* pour avoir des informations  

#### gitlab

Voir [dans gitlab](gitlab/README.md#gitlab).

#### grunner

Voir [dans gitlab](gitlab/README.md#grunner).

#### searcheng

Voir [dans manager](manager/README.md#searcheng).

#### trade

Si trade est dans la version "big", il dispose alors de 2 IPs sur le clearnet (.10 et .100)

Voir [dans trading](trading/README.md#trade).

#### bigtrade

Un trade en version "big" est une machine qui permet d'héberger les bots de plusieurs comptes.

Voir [dans trading](trading/README.md#bigtrade).

#### btcnode

btcnode peut être en .11 ou .41 sur le clearnet.

Si vous ne déployez pas de myNode, placez votre btcnode en .11

Si vous disposez d'un myNode sur le même réseau, il faudra alors configurer le noeud comme un relai.

Voir [dans trading](trading/README.md#btcnode).

#### myNode

Voir [dans trading](trading/README.md#mynode).

#### blocknode

Voir [dans trading](trading/README.md#blocknode).

#### litenode

Voir [dans trading](trading/README.md#litenode).

#### pivxnode

Voir [dans trading](trading/README.md#pivxnode).

####dogenode

Voir [dans trading](trading/README.md#dogenode).

####sysnode

Voir [dans trading](trading/README.md#sysnode).

####ethnode

ethnode peut être en .18 ou .42 sur le clearnet.

Si vous ne déployez pas de dappnode, placez votre ethnode en .18

Si vous déployez un dappnode, configurez cette machine comme relai (il n'y a alors pas besoin d'une configuration musclée).

Voir [dans trading](trading/README.md#ethnode).

#### dappnode

Voir [dans trading](trading/README.md#dappnode).

#### hydranode

Voir [dans trading](trading/README.md#hydranode).

#### vpn

| OS supporté                        | machines supportées                  | matériel nécessaire      |
| ---------------------------------- | ------------------------------------ | ------------------------ |
| ubuntu focal 20.04 serveur 64 bits | **seul les ODroidC2 ont été testés** | un adaptateur ETH USB3   |

**Attention**, pensez bien à utiliser un [doubleur d'alimentation](https://www.amazon.fr/Logilink-CU0070-Câble-Mâle-Femelle/dp/B00DI6ZGAM/) pour alimenter votre carte.

**Attention**, l'adapteur ETH USB3 doit être lui aussi alimenté avec un [doubleur d'alimentation](https://www.amazon.fr/Logilink-CU0070-Câble-Mâle-Femelle/dp/B00DI6ZGAM/) que vous allez brancher sur un USB3 pour les data (fil épais) et en USB2 pour l'alimentation (fil fin).

Je conseille ces [adapteurs USB3 ETH](https://www.amazon.fr/UGREEN-Adaptateur-Ethernet-Supporte-Supérieur/dp/B00MYTSN18/) ou [ceux-ci](https://www.amazon.fr/UGREEN-Adaptateur-Ethernet-100Mbps-Supporte/dp/B00MYT481C/).

##### Règlages de votre box

Rien à faire.

##### Se cacher ou presque

Souvent, sur internet, on lit que l'usage d'un VPN est **LA** solution pour pouvoir cacher sa navigation internet et ses téléchargements. Bien entendu, comme tout ce qui est "bien promu/connu", c'est faux. Les fournisseurs VPN ont l'obligation de fournir l'historique de connexion, sur simple demande des autorités (ex : gendarmerie), sinon, ils ne peuvent opérer en Europe (donc France). Cela signifie que si les autorités veulent savoir ce que vous êtes aller voir, ou télécharger, ils le peuvent. Par contre, ils vont devoir ouvrir une procédure, et c'est uniquement à cause de cela que vous êtes "protégés", il ne feront pas de la paperasse pour 50/100 torrents téléchargés par an.

C'est donc un simple moyen (mais payant) de pouvoir alimenter sa [seedbox](#seedbox).

Pour ce qui est du darkweb, tournez vous vers [tor](https://www.torproject.org/) ou loki (LA solution), c'est plus sûr (mais pas infaillible). En effet, beaucoup de noeuds Tor appartiennent aux autorités, et donc identifient ce qui transite. Par contre, en hébergeant un [relay](https://www.eff.org/fr/pages/what-tor-relay) en exit, ils devront prouver que ce traffic est le votre, ça devient beaucoup plus compliqué. Bien entendu, si vous voulez suivre cette voie (utiliser Tor pour faire des choses illégales), vous vous embarquez dans des grosses galères. Si les autorités ne peuvent prouver que vous avez hacké une société (récup de login/password), un particulier (numéro CB), acheté de la drogue ou des armes, ou pire encore, ils ont la cartouche ultime : vous serez surement classé dans la case "terroriste", puisque sureté nationale. Rappelez vous toujours avant de faire quoi que ce soit : si ce n'est pas auditable, c'est douteux, et si c'est douteux, c'est suffisant avec Vigipirate à son niveau "Urgence attentat".

Personnellement, j'utilise Tor pour lire des articles "généraux" sur la sécurité informatique (comment hacker, les algos de cryptographie, ...) (et si je suis tracé, ben j'ai rien fait de mal sinon m'instruire pour mon activité professionelle) et pour les services crypto (cacher son IP pour ne pas se faire hacker son wallet), ce qui est pleinement légal.

La mise en place d'un VPN permet de de bootstapper un noeud Tor en passant en dessous des radars. Aussi, en déployant un VPN, un noeud Tor sera alors déployé sur votre infrastructure, et un proxy sur VPN vous permettra d'aller faire un tour du coté sombre sans déclencher d'alarmes, mais pas sans laisser de traces...

Aujourd'hui, les vpn supportés sont :
- les VPNs "conventionnels" (j'ai utilisé PureVPN et AirVPN, mais les autres fonctionnent aussi, AirVpn fais du chacha20 adapté aux processeurs arm),
- un DVPN sentinel,
- un DVPN mysterium,
- un routage lokinet (via exit-node).

Voir [dans confidentialité](privacy.md) pour plus d'informations.

##### Installation

l'adapteur ETH USB3 doit être branché (en direct càd sans hub entre la machine et l'adaptateur).

* Vous devez identifier votre *domain*, imaginons que vous ayez acheté "ppprod.ovh", ce sera votre *domain*.
* Vous devez ensuite identifier votre *masterdomain*, si vous ne savez pas ce que c'est, et bien c'est que vous devez utiliser la valeur par défaut : *ppprod.biz*.
* Loggez vous sur la machine, passez root (avec *sudo -sH*)
* Faites un *vi /etc/domain* et saisissez le *domain* (exemple : *ppprod.ovh*)
* Faites un *vi /etc/masterdomain* et saisissez le *masterdomain* (sauf exception, *ppprod.biz*)
* Faites un *vi /etc/git_user* et saisissez les informations suivantes :
  * USER_EMAIL=*<le mail donnée à l'association, celui sur lequel vous recevez les courriers de l'association>*
  * USER_NAME=*<le login de votre compte git sur le gitlab de l'association>*
* configurer le (D)VPN que vous utilisez, voir [dans (d)vpn](vpn/README.md#configuration)
* Faites un *cd /root*, puis *mkdir ppprod && cd ppprod*,
* Faites un *git clone 'http://www.ppprod.biz/gitlab/ppprod/scripts.git'*
* Faites un *cd scripts*, puis un *vi .git/config*
  * completez la ligne *url = http://www.ppprod.biz/gitlab/ppprod/scripts.git* en ajoutant *le login de votre compte git sur le gitlab de l'association* et *le mot de passe du gitlab*, ce qui doit donner la ligne *url = http://**le login**:**le mot de passe**@www.ppprod.biz/gitlab/ppprod/scripts.git*
* Faites un *git pull*
* Faites un *cd vpn*
* Lancez *./install.sh* et regardez que tout se passe bien
  * en cas de problème, utilisez le *chat telegram PpProd_scripts* pour avoir des informations  

#### openwrt

Voir [dans infrastructure matérielle](network_topo.md#openwrt).

#### pms

Voir [dans multimedia](multimedia.md#pms).

#### seedbox

Voir [dans multimedia](multimedia.md#seedbox).

#### lokivpn

Voir [dans confidentialité](privacy.md#lokivpn).

#### sentvpn

Voir [dans confidentialité](privacy.md#sentvpn).

#### mystvpn

Voir [dans confidentialité](privacy.md#mystvpn).

#### btcpays

Voir [dans trading](trading/README.md#btcpays).

#### xmrnode

Voir [dans trading](trading/README.md#xmrnode).

####jeedom

Voir [dans maison connectée](home_mngt.md#jeedom).

####zwavegwt

Voir [dans maison connectée](home_mngt.md#zwavegwt).

#### bornes wifis

Voir [dans infrastructure matérielle](network_topo.md#bornes_wifis).

## Exemples de configurations materielles

| contact                      | domaine       | machine       |  matériel
|------------------------------|---------------|---------------|---------- 
| ???@???.com     | ppprod.biz    | manager       | OdroidC4 avec HD 250G
|                              |               | fap           | RasPi3b+ avec clé USB, WiFI rtl8812au et lecteur USB pour SIM
|                              |               | vpn           | Odroid C2
|                              |               | gitlab        | Rock64 avec HD
|                              |               | trade         | RockPi4 avec 1 HD 1T
|                              | ppprod.ovh    | manager       | RockPi4 avec clé USB
|                              |               | fap           | RasPi3b+ avec clé USB, WiFI rtl8812au et lecteur USB pour SIM 
|                              |               | vpn           | Odroid C2
|                              |               | seedbox       | Odroid C2 avec HD 2T
|                              |               | nextcloud+pms | RasPi4 4G avec HD 4T


## Contacts en fonction des groupes

| contact                      | groupe               
| ---------------------------- | -------------------- 
| ???@???.com     | tous                 
