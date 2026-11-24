# 📅 Plano de 6 semanas — Portfólio SOC N1

**Ritmo:** ~1h30 por dia em dias úteis + 3h no fim de semana (~10h/semana).
**Custo:** R$ 0. Todas as ferramentas são gratuitas. Algumas salas do TryHackMe exigem assinatura; se der problema, use as alternativas citadas.
**Hardware:** mínimo 8 GB de RAM (16 GB ideal para a semana 4, que roda 3 VMs).

## Regras de ouro
1. **Só ataque máquinas suas**, numa rede isolada (host-only). Atacar sistemas de terceiros é crime (Lei 12.737/2012).
2. **Só commite o que você executou e consegue explicar.**
3. **Faça commit todo dia que estudar**, mesmo que pequeno ("anotações sobre handshake TCP").
4. Use o [`templates/writeup-template.md`](templates/writeup-template.md) em todo lab. Tire prints de tudo.
5. Ao fim de cada semana, responda em voz alta as **perguntas de entrevista** sem consultar nada. Se travar, revise.

---

## Semana 0 — Preparação (1 fim de semana)

- [ ] Instalar **VirtualBox**
- [ ] Criar VM **Ubuntu Server 24.04** (2 GB RAM) → será o "alvo"
- [ ] Criar VM **Kali Linux** (2 GB RAM) → será o "atacante"
- [ ] Configurar as duas numa rede **Host-Only** e testar `ping` entre elas
- [ ] Tirar *snapshot* das duas VMs (para voltar se quebrar algo)
- [ ] Criar conta no **TryHackMe**, **LetsDefend** e **CyberDefenders**
- [ ] Aprender o básico de git: `add`, `commit`, `push`, `log`
- [ ] Subir este repositório no GitHub e preencher o "Sobre mim"

**Entregável:** README com "Sobre mim" e um print da topologia do lab (pode ser desenhado no draw.io).

---

## Semana 1 — Fundamentos de rede e Wireshark

**Objetivo:** enxergar a rede "por dentro" e entender o que é tráfego normal.

**Estudo**
- Modelo OSI x TCP/IP, TCP x UDP, handshake de 3 vias, portas comuns, subnetting /24 a /30
- TryHackMe: salas como *Wireshark: The Basics*, *Nmap Live Host Discovery*, *Nmap Basic Port Scans*

**Prática**
- [ ] Capturar com Wireshark um acesso HTTP e identificar o handshake SYN → SYN/ACK → ACK
- [ ] Capturar uma consulta DNS e achar a pergunta e a resposta
- [ ] Do Kali, rodar `nmap -sS` e `nmap -sT` contra o Ubuntu, capturando com `tcpdump -w scan.pcap`
- [ ] No Wireshark, mostrar a diferença entre os dois scans (o -sS não completa o handshake)
- [ ] Usar o filtro `tcp.flags.syn==1 && tcp.flags.ack==0` e explicar o que ele mostra

**Entregável:** `semana-01-redes-wireshark/README.md` com writeup e prints, mais uma tabela "porta → serviço → risco" com 15 portas feita por você.

**Perguntas de entrevista**
- Qual a diferença entre TCP e UDP? Dê um exemplo de cada.
- O que acontece no handshake TCP? Como um SYN scan se aproveita dele?
- Quantos hosts cabem numa /26?
- Você vê muito tráfego na porta 53 saindo para um IP externo desconhecido. O que pode ser?

---

## Semana 2 — Logs Linux e detecção de brute force

**Objetivo:** ler logs crus e detectar um ataque sem ferramenta pronta.

**Estudo**
- Onde ficam os logs: `/var/log/auth.log`, `journalctl`, syslog
- `grep`, `awk`, `cut`, `sort`, `uniq -c`, o básico de expressões regulares
- MITRE ATT&CK T1110 (Brute Force)

**Prática**
- [ ] No Ubuntu, habilitar SSH com login por senha e criar um usuário com senha fraca
- [ ] Do Kali, rodar `hydra` contra o SSH com uma wordlist pequena (a senha certa deve estar nela)
- [ ] Com **um comando só** (pipes), listar os IPs com mais falhas:
      `grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn`
- [ ] Encontrar no log o momento exato do login bem-sucedido do atacante
- [ ] Escrever um script Python **seu** que faça o mesmo (pode consultar o `ssh_bruteforce_detector.py` do repo de referência, mas reescreva e entenda cada linha)
- [ ] Instalar o `fail2ban`, repetir o ataque e mostrar o IP sendo bloqueado

**Entregável:** writeup + script + prints do antes/depois do fail2ban.

**Perguntas de entrevista**
- Como diferenciar brute force de password spray?
- Um IP teve 300 falhas e depois 1 sucesso. Qual a sua ação?
- Quais medidas impedem brute force em SSH?

---

## Semana 3 — SIEM com Wazuh

**Objetivo:** ver o mesmo ataque da semana 2 virar **alerta** num SIEM.

**Estudo**
- O que é SIEM, agente, decoder, regra, severidade
- TryHackMe: sala *Wazuh* (alternativa: documentação oficial "Quickstart")

**Prática**
- [ ] Instalar o Wazuh (opção mais leve: a OVA oficial ou o *all-in-one* numa VM com 4 GB)
- [ ] Instalar o agente no Ubuntu e confirmar que ele aparece no painel
- [ ] Repetir o hydra e encontrar os alertas (regra 5710/5712 e afins)
- [ ] Ver o mapeamento MITRE que o Wazuh faz automaticamente
- [ ] Ativar o **File Integrity Monitoring** em `/etc` e alterar um arquivo para gerar alerta
- [ ] Criar **uma regra customizada** sua (ex.: alerta quando alguém usa `sudo su`)

