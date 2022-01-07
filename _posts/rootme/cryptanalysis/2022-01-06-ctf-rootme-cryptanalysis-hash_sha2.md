---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - Hash - SHA-2"
date: 2022-01-06
excerpt: "Challenge Hash - SHA-2 - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

> This hash was stolen during a session interception on a critical application, errors may have occurred during transmission. No crack attempt has resulted so far; hash format seems unknown. Find the corresponding plaintext.
>The answer is the SHA-1 of this password.

## Hash - SHA-2

Metade do desafio é legal, a outra metade é um [Guessing CTF](https://www.youtube.com/watch?v=L1RvK1443Yw), então fica dificil defender.

Nos foi fornecido a hash `96719db60d8e3f498c98d94155e1296aac105ck4923290c89eeeb3ba26d3eef92`. Não consegui identificar visualmente o tipo dela, nenhum identificador de hash reconheceu, o proprio desafio nos revela que __pode ter ocorrido erros na transmissão__, então minha alternativa foi ler a [rfc5754 ](https://repository.root-me.org/RFC/EN%20-%20rfc5754.txt) e não achei uma resposta relevante, então comecei a procurar os atributos que definem um hash __SHA2__.

Após algumas buscas, houve 2 atributos que me chamaram a atenção: Quantidade de caracteres na hash e Caracteres que compõem uma hash __SHA2__. Resumindo a novela, a hash tem que possuir __64 caracteres__ e os caracteres tem que ser __0-9a-fA-F__. A nossa hash possui 65 caracteres e percorrendo a olho nu achei o caractere __k__ na hash, o qual não faz parte do grupo de composição.

Retirando o caractere __k__, já podemos identificar pelo __hash-identifier__ e ele confirmar que é um __SHA-256__. Agora é realizar o bruteforce com o __john__ e conforme solicitado no desafio, informar a hash dessa password em __SHA1__.

![Hash-Identifier](/img_posts/ctf/rootme/cryptanalysis/hash-sha2-1.png)

Agora vem a parte que não gosto desses desafios, a wordlist necessária pra quebrar a hash não era nenhuma das mais conhecidas. `Ah tulio, mas qual o problema disso?` Perca de tempo mesmo, não há motivos pra partir um bruteforce cru, caractere por caractere. A idéia do desafio é boa, aprendi sobre a composição do __SHA2__, mas complica quando eu passo 2 horas procurando por uma wordlist que funcione e bytheWay, pasmem, não achei. Fui ler writes e só tem a string __4dM1n__ como refêrencia.

`Ah tulio, mas você poderia ter feito uma rule pra testar o l33t speak`. Sim, mas denovo, vira __Guessing__, apenas adivinhação.

Enfim, Após o bruteforce com o john, só fazer o hash __SHA1__ de __4dM1n__.

![Hash - SHA-2](/img_posts/ctf/rootme/cryptanalysis/hash-sha2-2.png)

 








