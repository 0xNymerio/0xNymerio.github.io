---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - Hash - DCC"
date: 2022-01-02
excerpt: "Challenge Hash - DCC2 - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

> Retrieve the password of the Administrator user from the information output by the secretsdump tool of the Impacket suite.

## Hash - DCC

Primeiro desafio quebrando hashes, inclusive essa aqui eu dei uma patinada por algo bobo, mas sempre tem solução! O desafio nos fornece uma saida do __secretsdump__ e dela, devemos localizar a hash __DCC__ e depois quebra-la por bruteforce.

```
[*] Dumping cached domain logon information (domain/username:hash)
ROOTME.LOCAL/PODALIRIUS:$DCC2$10240#PODALIRIUS#9d3e8dbe4d9816fa1a5dda431ef2f6f1
ROOTME.LOCAL/SHUTDOWN:$DCC2$10240#SHUTDOWN#9d3e8dbe4d9816fa1a5dda431ef2f6f1
ROOTME.LOCAL/Administrator:15a57c279ebdfea574ad1ff91eb6ef0c:Administrator
```

O __DCC__ é um hash de domínio Windows, como buscamos o usuário __Administrator__, nossa única opção para quebrar é o hash abaixo:

`ROOTME.LOCAL/Administrator:15a57c279ebdfea574ad1ff91eb6ef0c:Administrator`

Agora o pulo do gato, o desafio nos forneceu um link que contém [exemplos dos hashes para o hashcat](https://hashcat.net/wiki/doku.php?id=example_hashes), porém não fica claro no exemplo já que a hash fornecida não possui um padrão semelhante. Pesquisando um pouco, o padrão __DCC__ para quebrarmos é: __usuário__:__string de numeros__. Caso não seja tratado assim a entrada, nossa ferramenta não vai conseguir identificar a hash corretamente.

Entendido a estrutura, vamos criar um arquivo e inserir a seguinte hash: __Administrator:15a57c279ebdfea574ad1ff91eb6ef0c__. Eu preferi utilizar o __john__ e apontar a wordlist __rockyou__ (amplamente utilizada em ctfs), mas ai a escolha da ferramenta é a gosto do freguês.

Só dar play no bruteforce e pegar a senha em texto claro!

![Hash - DCC](/img_posts/ctf/rootme/cryptanalysis/hash-dcc.png)






