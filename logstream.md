# Grey Hack: Guia Completo de Limpeza de Logs e Técnicas de Ataque Sem Root

Grey Hack é um simulador de hacking multiplayer disponível na Steam onde jogadores aprendem conceitos de segurança cibernética em um ambiente gamificado. Este guia apresenta técnicas avançadas de manipulação de logs, escalação de privilégios e segurança operacional descobertas pela comunidade, indo muito além da documentação básica do jogo.

---

## 1. Fundamentos do Sistema

### Sistema de Logs no Grey Hack

O arquivo principal de log em cada computador do jogo está localizado em **`/var/system.log`**. Este arquivo não pode ser editado com comandos tradicionais como `get_content` ou `set_content` — apenas através do LogViewer.exe, substituição de arquivo ou exclusão direta. O sistema registra eventos críticos com timestamps e endereços IP automaticamente.

**Eventos registrados no system.log:**

| Tipo de Entrada | Gatilho |
|-----------------|---------|
| "connection established on port ##" | Quando net_session é obtido em servidor |
| "shell obtained on port ##" | Ao obter acesso shell em qualquer máquina |
| "file deleted [IP]" | Ao deletar qualquer arquivo |
| "connection routed [IP]" | Ao rotear conexão via `connect_service` |
| "connection closed [IP]" | Ao desconectar/sair do `start_terminal` |

Transações bancárias são registradas separadamente em **`/server/transactions.log`**, contendo informações de remetente e destinatário.

### Hierarquia de Permissões

O Grey Hack implementa três níveis de acesso: **Guest** (visitante), **User** (usuário comum) e **Root** (administrador). Cada nível tem capacidades progressivamente maiores, seguindo o modelo de permissões Unix tradicional.

- **Guest**: Pode executar programas em `/home/guest`, ler arquivos com permissão world-readable
- **User**: Acessa próprio diretório `/home/user`, frequentemente pode ler `/etc/passwd`
- **Root**: Controle total do sistema — chmod, exclusão, criação de usuários

**Arquivos e diretórios críticos:**

| Caminho | Função | Permissão Típica |
|---------|--------|------------------|
| `/etc/passwd` | Senhas de todos os usuários (hash) | rw-r----- |
| `/home/user/Config/Bank.txt` | Credenciais bancárias | rw------- |
| `/home/user/Config/Mail.txt` | Credenciais de email | rw------- |
| `/lib/` | Bibliotecas do sistema | r-xr-xr-x |
| `/boot/` | Arquivos de boot | rwx------ |
| `/var/system.log` | Logs do sistema | rw-rw---- |

---

## 2. Limpeza de Logs Sem Root — Tutorial Completo

### Método 1: LogViewer.exe (Padrão)

O LogViewer.exe requer **acesso root OU permissão de escrita** em `/var/system.log`. Este é o método mais seguro quando disponível.

```bash
cd /
cd var
LogViewer.exe system.log
```

Selecione entradas específicas para deletar e clique em "Save". A regra de ouro é deletar **apenas** entradas suspeitas, mantendo pares de "connection established" e "connection closed" intactos.

### Método 2: Sobrescrita de Arquivo (Sem Root)

Quando você tem permissão de escrita mas não root, use a técnica de corrupção de log:

**Opção A — Criar arquivo vazio:**
```bash
rm system.log
poke system.log
```

**Opção B — Técnica de corrupção:**
```bash
poke haha | mv haha /var/system.log
```

**Importante**: Este método apenas previne a **próxima** entrada de log. Desconecte imediatamente após a corrupção! O arquivo se regenera automaticamente após qualquer ação que dispara logs.

### Método 3: Manipulação via FTP

Para acesso exclusivo FTP, baixe o log, edite localmente e reenvie:

```bash
cd /var
get system.log
# Edite localmente removendo entradas incriminadoras
put /root/system.log /var/system.log
```

**Limitação crítica**: Conexões FTP ainda criam entradas "shell obtained" apesar da documentação indicar o contrário. Limpe logs imediatamente após conectar.

### Método 4: Ferramentas 5hell (Avançado)

O 5hell oferece limpeza automatizada com comandos dedicados:

**Limpeza local:**
```bash
silentclean     # Limpa system.log local
silentclean -d  # Limpa + auto-deleta rkit e 5hell
silentclean -n  # Nuke do sistema + auto-delete
```

**Limpeza remota:**
```bash
rclean [#]      # Seleciona objeto BUFFER para limpar remotamente
rclean -d [#]   # Limpa + deleta rkit/5hell
rclean -n [#]   # Limpa + nuke /boot
```

