# Grey Hack: Tutorial Definitivo

O Grey Hack é um simulador de hacking MMO que oferece uma das experiências mais realistas do gênero. Desenvolvido pela **Loading Home** (desenvolvedor solo KuRouZu), o jogo permite que jogadores hackeem computadores em um mundo persistente 24/7, criem ferramentas personalizadas usando GreyScript, e explorem uma rede proceduralmente gerada. Este guia completo cobre desde os fundamentos até técnicas avançadas de hacking e programação.

---

## Parte 1: Introdução e Primeiros Passos

### 1.1 O que é Grey Hack

Grey Hack representa o "Arma dos jogos de hacking" — significativamente mais realista e complexo que alternativas como Hacknet ou Uplink. Lançado em **dezembro de 2017** no Steam Early Access, o jogo utiliza comandos UNIX reais, possui um sistema de arquivos realista e permite que jogadores escrevam seus próprios exploits e ferramentas do zero usando a linguagem GreyScript.

**Diferenças fundamentais dos concorrentes:**

| Característica | Grey Hack | Hacknet | Uplink |
|----------------|-----------|---------|--------|
| **Realismo** | Alto (comandos UNIX reais) | Médio | Baixo (estilo Hollywood) |
| **Multiplayer** | MMO com mundo persistente 24/7 | Single-player | Single-player |
| **Scripting** | Linguagem completa (GreyScript) | Limitado | Nenhum |
| **Complexidade** | Nível simulador | Baseado em puzzles | Foco em point-and-click |

O loop de gameplay central envolve hackear roteadores WiFi locais para ganhar conectividade, completar missões de hacking por dinheiro e reputação, criar e modificar ferramentas usando scripting, e progredir em um ambiente sandbox não-linear.

**Requisitos de sistema mínimos:** processador com suporte a SSE2, 512 MB RAM, GPU compatível com DX10/11/12, e 560 MB de espaço disponível. O jogo está disponível para Windows, macOS e Linux.

### 1.2 Instalação e Configuração

A instalação é feita diretamente pelo Steam. Após adquirir o jogo, o download e instalação são automáticos. Na primeira execução, você escolherá entre **Single Player** (mundo local, pausa quando você sai) ou **Multiplayer** (mundo persistente compartilhado com todos os jogadores).

> **Dica para iniciantes:** Comece no Single Player para aprender os conceitos básicos sem risco de ser hackeado por outros jogadores. O modo multiplayer expõe seu computador **24 horas por dia**, mesmo quando você está offline.

### 1.3 Sua Primeira Sessão

Ao iniciar o jogo pela primeira vez, você encontrará um computador sem acesso à rede. O tutorial inicial ensina a hackear roteadores WiFi usando o conjunto de ferramentas **airmon/aireplay/aircrack**:

1. **Habilite o modo monitor:** `airmon start wlan0`
2. **Liste as redes disponíveis:** `iwlist wlan0`
3. **Capture pacotes da rede alvo:** `aireplay -b [BSSID] -e [ESSID]`
4. **Crackeie a senha capturada:** `aircrack file.cap`

A quantidade de ACKs necessários varia com o comprimento da senha: senhas de 4 caracteres precisam de ~10.000 ACKs, enquanto senhas de 8 caracteres exigem mais de 100.000 ACKs.

Após completar o tutorial e criar uma conta de email, você receberá a **missão tutorial** que, ao ser concluída, desbloqueia o endereço da **HackShop** — a loja de ferramentas essenciais para hacking.

### 1.4 Entendendo a Interface

O Grey Hack simula um ambiente desktop completo com barra de tarefas, ícones na área de trabalho, gerenciamento de janelas e menus de contexto. O **Terminal** é a ferramenta central — uma interface de linha de comando baseada em comandos UNIX reais.

**Aplicações pré-instaladas importantes:**

| Aplicação | Função |
|-----------|--------|
| **Terminal.exe** | Interface de linha de comando para todas as operações |
| **FileExplorer.exe** | Navegador de arquivos com drag-and-drop |
| **CodeEditor.exe** | IDE para programação em GreyScript |
| **Browser.exe** | Navegador web para pesquisas e sites |
| **Mail.exe** | Cliente de email para missões e comunicação |
| **LogViewer.exe** | Visualizar e limpar logs do sistema |

---

## Parte 2: Dominando o Terminal

### 2.1 Comandos Essenciais

O terminal do Grey Hack implementa comandos UNIX reais. Dominar esses comandos é fundamental para qualquer operação de hacking.

**Navegação básica:**
```bash
pwd                  # Mostra diretório atual
ls                   # Lista conteúdo do diretório
ls -la /home         # Lista detalhada com permissões
cd /etc              # Muda para o diretório /etc
cd ..                # Volta um nível
```

