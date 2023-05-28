---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 05 "
date: 2021-11-20
excerpt: "Bandit Level - 05 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 5
>The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:
>
>    human-readable
>
>    1033 bytes in size
>
>    not executable


A senha para o próximo desafio em um arquivo que só pode ser lido por humanos, possui 1033 bytes e não é executavel. Aqui o foco é entender os binários que podem nos auxiliar nessa tarefa, mas fica claro que o __find__ é a melhor solução para o caso.

### Bandit5 - Solução 1

Se observarmos o manual do **find** utilizando `$ man find` , podemos encontrar trechos especificos para cada condição do nosso desafio. Para procurar arquivos executaveis, o **find** possui o argumento `--executable`, mas o detalhe do nosso desafio é que queremos os não executaveis. Para tal, podemos negar o argumento com o parametro `\!` antes do `--executable`. Entender que o operador de negação existe amplia as diversas formas de filtros na sua busca!

Para realizarmos um filtro por size, o **find** também nos proporciona com a possibilidade de especificar se é por bytes, kibibytes, mibibytes e etc. Para nosso caso, podemos utilizar o argumento `-size 1033c`, sendo o caractere **c** para especificar que queremos em bytes.

Para determinar se queremos um filtropara arquivos human-readable, só utilizar o parametro `-readable`.

Com as peças identificadas, só nos resta unir os parametros em um unico argumento e coletar a flag:

> $ find inhere/ -readable \! executable -size 1033c

![Utilizando find](/img_posts/ctf/overthewire/bandit/lvl5-1.png)