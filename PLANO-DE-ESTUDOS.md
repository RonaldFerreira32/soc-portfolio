# 📅 Plano de Estudos — Do zero ao Portfólio SOC N1

| Fase | Duração | Foco |
|------|---------|------|
| Semana 0 | 1 fim de semana | Preparar o laboratório |
| **Fase 1 — Fundamentos** | 6 semanas | Entender como sistemas, redes e ataques funcionam |
| **Fase 2 — Prática de SOC** | 6 semanas | Detectar, investigar e documentar ataques |

**Total:** ~3 meses.

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

> 💡 A VM Windows de avaliação (usada na F3 e na semana 4) pode ser baixada quando chegar a hora.

---

# 🧱 FASE 1 — Fundamentos (6 semanas)

> Objetivo: entender **como as coisas funcionam** antes de aprender a defendê-las.
> Ninguém detecta um ataque a um sistema que não entende.

**Como estudar cada semana:**
1. Leia a seção **"Como funciona"** e depois aprofunde com os materiais indicados.
2. Faça a **prática**. Os conceitos só fixam quando você mexe.
3. Escreva as **anotações com suas palavras** em `fundamentos/` e faça commit.
4. Teste-se com as **perguntas de revisão**. Se não souber explicar para uma criança de 12 anos, ainda não entendeu.

**Materiais de apoio para a fase inteira** (escolha um como "espinha dorsal"):
- Treinamento oficial do **ISC2 CC**, gratuito e em inglês, alinhado com uma certificação de entrada
- Curso de **Security+ do Professor Messer** no YouTube, gratuito e em inglês
- TryHackMe, trilhas **Pre Security** e **Cyber Security 101**
- Para ler em português: guias e cartilhas do **CERT.br** (cartilha.cert.br)

---

## F1 — Conceitos de segurança e tipos de ameaça

### 💡 Como funciona
Segurança da informação protege três coisas, a **Tríade CIA**:
- **Confidencialidade**: só quem pode vê. *Ataque típico: vazamento de dados.*
- **Integridade**: ninguém altera sem permissão. *Ataque típico: adulterar um valor de transferência.*
- **Disponibilidade**: o sistema funciona quando precisa. *Ataque típico: DDoS, ransomware.*

**Risco = Ameaça × Vulnerabilidade × Impacto.** Uma porta destrancada (vulnerabilidade) só vira risco se houver um ladrão (ameaça) e algo de valor lá dentro (impacto). Segurança é **reduzir risco**, não zerar: risco zero não existe.

**Controles** reduzem o risco e se classificam assim:
- Por **função**: *preventivo* (firewall), *detectivo* (SIEM, câmera), *corretivo* (backup, antivírus removendo malware)
- Por **tipo**: *técnico* (MFA), *administrativo* (política de senhas, treinamento), *físico* (crachá, catraca)

**Defesa em profundidade:** várias camadas, como um castelo com fosso, muralha, guardas e cofre. Se uma camada falha, a próxima segura. O SOC é a camada que **percebe quando as outras falharam**.

**AAA:** *Autenticação* (quem é você? senha, biometria, token), *Autorização* (o que você pode fazer?), *Auditoria/Accounting* (registrar o que você fez, ou seja, os **logs** que o SOC analisa).

### 📚 Estude
- Tipos de malware: vírus, worm, trojan, ransomware, spyware, rootkit, RAT, botnet
- Engenharia social: phishing, spear phishing, vishing, smishing, pretexting, tailgating
- Ataques: DoS/DDoS, MITM, força bruta, credential stuffing, zero-day
- Tipos de atacante: script kiddie, hacktivista, cibercrime, APT/estado-nação, insider

### 🔧 Prática
- [ ] Escolher 5 incidentes reais noticiados (ex.: ataques de ransomware a empresas e órgãos públicos brasileiros) e, para cada um, identificar: qual pilar da CIA foi afetado, o tipo de ataque e o tipo de atacante, e qual controle teria evitado ou detectado
- [ ] Montar um glossário seu com 30 termos
- [ ] Classificar 10 controles de segurança da sua casa ou trabalho por função e tipo

### ❓ Revisão
- Ransomware afeta qual pilar da CIA? (Pegadinha: pode afetar os três. Por quê?)
- Diferença entre ameaça, vulnerabilidade e risco?
- Diferença entre vírus e worm?
- MFA é controle de qual função e de qual tipo?

