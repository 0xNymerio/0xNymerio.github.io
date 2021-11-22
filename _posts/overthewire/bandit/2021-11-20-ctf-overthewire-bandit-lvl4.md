---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 04 "
date: 2021-11-19
excerpt: "Bandit Level - 04 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 4
>The password for the next level is stored in the only human-readable file in the inhere directory. 
>Tip: if your terminal is messed up, try the “reset” command.

A senha para o próximo desafio em um arquivo que só pode ser lido por humanos, ou seja, está em um texto plano. Há diversas formas de codificar texto plano, sendo as mais comuns: __ASCII__ e __UTF_8__. A maneira mais facil é entender qual o tipo do arquivo, assim o header dele nos indica o que ele realmente é. Há várias formas de resolver esse desafio, mas das maneiras que eu conheço, todas vão ler o header do arquivo. Qual o problema de ler o header do arquivo? Nenhum! Mas não precisa inventar firula com comando de 3 linhas pra fazer a mesma coisa :D

### Bandit4 - Solução 1

Utilizando o __file__, podemos inspecionar os headers dos arquivos. Conforme na imagem abaixo, temos várias que são __data__ e apenas um que é __ASCII text__. É só ler o arquivo e já temos o nosso password.

> $ file inhere/*

![Utilizando file \\](/img_posts/ctf/overthewire/bandit/lvl4/lvl4-1.png)