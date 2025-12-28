# Guia Definitivo de Programação GreyScript

GreyScript é a linguagem de programação do jogo Grey Hack, baseada em MiniScript com extensões específicas para simulação de hacking. Este guia apresenta **todas as funcionalidades documentadas** da linguagem, incluindo tipos de dados, operadores, estruturas de controle, métodos nativos e a API completa de objetos do jogo. A linguagem utiliza tipagem dinâmica, suporta programação orientada a objetos e oferece uma rica biblioteca de ferramentas para exploração de redes, manipulação de sistemas de arquivos e operações criptográficas.

---

## 1. Fundamentos da Linguagem

### 1.1 Tipos de dados

GreyScript possui **seis tipos de dados fundamentais**, todos com tipagem dinâmica inferida automaticamente pelo interpretador.

**Number** representa valores numéricos em precisão total, sem distinção entre inteiros e decimais. Valores como `42`, `3.14159` e `-17` são todos tratados como number. **String** armazena texto usando exclusivamente aspas duplas — aspas simples não são suportadas. Para incluir aspas no texto, duplique-as: `"Ela disse ""Olá"""`. **List** é uma coleção ordenada e mutável que aceita elementos de qualquer tipo: `[1, "texto", [2,3], {"chave": "valor"}]`. **Map** armazena pares chave-valor onde chaves podem ser strings ou números: `{"nome": "João", 42: "resposta"}`. **Null** representa ausência de valor, retornado por funções que falham ou variáveis não inicializadas. **Function** são valores de primeira classe, podendo ser armazenadas em variáveis e passadas como argumentos.

```greyscript
x = 42                      // Number
nome = "Alice"              // String  
itens = ["a", "b", "c"]     // List
dados = {"chave": "valor"}  // Map
vazio = null                // Null
dobrar = function(n)        // Function
    return n * 2
end function
```

**Valores truthy e falsy** são fundamentais: `0`, `null`, `""` (string vazia), `[]` (lista vazia) e `{}` (map vazio) avaliam como false. Todos os demais valores avaliam como true. Os keywords `true` e `false` existem mas equivalem numericamente a `1` e `0`.

### 1.2 Variáveis e escopo

Variáveis são declaradas implicitamente por atribuição e são **locais por padrão** dentro de funções. O sistema de escopo utiliza quatro keywords especiais para controle granular.

| Keyword | Descrição |
|---------|-----------|
| `globals` | Acessa/modifica variáveis no escopo global |
| `locals` | Referência explícita ao escopo local |
| `outer` | Acessa escopo da função encapsuladora |
| `self` | Referência ao objeto atual em métodos |

```greyscript
contador = 0  // Global

incrementar = function
    globals.contador = globals.contador + 1  // Modifica global
end function

incrementar
print(contador)  // 1
```

> **Aviso importante**: Para **modificar** uma variável global dentro de uma função, você DEVE usar `globals.`. Para apenas **ler**, o prefixo é opcional se não houver variável local com o mesmo nome.

### 1.3 Operadores

**Operadores aritméticos** funcionam tanto com números quanto strings em alguns casos:

| Operador | Números | Strings |
|----------|---------|---------|
| `+` | Adição | Concatenação |
| `-` | Subtração | Remove primeira ocorrência |
| `*` | Multiplicação | Replicação |
| `/` | Divisão | Divisão (corta string) |
| `%` | Módulo | — |
| `^` | Potência | — |

```greyscript
"Hello" + " World"    // "Hello World"
"test" * 3            // "testtesttest"
"hello" - "l"         // "helo" (remove primeiro 'l')
"abcd" / 2            // "ab"
```

**Operadores de comparação** retornam `1` (true) ou `0` (false): `==`, `!=`, `>`, `<`, `>=`, `<=`. Comparações encadeadas são suportadas: `0 < x < 10`.

**Operadores lógicos** usam palavras reservadas: `and`, `or`, `not`.

**Operadores de atribuição compostos**: `+=`, `-=`, `*=`, `/=`, `%=`, `^=`. Nota: `++` e `--` **não existem** em GreyScript.

**Operadores especiais**:
- `@` — Obtém referência de função sem invocá-la
- `isa` — Verifica tipo: `x isa list`
- `new` — Instancia objetos
- `[:]` — Slicing: `lista[1:3]`

### 1.4 Estruturas de controle

**Condicionais** usam `if`/`then`/`else if`/`else`/`end if`. Parênteses nas condições são **proibidos**:

```greyscript
// CORRETO
if x > 5 then
    print("grande")
else if x > 0 then
    print("positivo")
else
    print("não-positivo")
end if

// INCORRETO - NÃO use parênteses
if (x > 5) then  // ERRO

// Forma inline
if x > 5 then print("grande") else print("pequeno")
```

**Loop while** executa enquanto a condição for verdadeira:

```greyscript
i = 0
while i < 10
    print(i)
    i = i + 1
end while
```

**Loop for-in** itera sobre listas, strings, maps ou ranges:

```greyscript
// Sobre lista
for item in [1, 2, 3]
    print(item)
end for

// Sobre range
for i in range(1, 10)
    print(i)
end for

// Sobre map (retorna mini-map com .key e .value)
mapa = {"a": 1, "b": 2}
for entrada in mapa
    print(entrada.key + ": " + entrada.value)
end for
```

**break** sai do loop imediatamente. **continue** pula para próxima iteração.

### 1.5 Funções

Funções são definidas com `function`/`end function` e são valores de primeira classe:

