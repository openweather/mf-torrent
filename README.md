# Dissémination des modèles Météo-France par le protocole Bittorrent

## Concept général

Météo-France a ouvert au téléchangement public un extrait de ses modèles AROME et ARPEGE. Il s'agit de fichiers très volumineux, qui sont renouvelés plusieurs fois par jour.

Aujourd'hui, tous les utilisateurs téléchargent ces fichiers depuis une source unique. Cela crée un point de congestion. Il devient de plus en plus difficile de télécharger ces données, la saturation des serveurs Météo-France ne faisant qu'augmenter.

Nous demandons à Météo-France de disséminer ces fichiers en utilisant le protocole Bittorrent − particulièrement adapté à ce cas d'usage. Avec ce protocole, dès lors qu'un utilisateur télécharge un fragment de fichier, il devient à son tour une source pour les autres utilisateurs. Ainsi, le point de congestion disparait. La bande passante disponible augmente de manière proportionnelle avec le nombre d'utilisateurs. C'est la bande passante des utilisateurs qui est utilisée, et non plus celle de Météo-France.

Cette solution permettrait à Météo-France d'assurrer une dissémination de meilleure qualité, tout en réduisant ses coûts d'infrastructure.

## Mise en œuvre technique

### Génération des fichiers de métadonnées `.torrent`

Afin qu'un fichier puisse être partagé par le protocole Bittorrent, il est nécessaire de préalablement créer un fichier de métadonnées. Ce fichier contient, entre-autres, des informations relatives à la taille du fichier et à son découpage en plusieurs fragment.

L'utilitaire gratuit `mktorrent` peut être utilisé. Cet utilitaire est fourni dans la plupart des distributions linux. Il peut également être téléchargé sur le site suivant : http://mktorrent.sourceforge.net/

Il suffit d'executer une ligne de commande sur le serveur contenant le fichier à partager :

`mktorrent -a http://URL_DU_TRACKER -w http://URL_DU_WEBSEED FICHIER_A_PARTAGER.grib2`

Le paramètre `-a` indique l'adresse d'un serveur tracker. Voir plus bas.

Le paramètre `-w` est optionnel, et indique l'adresse d'un serveur webseed. Voir plus bas.


### Serveur tracker
Pour fonctionner, le fichier torrent a besoin d'être relié à un serveur tracker. Ce serveur centralise les informations pour permettre aux différents utilisateurs de se connecter entre eux.

Il est possible d'utiliser des trackers publics. Par exemple :
```
mktorrent \
-a udp://torrent.gresille.org:80/announce \
-a http://torrent.gresille.org/announce \
-a udp://tracker.openbittorrent.com:80 \
-a udp://tracker.publicbt.com:80 \
monfichier.grib2
```

La société BPZ Labs se tient à la disposition de Météo-France pour l'aider gratuitement à mettre en place son propre serveur tracker si l'institution le souhaite. La société BPZ Labs peut également fournir gratuitement à Météo-France un serveur tracker externe.

### Seed initial

Afin que les utilisateurs puissent télécharger les fichiers via le protocole Bittorrent, Météo-France doit mettre à la disposition des utilisateurs une copie initiale du fichier. cette pratique est appellée "seed"

Cela peut se faire de deux manières.

#### Seed par client Bittorrent

Il est possible de partager les fichiers initiaux en utilisant un logiciel tel que ```transmission-daemon```

Cette méthode nécessite néanmoins que les fichiers à partager soient accessibles depuis la machine sur laquelle tourne le processus.

Nous préconisons cette solution.

La société BPZ Labs se tient à la disposition de Météo-France pour l'aider gratuitement à mettre en place un serveur transmission. La société BPZ Labs peut également gratuitement fournir à Météo-France un serveur de seed externe disposant d'une bande passante de 1GBps.

#### Seed par serveur Webseed

Une autre solution consiste à inclure un lien web dans le fichier torrent.

Cette méthode est appellée Webseed. Lorsque le logiciel de l'utilisateur ne trouve aucune source Bittorrent, celui-ci bascule sur un téléchargement classique.

Cette méthode a comme avantage de ne pas nécessiter l'installation d'un logiciel suplémentaire sur les serveurs de Météo-France. Le serveur ```http``` actuel peut être utilisé. Il faudra néanmoins le régler pour que celui-ci accepte les requètes de type ```Content-Range``` https://developer.mozilla.org/fr/docs/Web/HTTP/Headers/Content-Range

À la création du fichier `.torrent`, il faudra alors passer l'url "normale" du fichier :

Exemple :
```
mktorrent \
-a http://URL_DU_TRACKER \
-w "http://dcpc-nwp.meteo.fr/services/PS_GetCache_DCPCPreviNum?token=__5yLVTdr-sGeHoPitnFc7TZ6MhBcJxuSsoZp6y0leVHU__&model=AROME&grid=0.01&package=HP1&time=00H&referencetime=2017-04-29T06:00:00Z&format=grib2" \
AROME_0.01_HP1_00H_201704290600.grib2
```
## Mise en œuvre opérationnelle

Afin de pousser les utilisateurs à utiliseur à utiliser la solution Bittorrent, Météo-France devra mettre à disposition les fichiers `.torrent` sur son portail données publiques.

Les fichiers devront, de préférence, avoir une url dont le motif est consistant. Exemple :
`https://donneespubliques.meteofrance.fr/torrent/modeles/arome-0.01/20170429060/AROME_0.01_HP1_00H_201704290600.grib2.torrent`

Météo-France devra mettre en avant la solution Bittorrent par rapport à la solution classique. Exemple :
```
- téléchargement classique
- téléchargement torrent (plus rapide)
```
Un guide devra être proposé aux utilisateurs pour les aider à utiliser la solution torrent. La société BPZ Labs se tient à la disposition de Météo-France pour rédiger ce guide.

## Points potentiels d'amélioration

### Découpage horaire des fichiers

Aujourd'hui, plusieurs échéances horaires sont rassemblées dans le même fichier.
Certains utilisateurs se retrouvent donc obligés de télécharger des données de plusieurs heures alors que seul un instant précis les intéresse. C'est un gagchis de tems et de bande passante pour tout le monde.

Cela permet également, en cas d'échec d'un téléchargement, de ne retélécharger que l'heure qui a échouée et non plus l'intégralité du fichier.

Nous suggérons de découper les fichiers par échéance horaire. C'est ce qui se fait chez les autres organismes météos à l'international.

La manipulation peut être faite d'une simple ligne de commande. Par exemple avec l'utilitaire `wgrib2`

### Mise en place d'une newsletter d'information aux utilisateurs

Il nous semblerait opportun de mettre en place une newsletter d'information pour les utilisateurs des données Météo-France. Ainsi, il serait possible de prévenir à l'avance les utilisateurs d'une modification des services.

## Proposition d'évolution du service premium

Météo-France a mis en place un service premium, probablement afin de couvrir les frais liés à la bande passante.
La tarification de ce service est actuellement incompréhensible et prohibitive.

Nous proposons l'évolution suivante :

- option "open" : mise à disposition des fichiers par le protocole Bittorrent (gratuit car c'est l'utilisateur contribue avec sa bande passante)
- option "base" : téléchargement des fichiers par le protocole http, mais sans engagement de débit (facturé au prix coûtant de la bande passante)
- option "premium" : permettant d'avoir un débit garanti et SLA (facturation libre de la valeur ajoutée)