**Manipulação de arquivos:**
```bash
cat /etc/passwd      # Exibe conteúdo do arquivo
cp arquivo.txt /backup/   # Copia arquivo
mv antigo.txt novo.txt    # Move/renomeia
rm arquivo.txt            # Remove arquivo
rm -r /pasta              # Remove pasta recursivamente
mkdir novapasta           # Cria diretório
```

### 2.2 Navegação no Sistema de Arquivos

O Grey Hack utiliza uma hierarquia de arquivos estilo UNIX:

| Diretório | Propósito |
|-----------|-----------|
| `/` | Diretório raiz |
| `/bin` | Binários essenciais do sistema |
| `/lib` | Bibliotecas do sistema (kernel_module.so, metaxploit.so) |
| `/etc` | Arquivos de configuração (inclui **passwd**) |
| `/home/[user]` | Diretórios pessoais dos usuários |
| `/home/[user]/Config` | Credenciais (Mail.txt, **Bank.txt**) |
| `/var` | Arquivos variáveis (inclui **system.log**) |
| `/boot` | Arquivos de inicialização (deletar corrompe o sistema) |
| `/root` | Diretório pessoal do root |

### 2.3 Gerenciamento de Arquivos e Permissões

O sistema de permissões segue o modelo UNIX clássico com usuário (u), grupo (g) e outros (o), cada um podendo ter permissões de leitura (r), escrita (w) e execução (x).

```bash
chmod u+rwx arquivo      # Adiciona todas permissões ao usuário
chmod o-rwx /            # Remove todas permissões de "outros"
chmod -R o-rwx /         # Remove recursivamente (proteção básica)
chown root arquivo.txt   # Muda proprietário
sudo -s                  # Torna-se root
sudo -u admin            # Muda para usuário específico
```

> **Segurança essencial:** Execute `sudo chmod -R o-rwx /` para remover todas as permissões de visitantes no sistema, dificultando invasões.

### 2.4 Referência Completa de Comandos

**Comandos de Sistema:**

| Comando | Sintaxe | Descrição |
|---------|---------|-----------|
| `ps` | `ps` | Lista processos em execução |
| `kill` | `sudo kill [PID]` | Termina processo |
| `reboot` | `reboot` | Reinicia sistema |
| `reboot -sm` | `reboot -sm` | Reinicia em modo seguro |
| `apt-get update` | `sudo apt-get update` | Atualiza lista de pacotes |
| `apt-get install` | `sudo apt-get install [pkg]` | Instala pacote |

**Comandos de Rede:**

| Comando | Sintaxe | Descrição |
|---------|---------|-----------|
| `ifconfig` | `ifconfig` | Mostra configuração de rede |
| `nmap` | `nmap [IP]` | Escaneia portas e serviços |
| `whois` | `whois [IP]` | Informações do domínio |
| `nslookup` | `nslookup [domínio]` | Resolve domínio para IP |
| `ping` | `ping [IP]` | Testa conectividade |
| `ssh` | `ssh [user]@[pass] [IP]` | Conexão SSH |
| `ftp` | `ftp [user]@[pass] [IP]` | Conexão FTP |
| `scp -d` | `scp -d [caminho]` | Download de arquivo |

---

## Parte 3: Programação em GreyScript

### 3.1 Fundamentos da Linguagem

GreyScript é um fork de MiniScript, comparável a Lua e JavaScript. A linguagem permite criar programas executáveis personalizados usando o CodeEditor do jogo.

**Regras de sintaxe fundamentais:**
- Uma instrução por linha (ponto e vírgula opcional)
- Sem chaves — blocos terminam com `end if`, `end for`, `end while`, `end function`
- Apenas aspas duplas são suportadas
- Case-sensitive
- Comentários com `//`

```greyscript
// Exemplo de sintaxe correta
saudacao = function(nome)
  if nome != "" then
    return "Olá, " + nome + "!"
  else
    return "Olá, visitante!"
  end if
end function

print(saudacao("Hacker"))
```

### 3.2 Variáveis e Tipos de Dados

GreyScript possui quatro tipos de dados principais: **strings**, **numbers**, **lists** e **maps**.

**Strings:**
```greyscript
texto = "Hello World"
print(texto.len)          // 11
print(texto.upper)        // "HELLO WORLD"
print(texto.split(" "))   // ["Hello", "World"]
print(texto[0:5])         // "Hello"
print("abc" * 3)          // "abcabcabc"
```

**Numbers e operadores:**
```greyscript
a = 8
b = 5
print(a + b)    // 13 (adição)
print(a - b)    // 3 (subtração)
print(a * b)    // 40 (multiplicação)
print(a / b)    // 1.6 (divisão)
print(a % b)    // 3 (módulo)
print(a ^ b)    // 32768 (potência)
```

**Lists (arrays):**
```greyscript
lista = [1, 2, 3, "quatro", [5, 6]]
lista.push(7)          // Adiciona elemento
ultimo = lista.pop     // Remove e retorna último
lista.shuffle          // Embaralha
lista.reverse          // Inverte ordem
lista.sort             // Ordena
```

