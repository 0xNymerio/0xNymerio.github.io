---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 02 "
date: 2021-09-07
excerpt: "Bandit Level - 02 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 2
>The password for the next level is stored in a file called spaces in this filename located in the home directory.

Esse desafio é simples, o nome do arquivo é __spaces in this filename__ e digitando o seu nome sem realizar o escape do espaço, o comando __cat__ vai interpretar que você está passando multiplos argumentos, então nao vai conseguir ser o conteudo.


### Bandit2 - Solução 1

A solução mais simples é digitar o inicio do nome do arquivo e utilizar o botão __tab__ para completar com o restante. Importante, não é todo shell que possui o método interativo!

> $ cat spaces __pressionar tab para autocompletar__

### Bandit2 - Solução 2 

Em um shell que não possui interação, utilizar o caractere \ para realizar o escape dos espaços e conseguir digitar o nome do arquivo.

> $ cat spaces\ in\ this\ filename

![Utilizando o cat + escape \\](/img_posts/ctf/overthewire/bandit/lvl2/lvl2-1.png)


