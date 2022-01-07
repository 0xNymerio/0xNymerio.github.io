---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - Hash - LM"
date: 2022-01-03
excerpt: "Challenge Hash - LM - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

> Retrieve the password of the Administrator user from the information output by the secretsdump tool of the Impacket suite.
> Note: the flag is in lowercase

## Hash - LM

Another day, another hash. mas dessa vez um hash __LM__. O desafio nos fornece uma saida do __secretsdump__, temos que identificar a hash e quebra-la. 

```
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:d3bf255c530633b9aad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
ASPNET:1025:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DBAdmin:1028:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
sshd:1037:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
service_user:1038:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

Temos apenas uma única hash Administrator, então não vou entrar no mérito de identificar uma hash __LM__. A estrutura é exatamente essa no scretsdump, mas se vc tiver dúvida, utilize o __hash-identifier__. Segue abaixo a hash:

`Administrator:500:d3bf255c530633b9aad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::`

O importante é entender que a estrutura é __NomeUsuário__:__id__:__hashLM__:__hashNTLM__. E claro, vou quebrar utilizando o __john__ + wordlist __rockyou__, apenas fornecendo o trecho do hash __LM__. No nosso exemplo, vamos criar um arquivo e inserir da seguinte forma: 

`d3bf255c530633b9aad3b435b51404ee`

Só dar play no bruteforce e pegar a senha em texto claro!

![Hash - LM](/img_posts/ctf/rootme/cryptanalysis/hash-lm.png)