```greyscript
// Função básica
saudacao = function
    print("Olá!")
end function

// Com parâmetros
soma = function(a, b)
    return a + b
end function

// Com parâmetros padrão (devem ser literais)
cumprimentar = function(nome, msg="Olá")
    print(msg + ", " + nome)
end function

// Chamada sem argumentos - parênteses opcionais
saudacao
saudacao()

// Chamada com argumentos - parênteses obrigatórios em expressões
resultado = soma(5, 3)
```

Para obter referência de função sem executá-la, use `@`:

```greyscript
minhaFunc = function
    print("executada")
end function

ref = @minhaFunc   // Obtém referência
ref                // Agora executa
```

### Keywords reservadas completas

| Keyword | Uso |
|---------|-----|
| `if`, `then`, `else`, `else if`, `end if` | Condicionais |
| `while`, `end while` | Loop while |
| `for`, `in`, `end for` | Loop for |
| `function`, `end function` | Definição de função |
| `return` | Retorno de função |
| `break`, `continue` | Controle de loop |
| `and`, `or`, `not` | Operadores lógicos |
| `true`, `false` | Booleanos (= 1, 0) |
| `null` | Valor nulo |
| `new`, `isa` | OOP |
| `self`, `super` | Referências de objeto |
| `globals`, `locals`, `outer` | Controle de escopo |

---

## 2. Métodos de Tipos Nativos

### 2.1 String

Strings são **imutáveis** — todos os métodos retornam novas strings sem modificar a original.

#### Métodos de busca e verificação

| Método | Descrição |
|--------|-----------|
| `indexOf(substr, begin=null) → number \| null` | Índice da primeira ocorrência; `null` se não encontrado |
| `lastIndexOf(substr) → number \| null` | Índice da última ocorrência |
| `hasIndex(index) → number` | `1` se índice existe, `0` caso contrário |
| `is_match(pattern, regexOpts=null) → number` | `1` se corresponde ao regex |
| `matches(pattern, regexOpts=null) → list` | Lista de todas as correspondências regex |

```greyscript
texto = "Programação GreyScript"
texto.indexOf("Script")      // 11
texto.indexOf("Java")        // null (não -1!)
texto.lastIndexOf("a")       // 8
texto.hasIndex(5)            // 1
"abc123".is_match("[0-9]+")  // 1
"a1b2c3".matches("[0-9]")    // ["1", "2", "3"]
```

#### Métodos de transformação

| Método | Descrição |
|--------|-----------|
| `replace(old, new, regexOpts=null) → string` | Substitui TODAS as ocorrências |
| `remove(substr) → string` | Remove primeira ocorrência |
| `insert(index, value) → string` | Insere valor no índice |
| `split(separator, regexOpts=null) → list` | Divide em lista |
| `trim → string` | Remove espaços das extremidades |
| `lower → string` | Converte para minúsculas |
| `upper → string` | Converte para maiúsculas |

```greyscript
"aaa bbb ccc".replace("b", "X")  // "aaa XXX ccc"
"Hello World".remove("o")         // "Hell World"
"abc".insert(1, "X")              // "aXbc"
"a,b,c".split(",")                // ["a", "b", "c"]
"  espaços  ".trim                // "espaços"
"HELLO".lower                     // "hello"
```

#### Métodos de conversão e informação

| Método | Descrição |
|--------|-----------|
| `len → number` | Comprimento da string |
| `val → number` | Converte para número (0 se inválido) |
| `to_int → number \| string` | Converte para inteiro ou retorna original |
| `code → number` | Código Unicode do primeiro caractere |
| `values → list` | Lista de todos os caracteres |
| `indexes → list` | Lista de todos os índices |

```greyscript
"Hello".len        // 5
"3.14".val         // 3.14
"42".to_int        // 42
"A".code           // 65
"Hi".values        // ["H", "i"]
"abc".indexes      // [0, 1, 2]
```

#### Slicing de strings

```greyscript
texto = "GreyScript"
texto[0:4]         // "Grey"
texto[4:]          // "Script"  
texto[-6:]         // "Script"
texto[0:-6]        // "Grey"
// Função slice também funciona:
slice(texto, 0, 4) // "Grey"
```

### 2.2 List

Listas são **mutáveis** — métodos modificam a lista original.

#### Métodos de adição e remoção

| Método | Descrição |
|--------|-----------|
| `push(val) → list` | Adiciona ao final; retorna lista (encadeável) |
| `insert(index, val) → list` | Insere no índice especificado |
| `pop → any` | Remove e retorna último elemento |
| `pull → any` | Remove e retorna primeiro elemento |
| `remove(index) → null` | Remove elemento no índice |

```greyscript
nums = [1, 2, 3]
nums.push(4)           // [1, 2, 3, 4]
nums.push(5).push(6)   // Encadeamento funciona
nums.insert(0, 0)      // [0, 1, 2, 3, 4, 5, 6]
ultimo = nums.pop      // 6; nums = [0, 1, 2, 3, 4, 5]
primeiro = nums.pull   // 0; nums = [1, 2, 3, 4, 5]
nums.remove(2)         // nums = [1, 2, 4, 5]

// Remover por valor:
nums.remove(nums.indexOf(4))
```

#### Métodos de busca e verificação

| Método | Descrição |
|--------|-----------|
| `indexOf(val, begin=null) → number \| null` | Índice do elemento; `null` se não encontrado |
| `hasIndex(index) → number` | `1` se índice existe |
| `len → number` | Número de elementos |

```greyscript
empresas = ["Google", "Apple", "Microsoft"]
empresas.indexOf("Apple")     // 1
empresas.indexOf("Amazon")    // null
empresas.hasIndex(2)          // 1
empresas.len                  // 3
```

#### Métodos de ordenação e reorganização

