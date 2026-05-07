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
- **Sistema Alvo:** Metasploitable 2 (IPs: `192.168.56.101` e `192.168.56.102`)
- **Ferramentas:** Nmap, Medusa, Bash
- **Virtualização:** Oracle VM VirtualBox

---

## 🚀 Execução da Auditoria

### 1. Reconhecimento Prévio (Footprinting)
O primeiro passo foi identificar quais portas e serviços estavam abertos no servidor alvo. Para isso, foi utilizado o Nmap:
```bash
nmap -sV -p 21,22,80,445,139 192.168.56.101
