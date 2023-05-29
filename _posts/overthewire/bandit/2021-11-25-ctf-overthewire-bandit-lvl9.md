---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 09 "
date: 2021-11-25
excerpt: "Bandit Level - 09 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 9
>The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

O detalhe aqui é entender que o arquivo **data.txt** é do tipo **data**, portanto utilizar o **grep** para filtrar o conteudo não será possivel.

Aqui temos de entender que o binário **strings** busca todas as sequências de caracteres que podem ser printados, sendo a quantidade minima de 4 caracteres que vem por padrão. Entendendo esse comportamento, conseguimos buscar por sequencias que começam com 2 ou mais **=**.

### Bandit9 - Solução 1

Então temos de ler o arquivo com **strings** e injeto o resultado via **pipe** para o **grep** filtrar se a linha possui 2 ou mais **=** caracteres. Easy peasy!


> $ strings data.txt | grep "=="

![Utilizando strings](/img_posts/ctf/overthewire/bandit/lvl9-1.png)