| Método | Descrição |
|--------|-----------|
| `sort(key=null, asc=1) → list` | Ordena alfanumericamente |
| `reverse → list` | Inverte ordem |
| `shuffle → null` | Embaralha aleatoriamente |

```greyscript
[3, 1, 4, 1, 5].sort           // [1, 1, 3, 4, 5]
["c", "a", "b"].sort           // ["a", "b", "c"]

// Ordenar lista de maps
pessoas = [{"nome": "Zoe"}, {"nome": "Ana"}]
pessoas.sort("nome")           // Ordena por chave "nome"

[1, 2, 3].reverse              // [3, 2, 1]
```

#### Outros métodos

| Método | Descrição |
|--------|-----------|
| `join(separator) → string` | Concatena elementos com separador |
| `sum → number` | Soma valores numéricos (ignora não-numéricos) |
| `values → list` | Cópia dos valores |
| `indexes → list` | Lista de índices |

```greyscript
["a", "b", "c"].join("-")     // "a-b-c"
[1, 2, 3, 4].sum              // 10
[1, "x", 3].sum               // 4 (ignora "x")
```

### 2.3 Map

Maps são **mutáveis** e permitem chaves string ou numéricas.

| Método | Descrição |
|--------|-----------|
| `hasIndex(key) → number` | `1` se chave existe |
| `indexOf(value) → any \| null` | Retorna chave para o valor |
| `push(key) → map` | Adiciona chave com valor `1` |
| `remove(key) → number` | Remove par; retorna `1` sucesso, `0` falha |
| `pop → any` | Remove e retorna primeira chave |
| `shuffle → null` | Embaralha valores entre chaves |
| `indexes → list` | Lista de todas as chaves |
| `values → list` | Lista de todos os valores |
| `len → number` | Número de pares |
| `sum → number` | Soma dos valores numéricos |

```greyscript
mapa = {"a": 1, "b": 2, 0: "zero"}
mapa.hasIndex("a")     // 1
mapa.hasIndex(0)       // 1
mapa.hasIndex("0")     // 0 (tipos diferentes!)
mapa.indexOf(2)        // "b"
mapa.indexes           // ["a", "b", 0]
mapa.values            // [1, 2, "zero"]

// Acesso por chave
mapa["a"]              // 1
mapa.a                 // 1 (sintaxe de ponto para chaves válidas)
mapa[0]                // "zero"
```

### 2.4 Number

A maioria das operações numéricas são funções globais:

| Função | Descrição |
|--------|-----------|
| `abs(n) → number` | Valor absoluto |
| `floor(n) → number` | Arredonda para baixo |
| `ceil(n) → number` | Arredonda para cima |
| `round(n, decimais=0) → number` | Arredonda para decimais especificados |
| `sqrt(n) → number` | Raiz quadrada |
| `sign(n) → number` | Retorna -1, 0 ou 1 |
| `log(n, base) → number` | Logaritmo |
| `sin(rad)`, `cos(rad)`, `tan(rad)` | Funções trigonométricas |
| `asin(n)`, `acos(n)`, `atan(n)` | Funções trigonométricas inversas |
| `rnd(seed=null) → number` | Número aleatório entre 0 e 1 |
| `range(start, end, inc=1) → list` | Gera lista de números |
| `char(code) → string` | Caractere do código Unicode |
| `str(n) → string` | Converte número para string |

```greyscript
abs(-5)              // 5
floor(3.7)           // 3
ceil(3.2)            // 4
round(3.567, 2)      // 3.57
sqrt(16)             // 4
range(1, 5)          // [1, 2, 3, 4, 5]
range(0, 10, 2)      // [0, 2, 4, 6, 8, 10]
char(65)             // "A"
pi                   // 3.14159265358979

// Inteiro aleatório de 1 a 10:
floor(rnd * 10) + 1
```

---

## 3. Objetos do Jogo (API Reference)

### 3.1 Computer

Representa um computador no jogo, permitindo acesso ao sistema de arquivos, rede e processos.

**Como obter:**
```greyscript
pc = get_shell.host_computer
// Ou de exploits que retornam tipo "computer"
```

#### Métodos do sistema de arquivos

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `File` | `File(path) → File \| null` | Retorna objeto File no caminho |
| `touch` | `touch(path, fileName) → 1 \| string` | Cria arquivo vazio |
| `create_folder` | `create_folder(path, folderName) → 1 \| 0` | Cria diretório |

```greyscript
pc = get_shell.host_computer

// Obter arquivo
arquivo = pc.File("/etc/passwd")
if arquivo then print(arquivo.get_content)

// Criar arquivo
resultado = pc.touch("/home/user", "teste.txt")
if resultado == 1 then
    novoArquivo = pc.File("/home/user/teste.txt")
    novoArquivo.set_content("Conteúdo inicial")
end if

// Criar pasta
pc.create_folder("/home/user", "scripts")
```

#### Métodos de gerenciamento de usuários (requer root)

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `create_user` | `create_user(user, password) → 1 \| string` | Cria usuário |
| `delete_user` | `delete_user(user, removeHome?) → 1 \| string` | Remove usuário |
| `change_password` | `change_password(user, password) → 1 \| string` | Altera senha |
| `create_group` | `create_group(user, groupName) → 1 \| string` | Cria grupo |
| `delete_group` | `delete_group(user, groupName) → 1 \| string` | Remove grupo |
| `groups` | `groups(username) → string` | Lista grupos do usuário |

```greyscript
// Criar novo usuário
resultado = pc.create_user("hacker", "senha123")
if resultado == 1 then
    print("Usuário criado com sucesso")
else
    print("Erro: " + resultado)
end if
```

