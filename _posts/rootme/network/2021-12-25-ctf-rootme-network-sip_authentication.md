---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[NETWORK] - SIP - authentication "
date: 2021-12-25
excerpt: "Challenge SIP - authentication - Root-me.org"
tags: [ctf, rootme, network, challenge]
---

## Enunciado

>Find the password used to authenticate on the SIP infrastructure.

## SIP - authentication

Confesso que nem conhecia o que era __SIP__, mas fui pela lógica e procurei pelas palavras chaves para resolver esse aqui. O desafio nos fornece um txt contendo os seguintes dados:

>172.25.105.3"172.25.105.40"555"asterisk"REGISTER"sip:172.25.105.40"4787f7ce""""PLAIN"1234
>172.25.105.3"172.25.105.40"555"asterisk"INVITE"sip:1000@172.25.105.40"70fbfdae""""MD5"aa533f6efa2b2abac675c1ee6cbde327
>172.25.105.3"172.25.105.40"555"asterisk"BYE"sip:1000@172.25.105.40"70fbfdae""""MD5"0b306e9db1f819dd824acf3227b60e07

Visualmente antes de procurar por SIP, há alguns pontos chaves que me chamaram a atenção para direcionar as buscas, sendo:

* Os ips de comunicação: __172.25.105.3 e 172.25.105.40__.
* As solicitações __REGISTER,INVITE,BYE__.
* Formatos de autenticação: __PLAIN e MD5__.
* Hashes e Texto claro: __1234, aa533f6efa2b2abac675c1ee6cbde327 e 0b306e9db1f819dd824acf3227b60e07__.

A partir dessas informações, comecei a etapa de pesquisa sobre o __SIP__. Os IPs são locais, portanto, irrelavantes no primeiro momento. O material de referência disponibilizado foi a [rfc3261](https://repository.root-me.org/RFC/EN%20-%20rfc3261.txt), onde encontrei as informações a respeito do __REGISTER__. O servidor __SIP__ geralmente precisa de conexão com autenticação, o cliente que deve fornecer a mensagem com o método __REGISTER__ e em seguida enviar uma confirmação OK de que o registro foi salvo com sucesso. Juntando o quebra cabeça de: método de autenticação __REGISTER__ + __PLAIN__ + __1234__, a nossa flag é a senha em texto claro __1234__.

`Ah, mas e se eu estivesse buscando uma ferramentaa que realize um bruteforce nos hashes md5 sem qualquer tratamento no log?` Opa, tenho a solução pra ti! O __sipcrack__ é a ferramenta para isso, só passar a wordlist pelo parâmetro __-w__ + arquivo com os logs e pronto, ele vai interpretar a estrutura do __SIP__ e vai realizar o bruteforce nos hashes.

![sipcrack](/img_posts/ctf/rootme/network/sip-auth.png)