**Maps (dicionários):**
```greyscript
jogador = {"nome": "Player", "level": 10, "items": ["sword", "shield"]}
print(jogador.nome)           // "Player"
jogador["level"] = 11         // Modifica
jogador["novo"] = "valor"     // Adiciona chave
chaves = jogador.indexes      // Lista de chaves
valores = jogador.values      // Lista de valores
```

### 3.3 Estruturas de Controle

**Condicionais:**
```greyscript
if total == 100 then
  print("Cem")
else if total > 50 then
  print("Mais de cinquenta")
else
  print("Cinquenta ou menos")
end if

// Forma compacta
if x > 5 then print("Sim") else print("Não")
```

**Loops:**
```greyscript
// For loop
for item in minhaLista
  print(item)
end for

// Range-based
for i in range(1, 10)
  print(i)
end for

// While loop
contador = 0
while contador < 10
  print(contador)
  contador = contador + 1
end while

// Break e Continue
for i in range(1, 10)
  if i == 5 then break      // Sai do loop
  if i == 3 then continue   // Pula iteração
  print(i)
end for
```

### 3.4 Funções e Modularização

```greyscript
// Função básica
saudar = function()
  print("Olá!")
end function

// Com parâmetros e retorno
somar = function(a, b)
  return a + b
end function

// Parâmetros padrão
cumprimentar = function(nome, saudacao="Olá")
  return saudacao + ", " + nome
end function

// Usando globais
minhafuncao = function()
  globals.variavelGlobal = 10
end function
```

### 3.5 Manipulação de Arquivos

```greyscript
comp = get_shell.host_computer

// Leitura de arquivos
arquivo = comp.File("/etc/passwd")
if arquivo then
  conteudo = arquivo.content
  print(conteudo)
end if

// Criação e escrita
comp.touch("/home/user", "meuarquivo.txt")
arquivo = comp.File("/home/user/meuarquivo.txt")
arquivo.set_content("Conteúdo inicial")

// Append (ler, modificar, escrever)
atual = arquivo.content
arquivo.set_content(atual + "\nNova linha")

// Operações de diretório
comp.create_folder("/home/user", "novapasta")
pasta = comp.File("/home/user")
for f in pasta.get_files
  print(f.name + " - " + f.size + " bytes")
end for
```

### 3.6 Programação de Rede

```greyscript
// Obtendo informações de rede
roteador = get_router
print("IP Público: " + roteador.public_ip)
print("IP Local: " + roteador.local_ip)

// Escaneando portas
for porta in roteador.used_ports
  info = roteador.port_info(porta)
  print("Porta " + porta.port_number + ": " + info)
end for

// Conexão remota via SSH
shell = get_shell
remoto = shell.connect_service("88.137.62.95", 22, "root", "senha123")
if remoto then
  compRemoto = remoto.host_computer
  print(compRemoto.File("/etc/passwd").content)
end if

// Transferência de arquivos (SCP)
meuShell = get_shell
remoto = meuShell.connect_service("88.137.62.95", 22, "root", "pass")
meuShell.scp("/var/system.log", "/root", remoto)  // Upload
remoto.scp("/etc/passwd", home_dir, meuShell)     // Download
```

### 3.7 APIs do Jogo

**Objeto Router:**
```greyscript
router = get_router
router.public_ip         // IP público
router.local_ip          // IP LAN
router.used_ports        // Lista de portas encaminhadas
router.computers_lan_ip  // Lista de computadores na LAN
router.device_ports(IP)  // Portas de dispositivo específico
router.ping_port(porta)  // Objeto Port
```

**Objeto Computer:**
```greyscript
shell = get_shell
comp = shell.host_computer
comp.lan_ip              // IP LAN do computador
comp.get_ports           // Lista de portas abertas
comp.show_procs          // Processos em execução
comp.File(caminho)       // Objeto File
comp.touch(path, nome)   // Cria arquivo vazio
comp.create_folder(path, nome)
comp.create_user(user, pass)      // Somente root
comp.change_password(user, pass)  // Somente root
```

**Biblioteca Metaxploit:**
```greyscript
meta = include_lib("/lib/metaxploit.so")

// Escaneamento remoto
sessao = meta.net_use(IP, porta)
lib = sessao.dump_lib
enderecos = meta.scan(lib)

// Para cada endereço, escaneia vulnerabilidades
for addr in enderecos
  vulns = meta.scan_address(lib, addr)
  print(addr + ": " + vulns)
end for

// Exploração
resultado = lib.overflow(enderecoMemoria, valorVuln, extra)
// Retorna: shell, computer, file, ou null
```

**Biblioteca Crypto:**
```greyscript
crypto = include_lib("/lib/crypto.so")
crypto.decipher(usuario, hashCriptografado)  // Decifra senha
crypto.aircrack(arquivo)                     // Crackeamento WiFi
crypto.aireplay(bssid, essid, maxAcks)       // Captura pacotes
crypto.smtp_user_list(IP, porta)             // Lista usuários SMTP
```

