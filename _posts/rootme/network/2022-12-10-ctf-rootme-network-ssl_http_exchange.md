---
layout: post
title: "[CTF]-[ROOT-ME]-[CHALLENGES]-[NETWORK] - SSL - HTTP Exchange"
date: 2022-12-10
excerpt: "Challenge SSL - HTTP Exchange - Root-me.org"
tags: [ctf, rootme, network, challenge]
---

## Enunciado

>This challenge comes from the 19th DEFCON CTF’s qualification (pkt300).
>
>CLUE : "google is your friend : inurl:server.pem..."

## SSL - HTTP Exchange

O desafio nos fornece um arquivo **ch5.pcap** e temos que encontrar a flag. Como o próprio nome e o material de apoio fornecido para resolver o desafio, podemos presumir que vamos analisar um tráfego criptografado por **SSL/TLS**. Ao abrir o PCAP e utilizando o material de apoio da [RFC2246](https://www.ietf.org/rfc/rfc2246.txt) que detalha o protocolo **TLS 1.0**, podemos indentificar o handshake realizado pelo protocolo no pcap fornecido. Sabemos que é uma comunicação TLS com alvo na porta 4433, porém precisamos da chave privada para descriptografar o tráfego. Seguindo a Stream da comunicação foi possivel identificar a string **M2Crypto Certificate Master1.0**, o que utilizei como base para as buscas da chave.

![ch5.pcap](/img_posts/ctf/rootme/network/ssl-http-1.png)

## Dica - Mindset

Aqui é um ponto muito importante de como extrair mais informações para resolver o desafio (coisa que eu não fiz). Eu utilizei a propria pista fornecida de procurar por chaves privadas expostas + string **M2Crypto Certificate Master1.0** e ir para a tentativa e erro procurando por chaves mais antigas (o desafio é de 2010), mas há uma info que achei muito relevante depois que finalizei o desafio e li um write-up: O **MAC ADDRESS**!

O **MAC ADDRESS** da comunicação é um MAC da Apple, isso é uma grande dica pra um CTF e já diminui a superfície de busca. Literalmente é primeiro [link](https://opensource.apple.com/source/ChatServer/ChatServer-37.4/libraries/Twisted-1.3.0/twisted/test/server.pem.auto.html) do google com as chaves prontas pra serem utilizadas.

## Wireshark + key.pem

Agora não tem segredo, se utilizarmos a chave privada e adicionarmos no wireshark que: para todo protocolo TLS, utilizar aquela chave para descriptografar o trafego, teremos nossa flag! Importante se atentar ao cabeçalho de uma chave RSA e deve-se salver com a extensão `.pem` para adicionar no Wireshark.

```
-----BEGIN RSA PRIVATE KEY-----
MIIBPAIBAAJBAKy+e3dulvXzV7zoTZWc5TzgApr8DmeQHTYC8ydfzH7EECe4R1Xh
5kwIzOuuFfn178FBiS84gngaNcrFi0Z5fAkCAwEAAQJBAIqm/bz4NA1H++Vx5Ewx
OcKp3w19QSaZAwlGRtsUxrP7436QjnREM3Bm8ygU11BjkPVmtrKm6AayQfCHqJoT
ZIECIQDW0BoMoL0HOYM/mrTLhaykYAVqgIeJsPjvkEhTFXWBuQIhAM3deFAvWNu4
nklUQ37XsCT2c9tmNt1LAT+slG2JOTTRAiAuXDtC/m3NYVwyHfFm+zKHRzHkClk2
HjubeEgjpj32AQIhAJqMGTaZVOwevTXvvHwNEH+vRWsAYU/gbx+OQB+7VOcBAiEA
oolb6NMg/R3enNPvS1O4UU1H8wpaF77L4yiSWlE0p4w=
-----END RSA PRIVATE KEY-----
```
No Wireshark abrir: `Edit > Preferences > Protocols > TLS > RSA Key List (edit)`. Então é necessário determinar para qual host, porta e protocolo será utilizado a chave. 

![Wireshark Config](/img_posts/ctf/rootme/network/ssl-http-2.png)

Em sequência o proprio Wireshark realiza o reload do arquivo e será possivel olhar o conteudo enviado após a troca da cifra ser confirmada (Change Cipher Spec Finished). No caso há um erro de mal formatado e o próprio Wireshark determina uma alerta, mas essa é nossa flag!

![Decrypted TLS](/img_posts/ctf/rootme/network/ssl-http-3.png)

