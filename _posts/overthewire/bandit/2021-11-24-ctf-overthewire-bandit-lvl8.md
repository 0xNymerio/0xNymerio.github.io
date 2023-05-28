---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 08 "
date: 2021-11-24
excerpt: "Bandit Level - 08 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 8
>The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

Uma password que apenas ocorre uma unica vez em um arquivo de 1001 linhas com várias passwords repetidas, se organizar certinho, podemos achar!

### Bandit8 - Solução 1

Aqui é necessário entender bem como os binários **uniq** e **sort** funcionam.

O **uniq** vai identificar valores unicos dado a top-down do arquivo, linha por linha e comparando os valores proximos para definir se não duplicados ou não. Portanto utilizarmos apenas ele não terá o resultado que queremos, mesmo que usando o parametro `-c` para contar quantas vezes a password aparece. O que teremos é ele identificando como valores unicos porque as linhas não estão ordenadas para ele procurar os valores proximos iguais.

![Utilizando errado o uniq](/img_posts/ctf/overthewire/bandit/lvl8-1.png)

Dessa forma, precisamos então ordenar as passwords utilizando o **sort** e em sequência, podemos contar sua quantidade. No print abaixo há um trecho do output como **"10 knfapnfanwfwaomf"**, identificando então que a password **"knfapnfanwfwaomf"** foi repetida 10 vezes.

Sabendo dessa informação, podemos utilizar o **grep** com o parametro `-v` para especificar o que não queremos e ir filtrando o output, no caso removendo os parametros que se repetem 10 vezes.

> $ cat data.txt | sort | uniq -c | grep -v "10"

![Utilizando sort + uniq](/img_posts/ctf/overthewire/bandit/lvl8-2.png)