---

## F2 — Linux na prática

### 💡 Como funciona
A maioria dos servidores da internet roda Linux, e boa parte das ferramentas de segurança também. No Linux, **tudo é arquivo**: configurações ficam em `/etc`, logs em `/var/log`, programas em `/usr/bin` e as pastas dos usuários em `/home`.

**Permissões** definem quem pode fazer o quê. `-rwxr-x---` significa: o **dono** lê, escreve e executa; o **grupo** lê e executa; os **outros** não fazem nada. O usuário `root` pode tudo. Por isso atacantes buscam **escalar privilégio** até chegar nele, e por isso usamos `sudo` em vez de logar como root (**princípio do menor privilégio**).

**Processos** são programas rodando, cada um com um PID e um dono. **Serviços** (daemons) são processos que rodam em segundo plano, como o `sshd`, que aceita conexões SSH. Um malware é, no fim, só mais um processo, e saber listar processos é saber caçá-lo.

### 📚 Estude
- TryHackMe: salas *Linux Fundamentals* partes 1, 2 e 3
- Comandos: `ls`, `cd`, `cat`, `less`, `grep`, `find`, `chmod`, `chown`, `ps`, `top`, `kill`, `systemctl`, `journalctl`, `ss`, `ip a`, `apt`, `sudo`, `useradd`, `passwd`
- Pipes (`|`) e redirecionamento (`>`, `>>`)

### 🔧 Prática (na VM Ubuntu)
- [ ] Criar 2 usuários e 1 grupo; montar uma pasta que só o grupo consegue ler
- [ ] Achar todos os arquivos com permissão SUID: `find / -perm -4000 2>/dev/null`. Pesquisar por que isso interessa a um atacante
- [ ] Listar as portas abertas com `ss -tulpn` e descobrir qual processo usa cada uma
- [ ] Parar e iniciar o serviço SSH com `systemctl` e ver o efeito nos logs com `journalctl -u ssh`
- [ ] Montar uma "cola" (cheatsheet) com os 30 comandos mais úteis, explicados por você

### ❓ Revisão
- O que significa `chmod 750 arquivo`?
- Por que não usar root no dia a dia?
- Como descobrir qual processo está escutando na porta 22?
- Onde ficam os logs de autenticação no Ubuntu?

---

## F3 — Windows e Active Directory

### 💡 Como funciona
Empresas usam majoritariamente Windows nas estações, gerenciadas por um **Active Directory (AD)**. Pense no AD como a **lista telefônica e a portaria** da empresa: guarda todos os usuários, computadores e grupos, e decide quem entra onde.

- O **Domain Controller (DC)** é o servidor que roda o AD. Se o atacante domina o DC, domina a empresa inteira. Por isso ele é o alvo final de quase todo ataque corporativo.
- **Kerberos** é o protocolo de login do AD. Funciona com "ingressos" (*tickets*): você prova quem é uma vez e recebe um ingresso para acessar os serviços sem digitar a senha de novo. Ataques como *Kerberoasting* e *Pass-the-Ticket* abusam desses ingressos.
- **GPO** (Group Policy) são regras aplicadas em massa, como "todos os PCs bloqueiam a tela após 5 minutos".
- Grupos privilegiados como **Domain Admins** são as "chaves mestras". Toda alteração neles deve gerar alerta no SOC.

No Windows, o **Registro** é um grande banco de configurações. Malwares adoram gravar nele chaves como `Run` para iniciar junto com o sistema (**persistência**).

### 📚 Estude
- TryHackMe: salas *Windows Fundamentals* partes 1 a 3 e *Active Directory Basics*
- Comandos: `whoami /all`, `net user`, `net localgroup administrators`, `tasklist`, `netstat -ano`, `ipconfig /all`, `Get-Process`, `Get-Service`, `Get-LocalUser`
- Ferramentas: Gerenciador de Tarefas, Visualizador de Eventos, `services.msc`, `regedit`, Sysinternals (Process Explorer, Autoruns)

### 🔧 Prática
- [ ] Na VM Windows de avaliação: criar um usuário comum e um admin e comparar a saída de `whoami /all` em cada um
- [ ] Abrir o **Autoruns** e listar tudo que inicia com o sistema; pesquisar 3 itens que você não conhece
- [ ] Usar `netstat -ano` e associar cada conexão a um processo pelo PID
- [ ] *(Opcional, precisa de 16 GB de RAM)* Promover um Windows Server a DC, colocar o Windows 10 no domínio e criar uma GPO

