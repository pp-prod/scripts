# confidentialité

## Introduction

Cette page est dédiée a l'explication de la partie confidentialité dans l'infrastructure.

Elle explique, sur les différentes machines, et au travers des différents sites, comment les informations circulent et sorte sur internet.

## Licence

En devenant membre de l'association PPProd, vous avez accepté et signé 
- les termes de [la licence de ce projet](license.md) ([licence de l'association](https://drive.google.com/file/d/1RIolXZQcsCAHuJQn9r3PQD_NZvdJHOVy/view?usp=sharing), appliqué à tous les projets de l'[association PPProd](https://drive.google.com/file/d/1jZzIhDD1UfE6J6BTcf3pvz8nwBP4SeKP/view?usp=sharing))
- [la politique de contribution](contribution.md) ([politique de contribution de l'association](https://drive.google.com/file/d/1RIolXZQcsCAHuJQn9r3PQD_NZvdJHOVy/view?usp=sharing), appliqué à tous les projets de l'[association PPProd](https://drive.google.com/file/d/1jZzIhDD1UfE6J6BTcf3pvz8nwBP4SeKP/view?usp=sharing))

Dans les grandes lignes, cela signifie que tout ce qui est dans le [gitlab de l'association](https://www.ppprod.biz/gitlab/) appartient à l'association (donc à vous tant que vous êtes membre), et que tout ce que vous modifiez/écrivez doit être donné à l'association (donc à vous et aux autres membres).

## le masterdomain

Le masterdomain est l'infrastructure qui est utilisée en cas de non-disponibilité de certains services.

En effet, si vous n'hébergez pas un ou plusieurs moyen de sortie sur le net de façon anonyme, les services du masterdomain seront utilisés en dernier recours.

## les différentes façons de naviguer sur internet anonymement

### Les VPNs

On dit VPN pour Virtual Private Network, il s'agit de construire un sous-réseau entre vous et votre fournisseur VPN.

Ensuite, tous les échanges passent par se sous-réseau et sortent avec l'adresse du VPN.

Souvent, sur internet, on lit que l'usage d'un VPN est **LA** solution pour pouvoir cacher sa navigation internet et ses téléchargements. Bien entendu, comme tout ce qui est "bien promu/connu", c'est faux. Les fournisseurs VPN ont l'obligation de fournir l'historique de connexion, sur simple demande des autorités (ex : gendarmerie), sinon, ils ne peuvent opérer en Europe (donc France). Cela signifie que si les autorités veulent savoir ce que vous êtes aller voir, ou télécharger, ils le peuvent. 

Il existe cependant des VPNs qui ne stokent pas les transferts, mais juste les connexions. Il s'agit de VPNs qui sont hébergés par des particuliers, comme les noeuds sentinel.

### les sous-réseaux cryptés

Il s'agit, dans la pratique, exactement de la même chose que les VPNs, sauf que ces réseaux se disent hors clearnet, c'est ce qu'on appele le darknet.

Il existe plusieurs darknets.

Certains réseaux sont juste des inter-connexions cryptés, comme yggdrasil. La force de leur cryptage fait la force du réseau, la résistance à l'écoute. Mais un participant du réseau peu récupérer certaines infos. Ces réseaux sont donc liés à la confiance donnée aux autres participants.

D'autres réseaux utilisent du mixage et/ou des faux paquets. Il s'agit des réseaux Tor, Loki ou myst.

Tor est surement le plus abouti, mais aussi le plus surveillé, car son modèle permet à quiconque de se joindre au réseau, de multiplier les noeuds, donc d'augmenter la probabilité de voir passer un de vos paquets, plus par recoupement, d'arriver a connaitre votre navigation.

Myst reprend le mixage de Tor, plus du brouillage, mais se base sur des noeuds crypto pour identfier/localiser les relais.

Loki reprend un algo de cryptage et de salage de i2p, un réseau masqué difficilement écoutable, et rajouter le chainage en oignons de Tor, mais se base sur des noeuds crypto pour identfier/localiser les relais.

### les proxys

Les proxys sont un moyen de sortir d'un reseau, en utilisant l'adresse IP du proxy.

Les proxys sont dédiés à un ou plusieurs services (comme un DNS ou un proxy web).

En utilisant un proxy sur un sous-réseau crypté, vous avez exactement le même service qu'un VPN.

### conclusion

Il n'y a aucun moyen d'être certain de ne pas laisser de traces. En fait, on peut même etre sur qu'on en laisse.

Le jeu du chat et de la sourie va donc consister à utiliser tout un arsenal de moyen, et de ne pas tous faire passer par un seul canal.

**ATTENTION**, bien entendu, si vous arrivez à "perdre" votre fournisseur d'accès, il va, de fait, comprendre que quelque chose n'est pas standard, et vous en verrez immédiatement le résultat.

Par exemple, mon fournisseur m'a bloqué les DNS, pour me forcer à utiliser le sien, après aoir observé que je ne l'utilisais pas. J'ai perdu un bon paquet d'accès au web (dur sans DNS) pour les machines qui sont connectés à ma box mais n'utilisent pas les services.

Soit, j'héberge mon propre DNS niveau 0.

De la même façon, en analysant mon traffic et en observant que tout était crypté, je me suis retrouvé sur des listes référencant des VPNs (c'est à dire qu'il considèrent que mon traffic n'est pas forcément "que" le miens, puisqu'ils ne peuvent l'analyser), et j'ai perdu l'accès aux services de vidéo à la demande Canal, Netflix et Prime.

Soit, je me fais mon propre service de vidéo à la demande.

## Ce qui est mis en place

Ce qui est mis en place est dépéndant des machines que vous dédiez à ce jeu.

Si vous commencer à vouloir vous cacher, vous l'aurez compris, c'est très mal vu par votre fournisseur web, très très mal vu, par l'état Français.

### les machines

#### manager

#### fap

#### searcheng

#### lokivpn

TODO

#### sentvpn

TODO

#### mystvpn

TODO


### premier point, résolution DNS

#### sur manager

|  machines dispo     | unbound           | hsnd | alfis (ygg)                  |
|---------------------|-------------------|------|------------------------------|
| manager             | hsnd, master      | oui  | bootstrap master + tor local |
| manager + fap       | fap, hnsd, master | oui  | bootstrap fap + tor local    |

#### sur fap

|  machines dispo     | pihole          | tor-dns |
|---------------------|-----------------|---------|
| fap                 | cloudflare, tor | oui     |

| service    | listen       | port |
|------------|--------------|------|
| pihole     | 0.0.0.0      | 53   |
| cloudflare | 127.0.0.1    | 5053 |
| tor-dns    | 127.0.0.1    | 6053 |


#### sur vpn

|  machines dispo     | bind         |
|---------------------|--------------|
| vpn                 | fap, manager |

#### utilisation

- sur les machines clearnet, utiliser la box ou manager ou fap (sur .1.3) selon le besoin
- sur freenet, utiliser fap
- sur hiddennet, utiliser vpn ou manager (sur .128.2) selon besoin

### deuxième point, surfing the web

#### sur manager

|  machines dispo     | tor                       | ygg public              | lokinet                  |
|---------------------|---------------------------|-------------------------|--------------------------|
| manager             | oui, bootstrap sur master | bootstrap sur tor local | bootstrap sur ygg public |
| manager + fap       | oui, bootstrap sur master | bootstrap sur tor local | bootstrap sur ygg public |
| manager + vpn       | oui, bootstrap sur vpn    | bootstrap sur tor local | bootstrap sur ygg public |
| manager + fap + vpn | oui, bootstrap sur vpn    | bootstrap sur tor local | bootstrap sur ygg public |

#### sur fap

|  machines dispo     | tor                     |
|---------------------|-------------------------|
| fap                 | oui, bootstrap local    |
| fap + manager       | oui, bootstrap local    |
| fap + vpn           | oui, bootstrap sur vpn  |
| fap + manager + vpn | oui, bootstrap sur vpn  |

**Configurer brave sur le réseau _hiddennet_**



#### sur vpn

TODO

#### utilisation

TODO

### troisième point, downloading

TODO

### quatrième point, héberger des services

TODO