#### Métodos de rede

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `local_ip` | `local_ip() → string` | IP local do computador |
| `public_ip` | `public_ip() → string` | IP público |
| `network_gateway` | `network_gateway() → string` | IP do gateway |
| `is_network_active` | `is_network_active() → number` | `1` se conectado |
| `active_net_card` | `active_net_card() → string` | "WIFI" ou "ETHERNET" |
| `network_devices` | `network_devices() → list` | Lista de dispositivos de rede |
| `wifi_networks` | `wifi_networks(device) → list` | Redes WiFi disponíveis |
| `connect_wifi` | `connect_wifi(device, bssid, essid, pass) → 1 \| string \| null` | Conecta ao WiFi |

```greyscript
print("IP Local: " + pc.local_ip)
print("IP Público: " + pc.public_ip)
print("Gateway: " + pc.network_gateway)
print("Conexão: " + pc.active_net_card)

// Listar redes WiFi
redes = pc.wifi_networks("wlan0")
for rede in redes
    partes = rede.split(" ")
    print("BSSID: " + partes[0] + " | ESSID: " + partes[2])
end for
```

#### Métodos de portas e processos

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `get_ports` | `get_ports() → list[Port]` | Lista de portas com serviços |
| `show_procs` | `show_procs() → string` | Processos em execução |
| `close_program` | `close_program(PID) → 1 \| 0 \| string` | Encerra processo |
| `get_name` | `get_name() → string` | Hostname do computador |

### 3.2 File

Representa arquivos e diretórios no sistema de arquivos.

**Como obter:**
```greyscript
arquivo = get_shell.host_computer.File("/caminho/arquivo")
```

#### Propriedades (acessadas como métodos)

| Propriedade | Tipo | Descrição |
|-------------|------|-----------|
| `name` | string | Nome do arquivo/pasta |
| `path` | string | Caminho absoluto |
| `parent` | File \| null | Diretório pai |
| `owner` | string | Usuário proprietário |
| `group` | string | Grupo proprietário |
| `permissions` | string | Permissões no formato Unix |
| `size` | string | Tamanho em bytes |
| `is_folder` | number | `1` se diretório |
| `is_binary` | number | `1` se binário |

```greyscript
arquivo = pc.File("/etc/passwd")
print("Nome: " + arquivo.name)           // "passwd"
print("Caminho: " + arquivo.path)        // "/etc/passwd"
print("Proprietário: " + arquivo.owner)  // "root"
print("Permissões: " + arquivo.permissions)  // "-rw-r--r--"
print("Tamanho: " + arquivo.size + " bytes")
print("É pasta: " + arquivo.is_folder)   // 0
```

#### Métodos de conteúdo

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `get_content` | `get_content() → string` | Lê conteúdo do arquivo |
| `set_content` | `set_content(content) → 1 \| string` | Define conteúdo |
| `allow_import` | `allow_import() → number` | `1` se pode ser importado |

```greyscript
// Ler arquivo
conteudo = arquivo.get_content
print(conteudo)

// Escrever arquivo
arquivo.set_content("Novo conteúdo")
```

#### Métodos de operações de arquivo

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `copy` | `copy(destPath, newName) → 1 \| string \| null` | Copia arquivo |
| `move` | `move(destPath, newName) → 1 \| string \| null` | Move arquivo |
| `rename` | `rename(newName) → string` | Renomeia (vazio = sucesso) |
| `delete` | `delete() → string \| null` | Exclui arquivo |

```greyscript
arquivo.copy("/home/user/backup", "passwd.bak")
arquivo.move("/tmp", "passwd.old")
arquivo.rename("novo_nome.txt")
arquivo.delete
```

#### Métodos de permissão

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `chmod` | `chmod(perm, recursive?) → string` | Altera permissões |
| `has_permission` | `has_permission(perm) → number` | Verifica permissão |
| `set_owner` | `set_owner(owner, recursive?) → 1 \| string` | Define proprietário |
| `set_group` | `set_group(group, recursive?) → 1 \| string` | Define grupo |

**Formato chmod:** `[u/g/o][+/-][r/w/x]`
- `u` = usuário, `g` = grupo, `o` = outros
- `+` = adicionar, `-` = remover
- `r` = leitura, `w` = escrita, `x` = execução

```greyscript
arquivo.chmod("u+rwx")     // Todas permissões para dono
arquivo.chmod("o-rwx")     // Remove todas para outros
arquivo.chmod("g+r", 1)    // Recursivo

if arquivo.has_permission("r") then
    print(arquivo.get_content)
end if
```

#### Métodos de diretório

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `get_folders` | `get_folders() → list[File]` | Lista subdiretórios |
| `get_files` | `get_files() → list[File]` | Lista arquivos (não pastas) |

```greyscript
raiz = pc.File("/")
for pasta in raiz.get_folders
    print("📁 " + pasta.name)
end for
for arq in raiz.get_files
    print("📄 " + arq.name)
end for
```

### 3.3 Shell

Representa uma sessão de terminal com um usuário específico.

**Como obter:**
```greyscript
shell = get_shell                              // Shell atual
shellRoot = get_shell("root", "senha")         // Shell com credenciais
remoto = shell.connect_service(ip, port, user, pass)  // Shell remoto
```

#### Métodos principais

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `host_computer` | `host_computer() → Computer \| null` | Computador associado |
| `start_terminal` | `start_terminal() → string \| null` | Abre terminal interativo |
| `connect_service` | `connect_service(ip, port, user, pass, service?) → Shell \| FtpShell \| string` | Conecta a serviço remoto |
| `ping` | `ping(ip) → 1 \| 0 \| string \| null` | Testa conectividade |
| `scp` | `scp(origem, destino, shellRemoto) → 1 \| string \| null` | Copia entre shells |
| `launch` | `launch(path, args) → null \| 0` | Executa programa |
| `build` | `build(srcPath, buildPath, allowImport?) → string` | Compila código |

