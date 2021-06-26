# Projet Matrix

## Sommaire

-  [Introduction](#intro)
-  [Qu’est-ce que Matrix ?](#about)
-  [Un peu d’histoire](#story)
-  [Fonctionnement de l’architecture Matrix](#architecture)
-  [1 - Installation d’un serveur Matrix Local](#matrixlan)
-  [2 - Installation d’un serveur Matrix Publique](#matrixwan)
-  [3 - Création d’un relais VoIP avec un TURN SERVER](#voip)
-  [4 - Déploiement d’un Bridge entre Matrix et Telegram](#bridge)
  
[Fichiers de Configurations](#conf)

[Sources](#source)

[Licences](#license)

## Introduction <a id="intro"></a>

Ce dépôt a été créé suite à notre Projet Matrix pour permettre à d'autres utilisateurs de suivres les documentations techniques que nous avons rédigés

Ce projet a été réalisé dans le cadre de notre de 1ère année de notre formation d’ingénieur par apprentissage à Télécom SudParis. 

Nous avions pour consigne de réaliser un projet du 02/02/21 au 29/02/21 en rapport avec l’informatique et les réseaux au choix libre, de maintenir tout au long de son avancement une documentation sur un GitLab accessible par les différents membres du groupe ainsi que le responsable académique et enfin de terminé ce projet par la réalisation d’un rapport et d’une soutenance technique.

Nous avons fait le choix du projet Matrix car il nous a été conseillé par notre responsable académique, qui nous a partagé sont intérêt envers ce projet. 

## Qu’est-ce que Matrix ? <a id="about"></a>

Matrix est un projet open-source de réalisation d’un standard pour l’interopérabilité, la décentralisation et la communication en temps réel au travers du protocole IP.

En effet, il permet à deux utilisateurs de communiquer tout en utilisant un fournisseur de service de communication différent (ex : Element, WhatApp, Signal, ect.). Cette communication peut s’effectuer via un salon textuel, vocal au travers de la VoIP (Voix sur IP), visiophonie, etc.

Matrix est un standard ouvert, ce qui signifie que toute ses spécifications techniques sont publiques et sans restriction d’accès ni de mise en œuvre.

## Un peu d’histoire <a id="story"></a>

Le projet Matrix à été initialement créé par Amdocs, une entreprise multinationale spécialisées dans le logiciel et les services de communications. Amdocs a ensuite financé l'essentiel du travail de développement de 2014 à octobre 2017.

En 2015, une filiale d'Amdocs a été créée, dénommée "Vector Creations Limited", et le personnel Matrix a été transféré dans cette entreprise.En juillet 2017, le financement d'Amdocs avait été annoncé et dans les semaines suivantes, l'équipe principale créait sa propre société basée au Royaume-Uni, « New Vector », principalement conçue pour soutenir le développement de Matrix.

Après avoir annoncé le 23 juin 2020 le changement de nom de Riot.im (le client de messagerie) et New Vector (la société qui développe Riot) ont été renommés Element le 15 juillet 2020.

En juillet 2020, Element, a remporté le plus gros contrat au monde pour un service logiciel collaboratif, fournissant un demi-million de licences pour faciliter la communication dans le système éducatif allemand32

## Fonctionnement de l’architecture Matrix <a id="architecture"></a>

## 1 - Installation d’un serveur Matrix Local <a id="matrixlan"></a>

Documentation technique pour l'installation d'un homeserver Synpase en local disponible [ici](Matrix_LAN.md)

## 2 - Installation d’un serveur Matrix Publique <a id="matrixwan"></a>

Documentation technique pour l'installation d'un homeserver Synpase sur le réseau publique disponible [ici](Matrix_Homeserver_Synapse.md)

## 3 - Création d’un relais VoIP avec un TURN SERVER <a id="voip"></a>

Documentation technique pour la création d'un relais VoiP sur un homeserver Synpase disponible [ici](Activation_webRTC.md)

## 4 - Déploiement d’un Bridge entre Matrix et Telegram <a id="bridge"></a>

Documentation technique pour le déploiement d'un Bridge entre Matrix et Telegram sur un homeserver Synpase disponible [ici](Bridge_Matrix_Telegram.md)

## Fichiers de Configurations <a id="conf"></a>

Fichiers de configurations [Synapse](etc/matrix-synapse)

Fichiers de configurations [Nginx](etc/nginx)

Fichiers de configurations [Bridge Telegram](etc/matrix-synapse/matrix-telegram)

## SOURCES <a id="source"></a>

- [Site Matrix](https://matrix.org)
- [Installation de Synapse](https://www.natrius.eu/dokuwiki/doku.php?id=digital:server:matrixsynapse)
- [Relais VoIP](https://github.com/matrix-org/synapse/blob/develop/docs/turn-howto.md)
- [Bridge Matrix Telegram](https://docs.mau.fi/bridges/python/setup/index.html?bridge=telegram)

## LICENCES <a id="license"></a>

- [Licence Apache](LICENCE_APACHE.txt)
- [Licence Nginx](LICENCE_NGINX.txt)
