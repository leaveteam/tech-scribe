---
layout: post
title: "ImageMagick"
description: "Commandes utiles de l'outil ImageMagick"
category: Tools
tags: [Tools, Shell, Linux]
---
{% include JB/setup %}

## Créer une image ##

La ligne de commande suivante créer une image blanche de 100 pixels par 100 pixels :

~~~~~~~
convert -size 100x100 xc:white canvas.jp
~~~~~~~


## Ecrire du texte sur une image ##

La ligne de commande suivante ecrit en taille 26 "My Caption" en jaune sur l'image orig.jpg aux coordonnées (200, 200) et enregistre le résultat dans dest.jpg.

~~~~~~~
convert -pointsize 26 -fill yellow -draw 'text 200,200 "My Caption" ' orig.jpg dest.jpg
~~~~~~~

Pour aller plus loin, il est possible d'ajouter l'argument "-font Helvetica" pour sélectionner la police.