```greyscript
shell = get_shell
pc = shell.host_computer

// Conectar a SSH remoto
remoto = shell.connect_service("192.168.1.100", 22, "root", "password")
if remoto != null then
    print("Conectado a: " + remoto.host_computer.local_ip)
    
    // Copiar arquivo para remoto
    shell.scp("/home/user/exploit.src", "/tmp", remoto)
    
    // Executar comando no remoto
    remoto.launch("/bin/ls", "-la /root")
    
    // Abrir terminal interativo
    remoto.start_terminal
else
    print("Conexão falhou")
end if

// Compilar programa
resultado = shell.build("/home/user/hack.src", "/home/user")
if resultado == "" then
    print("Compilação bem-sucedida!")
    shell.launch("/home/user/hack", "arg1 arg2")
end if
```

### 3.4 Router

Representa um roteador de rede.

**Como obter:**
```greyscript
roteador = get_router                    // Roteador local
roteador = get_router("85.123.45.67")    // Roteador por IP
switch = get_switch("192.168.1.1")       // Switch na LAN
```

#### Métodos

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `public_ip` | `public_ip() → string` | IP público |
| `local_ip` | `local_ip() → string` | IP local |
| `essid_name` | `essid_name() → string` | Nome da rede WiFi |
| `bssid_name` | `bssid_name() → string` | MAC address |
| `kernel_version` | `kernel_version() → string` | Versão do kernel_router.so |
| `used_ports` | `used_ports() → list[Port]` | Portas com redirecionamento |
| `ping_port` | `ping_port(port) → Port \| null` | Verifica porta específica |
| `port_info` | `port_info(portObj) → string` | Informações do serviço |
| `device_ports` | `device_ports(lanIP) → list[Port]` | Portas de dispositivo |
| `devices_lan_ip` | `devices_lan_ip() → list[string]` | IPs dos dispositivos conectados |
| `firewall_rules` | `firewall_rules() → string` | Regras de firewall |

```greyscript
roteador = get_router
print("IP Público: " + roteador.public_ip)
print("ESSID: " + roteador.essid_name)
print("BSSID: " + roteador.bssid_name)
print("Kernel: " + roteador.kernel_version)

// Escanear rede
print("\n--- Dispositivos na Rede ---")
for ip in roteador.devices_lan_ip
    print("Dispositivo: " + ip)
    for porta in roteador.device_ports(ip)
        info = roteador.port_info(porta)
        status = "ABERTA"
        if porta.is_closed then status = "FECHADA"
        print("  Porta " + porta.port_number + " [" + status + "]: " + info)
    end for
end for
```

### 3.5 Port

Representa uma porta de serviço.

**Como obter:**
```greyscript
portas = roteador.used_ports
porta = roteador.ping_port(22)
portas = pc.get_ports
```

#### Propriedades

| Propriedade | Tipo | Descrição |
|-------------|------|-----------|
| `port_number` | number | Número da porta |
| `is_closed` | number | `1` se fechada |
| `get_lan_ip` | string | IP LAN para onde redireciona |

**Portas padrão do jogo:**

| Porta | Serviço | Biblioteca |
|-------|---------|------------|
| 21 | FTP | libftp.so |
| 22 | SSH | libssh.so |
| 25 | SMTP | libsmtp.so |
| 80/8080 | HTTP | libhttp.so |
| 1222 | Rshell | — |
| 1542 | Repository | — |
| 3306-3308 | SQL | libmysql.so |

### 3.6 Metaxploit

Framework de exploração de vulnerabilidades.

**Como obter:**
```greyscript
meta = include_lib("/lib/metaxploit.so")
if not meta then exit("Erro: metaxploit.so não encontrado")
```

#### Métodos

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `load` | `load(path) → MetaLib \| null` | Carrega biblioteca local |
| `net_use` | `net_use(ip, port?) → NetSession \| null` | Estabelece sessão remota |
| `scan` | `scan(metaLib) → list[string]` | Lista endereços de memória vulneráveis |
| `scan_address` | `scan_address(metaLib, addr) → string \| null` | Detalhes da vulnerabilidade |
| `sniffer` | `sniffer(saveEncSource?) → string \| null` | Captura pacotes de rede |
| `rshell_client` | `rshell_client(addr, port?, procName?) → number \| string` | Inicia cliente reverse shell |
| `rshell_server` | `rshell_server() → list[Shell]` | Lista shells de conexões reversas |

```greyscript
meta = include_lib("/lib/metaxploit.so")

// Explorar biblioteca local
lib = meta.load("/lib/init.so")
print("Biblioteca: " + lib.lib_name + " v" + lib.version)

enderecos = meta.scan(lib)
for addr in enderecos
    info = meta.scan_address(lib, addr)
    print("\n" + addr + ":")
    print(info)
end for
```

### 3.7 MetaLib

Objeto de biblioteca carregada para exploração.

**Como obter:**
```greyscript
// Local
lib = meta.load("/lib/alvo.so")

// Remoto
sessao = meta.net_use("192.168.1.5", 22)
lib = sessao.dump_lib
```

#### Propriedades e métodos

| Membro | Tipo | Descrição |
|--------|------|-----------|
| `lib_name` | string | Nome do arquivo da biblioteca |
| `version` | string | Versão da biblioteca |
| `overflow` | `overflow(addr, value, extra?) → Shell \| Computer \| File \| number \| string \| null` | Executa buffer overflow |