**Entregável:** writeup com prints do dashboard, da regra customizada e do alerta disparando.

**Perguntas de entrevista**
- O que é um SIEM e por que o SOC precisa dele?
- O que é falso positivo? Como você reduziria falsos positivos de uma regra?
- Chegou um alerta de severidade alta. Quais os seus primeiros 5 passos?

---

## Semana 4 — Windows Event Logs e Sysmon

**Objetivo:** dominar os logs do Windows, que são a maioria dos alertas num SOC corporativo.

**Estudo**
- Event IDs: 4624, 4625, 4688, 4720, 4732, 1102, mais os Logon Types (2, 3, 10)
- Sysmon: Event ID 1 (processo), 3 (rede), 11 (arquivo)
- TryHackMe: salas como *Windows Event Logs* e *Sysmon*

**Prática**
- [ ] Baixar a VM gratuita de avaliação do **Windows 10/11** ou do Windows Server (site da Microsoft)
- [ ] Instalar o **Sysmon** com a config do SwiftOnSecurity e o **agente Wazuh**
- [ ] Gerar e encontrar no Event Viewer **e** no Wazuh:
  - [ ] logons falhos (4625)
  - [ ] criação de usuário (4720) e adição ao grupo Administradores (4732)
  - [ ] PowerShell com `-EncodedCommand` (codifique um comando inofensivo como `whoami`)
- [ ] Escrever **uma regra Sigma** sua para um desses eventos e convertê-la com o `sigma-cli`

**Entregável:** writeup + regra Sigma + tabela de Event IDs feita por você com exemplos reais dos seus prints.

**Perguntas de entrevista**
- Diferença entre os eventos 4624 e 4625? O que significa Logon Type 10?
- Por que um atacante usaria PowerShell codificado em Base64?
- O que você pensa ao ver o evento 1102?

---

## Semana 5 — Análise de phishing

**Objetivo:** fazer a triagem mais comum de um analista N1.

**Estudo**
- Cabeçalhos de e-mail, SPF, DKIM, DMARC, `Return-Path` x `From`
- Ferramentas: VirusTotal, URLScan.io, AbuseIPDB, MXToolbox, CyberChef
- TryHackMe: salas como *Phishing Analysis Fundamentals* (alternativa: alertas gratuitos do LetsDefend)

**Prática**
- [ ] Pegar 3 e-mails reais de phishing (sua caixa de spam ou o repositório público *phishing_pot* no GitHub)
- [ ] Para cada um: analisar os cabeçalhos, extrair IOCs (URLs, domínios, IPs, hashes) e consultar a reputação
- [ ] **Nunca abrir anexos ou links na sua máquina.** Use o URLScan ou uma sandbox online
- [ ] Classificar cada um como malicioso, spam ou legítimo, com justificativa
- [ ] Escrever **seu próprio** playbook de phishing (pode comparar com o do repo de referência depois)
- [ ] Resolver pelo menos 5 alertas no LetsDefend e anotar o raciocínio

**Entregável:** 3 análises de e-mail + playbook + resumo dos alertas resolvidos no LetsDefend.

**Perguntas de entrevista**
- Como você confirma se um e-mail é phishing?
- Um usuário clicou no link e digitou a senha. O que fazer, em ordem?
- O que é SPF? Ele sozinho impede spoofing?

---

## Semana 6 — Suricata + projeto final (incidente completo)

**Objetivo:** juntar tudo num caso de ponta a ponta. É o que você vai **contar na entrevista**.

**Prática: IDS**
- [ ] Instalar o **Suricata** no Ubuntu
- [ ] Escrever 3 regras suas (ex.: SYN scan, User-Agent `sqlmap`, `/etc/passwd` na URL)
- [ ] Disparar cada uma a partir do Kali e mostrar o alerta no `fast.log`
- [ ] Integrar o `eve.json` ao Wazuh

**Projeto final: simular uma cadeia de ataque e investigá-la como analista**
1. Kali faz um port scan no Ubuntu (Recon, T1046)
2. Brute force no SSH com hydra (T1110)
3. Login bem-sucedido
4. Criação de usuário para persistência: `sudo useradd backdoor` (T1136)
5. Download de um arquivo "malicioso" (um `.txt` qualquer) via `wget` de um servidor no Kali

Depois, **sem olhar o que você fez**, investigue só pelo Wazuh e pelo Suricata e escreva um **relatório de incidente** com:
- [ ] Resumo executivo (para um gestor, sem termos técnicos)
- [ ] Timeline com horários tirados dos logs
- [ ] IOCs
- [ ] Mapeamento MITRE ATT&CK
- [ ] Contenção, erradicação e recomendações

**Entregável:** pasta do projeto final com o relatório. **Fixe este repositório no seu perfil do GitHub.**

**Perguntas de entrevista**
- Me conte um incidente que você investigou. (Responda com o projeto final, de 2 a 3 minutos.)
- O que é IDS x IPS? E Suricata x Wazuh?
- Como você priorizaria 50 alertas abertos ao mesmo tempo?

---

## Depois das 6 semanas
- [ ] Atualizar o currículo: uma linha por lab com resultado concreto ("Detectei brute force SSH com Wazuh e criei regra customizada...")
- [ ] Publicar no LinkedIn um post curto sobre o projeto final, com prints
- [ ] Concluir a trilha **SOC Level 1** do TryHackMe
- [ ] Mirar uma certificação de entrada: **ISC2 CC** (prova gratuita em algumas campanhas), **CompTIA Security+** ou **Google Cybersecurity**
- [ ] Continuar com 1 lab ou writeup por semana (CyberDefenders, Blue Team Labs Online)
