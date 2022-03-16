---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[CRYPTANALYSIS] - Shift Cipher"
date: 2022-03-15
excerpt: "Challenge Shift Cipher - Root-me.org"
tags: [ctf, rootme, cryptanalysis, challenge]
---

## Enunciado

>Index : keep on turning.

## Shift Cipher


Nesse desafio foi fornecido o arquivo __ch7.bin__ e ao olharmos seu conteúdo, recebemos um texto embaralhado. Pela quantidade de caracteres e pelos caracteres apresentados e baseado no nome do desafio, sabemos que é uma criptografia de mover o index a direita ou a esquerda, semelhante a cifra de cesar, mas ela não possuia esses outros tipos de caracteres quando foi elaborada (apenas a-zA-Z). A primeira vista, em vez de utilizar o alfabeto, aparenta ter utilizado a tabela __ASCII__  como index, agora precisamos descobrir o valor de quantos caracteres foram movidos!

![Texto Criptografado](/img_posts/ctf/rootme/cryptanalysis/shift-cipher-1.png)

Eu poderia ser maluco e mover manualmente até descobrir o valor ou realizar uma análise da cadeia dos caracteres e tentar encontrar um padrão para descobrir o valor atual da cifra, mas prefiro utilizar o Python pra realizar um brute force e manualmente inspecionar os resultados que façam sentido ao desafio.

Existem 8 bits em um byte de caractere, então adicionamos 1 bit a cada caractere para cada tentativa do bruteforce. Utilizei o código abaixo para: Carregar o arquivo e para cada caractere vamos adicionar o valor de 1 até 256, retornando o resultado em um array de bytes. Depois eu faço o decode de utilizando __utf-8__ apenas para facilitar a visualização, senão o __bytesarray__ imprime ele mesmo em com o __b"__ no inicio da string, o que nao é um problema, mas fica visualmente poluido pra nossa inspeção visual.

~~~ python
with open("ch7.bin","rb") as f:
    cifrado = f.read()
print(cifrado)

for i in range(256):
    a = (bytearray([(char+i) % 256 for char in cifrado]))
    print(a.decode('utf-8', 'ignore'))
~~~

![Texto Criptografado](/img_posts/ctf/rootme/cryptanalysis/shift-cipher-2.png)

Precisamos mover o index em __246__ ou __-10__, desafio bem elaborado, nada melhor que relembrar que o __ASCII__ originalmente é 7-bits e estudar um pouco mais sobre temas como unicode/utf-8.




 








