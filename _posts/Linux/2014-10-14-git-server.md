---
layout: post
title: "Installation d'un serveur git et de gitolite"
description: "Installation d'un serveur git et de gitolite"
category: Linux
tags: [Linux, Git]
---
{% include JB/setup %}

##Installation d'un serveur git##

Installation du serveur de versionnement git sur un serveur Debian :

~~~~~~~
apt-get install git
adduser --system --group --shell /bin/bash --disabled-password git
su git
ssh-keygen -b 4096 -t rsa -f $HOME/.ssh/id_rsa
exit
chsh git -s /usr/bin/git-shell
~~~~~~~

Vérification de la configuration de ssh :

~~~~~~~
$ grep -E 'PubkeyAuthentication|AuthorizedKeysFile' /etc/ssh/sshd_config
PubkeyAuthentication yes
#AuthorizedKeysFile     %h/.ssh/authorized_keys
~~~~~~~


##Création d'un dépôt git##

Création du répertoire côté serveur :

~~~~~~~
chsh git -s /bin/bash
su git
cd
mkdir project.git
cd project.git
git --bare init
chsh git -s /usr/bin/git-shell
~~~~~~~

Récupération du projet côté client :

~~~~~~~
git clone git@gitserver:project.git
cd project
echo README > README
git commit -am 'fix for the README file'
git push origin master
~~~~~~~


##Installation de gitolite##

Installation du gestionnaire de droit pour git :

~~~~~~~
apt-get install gitolite
su git
cd
rm -f .ssh/authorized_keys
gl-setup $HOME/.ssh/id_rsa.pub
chmod a-x .ssh/authorized_keys
~~~~~~~

Pour gérer la configuration :

~~~~~~~
git clone git@gitserver:gitolite-admin
~~~~~~~