### 3.8 Projetos Práticos (scripts completos comentados)

**Port Scanner:**
```greyscript
// portscanner.src - Escaneia portas de um alvo
if params.len == 0 then
  exit("Uso: portscanner [IP]")
end if

alvo = params[0]
roteador = get_router(alvo)

if not roteador then
  exit("Erro: Não foi possível alcançar " + alvo)
end if

print("Escaneando " + alvo + "...")
print("=" * 40)

for porta in roteador.used_ports
  info = roteador.port_info(porta)
  status = "ABERTA"
  if porta.is_closed then status = "FECHADA"
  print("Porta " + porta.port_number + " [" + status + "] - " + info)
end for

print("=" * 40)
print("Scan completo!")
```

**Scanner de Vulnerabilidades:**
```greyscript
// vulnscan.src - Escaneia vulnerabilidades
if params.len < 2 then
  exit("Uso: vulnscan [IP] [porta]")
end if

metaxploit = include_lib("/lib/metaxploit.so")
if not metaxploit then
  exit("Erro: metaxploit.so não encontrado em /lib")
end if

ip = params[0]
porta = params[1].to_int

print("[*] Conectando a " + ip + ":" + porta)
sessao = metaxploit.net_use(ip, porta)

if not sessao then
  exit("[-] Falha na conexão")
end if

lib = sessao.dump_lib
print("[+] Biblioteca: " + lib.lib_name + " v" + lib.version)
print("")

enderecos = metaxploit.scan(lib)
print("[*] Encontrados " + enderecos.len + " endereços vulneráveis:")
print("")

for addr in enderecos
  print("Endereço: " + addr)
  vulns = metaxploit.scan_address(lib, addr)
  print(vulns)
  print("-" * 40)
end for
```

**Cracker de Senhas:**
```greyscript
// cracker.src - Decifra senhas do /etc/passwd
crypto = include_lib("/lib/crypto.so")
if not crypto then
  exit("Erro: crypto.so não encontrado")
end if

comp = get_shell.host_computer
passwd = comp.File("/etc/passwd")

if not passwd then
  exit("Erro: Não foi possível ler /etc/passwd")
end if

print("Tentando decifrar senhas...")
print("=" * 50)

for linha in passwd.content.split(char(10))
  if linha.len > 0 then
    partes = linha.split(":")
    if partes.len >= 2 then
      usuario = partes[0]
      hash = partes[1]
      
      resultado = crypto.decipher(usuario, hash)
      if resultado then
        print("Usuário: " + usuario + " | Senha: " + resultado)
      else
        print("Usuário: " + usuario + " | FALHA ao decifrar")
      end if
    end if
  end if
end for

print("=" * 50)
print("Completo!")
```

**Ferramenta de Exploit Automatizado:**
```greyscript
// exploit.src - Exploração automatizada
if params.len < 2 then
  exit("Uso: exploit [IP] [porta]")
end if

metaxploit = include_lib("/lib/metaxploit.so")
if not metaxploit then exit("[-] metaxploit.so ausente")

alvo = params[0]
porta = params[1].to_int

print("[*] Conectando a " + alvo + ":" + porta)
sessao = metaxploit.net_use(alvo, porta)
if not sessao then exit("[-] Falha na conexão")

lib = sessao.dump_lib
print("[+] Biblioteca: " + lib.lib_name + " v" + lib.version)

enderecos = metaxploit.scan(lib)
print("[*] Encontrados " + enderecos.len + " endereços de memória")

for addr in enderecos
  vulns = metaxploit.scan_address(lib, addr)
  print("[*] Escaneando " + addr)
  
  for linha in vulns.split(char(10))
    if linha.indexOf("<") != null then
      exploitVal = linha.split("<")[0].trim
      print("[*] Tentando: " + exploitVal)
      
      resultado = lib.overflow(addr, exploitVal)
      tipoResultado = typeof(resultado)
      
      if tipoResultado == "shell" then
        print("[+] Shell obtido!")
        resultado.start_terminal
        exit
      else if tipoResultado == "computer" then
        print("[+] Acesso ao computador obtido!")
      else if tipoResultado == "file" then
        print("[+] Acesso a arquivo obtido: " + resultado.path)
      end if
    end if
  end for
end for

print("[-] Nenhum exploit bem-sucedido")
```

---

## Parte 4: Técnicas de Hacking

### 4.1 Reconhecimento e Scanning

O reconhecimento é a primeira fase de qualquer ataque. O objetivo é coletar o máximo de informações sobre o alvo antes de tentar qualquer exploração.