### ❓ Revisão
- O que é um Domain Controller e por que ele é tão visado?
- Explique Kerberos com uma analogia sua.
- Cite 2 lugares onde um malware pode se esconder para iniciar com o Windows.
- Para que serve uma GPO?

---

## F4 — Redes a fundo

### 💡 Como funciona
Acompanhe o que acontece quando você digita `google.com` no navegador:
1. **DHCP:** ao ligar na rede, seu PC "grita" pedindo um IP e o roteador responde com um IP, o gateway e o servidor DNS.
2. **DNS:** o PC pergunta "qual o IP de google.com?" e recebe o endereço. *(Atacantes abusam do DNS para esconder comunicação; é o **DNS tunneling**.)*
3. **ARP:** para falar com o gateway na rede local, o PC pergunta "quem tem o IP 192.168.0.1? Me diga seu MAC". *(**ARP spoofing**: o atacante responde "sou eu!" e passa a ficar no meio da conversa, o MITM.)*
4. **NAT:** o roteador troca seu IP privado (192.168.x.x) pelo IP público antes de mandar o pacote para a internet.
5. **Roteamento:** cada roteador no caminho decide o próximo salto até o destino.
6. **TCP + TLS:** handshake TCP e depois o handshake TLS para criptografar (veja a F5).
7. **HTTP:** finalmente o pedido da página.

O **firewall** fica no caminho e decide o que passa com base em IP, porta e protocolo. O **proxy** intermedia o acesso web e registra tudo. A **VPN** cria um túnel criptografado sobre a internet. **VLANs** dividem um switch físico em várias redes lógicas, o que é a base da segmentação.

### 📚 Estude
- Professor Messer (Network+), módulos de DHCP, DNS, NAT, roteamento e VLAN
- Tipos de registro DNS: A, AAAA, MX, CNAME, TXT, PTR
- Diferença entre hub, switch, roteador, firewall, IDS/IPS e proxy

### 🔧 Prática
- [ ] **Cisco Packet Tracer** (gratuito com conta da Cisco Networking Academy): montar uma rede com 2 VLANs, um roteador fazendo roteamento entre elas e um servidor DHCP
- [ ] Usar `dig` e `nslookup` para consultar os registros A, MX e TXT de um domínio. Achar o registro SPF no TXT
- [ ] Rodar `traceroute google.com` e explicar cada salto
- [ ] Capturar no Wireshark o processo DHCP completo (DORA: Discover, Offer, Request, Ack) ao reconectar a VM
- [ ] Desenhar no draw.io o caminho de um acesso web, do PC até o servidor

### ❓ Revisão
- O que acontece, passo a passo, quando você acessa um site?
- Por que IP privado não "sai" para a internet sem NAT?
- O que é ARP spoofing e qual controle o impede?
- Diferença entre firewall, IDS e IPS?

---

## F5 — Criptografia e vulnerabilidades web

### 💡 Como funciona
**Criptografia** transforma dados legíveis em ilegíveis usando uma chave.
- **Simétrica** (AES): a **mesma chave** tranca e destranca. É rápida, mas como entregar a chave com segurança?
- **Assimétrica** (RSA, ECC): um **par de chaves**. O que a chave pública tranca, só a privada destranca. Resolve o problema da entrega da chave, mas é lenta.
- **Hash** (SHA-256): uma "impressão digital" dos dados. **Não tem volta.** Serve para conferir integridade e guardar senhas. Mudar 1 bit muda o hash inteiro. *(MD5 e SHA-1 estão quebrados.)*
- **Assinatura digital:** hash do documento cifrado com a chave **privada**. Qualquer um confere com a chave pública e tem a prova de autoria e de integridade.

**TLS (o cadeado do HTTPS)** junta tudo: o site apresenta um **certificado** (a chave pública assinada por uma Autoridade Certificadora confiável); usa criptografia assimétrica para combinar uma chave simétrica; e daí em diante usa a simétrica, que é rápida.

