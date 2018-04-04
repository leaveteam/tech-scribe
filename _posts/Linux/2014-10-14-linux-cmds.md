---
layout: post
title: "Trucs et astuces pour Bash"
description: "Trucs et astuces pour Bash"
category: Linux
tags: [Linux, Shell, Bash]
---
{% include JB/setup %}

##Affichage de l'adresse IP##

~~~~~~~
apt-get install moreutils

ifdata -pa eth0
~~~~~~~


##Bash tricks##
The following tricks use the bash history.

~~~~~~~
$ echo toto
toto

$ echo !$
echo toto
toto
~~~~~~~

~~~~~~~
$ echo toto titi
toto titi

$ echo !-1:1
echo toto
toto
~~~~~~~

~~~~~~~
$ echo tata toto titi
tata toto titi

$ echo tutu tyty
tutu tyty

$ echo !-2:2
echo toto
toto
~~~~~~~

~~~~~~~
$ echo tata toto titi
tata toto titi

$ echo tutu tyty
tutu tyty

$ echo !-2:1
echo tata
tata
~~~~~~~

