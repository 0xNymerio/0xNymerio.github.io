---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 03 "
date: 2021-11-19
excerpt: "Bandit Level - 03 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 3
>The password for the next level is stored in a hidden file in the inhere directory.

A senha para o próximo desafio está em um arquivo oculto, há mil maneiras de achar e ler arquivos ocultos, hoje vou mostrar duas formas!

### Bandit3 - Solução 1

A mais simples de todas, utilizar os parâmetros corretos para o comando __ls__. O __-l__ exibe em detalhes, mostrando permissões, datas, usuários e grupos e claro, os arquivos. O __-h__ exibe em __human redable__, que seja melhor para humanos lerem informações como quantidade de bytes do arquivo, funciona quando utilizamos o __-l__ ou o __-s__ em conjunto. Falando em __-s__, ele exibe o tamanho dos arquivos em blocos. Por último o __-a__, o qual exibe arquivos comuns e arquivos ocultos.

Apesar de uma longa explicação dos parâmetros do __ls__, já sabemos que o arquivo está dentro do diretório __inhere__. Para eixibir então os arquivos ocultos, em detalhes e com tamanhos em leitura mais agradavel (giga e mega), podemos utilizar o comando a seguir. Claro, o __.__ simbola que estamos buscando no diretório atual, então suponho que você já tenha entrado no diretório __inhere__.

> $ ls -lhsa .

Todo arquivo oculto em linux/unix começa com __.__ (ponto), a fim de simbolizar que é um arquivo oculto. Para ler o arquivo, se atentar de inserir o nome completo, no caso: __.hidden__.

![Utilizando ls \\](/img_posts/ctf/overthewire/bandit/lvl3/lvl3-1.png)

### Bandit3 - Solução 2 

Essa solução é mais sofisticada, mas apenas por um motivo: caso nós _não_ soubessemos onde ou qual diretório o arquivo oculto está o escondido dentro do __inhere__, poderiamos procurar recursivamente utilizando o __find__. Estou utilizando os parâmetros __name__ para determinar o começo do nome do arquivo, que no caso é ".*" . O ponto __.__ é por ser um arquivo oculto e o __*__ é um __wildcard__ que simboliza um numero qualquer (um ou mais) de caracteres e números.

> $ cat spaces\ in\ this\ filename

![Utilizando find \\](/img_posts/ctf/overthewire/bandit/lvl3/lvl3-1.png)