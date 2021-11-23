---
layout: post
title: "[CTF]-[TRYHACKME]-[PRATICE]-[EASY] - Bounty Hacker "
date: 2021-11-14
excerpt: "Bounty Hacker - tryhackme.com"
tags: [ctf, tryhackme, thm, pratice, easy]
---

## Link

Link da [Bounty Hacker](https://tryhackme.com/room/cowboyhacker).

## Enunciado

>You were boasting on and on about your elite hacker skills in the bar and a few Bounty Hunters decided they'd take you up on claims! 
>Prove your status is more than just a few glasses at the bar. I sense bell peppers & beef in your future!

## Execução

Essa é uma máquina simples, mas é necessário entender os fundamentos pra realmente saber o que está fazendo. Como sempre, começamos por pela etapa de **Information Gathering/Scanning**.

Inicio com um Stealth Scan com o Nmap, coloquei apenas as top 400 portas para facilitar. Imaginando que é um CTF de categoria `Easy`, não espero logo de cara encontrar algum serviço em portas altas.

`# nmap -sS -Pn -v --top-ports=400 10.10.97.37` 

![nmap](/img_posts/ctf/tryhackme/pratice/bountyhacker/nmap_ports.png)

Ao perceber uma porta 80 aberta, já inicio o meu **gobuster** para realizar um bruteforce nos diretórios.

`# gobuster dir -e -u http://10.10.97.37/ -w /usr/share/wordlists/dirb/small.txt`

![gobuster](/img_posts/ctf/tryhackme/pratice/bountyhacker/gobuster.png)

Enquanto as ferramentas trabalham, vou inspecionar a página web manualmente. Nada de robots.txt e nada do **gobuster** me indicar qualquer diretório, a primeira análise é de que é uma página estática sem qualquer interação com o usuário.

![index](/img_posts/ctf/tryhackme/pratice/bountyhacker/index.png)

Partindo para os serviços, eu gosto de escanear a versão dos serviços após identifica-los (é só preferencia mesmo, nao é crime não). Consigo as versões do vsftpd 3.0.3 e OpenSSH 7.2p2. Pode existir vulnerabilidades nesses serviços? Sim, mas prefiro interagir com os serviços antes e extrair o máximo de informação. 

`# nmap -sSV -Pn -p 21,22,80 10.10.97.37`

![nmap](/img_posts/ctf/tryhackme/pratice/bountyhacker/nmap_version.png)

Interagindo com o SSH, é possivel se autenticar no serviço utilizando password e chaves. Bruteforce no ssh? Calma, calma.

`# ssh root@10.10.97.37 -vv`

![ssh_pub](/img_posts/ctf/tryhackme/pratice/bountyhacker/ssh_pub.png)

Agora vamos interagir com o FTP e sim, com o clássico **Anonymous Login**. Claro que está habilitado o login anônimo! Há dois arquivos, vamos baixa-los para a máquina e analisar eles. Temos agora então dois arquivos, sendo: **locks.txt** e **task.txt**.

`# ftp 10.10.97.37 `

![ftp](/img_posts/ctf/tryhackme/pratice/bountyhacker/ftp.png)

Do **locks.txt** temos uma lista de várias palavras, minha primeira dedução é que esta é uma lista para realizar um bruteforce. No **task.txt**, temos uma lista de coisas pra fazer, porém ela foi assinada pelo **lin**. Se eu tenho uma lista de bruteforce e outra com a info de um usuário, a receita de bolo é realizar um ataque bruteforce no usuário **lin**.

![info](/img_posts/ctf/tryhackme/pratice/bountyhacker/info.png)

Como já exploramos o serviço do FTP, já parti direto para o bruteforce no ssh. Eu particulamente gosto do **Hydra**, mas fique a vontade de utilizar a ferramenta que quiser. Máquina tranquila, user ownado, agora é logar no serviço e procurar as flags.

`# hydra -l lin -P locks.txt 10.10.97.37 ssh `

![hydra](/img_posts/ctf/tryhackme/pratice/bountyhacker/hydra.png)

Logando então utilizando o comando `# ssh lin@10.10.97.37` e inserindo a senha obtida do bruteforce, conseguimos no diretório do usuário a promeira flag **users.txt**. Agora, se há uma flag users, há uma flag *root*!

![flag1](/img_posts/ctf/tryhackme/pratice/bountyhacker/flag_1.png)

Vamos então escalar privilégio e obter a flag root. Há **n** maneiras, eu sempre busco primeiro os arquivos que o usuário tem permissão de **sudo** para executar. No alvo, conseguimos executar o **tar** como sudo. Há várias formas de escalar privilégio, vou deixar aqui no [link](https://gtfobins.github.io/gtfobins/tar/) um material de apoio para entender a exploração do **tar**. 

`# sudo -l`

Vamos utilizar o **tar** e criar um arquivo. Durante a criação, vamos estabelecer um checkpoint e executar o **/bin/sh**, dessa forma conseguimos escalar o privilégio que precisamos. Agora somos o usuário root e podemos ler a nossa segunda flag: **root.txt**.

`# sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh`

![flag-2](/img_posts/ctf/tryhackme/pratice/bountyhacker/flag_2.png)

## Mitigação

>Para gerar valor ao nosso exercicio, quais são as mitigações que podem ser aplicadas? Qual o valor que podemos agregar ao cliente/usuário/write-up?

* Retirar o login anônimo no ftp.
* Remover os arquivos sensiveis do ftp (locks e taks), ou então protege-los com senha forte.
* Revisar o grupo sudoers e se o usuário **lin** pode utilizar o **tar** com esse tipo de permissão.
  
Espero que tenha gostado!


