---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 01 "
date: 2021-09-08
excerpt: "Bandit Level - 01 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 1
>The password for the next level is stored in a file called - located in the home directory.

O arquivo se chama __-__ e já sabemos que está no diretório home, agora nos resta entender como ser esse arquivo. No material de referências há informações sobre __dashed filename__ e o objetivo é entendermos como podemos realizar o escape desse caracter, já que o bash ou o kernel não o interpretam como um caracter especial. Na maioria dos comandos, ele simbola que você está passando um argumento, como __-flag__ ou __-ip__, então quando você tentar ser esse arquivo se uma forma simples como __cat -__, ele vai interpretar que você estava inserindo o inicio de alguma argumento especial do __cat__ ou de qualquer outro comando.


### Bandit1 - Solução 1

Podemos utilizar o __cat__ para ler o arquivo, porém ele interpreta a leitura de um __dashed filename__ de um jeito diferente. O __cat__ enxerga o caractere __-__ como uma string e ele a trata como um sinônimo de __stdin__. A unica forma de contornar isso é alterar como o __cat__ vê a string __-__ de uma forma que ainda se refira a um arquivo chamado __-__. A maneira usual de fazer isso é prefixar o nome do arquivo com um caminho __./-__ ou utilizando o caminho absoluto __/home/nym/-__. Essa é uma técnica também utilizada para contornar problemas semelhantes em que as opções de linha de comando entram em conflito com nomes de arquivos, em exemplo __./-e__ não aparece como a opção de linha de comando __-e__ para um programa.

> $ cat ./-

![Utilizando o cat - v1](/img_posts/ctf/overthewire/lvl1/lvl1-1.png)

### Bandit1 - Solução 2 

Ainda utilizando o __cat__, outra maneira é utilizando o operador de redirecionamento __<__ e injetar o arquivo no comando __cat__.

> $ cat < -

![Utilizando o cat - v2](/img_posts/ctf/overthewire/lvl1/lvl1-2.png)