### Quais Logs Deletar (Decisão Estratégica)

| Ação | Tipo de Log | Recomendação |
|------|-------------|--------------|
| **DELETAR** | "shell obtained on port ##" | Evidência primária de intrusão |
| **DELETAR** | "file deleted [IP]" | Prova de manipulação |
| **DELETAR** | "connection routed [IP]" | Mostra uso de proxies |
| **MANTER** | "connection established" | Tráfego normal, remoção gera suspeita |
| **MANTER** | "connection closed" | Deve ter par correspondente |

**Zona de perigo**: Se você deletar TODOS os logs e desconectar, apenas SEU IP aparecerá no log de "connection closed" — isso é **altamente suspeito** para admins NPC e inicia trace passivo.

### Tabela Comparativa de Métodos

| Método | Velocidade | Segurança | Confiabilidade | Requer Root |
|--------|------------|-----------|----------------|-------------|
| LogViewer seletivo | Média | Alta | Alta | Sim |
| Deletar arquivo inteiro | Rápida | Baixa | Média | Não |
| Substituição FTP | Lenta | Média | Média | Não |
| Corrupção de arquivo | Rápida | Média | Baixa | Não |
| 5hell automatizado | Rápida | Alta | Alta | Varia |

---

## 3. Técnicas de Ataque Sem Root — Tutorial Completo

### Checklist Pré-Ataque

Antes de qualquer operação, execute a seguinte sequência:

```bash
whoami                    # Confirma nível de acesso atual
ls /lib                   # Verifica bibliotecas disponíveis
ls /home                  # Obtém nomes de usuários
ps                        # Procura por dsession (admin ativo)
```

### Escalação de Privilégios via Exploits Locais

O Grey Hack oferece três bibliotecas principais para escalação: **kernel_module.so**, **init.so** e **net.so**. Cada uma explora vulnerabilidades diferentes no sistema.

**Passo 1 — Escanear vulnerabilidades:**
```greyscript
meta = include_lib("/lib/metaxploit.so")
lib = meta.load("/lib/kernel_module.so")
addresses = meta.scan(lib)
```

**Passo 2 — Analisar endereços de memória:**
```greyscript
data = meta.scan_address(lib, "0x39B56EAA")
// Retorna requisitos como "active user" ou "registered users equal to 2"
```

**Passo 3 — Executar exploit:**
```greyscript
result = lib.overflow("0x2CF07FF0", "bl_treel")
// Retorna: shell, computer, file ou number
```

**Tipos de retorno do overflow():**
- **shell** — Melhor resultado, execução de comandos como outro usuário
- **computer** — Acesso ao objeto computer para manipulação de arquivos
- **file** — Acesso a arquivo específico (frequentemente /etc/passwd)
- **number** — Requisitos do exploit não satisfeitos

### Workflow Completo de Escalação

**Cenário: Guest Shell → Root Access**

1. **Como guest, verifique misconfiguracões:**
```bash
cat /etc/passwd           # Tenta acesso direto
ls -la /etc               # Verifica permissões
ls /home/*/Config/        # Procura configs legíveis
```

2. **Rota de engenharia social (se arquivos bloqueados):**
```bash
whois [target_ip]         # Info do admin
smtp-user-list [ip] 25    # Lista emails de usuários
# Envie email "Login issues" para admin impersonando usuário
# Receba senha na resposta
```

3. **Mude para User:**
```bash
sudo -u username
# Digite senha recebida
```

4. **Obtenha senha root:**
```bash
cat /etc/passwd
scp -d /etc/passwd        # Download para sua máquina
decipher                  # Crackeie senha root
```

5. **Escale para Root:**
```bash
sudo -s
# Digite senha crackeada
```

### Mecânicas de Engenharia Social

O sistema de email do Grey Hack inclui templates predefinidos (botão PF) para diferentes cenários:

| Template | Propósito | Uso |
|----------|-----------|-----|
| **Administrative action** | Obter senha de usuário | Impersona admin |
| **Login issues** | Obter senha de usuário | Truque admin a revelar |
| **Online user** | Fazer usuário logar | Satisfaz requisito "active user" |
| **Admin online** | Fazer root logar | Satisfaz requisito "active root" |
| **System information** | Obter versões de bibliotecas | Identifica exploits adequados |
| **Funny Game** | Deploy de rshell payload | Ataca máquinas sem portas abertas |

