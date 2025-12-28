# WiFi Harvester v1.0 - Tutorial Completo

## Descrição

Script 100% automático para **Grey Hack** que hackeia **TODAS** as redes WiFi disponíveis em sequência e salva as senhas em um único arquivo, sem conectar em nenhuma rede.

---

## Script Completo

```greyscript
// wifi_harvest.src - WiFi Harvester - Coleta TODAS as senhas
// Uso: wifi_harvest (hackeia todas as redes e salva senhas)

clear_screen
print("<b>====== WiFi Harvester v1.0 ======</b>")
print("<b>   Coleta de Senhas em Massa    </b>")
print("")

// Carregar biblioteca crypto
crypto = include_lib("/lib/crypto.so")
if not crypto then
    exit("Erro: /lib/crypto.so nao encontrado!")
end if

computador = get_shell.host_computer

// Caminho do arquivo de senhas
arquivoSenhas = "/home/FredericoKluser/all_wifi_passwords.txt"

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

totalRedes = redesProcessadas.len
print("[*] Total de redes encontradas: " + totalRedes)
print("[*] Iniciando coleta de senhas...")
print("")

// Inicializar conteudo do arquivo
conteudoTotal = "========================================" + char(10)
conteudoTotal = conteudoTotal + "   WiFi Harvester - Senhas Coletadas   " + char(10)
conteudoTotal = conteudoTotal + "   Data: " + current_date + char(10)
conteudoTotal = conteudoTotal + "========================================" + char(10)
conteudoTotal = conteudoTotal + char(10)

// Contadores
sucessos = 0
falhas = 0

// Loop para hackear cada rede
for idx in range(0, redesProcessadas.len)
    alvo = redesProcessadas[idx]
    
    print("--------------------------------------------------")
    print("[" + (idx + 1) + "/" + totalRedes + "] Atacando: " + alvo.essid)
    print("    BSSID: " + alvo.bssid + " | Sinal: " + alvo.pwr + "%")
    
    // Capturar pacotes
    print("    [*] Capturando pacotes...")
    resultado = crypto.aireplay(alvo.bssid, alvo.essid, 10000)
    
    if typeof(resultado) == "string" then
        print("    [!] Aviso: " + resultado)
    end if
    
    // Aguardar file.cap
    wait(2)
    
    // Crackear senha
    print("    [*] Crackeando...")
    capPath = current_path + "/file.cap"
    senha = crypto.aircrack(capPath)
    
    if senha then
        print("    [+] SENHA: " + senha)
        sucessos = sucessos + 1
        
        // Adicionar ao conteudo
        conteudoTotal = conteudoTotal + "----------------------------------------" + char(10)
        conteudoTotal = conteudoTotal + "Rede #" + (idx + 1) + char(10)
        conteudoTotal = conteudoTotal + "ESSID: " + alvo.essid + char(10)
        conteudoTotal = conteudoTotal + "BSSID: " + alvo.bssid + char(10)
        conteudoTotal = conteudoTotal + "SINAL: " + alvo.pwr + "%" + char(10)
        conteudoTotal = conteudoTotal + "SENHA: " + senha + char(10)
        conteudoTotal = conteudoTotal + char(10)
    else
        print("    [-] Falha ao crackear")
        falhas = falhas + 1
    end if
    
    // Limpar file.cap
    capFile = computador.File(capPath)
    if capFile then
        capFile.delete
    end if
    
    wait(0.5)
end for

// Adicionar estatisticas finais
conteudoTotal = conteudoTotal + "========================================" + char(10)
conteudoTotal = conteudoTotal + "ESTATISTICAS:" + char(10)
conteudoTotal = conteudoTotal + "Total de redes: " + totalRedes + char(10)
conteudoTotal = conteudoTotal + "Senhas obtidas: " + sucessos + char(10)
conteudoTotal = conteudoTotal + "Falhas: " + falhas + char(10)
conteudoTotal = conteudoTotal + "========================================" + char(10)

// Desativar modo monitor
print("")
print("--------------------------------------------------")
print("[*] Desativando modo monitor...")
crypto.airmon("stop", "wlan0")

// Salvar arquivo de senhas
print("[*] Salvando senhas em " + arquivoSenhas + "...")

// Verificar se diretorio existe
pastaDestino = "/home/FredericoKluser"
pasta = computador.File(pastaDestino)
if not pasta then
    print("[!] Pasta " + pastaDestino + " nao existe. Criando...")
    computador.create_folder("/home", "FredericoKluser")
end if

// Criar/sobrescrever arquivo
computador.touch(pastaDestino, "all_wifi_passwords.txt")
arquivo = computador.File(arquivoSenhas)
if arquivo then
    arquivo.set_content(conteudoTotal)
    print("[+] Arquivo salvo com sucesso!")
else
    print("[!] Erro ao salvar arquivo. Salvando em home_dir...")
    computador.touch(home_dir, "all_wifi_passwords.txt")
  arquivo = computador.File(home_dir + "/all_wifi_passwords.txt")
    arquivo.set_content(conteudoTotal)
    print("[+] Salvo em: " + home_dir + "/all_wifi_passwords.txt")
end if

// Resumo final
print("")
print("<b>====== COLETA FINALIZADA ======</b>")
print("")
print("[+] Redes escaneadas: " + totalRedes)
print("[+] Senhas obtidas:   " + sucessos)
print("[-] Falhas:           " + falhas)
print("")
print("[+] Senhas salvas em: " + arquivoSenhas)
print("")
```

