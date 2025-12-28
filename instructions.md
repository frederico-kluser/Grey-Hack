# Instruções iniciais

#### instruções iniciais:

- conectar wifi
- criar email
- criar conta banco

#### shop:

- nmap

#### hackshop:

- AdminMonitor.exe
- metaxploit.so
- scanrouter

#### internet:

- ConfigLan.exe

## Script decipher simples (user kluser)

1. Criar/editar o fonte: `CodeEditor.exe /home/kluser/decipher.src` e colar o conteúdo atual de decipher.src.
2. Compilar para o diretório atual: `build /home/kluser/decipher.src /home/kluser`.
3. Executar informando o hash direto: `/home/kluser/decipher <hash>`.
4. Ou rodar sem argumentos para digitar o hash quando solicitado: `/home/kluser/decipher` ⇒ inserir o valor quando o
   prompt aparecer.
5. O resultado descriptografado é exibido entre os logs `[RESULTADO] ...`; em caso de erro, revisar se `/lib/crypto.so`
   está presente.

## Script wifi-hack manual (user kluser)

1. Criar/editar o fonte: `CodeEditor.exe /home/kluser/wifi-hack.src` e colar o conteúdo atual de wifi-hack.src.
2. Compilar no diretório atual: `build /home/kluser/wifi-hack.src /home/kluser`.
3. Executar para listar e escolher a rede: `/home/kluser/wifi-hack` ⇒ digite o índice exibido para iniciar o ataque.
4. Aguarde a captura/aircrack; a senha aparece como `[SENHA] ...` ao final.
5. Caso nenhuma rede apareça ou o crack falhe, revise o modo monitor e tente novamente.