**Encontrando informações de NPCs:**
```bash
whois [IP_address]              # Contato do admin
smtp-user-list [IP] [port]      # Lista usuários de email
ls /home                        # Usernames
cat /home/user/Config/Mail.txt  # Endereços de email
```

### Ataque Funny Game (rshell) para Máquinas Fechadas

Para alvos **sem portas abertas**, use o ataque via email:

1. Configure servidor rshell no seu servidor alugado
2. Configure port forwarding para porta 1222
3. Crie email com template "Funny Game"
4. Anexe cliente rshell configurado para seu servidor
5. Envie para NPC alvo
6. Aguarde conexão no rshell-interface
7. Ganhe acesso shell quando NPC executar payload

### Procedimentos Pós-Ataque

```bash
LogViewer.exe             # Remove entradas "shell obtained"
# Delete APENAS logs suspeitos
# Mantenha pares connection established/closed
exit                      # Saia limpo
```

---

## 4. OPSEC — Melhores Práticas

### Regra Fundamental: Nunca Conecte Diretamente

A regra de ouro do OPSEC em Grey Hack: **nunca conecte diretamente do seu PC principal a alvos de ataque**. Use servidores alugados como proxies para TODAS as atividades de hacking.

### Configurando Bounce Chains

**Passo 1 — Alugar servidores:**
1. Abra Browser e pesquise "shop" ou "internet"
2. Visite site de ISP
3. Clique na aba Service → Rent Server
4. Baixe ConfigLan para gerenciar servidores
5. Adicione servidor ao mapa via email recebido

**Passo 2 — Configurar conexão bounce (via SSH):**
```bash
ssh root@password 189.20.34.5   # 1º bounce
ssh root@password 20.40.5.120   # 2º bounce
ssh root@password [target_IP]   # Alvo final
```

**Passo 3 — Proteger servidores alugados:**
```bash
sudo chmod -R o-rwx /
sudo chmod -R g-rwx /
```

**Recomendação**: Use **5-15 servidores** em cadeia para aumentar tempo de trace.

### AdminMonitor.exe — Ferramenta Essencial

O AdminMonitor.exe do HackShop é obrigatório para qualquer operação:

- **Avisa quando admin NPC loga** no sistema conectado
- **Alerta sobre traces ativos** com countdown
- Sempre execute em background no seu PC HOME
- Verifique `dsession` no output de `ps` — significa admin ativo

### Checklist OPSEC Completo

**PRÉ-HACK:**
- [ ] AdminMonitor.exe rodando no PC home
- [ ] Conectado via servidor alugado (nunca direto)
- [ ] Múltiplos servidores bounce configurados (5+ ideal)
- [ ] Ferramentas uploadadas nos servidores bounce

**DURANTE HACK:**
- [ ] Verificar `ps` para root dsession
- [ ] Monitorar AdminMonitor para alertas
- [ ] Minimizar operações de arquivo com admin ativo
- [ ] Trabalhar rápido, não demorar

**PÓS-HACK:**
- [ ] Deletar logs "shell obtained" no alvo
- [ ] Deletar logs "file deleted" no alvo
- [ ] Deletar logs "connection routed" se necessário
- [ ] Manter pares connection established/disconnect
- [ ] Sair limpo através de cada hop bounce
- [ ] Considerar mudar WiFi/IP após sessão

### Procedimentos de Emergência

**Se trace ativo iniciar:**
```bash
# Ações imediatas
cd /var
LogViewer.exe system.log
# Delete entradas "shell obtained" e "file deleted"
exit  # ANTES do countdown terminar
```

**Para cada hop na cadeia bounce:**
```bash
cd /var → rm system.log → exit  # Repita por hop
```

**Prevenção de trace passivo:**
- Nunca deixe logs "shell obtained" em máquinas alvo
- Use servidores alugados como primeiro ponto bounce (sem admins)
- Quebre a cadeia eliminando "connection routed" de qualquer máquina

---

## 5. Recursos da Comunidade

### Ferramentas Populares

**5hell** — Shell mais completo da comunidade
- **Repositório:** github.com/jhook777/5hell-for-Grey-Hack-the-Game
- Prompt avançado, sistema BUFFER, comando `dig` para hacking automatizado, sistema de Custom Objects persistente

**rocShell** — Shell leve e eficiente
- **Repositório:** github.com/rocketorbit/rocShell

**Greybel-VS** — Extensão VSCode (33+ stars)
- **Repositório:** github.com/ayecue/greybel-vs
- Syntax highlighting, debugger, IntelliSense, diagnósticos