**Vulnerabilidade** é uma falha explorável. Toda vulnerabilidade pública ganha um **CVE** (um identificador, como CVE-2021-44228) e uma nota **CVSS** (de 0 a 10, a gravidade). O **OWASP Top 10** lista as falhas web mais comuns. A maioria nasce de **confiar no que o usuário digita**: o SQL Injection, por exemplo, acontece quando o texto do usuário vira parte do comando do banco de dados.

### 📚 Estude
- TryHackMe: salas *Cryptography Basics*, *Hashing Basics* e *OWASP Top 10*
- OWASP Top 10, versão atual (owasp.org)
- O que são CVE, CVSS, CWE, patch e zero-day

### 🔧 Prática
- [ ] `echo "teste" | sha256sum`, depois mudar uma letra e comparar os hashes
- [ ] Com `openssl`, cifrar e decifrar um arquivo com AES e depois gerar um par de chaves RSA
- [ ] Abrir o certificado de um site no navegador e identificar o emissor, a validade e a cadeia
- [ ] Rodar o **DVWA** (aplicação web propositalmente vulnerável) via Docker na sua VM e explorar SQL Injection e XSS no nível *low*
- [ ] Pesquisar o CVE do Log4Shell (CVE-2021-44228): o que era, a nota CVSS e por que foi tão grave

### ❓ Revisão
- Diferença entre criptografia e hash? Por que senhas são guardadas como hash?
- Como o HTTPS combina criptografia simétrica e assimétrica?
- O que é SQL Injection e como prevenir?
- O que significa um CVSS 9.8?

---

## F6 — Governança, nuvem e Python básico

### 💡 Como funciona
**Governança** é o "porquê" e as "regras do jogo" da segurança numa empresa:
- **Política** (o quê: "toda senha deve ter MFA") → **Norma/Procedimento** (como fazer) → **Controle** (a implementação técnica).
- **LGPD:** lei brasileira de proteção de dados pessoais. Um vazamento pode exigir comunicação à **ANPD** e aos titulares. O SOC participa disso.
- **ISO 27001:** norma internacional para um sistema de gestão de segurança.
- **NIST CSF:** framework com 6 funções: *Governar, Identificar, Proteger, Detectar, Responder, Recuperar*. **O SOC vive principalmente em Detectar e Responder.**

**Nuvem:** no **modelo de responsabilidade compartilhada**, o provedor (AWS, Azure) protege a infraestrutura e **você** protege o que coloca nela: dados, contas, configurações. A maior parte dos incidentes em nuvem vem de **configuração errada** (um bucket público, por exemplo) e de **credenciais vazadas**. O IAM é o "AD da nuvem".

**Python** para o SOC serve para automatizar tarefas repetitivas: ler logs, extrair IPs, consultar APIs de reputação. Não precisa ser programador, só saber ler e adaptar scripts.

### 📚 Estude
- Texto da LGPD, arts. 46 a 48 (segurança e comunicação de incidentes)
- Visão geral do NIST CSF 2.0
- TryHackMe: salas introdutórias de AWS/Azure ou o *AWS Cloud Practitioner Essentials* (gratuito no AWS Skill Builder)
- Python: variáveis, listas, dicionários, `if`, `for`, funções, leitura de arquivos e o básico do módulo `re` (regex)

### 🔧 Prática
- [ ] Escrever uma **política de senhas** de 1 página para uma empresa fictícia
- [ ] Mapear 5 ferramentas do plano (firewall, SIEM, backup, MFA, EDR) nas funções do NIST CSF
- [ ] Python: script que lê um arquivo de texto, extrai todos os IPs com regex e conta quantas vezes cada um aparece
- [ ] Python: script que recebe um hash e diz se é MD5, SHA-1 ou SHA-256 pelo tamanho

### ❓ Revisão
- Em quais funções do NIST CSF o SOC atua?
- O que a LGPD exige em caso de vazamento de dados pessoais?
- Na nuvem, de quem é a responsabilidade por um bucket configurado como público?
- Para que um analista SOC usa Python?

### ✅ Checkpoint da Fase 1
Antes de seguir para a prática, você deve conseguir explicar sem consultar nada: tríade CIA, risco, como um acesso web funciona do DHCP ao HTTP, permissões Linux, o que é AD/DC, hash x criptografia, SQL Injection e NIST CSF.
**Se quiser, este é um bom momento para fazer a prova do ISC2 CC.**

---

# 🛠️ FASE 2 — Prática de SOC (6 semanas)

## Semana 1 — Redes e Wireshark na prática

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
