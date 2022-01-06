---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - Hash - Message Digest 5"
date: 2022-01-04
excerpt: "Challenge Hash - Message Digest 5 - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

> Crack the given hash.

## Hash - Message Digest 5

To virando mestre em decifrar hashes simples ein! Tranquilo quebrar essa hash, vou pular a parte do __hash-identifier__ porque o nome do desafio já nos revela que é uma hash __MD5__, então vou partir direto pro john e quebra-la. Pelo menos no dia que acessei, a hash nao estava disponivel na página do desafio, então fui procurar ela em algum write-up pra poder conseguir a flag. Pra te adiantar, a hash fornecida é: `7ecc19e1a0be36ba2c6f05d06b5d3058`

Mesmo esquema, __john__ + __rockyou.txt__, só dar play no bruteforce e pegar a senha em texto claro!

![Hash - Message Digest 5](/img_posts/ctf/rootme/cryptanalysis/hash-md5.png)






