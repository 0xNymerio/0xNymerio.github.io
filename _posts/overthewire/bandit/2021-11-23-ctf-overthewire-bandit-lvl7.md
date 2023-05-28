---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 07 "
date: 2021-11-23
excerpt: "Bandit Level - 07 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 7
>The password for the next level is stored in the file data.txt next to the word millionth

### Bandit7 - Solução 1

Dos binários recomendados, a solução mais fácil é ler o arquivo com cat e filtrar com grep utilizando a palava millionth.

> $ cat data.txt | grep millionth

![Utilizando grep](/img_posts/ctf/overthewire/bandit/lvl7-1.png)    