**Tipos de retorno do overflow:**
- `shell` — Acesso shell remoto (mais comum)
- `computer` — Acesso direto ao objeto Computer (raro)
- `file` — Acesso a arquivo específico
- `number` — Resultado de troca de senha (1 = sucesso)
- `string` — Mensagem de erro
- `null` — Exploit falhou

```greyscript
meta = include_lib("/lib/metaxploit.so")
sessao = meta.net_use("192.168.1.5", 22)
lib = sessao.dump_lib

print("Alvo: " + lib.lib_name + " v" + lib.version)

enderecos = meta.scan(lib)
for addr in enderecos
    info = meta.scan_address(lib, addr)
    
    // Extrair valor vulnerável do info
    if info.indexOf("Unsafe check:") != null then
        inicio = info.indexOf("<b>") + 3
        fim = info.indexOf("</b>")
        valorVuln = info[inicio:fim]
        
        // Tentar exploit
        resultado = lib.overflow(addr, valorVuln)
        tipo = typeof(resultado)
        
        if tipo == "shell" then
            print("Shell obtido!")
            resultado.start_terminal
        else if tipo == "computer" then
            print("Acesso ao computador!")
            comp = resultado
        else if tipo == "file" then
            print("Arquivo: " + resultado.path)
        end if
    end if
end for
```

### 3.8 NetSession

Sessão de conexão remota para exploração.

**Como obter:**
```greyscript
sessao = meta.net_use("ip_alvo", porta)
```

#### Métodos

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `dump_lib` | `dump_lib() → MetaLib` | Biblioteca do serviço remoto |
| `get_num_conn_gateway` | `get_num_conn_gateway() → number` | Dispositivos conectados como gateway |
| `get_num_portforward` | `get_num_portforward() → number` | Portas com redirecionamento |
| `get_num_users` | `get_num_users() → number` | Número de usuários |
| `is_any_active_user` | `is_any_active_user() → number` | `1` se há usuários ativos |
| `is_root_active_user` | `is_root_active_user() → number` | `1` se root está ativo |

```greyscript
sessao = meta.net_use("192.168.1.5", 22)
if not sessao then exit("Conexão falhou")

lib = sessao.dump_lib
print("Biblioteca: " + lib.lib_name)
print("Usuários ativos: " + sessao.is_any_active_user)
print("Root ativo: " + sessao.is_root_active_user)
print("Total de usuários: " + sessao.get_num_users)
```

### 3.9 Crypto

Funções criptográficas e de cracking de WiFi.

**Como obter:**
```greyscript
crypto = include_lib("/lib/crypto.so")
```

#### Métodos

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `airmon` | `airmon(option, device) → string \| number` | Liga/desliga modo monitor |
| `aireplay` | `aireplay(bssid, essid, maxAcks?) → string \| null` | Captura pacotes WiFi |
| `aircrack` | `aircrack(capFile) → string \| null` | Crackeia senha WiFi |
| `decipher` | `decipher(hash) → string \| null` | Descriptografa hash de senha |
| `smtp_user_list` | `smtp_user_list(ip, port) → list \| string \| null` | Enumera usuários SMTP |

```greyscript
crypto = include_lib("/lib/crypto.so")
pc = get_shell.host_computer

// Crackear WiFi
crypto.airmon("start", "wlan0")

redes = pc.wifi_networks("wlan0")
alvo = redes[0].split(" ")
bssid = alvo[0]
essid = alvo[2]

// Capturar pacotes (7000-25000 ACKs recomendado)
crypto.aireplay(bssid, essid, 10000)

// Crackear
senha = crypto.aircrack(current_path + "/file.cap")
if senha then
    print("Senha encontrada: " + senha)
    pc.connect_wifi("wlan0", bssid, essid, senha)
end if

crypto.airmon("stop", "wlan0")
```

**Descriptografar senhas do /etc/passwd:**
```greyscript
conteudo = pc.File("/etc/passwd").get_content
linhas = conteudo.split(char(10))
for linha in linhas
    partes = linha.split(":")
    usuario = partes[0]
    hash = partes[1]
    senha = crypto.decipher(hash)
    if senha then
        print(usuario + " : " + senha)
    end if
end for
```

### 3.10 FtpShell / Service

**FtpShell** é retornado ao conectar via FTP:

```greyscript
ftp = shell.connect_service("192.168.1.5", 21, "user", "pass", "ftp")
```

| Método | Descrição |
|--------|-----------|
| `start_terminal` | Abre terminal FTP |
| `host_computer` | Computador remoto |
| `put` | Copia arquivos (alias de scp) |

**Service** gerencia serviços instalados:

```greyscript
service = include_lib("/lib/service.so")
service.install_service()   // Instala e inicia
service.start_service()     // Apenas inicia
service.stop_service()      // Para serviço
```

### 3.11 Mail / Wallet

**MetaMail** (sistema de email):

```greyscript
mail = mail_login("user@domain.com", "senha")
```

| Método | Descrição |
|--------|-----------|
| `fetch() → list` | Lista emails recebidos |
| `read(mail_id) → string` | Lê email completo |
| `send(addr, subject, msg) → 1 \| string` | Envia email |
| `delete(mail_id) → 1 \| string` | Exclui email |

**Blockchain e Wallet** (criptomoedas):

```greyscript
bc = include_lib("/lib/blockchain.so")
carteira = bc.login_wallet("usuario", "senha")
```

| Método Blockchain | Descrição |
|-------------------|-----------|
| `create_wallet(user, pass)` | Cria carteira mestre |
| `login_wallet(user, pass)` | Acessa carteira |
| `coin_price(coinName)` | Preço atual da moeda |

