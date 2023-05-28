---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 06 "
date: 2021-11-21
excerpt: "Bandit Level - 06 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 6
>The password for the next level is stored somewhere on the server
> and has all of the following properties:
>
>    owned by user bandit7
>
>    owned by group bandit6
>
>    33 bytes in size



A senha para o próximo desafio está em um arquivo que deve ter as seguintes propriedades:
- bandit7 é o dono do arquivo
- o arquivo pertence ao grupo bandit6
- possui 33bytes de tamanho.
- Pode estar em qualquer diretório do server

Como na solução passada, já sabemos que com o parametro `-size` podemos especificar a quantidades de bytes que um arquivo tem, agora nos resta idenficar os outros parametros!

### Bandit6 - Solução 1

Fácil fácil, olhando no manual novamente, `-user` para especificar o owner do arquivo e `-group` para especificar o grupo. Como estamos procurando em todo o servidor, vulgo diretório root, estou utilizando o `2>/dev/null` para enviar todos os outputs de erro para null, assim fica mais fácil de identificar o que queremos.

> $ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null

![Utilizando find](/img_posts/ctf/overthewire/bandit/lvl6-1.png)