---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - Hash - DCC2"
date: 2021-12-31
excerpt: "Challenge Hash - DCC2 - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

> Retrieve the password of the Administrator user from the information output by the secretsdump tool of the Impacket suite.

## Hash - DCC2

Agora vamos quebrar uma hash __DCC2__, será tranquilo tranquilo! O desafio nos fornece uma saida do __secretsdump__, temos que identificar a hash e quebra-la. 
```
[*] Dumping cached domain logon information (domain/username:hash)
ROOTME.LOCAL/PODALIRIUS:$DCC2$10240#PODALIRIUS#9d3e8dbe4d9816fa1a5dda431ef2f6f1
ROOTME.LOCAL/SHUTDOWN:$DCC2$10240#SHUTDOWN#9d3e8dbe4d9816fa1a5dda431ef2f6f1
ROOTME.LOCAL/Administrator:$DCC2$10240#Administrator#23d97555681813db79b2ade4b4a6ff25
```
Hashes __DCC2__ são bem fáceis de identificar, se possui o padrão __\$DCC2\$__, tá valendo! Dito isso, a unica hash de usuário __Administrator__ e que possui o padrão __\$DCC2\$__ é a abaixo:

`ROOTME.LOCAL/Administrator:$DCC2$10240#Administrator#23d97555681813db79b2ade4b4a6ff25`

Vou quebrar novamente utilizando o __john__ + wordlist __rockyou__, mas também temos que tratar a hash para ser aceita pela ferramenta. No caso da __DCC2__, precisamos inserir a partir do padrão __\$DCC2\$__ até o final da hash. No nosso exemplo, vamos criar um arquivo e inserir da seguinte forma: 

`$DCC2$10240#Administrator#23d97555681813db79b2ade4b4a6ff25`

Só dar play no bruteforce e pegar a senha em texto claro!

![Hash - DCC2](/img_posts/ctf/rootme/cryptanalysis/hash-dcc2.png)






