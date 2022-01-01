---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[NETWORK] - LDAP - Null Bind"
date: 2021-12-30
excerpt: "Challenge LDAP - Null Bind - Root-me.org"
tags: [ctf, rootme, network, challenge]
---

## Enunciado

> It seems that one of the anonymous created a new branch in the LDAP directory, somewhere in : 
> 
> dc=challenge01,dc=root-me,dc=org
> 
> Get access to its data and get his email adress.

__Challenge connection informations__

| __Info__     | __Values__              |
| -----------  | -----------             | 
| __Host__     | challenge01.root-me.org |
| __Protocol__ | TCP                     |
| __Port__     | 54013                   |

## LDAP - Null Bind

Esse não é meu tipo de desafio favorito, não pelo __LDAP__, mas sim por ser um [Guessing CTF](https://www.youtube.com/watch?v=L1RvK1443Yw). Mas de qualquer forma, há umas "dicas" (muitas aspas) para resolvermos o desafio e aprender um pouco.

Sobre o __LDAP__ eu recomendo o video do [Prof Messer](https://youtu.be/F2nFtlS8uEo) para ter um overview do protocolo, mas posso fornecer um super hiper mega resumo: __O LDAP é um protocolo para ler diretórios (records)__. Como sempre, pelo nome do desafio podemos ter algumas dicas sobre o que procurar, então com alguma pesquisa já sabemos que vamos explorar uma vulnerabilidade de vínculo anônimo do LDAP, que permite consultar o servidor LDAP e extrair informações sobre os usuários em seu diretório ativo, incluindo informações de classe de objeto, que podem exibir informações privadas e confidenciais, como números de telefone, e-mails, permissões, grupos etc.

Seja em ambientes Linux ou Windows, esta vulnerabilidade é resultado do servidor LDAP ter sido configurado para permitir conexões anônimas. Neste desafio, vamos nos conectar ao servidor e quando solicitado as credenciais, vamos enviar em branco. A [rfc4513](https://repository.root-me.org/RFC/EN%20-%20rfc4513.txt) explica sobre o mecanismo de autenticação anônima do __LDAP__ na seção __5.1.1__, coloquei o trecho abaixo para facilitar.

```
5.1.1.  Anonymous Authentication Mechanism of Simple Bind

   An LDAP client may use the anonymous authentication mechanism of the
   simple Bind method to explicitly establish an anonymous authorization
   state by sending a Bind request with a name value of zero length and
   specifying the simple authentication choice containing a password
   value of zero length.

```

# Ta, mas e ai?

Pois bem, entendido sobre a vulnerabilidade, vamos realizar uma consulta ao servidor __ldap__ sem qualquer autenticação. Aqui que entra o problema que bati um pouco a cabeça e tive que ler alguns write-ups, temos que adivinhar ou realizar um bruteforce para identificar o nome do branch que foi criado no servidor. Uma das dicas está no próprio enunciado, sendo __one of the anonymous created a new branch__, mas por não possuir um inglês nativo, eu tive a conclusão que estavam falando do grupo [Anonymous](https://pt.wikipedia.org/wiki/Anonymous) ao invés de considerar anonymous como uma __OU__.

 Eu entendi a vulnerabilidade, poderia utilizar qualquer ferramenta como o __ldapminer__ ou o __dirbuster__ para realizar bruteforce para encontrar __OUs__ e __contêineres__ disponíveis, mas como disse no começo, não é meu tipo de desafio e pra mim não ficou claro que __anonymous__ poderia ser uma dica para o nome da __OU__.

Enfim, leite já derramado, agora é realizar a comunicação com o servidor e obter as informações. Aqui eu utilizo o __ldapsearch__, há outras ferramentas, mas ele eu já conheço um pouco. Vamos utilizar o seguinte comando: `ldapsearch -x -b "ou=anonymous,dc=challenge01,dc=root-me,dc=org" -H "ldap://challenge01.root-me.org:54013"`. Sobre os parâmetros utilizados, aqui há suas descrições:

-  -x = Simple authentication
-  -b = base dn for search
-  -H = LDAP Uniform Resource Identifier(s)

![LDAP - Null Bind](/img_posts/ctf/rootme/network/ldap-null-bind.png)

Para evitar que seu sistema fique vulnerável a uma vulnerabilidade anônima do LDAP, desabilitar a autenticação anônima nas configurações do LDAP ou dos serviços da web impedirá qualquer tentativa de conexão e consultas, a menos que as credenciais de domínio sejam fornecidas.

