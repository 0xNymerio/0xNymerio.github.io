---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - Encoding - ASCII"
date: 2021-12-31
excerpt: "Challenge Encoding - ASCII - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

> Decode the string.

## Encoding - ASCII

Primeiro desafio tranquilo, recebos uma string e temos que converte-la para __ASCII__. A string __4C6520666C6167206465206365206368616C6C656E6765206573743A203261633337363438316165353436636436383964356239313237356433323465__ não nos diz muita coisa, mas pela minha experiência, minha primeira tentativa é que seja uma string em __hex__.

Bora testar então! Vou utilizar o comando `echo "4C6520666C6167206465206365206368616C6C656E6765206573743A203261633337363438316165353436636436383964356239313237356433323465" | xxd -r -p`, sendo o __echo__ para imprimir a string no terminal e utilizar o __xxd__ para revertemos de __hex to ASCII__. Claro, os parâmetros __-r__ é para reverse e o __-p__ é para plain output.

![Encoding - ASCII](/img_posts/ctf/rootme/cryptanalysis/encoding-ascii.png)


