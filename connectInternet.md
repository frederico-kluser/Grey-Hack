Entendi! Você quer um script **100% automático** sem perguntas, mas que ainda tenha a opção `manual` para escolher a rede. Aqui está tudo completo:

---

## Script Totalmente Automatizado

```greyscript
// wifi_auto.src - WiFi Hacker 100% Automatizado
// Uso: wifi_auto (automatico) | wifi_auto manual (escolher rede)

clear_screen
print("<b>====== WiFi Auto Hacker v2.0 ======</b>")
print("")

// Carregar biblioteca crypto
crypto = include_lib("/lib/crypto.so")
if not crypto then
    exit("Erro: /lib/crypto.so nao encontrado!")
end if

computador = get_shell.host_computer

// Ativar modo monitor
print("[*] Ativando modo monitor...")
crypto.airmon("start", "wlan0")
wait(0.5)

// Obter lista de redes WiFi
print("[*] Escaneando redes WiFi...")
print("")
networks = computador.wifi_networks("wlan0")

if networks.len == 0 then
    crypto.airmon("stop", "wlan0")
    exit("Erro: Nenhuma rede WiFi encontrada!")
end if

// Processar redes em estrutura organizada
redesProcessadas = []
for net in networks
    dados = net.split(" ")
    if dados.len >= 3 then
        sinal = dados[1].replace("%", "").to_int
        rede = {"bssid": dados[0], "pwr": sinal, "essid": dados[2]}
        redesProcessadas.push(rede)
    end if
end for

// Ordenar por sinal (maior primeiro)
redesProcessadas.sort("pwr")
redesProcessadas.reverse

// Mostrar redes encontradas
print(format_columns("NUM BSSID PWR ESSID"))
print("--------------------------------------------------")
i = 0
for rede in redesProcessadas
    linha = "[" + i + "] " + rede.bssid + " " + rede.pwr + "% " + rede.essid
    print(linha)
    i = i + 1
end for
print("")

// Verificar modo manual ou automatico
modoManual = false
if params.len > 0 then
    if params[0] == "manual" then
        modoManual = true
    end if
end if

// Selecionar alvo
alvo = null
if modoManual then
    escolha = user_input("[+] Selecione o numero da rede: ").to_int
    if escolha >= 0 and escolha < redesProcessadas.len then
        alvo = redesProcessadas[escolha]
    else
        crypto.airmon("stop", "wlan0")
        exit("Erro: Selecao invalida!")
    end if
else
    alvo = redesProcessadas[0]
    print("[*] Modo automatico: selecionando melhor sinal")
end if

print("[*] Alvo: " + alvo.essid + " (" + alvo.pwr + "%) - " + alvo.bssid)
print("")

// Capturar pacotes
print("[*] Capturando pacotes (10000 ACKs)...")
resultado = crypto.aireplay(alvo.bssid, alvo.essid, 10000)

if typeof(resultado) == "string" then
    print("[!] Aviso: " + resultado)
end if

// Aguardar file.cap
print("[*] Aguardando gravacao do file.cap...")
wait(3)

// Crackear senha
print("[*] Crackeando senha...")
capPath = current_path + "/file.cap"
senha = crypto.aircrack(capPath)

if not senha then
    crypto.airmon("stop", "wlan0")
    exit("Erro: Nao foi possivel crackear. Tente novamente.")
end if

print("")
print("[+] SENHA ENCONTRADA: " + senha)
print("")

// Desativar modo monitor
print("[*] Desativando modo monitor...")
crypto.airmon("stop", "wlan0")
wait(0.5)

// Conectar automaticamente
print("[*] Conectando a rede...")
res = computador.connect_wifi("wlan0", alvo.bssid, alvo.essid, senha)
if res == 1 then
    print("[+] Conectado com sucesso!")
else
    print("[!] Falha na conexao: " + res)
end if

// Salvar credenciais automaticamente
print("[*] Salvando credenciais...")
conteudo = "ESSID: " + alvo.essid + char(10)
conteudo = conteudo + "BSSID: " + alvo.bssid + char(10)
conteudo = conteudo + "SENHA: " + senha + char(10)
conteudo = conteudo + "DATA: " + current_date
computador.touch(home_dir, "wifi_password.txt")
arquivo = computador.File(home_dir + "/wifi_password.txt")
arquivo.set_content(conteudo)
print("[+] Salvo em: " + home_dir + "/wifi_password.txt")

// Limpar file.cap automaticamente
print("[*] Limpando rastros...")
capFile = computador.File(capPath)
if capFile then
    capFile.delete
    print("[+] file.cap removido")
end if

print("")
print("[+] CONCLUIDO! Voce esta conectado a " + alvo.essid)
```

---

## Tutorial Completo de Instalação

### Passo 1: Criar o arquivo fonte

```bash
touch /home/guest wifi_auto.src
```

### Passo 2: Editar e colar o código

```bash
CodeEditor.exe /home/guest/wifi_auto.src
```

Cole todo o código acima no editor e salve (Ctrl+S ou botão Save).

### Passo 3: Compilar

```bash
build /home/guest/wifi_auto.src /home/guest
```

### Passo 4: Mover para /bin (requer root)

```bash
sudo mv /home/guest/wifi_auto /bin/wifi_auto
```

### Passo 5: Limpar arquivos desnecessários

```bash
rm /home/guest/wifi_auto.src
```

---

## Como Usar

| Comando | Comportamento |
|---------|---------------|
| `wifi_auto` | 100% automático - seleciona melhor rede, hackeia, conecta, salva, limpa |
| `wifi_auto manual` | Mostra lista e pergunta qual rede você quer atacar |

---

## Saída Esperada (Modo Automático)

```
====== WiFi Auto Hacker v2.0 ======

[*] Ativando modo monitor...
[*] Escaneando redes WiFi...

NUM BSSID              PWR ESSID
--------------------------------------------------
[0] C7:E7:C8:43:9A:E7  76% RedeVizinho
[1] 3F:65:29:C5:83:AB  54% NET_WIFI
[2] FE:50:51:7F:61:47  32% VIVO-1234

[*] Modo automatico: selecionando melhor sinal
[*] Alvo: RedeVizinho (76%) - C7:E7:C8:43:9A:E7

[*] Capturando pacotes (10000 ACKs)...
Read 19143 packets (got 10067 ACKs)
[*] Aguardando gravacao do file.cap...
[*] Crackeando senha...

[+] SENHA ENCONTRADA: nius

[*] Desativando modo monitor...
[*] Conectando a rede...
[+] Conectado com sucesso!
[*] Salvando credenciais...
[+] Salvo em: /home/guest/wifi_password.txt
[*] Limpando rastros...
[+] file.cap removido

[+] CONCLUIDO! Voce esta conectado a RedeVizinho
```

---

## Resumo dos Comandos

```bash
# Instalação completa (copie e cole linha por linha)
touch /home/guest wifi_auto.src
CodeEditor.exe /home/guest/wifi_auto.src
# (cole o código e salve)
build /home/guest/wifi_auto.src /home/guest
sudo mv /home/guest/wifi_auto /bin/wifi_auto
rm /home/guest/wifi_auto.src

# Uso
wifi_auto          # automatico
wifi_auto manual   # escolher rede
```