| Método Wallet | Descrição |
|---------------|-----------|
| `buy_coin(coin, qtd, preco)` | Oferta de compra |
| `sell_coin(coin, qtd, preco)` | Oferta de venda |
| `get_balance(coin)` | Saldo da moeda |
| `list_coins()` | Moedas na carteira |

### 3.12 AptClient (Gerenciador de Pacotes)

```greyscript
apt = include_lib("/lib/aptclient.so")
```

| Método | Assinatura | Descrição |
|--------|------------|-----------|
| `show` | `show(repo) → string` | Lista pacotes do repositório |
| `search` | `search(package) → string` | Busca pacote |
| `update` | `update() → string` | Atualiza lista de pacotes |
| `add_repo` | `add_repo(addr, port?) → string` | Adiciona repositório |
| `del_repo` | `del_repo(addr) → string` | Remove repositório |
| `install` | `install(package, path?) → string` | Instala pacote |
| `check_upgrade` | `check_upgrade(path) → 1 \| 0 \| string` | Verifica atualização |

```greyscript
apt = include_lib("/lib/aptclient.so")

// Adicionar e atualizar repositório
apt.add_repo("192.168.1.50", 1542)
apt.update

// Buscar e instalar
info = apt.search("metaxploit.so")
print(info)
apt.install("metaxploit.so")

// Auto-atualizar bibliotecas
libs = ["/lib/metaxploit.so", "/lib/crypto.so"]
for lib in libs
    if apt.check_upgrade(lib) == 1 then
        nome = lib.split("/").pop
        apt.install(nome, "/lib")
        print("Atualizado: " + nome)
    end if
end for
```

---

## 4. Padrões e Idiomas Comuns

### 4.1 Snippets reutilizáveis

**Carregamento seguro de bibliotecas:**
```greyscript
carregar_lib = function(caminho)
    lib = include_lib(caminho)
    if not lib then
        exit("Erro: " + caminho + " não encontrado em /lib")
    end if
    return lib
end function

meta = carregar_lib("/lib/metaxploit.so")
crypto = carregar_lib("/lib/crypto.so")
```

**Parser de argumentos:**
```greyscript
if params.len < 1 or params[0] == "-h" or params[0] == "--help" then
    nomePrograma = program_path.split("/").pop
    exit("Uso: " + nomePrograma + " <ip> [porta]")
end if

ip = params[0]
porta = 22
if params.len > 1 then porta = params[1].to_int
```

**Travessia recursiva de diretórios:**
```greyscript
percorrer = function(pasta, profundidade)
    if profundidade > 10 then return  // Evita recursão infinita
    recuo = "  " * profundidade
    
    for arq in pasta.get_files
        print(recuo + "📄 " + arq.name)
    end for
    
    for dir in pasta.get_folders
        print(recuo + "📁 " + dir.name + "/")
        percorrer(dir, profundidade + 1)
    end for
end function

raiz = get_shell.host_computer.File("/")
percorrer(raiz, 0)
```

**Scanner de rede completo:**
```greyscript
escanear_alvo = function(ip, porta)
    meta = include_lib("/lib/metaxploit.so")
    if not meta then return "metaxploit não encontrado"
    
    sessao = meta.net_use(ip, porta)
    if not sessao then return "Conexão falhou"
    
    lib = sessao.dump_lib
    print("Alvo: " + lib.lib_name + " v" + lib.version)
    print("Usuários: " + sessao.get_num_users)
    print("Root ativo: " + sessao.is_root_active_user)
    
    enderecos = meta.scan(lib)
    for addr in enderecos
        vulns = meta.scan_address(lib, addr)
        print("\n" + addr + ":\n" + vulns)
    end for
    
    return "Scan completo"
end function
```

### 4.2 Tratamento de erros

**Padrão de verificação null:**
```greyscript
// Sempre verificar null antes de usar
arquivo = pc.File("/caminho/arquivo")
if not arquivo then
    exit("Arquivo não encontrado")
end if

// Para funções que retornam tipos mistos
resultado = lib.overflow(addr, valor)
if resultado == null then
    print("Exploit falhou")
else if typeof(resultado) == "shell" then
    resultado.start_terminal
else if typeof(resultado) == "string" then
    print("Erro: " + resultado)
end if
```

**Verificação de tipo:**
```greyscript
validar_tipo = function(valor, tipoEsperado)
    if typeof(valor) != tipoEsperado then
        exit("Tipo inválido: esperado " + tipoEsperado + ", obtido " + typeof(valor))
    end if
end function
```

### 4.3 Otimização

**Limitações conhecidas:**
- **Binários compilados:** máximo 80.000 caracteres
- **Saída de print:** máximo 2.000 caracteres por execução
- **Closures:** Variáveis de escopo externo não funcionam corretamente; use maps/listas mutáveis como workaround

**Workaround para closures quebradas:**
```greyscript
// NÃO FUNCIONA em GreyScript
criarContador = function
    contador = 0
    return function
        contador = contador + 1  // ERRO: contador desconhecido
        return contador
    end function
end function

// FUNCIONA: usar objeto mutável
criarContador = function
    estado = {"valor": 0}
    return function
        outer.estado.valor = outer.estado.valor + 1
        return outer.estado.valor
    end function
end function
```

**Evitar chamadas desnecessárias:**
```greyscript
// Ruim: chama host_computer múltiplas vezes
print(get_shell.host_computer.local_ip)
print(get_shell.host_computer.public_ip)

// Bom: armazena referência
pc = get_shell.host_computer
print(pc.local_ip)
print(pc.public_ip)
```

---

## 5. Referência Rápida

### 5.1 Tabela de todos os métodos por objeto

