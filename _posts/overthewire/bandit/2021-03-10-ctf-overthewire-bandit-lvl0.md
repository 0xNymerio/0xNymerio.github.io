---
layout: post
title: "[CTF]-[OVERTHEWIRE]-[BANDIT]- Level - 00 "
date: 2021-03-10
excerpt: "Bandit Level - 00 - overthewire.org"
tags: [ctf, overthewire, bandit]
---

## Enunciado do Level 0
>The goal of this level is for you to log into the game using SSH.
>The host to which you need to connect is bandit.labs.overthewire.org, on port 2220.
> The username is bandit0 and the password is bandit0.
> Once logged in, go to the Level 1 page to find out how to beat Level1.

## Entrando no Level 0
Level 0 é tranquilo, temos que se conectar ao `bandit.labs.overthewire.org` na porta `2220` utilizando as credenciais `bandit0:bandit0`, então podemos dar continuidade para obter a próxima key. Ele recomenda utilizarmos o `ssh`, mas e se não tivermos ele? Como dar continuidade? Quais outras opções temos?

### Bandit0 ? - Solução 1

No proprio manual do __ssh__ `($ man ssh)`, podemos conferir qual a sintaxe correta para se realizar uma conexão e como indicar com qual porta quer se conectar, já que a `porta padrão para ssh é a porta 22`. No próprio exemplo do manual ele já nos indica que o padrão é __user@hostname__.

![Sintaxe do ssh](/img_posts/ctf/overthewire/lvl0/lvl0-1.png)

Como não é uma URI, temos que utilizar o parâmetro __-p__ para indentificar qual a porta correta.

![Parâmetro -p](/img_posts/ctf/overthewire/lvl0/lvl0-2.png)

Com o entendimento de como montar a sintaxe do ssh, podemos realizar a conexão da seguinte forma:
<br><center> <b>$ ssh bandit0@bandit.labs.overthewire.org -p 2220</b></center>

Aparece duas mensagens, sendo:
1. Está autenticando no __host:porta__ espeficiado e que a conexão pode ser efetuada.
2. Sua chave __ECDSA__ foi criada e se você ainda quer dar continuidade na conexão.

 A __ECDSA__ (Elliptic Curve Digital Signature Algorithm) fornece tamanhos de chave menores para operações mais rápidas.

![ECDSA Key](/img_posts/ctf/overthewire/lvl0/lvl0-3.png)

 Aceitando e inserindo a senha __bandit0__ você estará no próximo nivel.

![Shell Bandit](/img_posts/ctf/overthewire/lvl0/lvl0-6.png)

### Bandit0 ? - Solução 2

Se o necessário é apenas realizar uma conexão utilizando o protocolo ssh, podemos então abusar e utilizar qualquer outro cliente ssh! Nesse caso, estou utilizando o __PuTTY__. Não tem segredo, a diferença é que por ser __GUI__ (Graphical User Interface), você tem que colocar as informações nos campos corretos, conforme na imagem abaixo.

 ![PuTTY](/img_posts/ctf/overthewire/lvl0/lvl0-4.png)

Ao aceitar o compartilhamento da chave, você pode acessar, super tranquilo.

![Shell Bandit](/img_posts/ctf/overthewire/lvl0/lvl0-5.png)

## Bandit - Level 0

### Bandit0 - Solução 1

Agora sim, dentro do nível 0, vamos procurar a __key__ para o __bandit1__. Como já sabemos que estamos no diretório __home__, vamos apenas confirmar se o arquivo está na pasta com o comando __ls__ e depois exibir utilizando __cat__ .

![Bandit0-1](/img_posts/ctf/overthewire/lvl0/lvl0-7.png)

> `Não sabe o que ls e cat fazem?` O __ls__ lista os conteúdos de um diretório e o __cat__ exibe o conteúdo dos arquivos em uma saída padrão. Tem diversos materiais para ensinar o básico sobre linux, como por exemplo o [Guia Foca](https://www.guiafoca.org/guiaonline/) e os manuais dos comandos, onde você pode digitar __$ man ls__ ou qualquer outro comando e conhecer mais sobre a ferramenta.


### Bandit0 - Solução 2

>"No linux, só é necessário dois comandos, sendo o __cd__ e o **echo**".

 Apesar de ser um meme bem conhecido, sim, podemos confirmar que o pessoal é criativo e realiza quase tudo com os dois comandos. Pode-se utilizar o **echo** para ler um arquivo com a sintaxe  **$ echo “$(<readme)”**, onde dentro de **$()**, o operador especial **<** lê o conteúdo do arquivo para realizar a expansão. Se quiser entender mais sobre o que é uma expansão dentro do linux, recomendo o artigo da [Linux Command](https://linuxcommand.org/lc3_lts0080.php).

![Bandit0-2](/img_posts/ctf/overthewire/lvl0/lvl0-8.png)
