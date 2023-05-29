---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 10 "
date: 2021-11-26
excerpt: "Bandit Level - 10 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 10
>The password for the next level is stored in the file data.txt, which contains base64 encoded data

Repita comigo: **base64 não é crytografia!** Como o conteúdo está encodado em base64, podemos utilizar o próprio binário **base64** para realizar o decode!

### Bandit10 - Solução 1

Em 90% das vezes conseguimos identificar que um arquivo está encodado por **base64** quando ele termina com o caractere **=**, porém à exceções! O ideal é realizar a validação desencodando.

Observando manual de **base64**, o parâmetro `-d` é para a opção de decode.

> $ cat data.txt | base64 -d

![Utilizando strings](/img_posts/ctf/overthewire/bandit/lvl10-1.png)