---

## Tutorial Completo de Instalação

### Passo 1: Criar o arquivo fonte

```bash
touch /home/FredericoKluser wifi_harvest.src
```

### Passo 2: Editar e colar o código

```bash
CodeEditor.exe /home/FredericoKluser/wifi_harvest.src
```

Cole todo o código acima no editor e salve (Ctrl+S ou botão Save).

### Passo 3: Compilar

```bash
build /home/FredericoKluser/wifi_harvest.src /home/FredericoKluser
```

### Passo 4: Mover para /bin (requer root)

```bash
sudo mv /home/FredericoKluser/wifi_harvest /bin/wifi_harvest
```

### Passo 5: Limpar arquivos desnecessários

```bash
rm /home/FredericoKluser/wifi_harvest.src
```

---

## Como Usar

| Comando | Comportamento |
|---------|---------------|
| `wifi_harvest` | Hackeia TODAS as redes WiFi automaticamente e salva as senhas |

---

## Saída Esperada

```
====== WiFi Harvester v1.0 ======
   Coleta de Senhas em Massa    

[*] Ativando modo monitor...
[*] Escaneando redes WiFi...

NUM BSSID              PWR ESSID
--------------------------------------------------
[0] C7:E7:C8:43:9A:E7  76% RedeVizinho
[1] 3F:65:29:C5:83:AB  54% NET_WIFI
[2] FE:50:51:7F:61:47  32% VIVO-1234

[*] Total de redes encontradas: 3
[*] Iniciando coleta de senhas...

--------------------------------------------------
[1/3] Atacando: RedeVizinho
    BSSID: C7:E7:C8:43:9A:E7 | Sinal: 76%
    [*] Capturando pacotes...
    [*] Crackeando...
    [+] SENHA: nius
--------------------------------------------------
[2/3] Atacando: NET_WIFI
    BSSID: 3F:65:29:C5:83:AB | Sinal: 54%
    [*] Capturando pacotes...
    [*] Crackeando...
    [+] SENHA: wifi2024
--------------------------------------------------
[3/3] Atacando: VIVO-1234
    BSSID: FE:50:51:7F:61:47 | Sinal: 32%
    [*] Capturando pacotes...
    [*] Crackeando...
    [-] Falha ao crackear

--------------------------------------------------
[*] Desativando modo monitor...
[*] Salvando senhas em /home/FredericoKluser/all_wifi_passwords.txt...
[+] Arquivo salvo com sucesso!

====== COLETA FINALIZADA ======

[+] Redes escaneadas: 3
[+] Senhas obtidas:   2
[-] Falhas:           1

[+] Senhas salvas em: /home/FredericoKluser/all_wifi_passwords.txt
```

---

## Formato do Arquivo all_wifi_passwords.txt

```
========================================
   WiFi Harvester - Senhas Coletadas   
   Data: 28/12/2025 14:32:15
========================================

----------------------------------------
Rede #1
ESSID: RedeVizinho
BSSID: C7:E7:C8:43:9A:E7
SINAL: 76%
SENHA: nius

----------------------------------------
Rede #2
ESSID: NET_WIFI
BSSID: 3F:65:29:C5:83:AB
SINAL: 54%
SENHA: wifi2024

========================================
ESTATISTICAS:
Total de redes: 3
Senhas obtidas: 2
Falhas: 1
========================================
```

---

## Diferenças do Script Original (wifi_auto)

| Característica | wifi_auto | wifi_harvest |
|----------------|-----------|--------------|
| Redes hackeadas | 1 (melhor sinal ou manual) | TODAS |
| Conexão WiFi | Sim, conecta automaticamente | Não, apenas coleta |
| Arquivo de saída | Sobrescreve a cada uso | Registra todas as redes |
| Estatísticas | Não | Sim (sucessos/falhas) |
| Progresso | Não | Sim (X/Total) |

---

## Resumo dos Comandos

```bash
# Instalação completa (copie e cole linha por linha)
touch /home/FredericoKluser wifi_harvest.src
CodeEditor.exe /home/FredericoKluser/wifi_harvest.src
# (cole o código e salve)
build /home/FredericoKluser/wifi_harvest.src /home/FredericoKluser
sudo mv /home/FredericoKluser/wifi_harvest /bin/wifi_harvest
rm /home/FredericoKluser/wifi_harvest.src

# Uso
wifi_harvest

# Ver senhas coletadas
cat /home/FredericoKluser/all_wifi_passwords.txt
```

---

## Requisitos

- `/lib/crypto.so` instalado no sistema
- Interface wireless `wlan0` disponível
- Permissões para ativar modo monitor

---

## Dicas

1. **Execute em locais com muitas redes** para coletar mais senhas
2. **O arquivo é sobrescrito** a cada execução - faça backup se necessário
3. **Redes com sinal fraco** podem falhar no crack
4. **Use `cat`** para visualizar as senhas coletadas rapidamente
