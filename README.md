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
La société BPZ Labs se tient à la disposition de Météo-France pour l'aider gratuitement à mettre en place son propre serveur tracker si l'institution le souhaite. La société BPZ Labs peut également fournir à Météo-France un serveur tracker prêt à l'emploi.

### Seed initial

#### Seed par client Bittorrent
#### Seed par serveur Webseed

## Mise en œuvre opérationnelle

## Points potentiels d'amélioration

## Proposition d'évolution du service premium