| Objeto | Métodos Principais |
|--------|-------------------|
| **Computer** | `File`, `touch`, `create_folder`, `create_user`, `delete_user`, `change_password`, `groups`, `local_ip`, `public_ip`, `network_gateway`, `is_network_active`, `get_ports`, `show_procs`, `close_program`, `wifi_networks`, `connect_wifi` |
| **File** | `name`, `path`, `parent`, `owner`, `group`, `permissions`, `size`, `is_folder`, `is_binary`, `get_content`, `set_content`, `copy`, `move`, `rename`, `delete`, `chmod`, `has_permission`, `set_owner`, `get_folders`, `get_files` |
| **Shell** | `host_computer`, `start_terminal`, `connect_service`, `ping`, `scp`, `launch`, `build` |
| **Router** | `public_ip`, `local_ip`, `essid_name`, `bssid_name`, `kernel_version`, `used_ports`, `ping_port`, `port_info`, `device_ports`, `devices_lan_ip`, `firewall_rules` |
| **Port** | `port_number`, `is_closed`, `get_lan_ip` |
| **Metaxploit** | `load`, `net_use`, `scan`, `scan_address`, `sniffer`, `rshell_client`, `rshell_server` |
| **MetaLib** | `lib_name`, `version`, `overflow` |
| **NetSession** | `dump_lib`, `get_num_users`, `is_any_active_user`, `is_root_active_user` |
| **Crypto** | `airmon`, `aireplay`, `aircrack`, `decipher`, `smtp_user_list` |
| **AptClient** | `show`, `search`, `update`, `add_repo`, `del_repo`, `install`, `check_upgrade` |

### 5.2 Keywords reservadas

```
if  then  else  else if  end if
while  end while
for  in  end for
function  end function
return  break  continue
and  or  not
true  false  null
new  isa
self  super
globals  locals  outer
```

### 5.3 Cheatsheet de sintaxe

```greyscript
// VARIÁVEIS
x = 42
texto = "string com aspas duplas"
lista = [1, 2, 3]
mapa = {"chave": "valor"}

// FUNÇÕES
minhaFunc = function(param1, param2="padrao")
    return param1 + param2
end function
resultado = minhaFunc(5, 3)  // Parênteses obrigatórios com args
minhaFunc                     // Sem args: parênteses opcionais
ref = @minhaFunc              // Referência sem executar

// CONDICIONAIS (sem parênteses!)
if x > 5 then
    print("grande")
else if x > 0 then
    print("positivo")
else
    print("zero ou negativo")
end if

// LOOPS
while condicao
    // código
end while

for item in colecao
    // código
end for

for i in range(1, 10)
    if i == 5 then break
    if i % 2 == 0 then continue
end for

// VERIFICAÇÃO DE TIPO
typeof(x)           // "number", "string", "list", "map", "null", "shell"...
x isa list          // 1 ou 0

// ESCOPO
globals.varGlobal = 1
outer.varEncapsuladora = 2

// STRINGS
"texto"[0:4]              // Slicing
"a,b,c".split(",")        // ["a", "b", "c"]
"hello".upper             // "HELLO"
"  trim  ".trim           // "trim"
"abc".indexOf("b")        // 1 (ou null se não encontrado)

// LISTAS
lista.push(4)             // Adiciona ao final
lista.pop                 // Remove e retorna último
lista.sort                // Ordena
lista.indexOf(2)          // Índice ou null

// MAPS
mapa["chave"]             // Acesso por chave
mapa.chave                // Sintaxe de ponto
mapa.hasIndex("chave")    // 1 ou 0
mapa.indexes              // Lista de chaves
mapa.values               // Lista de valores

// FUNÇÕES GLOBAIS IMPORTANTES
get_shell                 // Shell atual
get_router                // Roteador local
get_router("ip")          // Roteador por IP
include_lib("/lib/x.so")  // Carrega biblioteca
import_code("/caminho")   // Inclui código fonte
print("texto")            // Imprime
user_input("prompt")      // Entrada do usuário
exit("mensagem")          // Encerra com mensagem

// VARIÁVEIS GLOBAIS DO JOGO
params                    // Lista de argumentos
program_path              // Caminho do programa
current_path              // Diretório atual
home_dir                  // Diretório home
active_user               // Usuário atual
```

### Formatação de texto com TextMesh Pro

```greyscript
print("<b>Negrito</b>")
print("<i>Itálico</i>")
print("<color=red>Vermelho</color>")
print("<color=#FF5500>Cor hex</color>")
print("<size=20>Texto grande</size>")
```

### Fluxo típico de exploit

```greyscript
// 1. Carregar metaxploit
meta = include_lib("/lib/metaxploit.so")
if not meta then exit("metaxploit.so não encontrado")

// 2. Conectar ao alvo
sessao = meta.net_use("IP_ALVO", PORTA)
if not sessao then exit("Conexão falhou")

// 3. Obter biblioteca
lib = sessao.dump_lib
print(lib.lib_name + " v" + lib.version)

// 4. Escanear vulnerabilidades
enderecos = meta.scan(lib)
for addr in enderecos
    info = meta.scan_address(lib, addr)
    print(addr + ": " + info)
end for

// 5. Executar overflow
resultado = lib.overflow("0xENDERECO", "valor_vulneravel")
if typeof(resultado) == "shell" then
    resultado.start_terminal
end if
```

---

> **Nota**: Este guia cobre a API documentada do GreyScript. Alguns comportamentos podem variar entre versões do jogo. Métodos marcados com "Cannot be used in encryption configuration" têm restrições em certos modos de jogo. Sempre verifique valores null antes de chamar métodos em objetos retornados por funções que podem falhar.