**Scripts de Automação (psimonson/greyhack-scripts - 55 stars):**
- autohack — Hackeia bibliotecas remotas automaticamente
- autolocal — Explora bibliotecas locais
- autowifi — Cracking automatizado de WiFi
- autoclean — Limpeza pós-hacking

### Melhores Guias

**Hacker's Handbook v0.8** (795+ ratings)
- steamcommunity.com/sharedfiles/filedetails/?id=1905138308
- Guia mais abrangente: exploits, missões, engenharia social, proteção de servidor

**Grey Hack Basics — Primeiros Passos Multiplayer**
- steamcommunity.com/sharedfiles/filedetails/?id=2957034114

### Canais da Comunidade

- **Discord Oficial:** discord.gg/greyhack (verificação de telefone requerida)
- **Grey Repo:** greyrepo.xyz — Plataforma de compartilhamento de scripts
- **GitHub Topics:** github.com/topics/greyscript (29+ repositórios)
- **Steam Discussions:** steamcommunity.com/app/605230/discussions/

### Repositórios GitHub Essenciais

| Repositório | Stars | Descrição |
|-------------|-------|-----------|
| ayecue/greybel-js | 40 | Toolkit GreyScript |
| psimonson/greyhack-scripts | 55 | Coleção completa de scripts |
| Darkvalnar/greyscript | - | Multi-tool com extração bancária |
| salmon85/Grey_hack_scripts | - | Decipher com cache, chmod numérico |
| irtsa-dev/greyscript-tools | - | GRSA, UUID, Progress bar |

---

## 6. Troubleshooting

### Erros Comuns e Soluções

**"No such file" com decipher:**
- Garanta extensão .txt no arquivo
- Use caminhos completos: `/home/root/decipher /home/root/Mail.txt`
- Execute com sudo: `sudo -s` primeiro

**SCP não encontra arquivos:**
- Use flags corretas: `-d` para download, `-u` para upload
- Alternativa: FileExplorer.exe drag-and-drop
- Em servidores FTP: use `get` e `put`

**Exploit não funciona:**
- Verifique se versão corresponde exatamente
- Tente exploit diferente para mesmo serviço
- Alguns requerem usuário ativo — use engenharia social
- Tente outras portas ou exploit de router

**"Permission denied":**
- Use prefixo `sudo` para comandos que requerem elevação
- Execute `sudo -s` para shell root
- Verifique permissões com `ls -l`

### Recuperação de Detecção

**Se arquivos do sistema foram deletados:**
```bash
reboot -sm  # Boot em safe mode
# Restaure arquivos críticos de backup
```

**Se sendo trackeado:**
1. Verifique dsession em output de `ps`
2. Delete logs em /var/system.log rapidamente
3. Saia imediatamente com `exit`

**Após sessão de hacking (reset de IP):**
- Crackeie novo router WiFi para obter novo IP
- Logs de sessão anterior apontam para endereço morto
- Cria separação limpa entre operações

### Mecânicas Pouco Documentadas

- **Código de câmera de segurança = senha root** do sistema
- Servidores FTP têm comandos limitados (use `help`)
- Config de firewall do router: `gateway_ip:8080` no browser
- Safe mode: `reboot -sm` para recuperação
- Ver código fonte de ferramentas oficiais: `CodeEditor.exe -code [tool_name]`

---

## Conclusão

O domínio de Grey Hack requer compreensão integrada de três pilares: **manipulação de logs**, **escalação de privilégios** e **segurança operacional**. A técnica mais segura para limpar logs permanece a deleção seletiva via LogViewer.exe, mantendo sempre os pares de conexão intactos para evitar detecção por admins NPC. Para ataques sem root, a combinação de engenharia social com exploits locais oferece o caminho mais confiável para escalação — emails de "System information" revelam versões de bibliotecas, enquanto templates "Online user" satisfazem requisitos de exploits.

O investimento em infraestrutura de bounce chains com **5-15 servidores alugados** separa jogadores casuais de operadores eficientes. Ferramentas como **5hell** automatizam tarefas repetitivas, enquanto **AdminMonitor.exe** permanece insubstituível para awareness situacional. A comunidade no Discord oficial e repositórios GitHub como **greybel-vs** e **psimonson/greyhack-scripts** continuam expandindo as capacidades disponíveis, transformando Grey Hack em uma plataforma de aprendizado genuína para conceitos de segurança cibernética.