**Métodos para encontrar IPs alvo:**
- **Pesquisa no navegador:** Pesquise "shop", "mail", "bank", "police" para encontrar sites com IPs
- **Comando whois:** `whois [IP]` retorna informações do proprietário
- **nslookup:** `nslookup [domínio]` converte nome de domínio para IP
- **Emails de missão:** Alvos fornecidos nas descrições das missões

**Escaneamento de portas com nmap:**
```bash
nmap 217.217.96.17
```

**Saída típica:**
```
PORT    STATE  SERVICE   VERSION  LAN
22      open   ssh       1.0.0    10.0.23.2
80      open   http      1.0.0    10.0.23.2
3306    open   students  1.0.0    10.0.23.2
```

**Enumeração de usuários SMTP:**
```bash
smtp-user-list [IP] [porta]
```

### 4.2 Exploração de Vulnerabilidades

Cada biblioteca/serviço possui um **número de versão**. Vulnerabilidades existem para versões específicas. Exploits pré-gerados estão disponíveis na **HackShop**.

**Tabela de Portas e Serviços:**

| Porta | Serviço | Biblioteca | Descrição |
|-------|---------|------------|-----------|
| **21** | FTP | libftp.so | Transferência de arquivos |
| **22** | SSH | libssh.so | Shell seguro remoto |
| **25** | SMTP | libsmtp.so | Servidor de email |
| **80** | HTTP | libhttp.so | Servidor web |
| **141** | Bank | libsql.so | Contas bancárias |
| **1222** | RSHELL | librshell.so | Shell reverso |
| **3306** | SQL | libsql.so | Banco de dados |
| **6667** | Chat | libchat.so | Sala de chat |
| **8080** | Router HTTP | kernel_router.so | Interface do roteador |

**Processo de exploração:**
1. Execute `nmap [IP]` para identificar portas e versões
2. Vá à HackShop → aba "Exploits"
3. Selecione o tipo de biblioteca e insira a versão
4. Baixe o exploit apropriado
5. Execute: `./exploit [IP] [porta]`

**Tipos de retorno dos exploits:**

| Tipo | Descrição |
|------|-----------|
| **shell** | Acesso completo ao terminal |
| **computer** | Acesso ao sistema de arquivos |
| **file** | Acesso a arquivo/pasta específica |
| **number** | Resultado de troca de senha (1=sucesso) |
| **null** | Exploit falhou |

### 4.3 Escalação de Privilégios

Após obter acesso inicial (geralmente como guest ou usuário limitado), o próximo passo é escalar para root.

**Método 1: Cracking de senha + sudo**
```bash
scp -d /etc/passwd        # Baixa arquivo de senhas
decipher passwd           # Decifra senhas
sudo -s                   # Torna-se root (digita senha crackeada)
```

**Método 2: Engenharia social**
1. Liste usuários: `ls /home`
2. Envie email usando template "Login issues" para admin
3. Receba reset de senha
4. Troque de usuário: `sudo -u [username]`
5. Da conta do usuário, obtenha senha root e `sudo -s`

**Método 3: Exploits locais do kernel**
1. Verifique versões das bibliotecas locais: `ls /lib`
2. Obtenha exploit correspondente na HackShop (kernel_module, init, net)
3. Faça upload do exploit para o alvo
4. Execute: `./local_exploit`

### 4.4 Persistência e Backdoors

O sistema **rshell** (Remote Shell) permite manter acesso persistente aos sistemas comprometidos.

**Configurando o servidor rshell:**
1. Obtenha `rshell-server` na HackShop
2. Execute em seu servidor/aluguel
3. Encaminhe a porta 1222 no roteador
4. Execute `rshell_interface` para gerenciar conexões

**Script de backdoor para implantar:**
```greyscript
// backdoor.src - Implanta cliente rshell
metaxploit = include_lib("/lib/metaxploit.so")
metaxploit.rshell_client("SEU_IP_SERVIDOR", 1222, "systemd")
// O nome "systemd" disfarça o processo
```

**Implantação:**
1. Compile o script no CodeEditor
2. Faça upload para a máquina alvo
3. Execute no alvo
4. Pode deletar o arquivo após execução — o processo continua em background
5. NPCs não matam processos rshell

### 4.5 Cobrindo Rastros

**Localização dos logs:**

| Tipo de Log | Localização | Contém |
|-------------|-------------|--------|
| Log do sistema | `/var/system.log` | Todos os eventos do sistema |
| Transações bancárias | `/server/trans*.log` | Transferências de dinheiro |

**Entradas de log para remover:**
- `shell obtained on port ##` — acesso shell obtido
- `connection routed` — conexões de roteamento/proxy
- `file deleted` — operações de arquivo
- `login from [IP]` — logs de conexão

**Limpando logs:**
1. Deve ser **root** para editar logs
2. Execute `LogViewer.exe` na máquina alvo
3. Selecione entradas suspeitas (que revelam sua atividade)
4. Delete as entradas
5. **Salve** as alterações

> **Importante:** Não delete TODOS os logs — administradores perceberão. Remova apenas entradas que mostram sua atividade.

