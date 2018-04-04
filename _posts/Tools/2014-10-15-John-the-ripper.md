---
layout: post
title: "John The Ripper"
description: "Commandes utiles de l'outil jtr"
category: Tools
tags: [Tools, Shell, Linux, Hack]
---
{% include JB/setup %}

##Hash LM vers Hash NTLM##
Une fois les hashs LM découvert, il est souvent utiles de casser les hashs NTLM afin d'otenir le mot de passe à la casse prêt.

~~~~~~
john --rules=NT --wordlist=lmcracked.txt --format=nt hashdump.txt
~~~~~~
