---
layout: page
title: TechScribes
tagline: Bienvenue
---
{% include JB/setup %}

## Objectif de ce site

Le but de ce site est de référencer des trucs et astuces, orientés sécurité et tests d'intrusion, sous forme de fiches techniques simples et consises.


## Rédaction des fiches ##

Les fiches sont rédigées en Markdown, ce qui permet leur lecture par un humain et les recherches à l'aide d'outils avancés tel que Grep dans un terminal.


## Historique ##

L'ancienne version des fiches se basait sur l'outil pandoc qui permettait de transcrire l'arborescence de fiches sous forme d'une page HTML complète.

La nouvelle version s'appuie sur le moteur de génération jekyll mis en place pour la génération des pages sur github.io.


## Génération d'une version locale ##

Les fiches sont disponibles sur [GitHub](https://github.com/leaveteam/tech-scribe) ou en clonant le dépôt :

    $ git clone https://github.com/leaveteam/techscribes.git

Les fiches sont lisibles en l'état, mais pour plus de confort, il est possible de générer une version locale :

    $ gem install jekyll
    $ git clone https://github.com/leaveteam/techscribes.git TechScribes
    $ cd TechScribes
    $ jekyll serve

Il suffit ensuite d'ouvrir un navigateur et de se rendre sur [http://127.0.0.1:4000/](http://127.0.0.1:4000/).