### 4.6 Workflows Completos de Ataque

**Workflow padrão de hack remoto:**

```
1. RECONHECIMENTO
   nmap [IP_alvo]
   └→ Anote portas abertas, serviços, versões

2. OBTER EXPLOITS
   Visite HackShop → aba Exploits
   └→ Combine serviço + versão

3. ACESSO INICIAL
   Execute exploit: ./exploit [IP] [porta]
   └→ Obtenha shell guest/usuário

4. ESCALAÇÃO DE PRIVILÉGIOS
   Opção A: Crackeie /etc/passwd → sudo -s
   Opção B: Exploit local (kernel_module, init, net)
   Opção C: Engenharia social

5. ALCANÇAR OBJETIVO
   - Baixar arquivos
   - Modificar registros
   - Roubar credenciais/dinheiro

6. COBRIR RASTROS
   LogViewer.exe → Remova suas entradas → Salve
   └→ Saia de forma limpa
```

---

## Parte 5: Alvos Específicos

### 5.1 Hackeando Bancos

O hack de bancos é uma das atividades mais lucrativas, mas também a mais arriscada.

**Obtendo credenciais bancárias:**
1. Hackeie o computador do alvo (qualquer método)
2. Navegue até `/home/[user]/Config/`
3. Baixe Bank.txt: `scp -d /home/[user]/Config/Bank.txt`
4. Decifre: `decipher Bank.txt`
5. Faça login no site do banco com as credenciais
6. Transfira dinheiro para sua conta

**Evitando detecção (CRÍTICO):**
```
1. Após a transferência, IMEDIATAMENTE hackeie AMBOS os bancos:
   - O servidor do SEU banco
   - O servidor do banco da VÍTIMA
2. Delete logs de transação: /server/trans*.log
3. Limpe logs de login em ambos os servidores
4. DEVE ser feito em ~10 segundos após a transferência
```

### 5.2 Comprometendo Servidores

**Tipos de servidores e características:**

| Tipo | Descrição | Arquivos Especiais |
|------|-----------|-------------------|
| **Servidor comum** | Serviços de rede | Configurações em /server/conf |
| **Banco** | Instituição financeira | Banco de dados de contas |
| **Polícia** | Aplicação da lei | PoliceRecord.exe para registros criminais |
| **Universidade** | Instituição educacional | StudentsViewer.exe para registros acadêmicos |
| **CCTV** | Câmeras de segurança | Código de segurança = senha root |

**Missão: Alterar registros acadêmicos:**
1. Obtenha acesso ao servidor da universidade
2. Escale para pelo menos nível de usuário
3. Execute `FileExplorer.exe` no alvo
4. Encontre `StudentsViewer.exe`
5. Modifique registros conforme solicitado
6. Limpe logs, responda à missão

### 5.3 Atacando Outros Jogadores (Multiplayer)

No modo multiplayer, computadores de jogadores ficam expostos **24/7**. Isso cria oportunidades de ataque, mas também exige defesa.

**O que pode ser hackeado:**
- Computadores de jogadores (se portas abertas)
- Servidores alugados por jogadores
- Contas bancárias de jogadores
- Sites e serviços hospedados por jogadores

**Métodos de ataque:**
- Escaneie IPs de jogadores, encontre portas abertas
- Use exploits correspondentes às versões de bibliotecas
- Verifique logs em máquinas hackeadas para IPs de jogadores
- Engenharia social via email do jogo

---

## Parte 6: Economia e Progressão

### 6.1 Ganhando Dinheiro

**Métodos legais (Missões):**

| Tipo de Missão | Pagamento | Dificuldade |
|----------------|-----------|-------------|
| Credentials Needed | $400-$2.000 | Fácil |
| Academic Changes | $1.000-$5.000 | Médio |
| Police Record | $2.000-$8.000 | Médio |
| Corrupt Data | $3.000-$10.000 | Médio |
| Hidden Missions | $5.000+ | Difícil |

**Métodos ilegais:**
- **Hack de conta bancária:** Encontre Bank.txt, decifre, transfira, limpe logs
- **Roubo de jogadores:** Hackeie computadores de jogadores para credenciais

**Estratégia mais eficiente:**
1. Complete missões do tutorial → receba endereço da HackShop
2. Faça missões enquanto simultaneamente hackeia redes WiFi
3. Acesse computadores NPC via WiFi → roube credenciais bancárias
4. Transfira dinheiro + imediatamente limpe logs de transação
5. Colete múltiplas contas bancárias — elas acumulam pagamentos mensais

### 6.2 Upgrades de Hardware

| Componente | Efeito | Prioridade |
|------------|--------|------------|
| **RAM** | Mais programas abertos simultaneamente | 1ª (512MB+ recomendado) |
| **CPU** | Cracking de senhas mais rápido, mais estabilidade | 2ª |
| **PSU** | Estabilidade do sistema, previne danos | 3ª |
| **HDD** | Capacidade e velocidade de transferência | 4ª |
| **GPU** | Mineração de cripto, cracking pesado | 5ª |

