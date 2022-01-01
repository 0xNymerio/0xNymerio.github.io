---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - Encoding - UU"
date: 2021-12-31
excerpt: "Challenge Encoding - UU - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

> Get the validation password.

## Encoding - UU

Eu não conhecia sobre esse __UU Enconding__ e como não conheço, a primeira coisa é ler o material de referência para entender a estrutura do encode. Nesse desafio nos é fornecido uma string que possui um padrão bem peculiar e dado o material de refência, potemos já entender qual trecho que devemos decodificar.

Exemplo do material:
```
begin mode filename
. . .encoded data. . .
“empty” line
end
```
Da string recebida e comparando com a do material, sabemos que vamos decodificar o seguinte trecho: __B5F5R>2!S:6UP;&4@.RD*4\$%34R`](%5,5%)!4TE-4$Q%"@``__

```
_=_ 
_=_ Part 001 of 001 of file root-me_challenge_uudeview
_=_ 

begin 644 root-me_challenge_uudeview
B5F5R>2!S:6UP;&4@.RD*4$%34R`](%5,5%)!4TE-4$Q%"@``
`
end
```

Agora tá fácil! Em vez de inventar mil firulas, fui direto pra um desses encode/decoders online, no caso utilizei o esse aqui: [Decode UU](http://uuencode.online-domain-tools.com/).

Tá pronto o sorvetinho.

![Encoding - UU](/img_posts/ctf/rootme/cryptanalysis/encoding-uu.png)






