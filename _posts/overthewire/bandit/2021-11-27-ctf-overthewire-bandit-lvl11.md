---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 11 "
date: 2021-11-27
excerpt: "Bandit Level - 11 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 11
>The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

O material de apoio já dá a solução para o desafio: utilizar a cifra de substituição **ROT13** para rotacionar os caracteres em 13 posições e então decifrar a mensagem!

No proprio material há a sugestão de utilizar o binário **tr** para realizar a substituição, porém dar um passo a mais.

### Bandit11 - Solução 1

Utilizamos o **tr** para fazer substituição, delete, truncate e etc. Até aqui sem segredo, conforme o material podemos mover de **'A-Za-z'** para '**N-ZA-Mn-za-m'**, o que literal significa mover 13 posições dos caracteres.

> $ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'

![Utilizando o tr para rot13](/img_posts/ctf/overthewire/bandit/lvl11-1.png)

### Bandit11 - Solução 2
Dentro do **vim** há um módulo builtin para **rot13**. Abra o arquivo **$ vim data.txt** e em sequencia digite **":g? rot13"** , realizando então o replace dos caracteres.