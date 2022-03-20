---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - CISCO - Salted Password "
date: 2022-03-15
excerpt: "Challenge CISCO - Salted Password - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

>Your company’s network administrator forgot his administration passwords. He does however have a backup of his startup-config file. Use it to recover his passwords!
>The flag is the concatenation of the "enable" and "administrator" passwords.
>Good luck!

## CISCO - Salted Password

O beneficio de publicar seus ctf's é: Você sabe quando já passou por algum desafio semelhante, então é facil consultar o que você fez! Já realizei um desafio semelhante no [CISCO - password](https://0xnymerio.github.io/ctf-rootme-network-cisco_password), fui dar uma lida e refrescar o que tinha aprendido, então foi tranquilissimo.

Nos é fornecido um arquivo __pcf__ e conforme o enunciado, a flag é a concatenação das senhas de __"enable"__ e __"administrator"__, então como os ids fornecidos é o __5__, nossa única opção é realizar um bruteforce nos hashes fornecidos.

Utilizando o <https://github.com/axcheron/cisco_pwdecrypt> para realizar o bruteforce nas hashes, utilizei a classica __rockyou.txt__.

![Hash - Enable](/img_posts/ctf/rootme/cryptanalysis/cisco-salted-password-1.png)

Nota importante, não esqueça de dar o __escape__ nos caracteres da hash!

![Hash - Administrator](/img_posts/ctf/rootme/cryptanalysis/cisco-salted-password-1.png)

A flag final então é __dolphinsforeverP@ssw0rd!1__, bem tranquilo o desafio.



 