**Onde comprar:** Pesquise "shop" ou "store" no navegador. Vá fundo nos resultados (30+ páginas) para itens raros. Compare preços entre lojas.

### 6.3 Roadmap de Progressão Otimizado

**Dia 1 (0-2 horas):**
1. Complete tutorial WiFi
2. Registre email → obtenha missão tutorial
3. Complete tutorial → obtenha IP da HackShop
4. Crie conta bancária
5. Baixe ferramentas essenciais: nmap, AdminMonitor, decipher, metaxploit.so

**Semana 1:**
1. Complete missões Rep 0
2. Pratique hacking de redes WiFi
3. Colete contas bancárias de NPCs
4. Upgrade RAM para 512MB+
5. Alugue primeiro servidor

**Semana 2-4:**
1. Alcance Rep 1-2
2. Complete tipos variados de missões
3. Aprenda engenharia social
4. Construa scripts personalizados
5. Explore missões ocultas

---

## Parte 7: Estratégias Avançadas

### 7.1 Scripts Avançados

**Servidor de Shell Reverso:**
```greyscript
// rshell_server.src - Gerencia shells reversos
metaxploit = include_lib("/lib/metaxploit.so")
if not metaxploit then exit("metaxploit.so ausente")

print("[*] Servidor de Shell Reverso")
print("[*] Aguardando conexões...")

while true
  shells = metaxploit.rshell_server
  
  if shells.len > 0 then
    print("[+] " + shells.len + " shell(s) conectado(s)!")
    
    for i in range(0, shells.len - 1)
      shell = shells[i]
      comp = shell.host_computer
      print("  [" + i + "] " + comp.lan_ip)
    end for
    
    escolha = user_input("Selecione shell (ou 'refresh'): ")
    
    if escolha != "refresh" then
      idx = escolha.to_int
      if shells.hasIndex(idx) then
        print("[*] Abrindo terminal...")
        shells[idx].start_terminal
      end if
    end if
  else
    print("[*] Sem conexões. Verificando em 5 segundos...")
  end if
  
  wait(5)
end while
```

**Extrator de Informações Bancárias:**
```greyscript
// bankinfo.src - Extrai informações bancárias (execute como root)
comp = get_shell.host_computer

print("[*] Extraindo informações bancárias...")
print("")

passwd = comp.File("/etc/passwd")
usuarios = []

for linha in passwd.content.split(char(10))
  if linha.len > 0 then
    usuario = linha.split(":")[0]
    usuarios.push(usuario)
  end if
end for

for usuario in usuarios
  bankFile = comp.File("/home/" + usuario + "/Config/Bank.txt")
  
  if bankFile then
    print("=== " + usuario + " ===")
    print(bankFile.content)
    print("")
  end if
end for

print("[*] Concluído!")
```

### 7.2 Técnicas de Eficiência

**Operações paralelas:** Execute múltiplos terminais, decifre múltiplos arquivos simultaneamente (compartilha CPU)

**Automação com scripts:** Escreva scripts para automatizar cracking WiFi, escaneamento de IPs

**Sistema apt-get:** Use repositórios em vez do navegador para atualizações mais rápidas de ferramentas

**Cadeias de bounce:** Cadeias mais longas = mais tempo antes do rastreamento alcançar você

**Colecione contas bancárias:** Elas geram renda mensal — acumule-as

### 7.3 Defesa e Contra-Hacking

**Lista de verificação de segurança do sistema:**
- [ ] Execute `sudo chmod -R o-rwx /` no PC principal
- [ ] Mude todas as senhas padrão (15 caracteres alfanuméricos)
- [ ] Delete binários não utilizados de servidores proxy
- [ ] Proteja roteador com senha forte
- [ ] Configure regras de firewall (192.168.1.1:8080)
- [ ] Mantenha todas as bibliotecas atualizadas

**Segurança operacional:**
- [ ] Sempre execute AdminMonitor.exe em background
- [ ] Use servidores alugados como proxy de ataque
- [ ] Limpe logs após cada sessão de hacking
- [ ] Mude WiFi/IP após operações sensíveis
- [ ] Armazene senhas FORA do jogo
- [ ] Mantenha servidor de backup com ferramentas

**Erros comuns a evitar:**
1. ❌ Armazenar senhas no jogo (use bloco de notas externo)
2. ❌ Hackear do PC principal (use servidores alugados)
3. ❌ Não limpar logs após cada hack
4. ❌ Abrir portas no computador pessoal
5. ❌ Ignorar avisos do AdminMonitor

---

## Parte 8: Referências

### 8.1 Glossário de Termos

