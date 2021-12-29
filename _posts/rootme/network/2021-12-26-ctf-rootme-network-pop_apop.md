---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[NETWORK] - POP - APOP "
date: 2021-12-25
excerpt: "Challenge POP - APOP - Root-me.org"
tags: [ctf, rootme, network, challenge]
---

## Enunciado

>Find the user password in this network frame.

## POP - APOP

O desafio nos fornece um arquivo **ch23.pcap** e temos que descobrir a senha utilizada pelo usuário. Claro, vamos utilizar o __wireshark__ e analisar o tráfego. Já sabemos pelo nome do desafio que vamos analisar um tráfego __POP__, então já vou filtrar direto pelo protocolo e ignorar o restante das comunicações apresentadas.

![POP - APOP - 1](/img_posts/ctf/rootme/network/pop-apop-1.png)

Dentro do __wireshark__ há a funcionalidade __follow tcp stream__, onde ele traz o fluxo dos pacotes na mesma ordem que a camada da aplicação vê (Application layer - [Modelo OSI](https://pt.wikipedia.org/wiki/Modelo_OSI)). Assim podemos observar em texto claro o que foi trafegado. Eu não conhecia os detalhes do protocolo __POP__, mas o material de referência da [rfc1939](https://repository.root-me.org/RFC/EN%20-%20rfc1939.txt) foi crucial para resolver o desafio. Inicialmente imaginei que o protocolo passava "as claras" o login/senha pelo método __APOP__, então parti para um bruteforce no hash `4ddd4137b84ff2db7291b568289717f0`. Utilizei o __hash-identifier__ e testei algumas opções e não obtive sucesso, portanto, temos que ler a __rfc1939__ e entender a estrutura e o comportamento de autenticação do protocolo!

![POP - APOP - 2](/img_posts/ctf/rootme/network/pop-apop-2.png)

 Vou resumir porque os detalhes estão no link da __rfc1939__, mas a autenticação pelo método __APOP__ se dá por um parâmetro __digest__ (não sei traduzir isso, sorry), onde ele é calculado ao aplicar um algoritmo __MD5__ em uma string que contem o seguinte conteudo: timestamp com os colchetes angulares e seguido por uma __secret key__. O resultado do __digest__ é um valor de 16 octetos enviado em hexadecimal, utilizando caracteres ASCII minúsculos.

 O exemplo da __rfc1939__ é muito claro, pega a string `<1896.697170952@dbc.mtview.ca.us>tanstaaf` (composta pelos timestamp + secret key) e aplica um algoritmo __MD5__. Só isso? Pois é!

```
Examples:
Server: +OK POP3 server ready <1896.697170952@dbc.mtview.ca.us>
Client: APOP mrose c4c9334bac560ecc979e58001b3e22fb
Server: +OK maildrop has 1 message (369 octets)
 
In this example, the shared  secret  is  the  string  `tanstaaf'.
Hence, the MD5 algorithm is applied to the string: <1896.697170952@dbc.mtview.ca.us>tanstaaf
which produces a digest value of: c4c9334bac560ecc979e58001b3e22fb
```

Agora está fácil resolver o desafio, só precisamos de uma wordlist com algumas passwords mais utilizadas e concatenar com a string `<1755.1.5f403625.BcWGgpKzUPRC8vscWn0wuA==@vps-7e2f5a72>` fornecida pelo desafio. Depois só aplicar o algoritmo MD5 e comparar com a hash MD5 capturada no tráfego, ai tá pronto o sorvetinho!

Para uma melhor visualização do fluxo, será o seguinte loop: `<1755.1.5f403625.BcWGgpKzUPRC8vscWn0wuA==@vps-7e2f5a72>passwdDaWordlist` -> Algoritmo MD5. __Se__ essa saida for == 4ddd4137b84ff2db7291b568289717f0, __then pwned__!


## Do or do not, there is no try

Eu utilizei a wordlist __rockyou__ porque ela é grande e comum de ser utilizada em ctfs. Criei um script em __python__ para realizar essa tarefa. `Ah Tulio, mas você poderia ter usado o john/hashcat/qualquer coisa e passar a string para concatenar como salt e etc e etc etc`. Sim! Poderia, mas prefiro exercitar meu código, ainda mais por ser um desafio simples, é bom para manter afiado alguns aspectos da linguagem.

Novamente, o fluxo é simples: Para cada palavra na wordlist __rockyou__, concatenar a string capturada (__pop_string__) com a senha (__passwd__) e aplicar o hash __md5__. Se o hash for igual ao hash capturado durante a análise do tráfego (__hash_alvo__), então printar na tela  a __passwd__ utilizada para atingir qual resultado. Abaixo há o código em python que utilizei nesse desafio.

~~~ python
import hashlib
import base64

wordlist = "/usr/share/wordlists/rockyou.txt"
hash_alvo = "4ddd4137b84ff2db7291b568289717f0"
pop_string= "<1755.1.5f403625.BcWGgpKzUPRC8vscWn0wuA==@vps-7e2f5a72>"

with open(wordlist, encoding="ISO-8859-1" ) as f:
    word_lines = f.read().splitlines()

for passwd in word_lines:
    print("[Testing] -  " + passwd )
    concat_hash=pop_string + passwd
    md5_hash = hashlib.md5(concat_hash.encode('utf-8')).hexdigest()
    if md5_hash == hash_alvo:
        print("[FOUND]    Passwd:" + passwd)
        exit(1)
~~~

E claro que o script funciona, desafio bem divertido de estudar.

![POP - APOP - 3](/img_posts/ctf/rootme/network/pop-apop-3.png)
