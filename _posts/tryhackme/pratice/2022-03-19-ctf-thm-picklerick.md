---
layout: post
title: "[CTF]-[TRYHACKME]-[PRATICE]-[EASY] - Pickle Rick "
date: 2022-03-19
excerpt: "Bounty Hacker - tryhackme.com"
tags: [ctf, tryhackme, thm, pratice, easy, tematico]
---

## Link

Link do [Pickle Rick](https://tryhackme.com/room/picklerick).


## Enunciado

>This Rick and Morty themed challenge requires you to exploit a webserver to find 3 ingredients that will help Rick make his potion to transform himself back into a human from a pickle.

## Execução

Eu não curto máquina temática, fica muito __guessing__ ou o desafio consiste em juntar as pistas e automaticamente vc segue um único fluxo e "owna a máquina" (muitas aspas). No nossa caso, é a segunda opção, então vamos coletar e mapear as evidências e naturalmente o desafio nos vai fornecendo users/senhas/artificios para avançarmos.

Como sempre, começamos por pela etapa de **Information Gathering/Scanning**.

Ultimamente estou na velocidade da luz pra scanning, zero stealth + __--min-rate 200__ pra ctfs, então bora agilizar! Executo o __nmap__ em todas as portas, mas apenas há as seguintes portas abertas: __22 e 80__. Já aproveitei e levantei os serviços e versões, o apache chama atenção, mas como é um ctf __easy__ vou continuar enumerando.

![nmap](/img_posts/ctf/tryhackme/pratice/picklerick/nmap.png)

Meu primeiro alvo é o __ssh__. Tenho creds? Não, mas quero saber se o serviço está aceitando password, chave ou ambos. Nesse __ssh__ apenas é válido login via chave, portanto não tem o que perder tempo com bruteforce caso eu encontre algum __username__ pela aplicação.

![ssh](/img_posts/ctf/tryhackme/pratice/picklerick/ssh.png)

Nossa ultima opção é encontrar alguma pista/vulnerabilidade no site. De inicio aparenta ser um site estático em __php__, mas é necessário coletar mais informações sobre o alvo.

![index](/img_posts/ctf/tryhackme/pratice/picklerick/index.png)

Ao lermos o código fonte da página, podemos encontrar uma informação relevante: O username é __R1ckRul3s__. O __ssh__ não é uma opção, portanto devemos explorar os diretórios pela força bruta.

![source code](/img_posts/ctf/tryhackme/pratice/picklerick/info-source-code.png)

Utilizei o __gobuster__ para enumerar os diretórios utilizando uma wordlist curta. Assim caso eu ache algo, vou trabalhando nos vetores encontrados e depois deixo rodando em uma lista maior. No nosso caso, conseguimos encontrar o diretório __/assets__ bem como os arquivos __robots.txt, portal.php, login.php e denied.php__. Observando os redirecionamentos de __denied.php__ e __portal.php__ para o __login.php__, podemos presumir que é necessário estar autenticado para acessar essas páginas.

![gobuster](/img_posts/ctf/tryhackme/pratice/picklerick/gobuster.png)

Acessando o robots.txt conseguimos a string __Wubbalubbadubdub__. Ao acessarmos o __login.php__ encontramos um formulário de autenticação. Testei credenciais padrões como __admin, administrator, root, rick, picklerickle e R1ckRul3s__ para entender o comportamento da aplicação, mas sem diferença de resposta.

![login](/img_posts/ctf/tryhackme/pratice/picklerick/login-page.png)

Esses ctfs temáticos são complicados: Ou é 100% guessing ou são umas coisas bem toscas, mas paciência. Imaginando o cenário de guessing, já abri o __burpsuite__, fiz uma requisição de login e enviei para o intruder, utilizei o sniper para carregar uma lista pequena de passwords para realizar o bruteforce e inseri o username como __R1ckRul3s__. E sim, dei o play e fui analisar outros vetores, mas zero resultados com o bruteforce.

![burpsuite](/img_posts/ctf/tryhackme/pratice/picklerick/burp-sniper.png)

Ao acessar o diretório __/assets__ não encontrei nada relevante pro desafio, então é aquele momento de: __Putz, o ctf é temático né__. Não teve jeito, inseri o username __R1ckRul3s__ e a senha __Wubbalubbadubdub__ e foi batata, realizou a autenticação com sucesso.

![creds](/img_posts/ctf/tryhackme/pratice/picklerick/stupid-creds.png)

Tendo agora acesso ao __portal.php__, ele apresenta uma box que podemos enviar comandos. Testei __pwd__ e retornou __/var/www/html__, ai virou festa! Testei fazer uma __bind shell__ para minha máquina, afim de agilizar o processo de pós exploração, mas sem sucesso.

![nc](/img_posts/ctf/tryhackme/pratice/picklerick/try-nc.png)

Decidi enumerar o alvo pela própria aplicação, portanto meu primeiro passo é ler o __/etc/passwd__ para encontrar outros usuários, porém foi identificado que a aplicação possui uma restrição ao executar os comandos __cat__, __head__ ou semelhantes. Eu nem tinha me atentado, mas tentei ler o __/etc/passwd__ sem ao menos saber qual o meu usuário! Executei o comando __id__ e retornou __uid=33(www-data) gid=33(www-data) groups=33(www-data)__.

![no cat](/img_posts/ctf/tryhackme/pratice/picklerick/restrict-cat.png)

No diretório __/home__ há dois usuários, __rick__ e __ubuntu__, então vou enumerar as permissões do meu usuário atual. Utilizei o __find__ para procurar binários com o __suid__ ativo, mas sem sucesso, então mandei o CLÁSSICO __sudo -l__ e tcharam, nosso user consegue executar com permissaão root ao utilizar o sudo. Agora é só encontrar as flags dentro da máquina!

![sudo](/img_posts/ctf/tryhackme/pratice/picklerick/permissions.png)

## Flags

Primeira coisa é olhar a pasta do site para encontrar outros diretórios e arquivos que não encontramos com o __gobuster__. Há o arquivo __Sup3rS3cretPickl3Ingred.txt__ e nele há a primeira flag! Só acessar pela raiz da aplicação o arquivo.

![flag-1](/img_posts/ctf/tryhackme/pratice/picklerick/flag-1.png)

Como já tinha enumerado o diretório __/home__, dentro do __/home/rick__ há o arquivo __second ingredients__, como não temos head/cat, movi o arquivo para a pasta do site, assim poderia acessar o arquivo de texto sem problemas. Não esqueça de realizar o __escape do espaço__ ao realizar o comando, no meu caso, utilizei __sudo mv /home/rick/second\ ingredients /var/www/html/__.

![flag-2](/img_posts/ctf/tryhackme/pratice/picklerick/flag-2.png)

Já há flag no site e flag no __/home__, só falta a flag no __/root__! Encontrei o arquivo __3rd.txt__ e movi para a raiz da aplicação, sucesso!

![flag-3](/img_posts/ctf/tryhackme/pratice/picklerick/flag-3.png)


## Mitigação

>Para gerar valor ao nosso exercicio, quais são as mitigações que podem ser aplicadas? Qual o valor que podemos agregar ao cliente/usuário/write-up?

* Retirar a exposição do usuário __R1ckRul3s__ no código fonte do index.
* Alterar a senha do usuário __R1ckRul3s__, desvinculando se seu bordão óbvio e criando uma senha forte e complexa.
* Avaliar a necessidade da aplicação possuir a feature de enviar comandos para a máquina server. Caso necessário, criar recursos para executar apenas comandos espefícios e assumindo o risco que é uma feature vulneravel por natureza.
* Remover permissões totais de super usuário do usuário __www-data__.

CTF temático, meio guessing, mas divertido pra lembrar que: Por mais que se conheça __n__ ferramentas e técnicas, é necessário estar __atento__ as evidências, vetores e informações apresentadas no seu alvo. Podem parecer irrelevantes, mas no decorrer da exploração talvez elas sejam a diferença!

Espero que tenha gostado!