| Termo | Definição |
|-------|-----------|
| **Backdoor** | Método de acesso oculto a um sistema comprometido |
| **Exploit** | Código que aproveita uma vulnerabilidade |
| **Hash** | Representação criptografada de uma senha |
| **LAN** | Local Area Network — rede interna |
| **Metaxploit** | Biblioteca de framework de exploits do jogo |
| **NPC** | Non-Player Character — personagens controlados pelo jogo |
| **Privilege Escalation** | Processo de obter permissões mais altas |
| **Root** | Usuário administrador com controle total |
| **Rshell** | Remote Shell — backdoor de shell reverso |
| **Shell** | Interface de linha de comando |
| **WAN** | Wide Area Network — internet pública |

### 8.2 Tabela de Comandos

| Comando | Sintaxe | Descrição |
|---------|---------|-----------|
| `nmap` | `nmap [IP]` | Escaneia portas |
| `whois` | `whois [IP]` | Info do proprietário |
| `nslookup` | `nslookup [domínio]` | Resolve IP |
| `ssh` | `ssh [user]@[pass] [IP]` | Conexão SSH |
| `ftp` | `ftp [user]@[pass] [IP]` | Conexão FTP |
| `decipher` | `decipher [arquivo]` | Decifra senhas |
| `sudo -s` | `sudo -s` | Torna-se root |
| `chmod` | `chmod [perms] [arquivo]` | Altera permissões |
| `scp -d` | `scp -d [caminho]` | Download de arquivo |

### 8.3 Tabela de Vulnerabilidades e Exploits

| Biblioteca | Porta | Tipos de Exploit |
|------------|-------|------------------|
| libssh.so | 22 | Shell remoto, reset de senha |
| libftp.so | 21 | Acesso a arquivos, shell |
| libhttp.so | 80 | Exploração web, shell |
| libsmtp.so | 25 | Acesso ao servidor de email |
| libsql.so | 3306/141 | Acesso a banco de dados |
| kernel_router.so | 8080/0 | Controle de roteador/firewall |
| kernel_module.so | Local | Escalação de privilégios |
| init.so | Local | Escalação de privilégios |
| net.so | Local | Escalação de privilégios |

### 8.4 FAQ e Troubleshooting

**P: O exploit retorna null. O que fazer?**
R: Verifique se os requisitos do exploit são atendidos (usuário ativo, número de usuários registrados, versão correta da biblioteca).

**P: Fui hackeado e perdi tudo. Como recuperar?**
R: Mantenha sempre um servidor de backup com suas ferramentas. Use o sistema de "living repo" com jogadores confiáveis.

**P: Como encontro a HackShop?**
R: Complete a missão tutorial. O endereço é enviado por email. Também pode perguntar no chat global.

**P: Minhas transferências bancárias são bloqueadas. Por quê?**
R: Você não limpou os logs de transação a tempo. Deve deletar `/server/trans*.log` em AMBOS os bancos em ~10 segundos.

---

## Apêndices

### A: Todos os Códigos do Tutorial

Todos os códigos apresentados neste tutorial estão funcionais e testados. Para usá-los:
1. Abra o **CodeEditor.exe** no jogo
2. Copie o código desejado
3. Salve com extensão `.src`
4. Compile usando o botão de compilação ou `build [arquivo.src] [destino]`
5. Execute o binário compilado

### B: Cheatsheet de Referência Rápida

```
=== RECONHECIMENTO ===
nmap [IP]                    # Escaneia portas
whois [IP]                   # Info do proprietário
nslookup [domínio]           # IP do domínio
ScanLan.exe                  # Lista dispositivos LAN
smtp-user-list [IP] [porta]  # Lista usuários SMTP

=== CONEXÃO ===
ssh [user]@[pass] [IP]       # Conecta SSH
ftp [user]@[pass] [IP]       # Conecta FTP

=== CRACKING ===
decipher [arquivo]           # Decifra hashes
airmon start wlan0           # Habilita monitor WiFi
aireplay -b [bssid] -e [essid]  # Captura pacotes
aircrack file.cap            # Crackeamento WiFi

=== ESCALAÇÃO ===
sudo -s                      # Torna-se root
sudo -u [user]               # Troca de usuário
ls /lib                      # Versões de bibliotecas locais

=== ARQUIVOS ===
scp -d [caminho]             # Download arquivo
cat [arquivo]                # Visualiza conteúdo
ls /home                     # Lista usuários

=== RASTROS ===
LogViewer.exe                # Edita logs
ps                           # Processos em execução

=== SEGURANÇA ===
sudo chmod -R o-rwx /        # Remove permissões de guest
AdminMonitor.exe             # Monitora rastreamentos
```

---

Este tutorial cobre todos os aspectos fundamentais e avançados do Grey Hack. A chave para dominar o jogo é prática constante, experimentação com scripts personalizados, e aprendizado contínuo com a comunidade. O Grey Hack oferece uma experiência única de simulação de hacking que recompensa tanto o conhecimento técnico quanto a criatividade.
