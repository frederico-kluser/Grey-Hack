# Guia definitivo da Hack Shop em Grey Hack

A Hack Shop é a loja secreta onde jogadores compram exploits e ferramentas essenciais para progredir no Grey Hack. **O segredo para compras inteligentes está em dominar a aba de Exploits** — muitos iniciantes pensam que a loja está vazia porque procuram apenas na aba de ferramentas. Este guia decodifica cada produto, explica a terminologia técnica das descrições, e oferece uma estratégia de compra otimizada para cada fase do jogo.

---

## Referência rápida: prioridades de compra

Antes de qualquer missão, adquira estas ferramentas na ordem indicada. Novos jogadores recebem **10 cupons gratuitos** que podem ser usados para obter software sem custo — use-os estrategicamente.

| Ferramenta | Destino | Prioridade | Motivo |
|------------|---------|------------|--------|
| metaxploit.so | /lib | Crítica | Todos os exploits dependem desta biblioteca |
| crypto.so | /lib | Crítica | Necessária para quebra de senhas |
| decipher | /bin | Crítica | Decifra senhas de Bank.txt, passwd, Mail.txt |
| nmap (loja normal) | /bin | Crítica | Identifica portas abertas e versões de serviços |
| AdminMonitor.exe | /usr/bin | Alta | Alerta quando o admin NPC detecta você |
| scanrouter | /bin | Alta | Revela versão do kernel_router |

**Regra de ouro**: nunca compre exploits antes de verificar as versões com nmap e scanrouter. Comprar exploits incompatíveis desperdiça dinheiro.

---

## Como funciona a Hack Shop

### Acesso à loja secreta

A Hack Shop não aparece em buscas do navegador do jogo. Para acessá-la, você precisa completar a missão tutorial, que envolve criar uma conta de email, hackear um alvo simples e decifrar uma senha. Após completar essa missão, você recebe um email contendo o **endereço IP da Hack Shop**. Digite esse IP diretamente na barra de endereços do navegador.

O IP é único e sensível — em servidores multiplayer, compartilhá-lo no chat geral expõe você a ataques de outros jogadores. Recomenda-se anotar o IP fora do jogo para referência futura. Se perder o IP no modo single player, vá em Settings.exe, resete o tutorial e reconecte ao WiFi para receber o email novamente.

### Sistema de moeda e métodos para ganhar dinheiro

O jogo usa um sistema bancário simulado onde transações passam por contas virtuais. O método mais lucrativo é **roubar arquivos Bank.txt** de computadores NPC (geralmente em /home/usuario/Config/Bank.txt) e usar o decipher para decifrar as credenciais. NPCs recebem pagamentos mensais no jogo, então contas salvas continuam gerando renda passiva.

Missões Black Hat da própria Hack Shop (aba Jobs) também pagam, com valores variando conforme a dificuldade e requisitos de reputação. Missões de Reputação 0 sempre têm portas encaminhadas no roteador, facilitando o acesso — ideais para iniciantes.

### Interface e navegação

A Hack Shop possui três abas principais que funcionam de formas distintas.

A **aba Tools** lista ferramentas executáveis e bibliotecas prontas para download. Aqui você encontra metaxploit.so, crypto.so, decipher, AdminMonitor.exe, scanrouter, sniffer, e as ferramentas rshell.

A **aba Exploits** opera por busca — ela parece vazia inicialmente porque você precisa selecionar um tipo de biblioteca no dropdown, inserir um número de versão específico, e clicar em Search. Só então os exploits disponíveis para aquela versão aparecem.

A **aba Jobs** lista missões Black Hat disponíveis, organizadas por requisitos de reputação. Missões Rep 0 são mais fáceis; Rep 1+ podem exigir hacking de roteadores.

### Ciclos de atualização e disponibilidade

No modo single player, após criar um novo mundo, aguarde aproximadamente 10 minutos para a loja gerar seu inventário inicial. Novos exploits surgem aproximadamente a cada 2 minutos.

O sistema de versões funciona assim: quando você usa ExploitReport.exe para reportar uma vulnerabilidade, a biblioteca afetada incrementa sua versão e gera um novo conjunto aleatório de exploits. No multiplayer, isso acontece mais rápido porque todos os jogadores influenciam o sistema. **No single player, evite reportar vulnerabilidades** — isso torna o jogo mais difícil, pois você precisará encontrar novos exploits para versões atualizadas.

---

## Catálogo completo de produtos

### Exploits remotos: atacando serviços de rede

Exploits remotos atacam serviços específicos rodando em portas abertas. Use nmap para identificar quais serviços o alvo possui antes de comprar.

| Biblioteca | Serviço alvo | Porta padrão | Quando usar |
|------------|--------------|--------------|-------------|
| libssh.so | SSH | 22 | Servidores com acesso SSH |
| libftp.so | FTP | 21 | Servidores de arquivos |
| libhttp.so | HTTP | 80 | Servidores web |
| libsmtp.so | SMTP | 25 | Servidores de email |
| libchat.so | Chat | 6667 | Salas de chat |
| librshell.so | Rshell | 1222 | Backdoors instalados |
| librepository.so | Repositório | 1542 | Servidores apt |
| libsql.so | SQL/Portas fechadas | Variável | Bancos de dados |
| libcam.so | Câmeras | Variável | Sistemas de segurança |

### Exploits locais: escalando privilégios

Após obter acesso inicial (geralmente como usuário comum), exploits locais permitem escalar para root. Estas bibliotecas ficam em /lib no sistema alvo.

| Biblioteca | Função |
|------------|--------|
| kernel_module.so | Principal método de escalação para root |
| init.so | Exploits do sistema de inicialização |
| net.so | Exploits do subsistema de rede |

### Exploits de roteador: a chave para redes protegidas

Quando o alvo não tem portas abertas, **kernel_router** é sua única opção. Roteadores controlam quais portas são encaminhadas para a LAN interna. Hackeando o roteador, você consegue acessar dispositivos que de outra forma seriam inacessíveis.

Use scanrouter para descobrir a versão do kernel_router antes de comprar o exploit correspondente.

### Ferramentas essenciais explicadas

**metaxploit.so** é a biblioteca núcleo que todos os exploits requerem para funcionar. Sem ela instalada em /lib, nenhum exploit roda. Mantenha-a sempre atualizada — quando bibliotecas são patcheadas, metaxploit.so também atualiza. Use `apt-get upgrade` periodicamente ou baixe novamente da Hack Shop.

**crypto.so** é necessária para qualquer operação de quebra de senhas. O programa decipher depende dela para funcionar.

**AdminMonitor.exe** monitora quando o administrador NPC do sistema que você está hackeando faz login ou inicia um trace contra você. Execute-o em segundo plano sempre que estiver hackeando — ele pode salvar sua operação.

**scanrouter** revela a versão do kernel_router de um IP específico, permitindo que você procure o exploit correto na Hack Shop.

**sniffer** captura senhas que trafegam pelo roteador ao qual você tem acesso. Ferramenta passiva para coleta de credenciais ao longo do tempo.

**rshell-server e rshell-interface** criam backdoors persistentes. Instale o servidor em uma máquina alugada, envie o cliente para vítimas via engenharia social, e mantenha acesso mesmo após elas "consertarem" vulnerabilidades.

---

## Decodificando descrições de exploits

As descrições dos exploits revelam exatamente o que você obterá — se souber interpretá-las. Esta seção traduz a terminologia técnica para linguagem prática.

### Tipos de resultado: o que o exploit retorna

A frase mais importante para identificar é **"Get access to a shell"** — somente exploits com essa descrição permitem que você conecte ao sistema alvo. Outras frases indicam funcionalidades diferentes.

| Texto na descrição | Significado prático |
|--------------------|---------------------|
| "Get access to a shell" | Você pode conectar ao sistema — é isso que você quer |
| "Get access to a root shell" | Acesso total como administrador — melhor resultado possível |
| "Get access to a non-root shell" | Acesso como usuário comum — precisa escalar privilégios depois |
| "Get access to a guest shell" | Acesso muito limitado — raro em exploits atuais |
| "Change password" | Modifica senha de usuário — requer parâmetro extra |
| "Get info" | Apenas revela informações — NÃO permite conexão |
| "Get access to a file" | Retorna objeto de arquivo específico |

### Requisitos dos exploits

Cada exploit pode ter condições que devem ser satisfeitas para funcionar. Quando você escaneia vulnerabilidades, verá linhas começando com asterisco indicando esses requisitos.

**"Checking an active user"** significa que um usuário não-root precisa estar logado no sistema alvo. Use engenharia social com o assunto "Online user" para fazer um NPC logar.

**"Checking root active user"** exige que o root esteja logado. Use engenharia social com "Admin online" para simular isso.

**"Checking registered users equal to X"** verifica se o sistema tem X ou mais contas de usuário. A maioria dos sistemas satisfaz esse requisito naturalmente.

**"Checking forwarded ports equal to X"** requer que o roteador tenha X portas encaminhadas. Missões Rep 0 sempre têm portas encaminhadas; Rep 1+ nem sempre.

### Ranking de dificuldade de requisitos

Exploits sem requisitos são os mais fáceis de usar. Requisitos de "registered users" geralmente são satisfeitos automaticamente. Requisitos de "active user" exigem engenharia social básica. Requisitos de "root active user" são os mais difíceis. Requisitos de "forwarded ports" dependem da configuração do roteador — se não forem satisfeitos, você precisará hackear o roteador primeiro.

### Sistema de versões

Versões seguem o formato X.Y.Z (por exemplo, 1.0.0, 1.2.6). **Exploits são específicos para versão** — um exploit para libssh 1.0.0 não funciona em libssh 1.0.1. Sempre verifique a versão exata com nmap antes de comprar.

Versões mais altas geralmente têm conjuntos diferentes de vulnerabilidades. Quando bibliotecas são patcheadas (via reports ou uso intenso no multiplayer), novas versões surgem com novos exploits.

---

## Guia estratégico por fase do jogo

### Fase inicial: fundação

Imediatamente após o tutorial, use seus cupons gratuitos para obter metaxploit.so, crypto.so, e decipher. Baixe nmap da loja normal (é gratuito). Pegue AdminMonitor.exe e scanrouter da Hack Shop.

Com essas ferramentas, você pode aceitar missões Rep 0 de "Credentials Needed". O fluxo básico é: nmap no alvo para ver portas abertas, identificar a versão do serviço, buscar exploit correspondente na Hack Shop, executar o exploit, usar decipher nos arquivos de senha encontrados.

**Dica de economia**: missões de credenciais podem ser completadas apenas com engenharia social, sem gastar em exploits. Envie emails de phishing solicitando informações — alguns NPCs respondem.

### Fase intermediária: expandindo capacidades

Após completar algumas missões Rep 0, você terá dinheiro para investir em exploits mais versáteis. Priorize **kernel_router** — ele desbloqueia acesso a dispositivos em LANs protegidas, essencial para missões Rep 1+.

Compre um exploit de cada tipo principal de biblioteca (libssh, libhttp, libftp) nas versões mais comuns. A estratégia avançada é comprar a **versão em código-fonte** — você pode estudar como o exploit funciona, modificá-lo, e adaptar para outras situações sem comprar múltiplos exploits similares.

Considere alugar um servidor em sites de ISP do jogo. Servidores alugados escondem seu IP real — o pacote pequeno de 3MB/s é suficiente para a maioria das operações.

### Fase avançada: otimização e automação

Jogadores experientes migram para scripts customizados da comunidade. O GitHub tem ferramentas como **5hell**, **Rocshell**, e **EZshell** que automatizam grande parte do processo de hacking. Estas ferramentas substituem a necessidade de comprar múltiplos exploits.

Invista em **upgrade de CPU** — processadores mais rápidos aceleram quebra de senhas e escaneamento. Considere o **sniffer** para coleta passiva de credenciais em roteadores que você controla.

As ferramentas **rshell** são úteis para engenharia social avançada. Você instala o servidor em sua máquina alugada, envia o cliente como "atualização de segurança" para alvos, e mantém acesso persistente.

---

## Erros comuns e como evitá-los

### A loja parece estar vazia

O erro mais reportado por iniciantes. A aba Tools tem poucas opções porque as ferramentas principais estão lá. Para ver exploits, você **precisa usar a aba Exploits** e fazer uma busca especificando tipo de biblioteca e versão.

### Comprar exploits com versão errada

Jogadores ansiosos compram exploits antes de verificar as versões do alvo. O resultado é dinheiro desperdiçado em exploits inutilizáveis. **Sempre execute nmap primeiro** — a saída mostra a versão exata de cada serviço. Para roteadores, use scanrouter.

### Ignorar requisitos dos exploits

Um exploit que requer "root active user" falhará silenciosamente se o root não estiver logado. Leia as descrições completas e planeje como satisfazer cada requisito antes de comprar.

### Comprar múltiplos exploits do mesmo tipo

Exploits similares fazem coisas parecidas. A comunidade recomenda comprar **apenas um exploit por tipo de biblioteca**, estudar seu código-fonte, e adaptar para outras situações. Isso economiza dinheiro e ensina programação GreyScript.

### Reportar vulnerabilidades no single player

Usar ExploitReport.exe no modo single player atualiza as bibliotecas, tornando seus exploits atuais obsoletos. Você precisará comprar novos exploits para as novas versões. No multiplayer isso acontece naturalmente; no single player, evite reportar a menos que precise de exploits específicos que não existem nas versões atuais.

### Não manter bibliotecas atualizadas

metaxploit.so e crypto.so precisam estar sincronizadas com as versões do sistema. Periodicamente execute `apt-get update` seguido de `apt-get upgrade`, ou baixe novamente da Hack Shop.

---

## Glossário técnico completo

### Termos de resultado

| Termo | Significado |
|-------|-------------|
| shell | Acesso ao terminal do sistema — permite executar comandos |
| root shell | Terminal com privilégios de administrador — acesso total |
| non-root shell | Terminal como usuário comum — acesso limitado |
| guest shell | Terminal com mínimos privilégios — muito restrito |
| computer object | Referência ao computador para manipulação de arquivos |
| file object | Referência a arquivo específico |

### Termos de vulnerabilidade

| Termo | Significado |
|-------|-------------|
| memory address (0x1DD41E37) | Endereço hexadecimal onde a vulnerabilidade existe |
| buffer overflow | Tipo de ataque que explora limites de memória |
| unsecure value | Nome do valor vulnerável usado na função overflow |
| lib.overflow() | Função GreyScript que executa o exploit |

### Termos de requisitos

| Termo | Significado |
|-------|-------------|
| active user | Usuário não-root logado no sistema |
| root active user | Administrador logado no sistema |
| registered users | Total de contas de usuário no sistema |
| forwarded ports | Portas encaminhadas do roteador para o dispositivo |
| local connection | Deve estar na mesma rede do alvo |

### Termos de biblioteca

| Termo | Significado |
|-------|-------------|
| lib | Biblioteca de software (arquivo .so) |
| remote exploit | Ataca serviços de rede em portas abertas |
| local exploit | Escala privilégios após acesso inicial |
| kernel_router | Biblioteca específica para roteadores |

---

## Dicas avançadas da comunidade

### Sinergias entre compras

A combinação **exploit de roteador + exploit local** é fundamental para missões difíceis. Primeiro você hackeia o roteador para acessar a LAN, depois usa exploits locais para escalar privilégios no alvo final.

**Sniffer + acesso ao roteador** permite coleta passiva de senhas. Configure o sniffer em um roteador comprometido e volte periodicamente para colher credenciais.

### Quando pular uma missão

Se o alvo não tem portas abertas, você não tem exploit para a versão do roteador, e não há outros vetores de ataque, a comunidade recomenda simplesmente pular para outra missão. Nem toda missão é completável com seu arsenal atual.

### Maximizando retorno de missões

Além do pagamento da missão, sempre procure arquivos Bank.txt nos computadores que você hackeia. NPCs recebem pagamentos mensais, então contas bancárias roubadas geram renda passiva contínua — frequentemente mais valiosa que o pagamento da missão.

### Ferramentas da comunidade

Antes de comprar ferramentas pagas, verifique o GitHub por alternativas gratuitas. **5hell** é atualmente a ferramenta mais recomendada pela comunidade, substituindo o antigo Viper que foi descontinuado. Compilar estas ferramentas requer conhecimento de GreyScript, mas elimina a necessidade de comprar múltiplos exploits.

---

## Conclusão

O domínio da Hack Shop em Grey Hack depende de três pilares: entender que exploits são específicos por versão e devem ser pesquisados ativamente na aba Exploits; saber interpretar descrições para identificar quais exploits realmente permitem conexão; e seguir uma progressão estratégica de compras que maximiza valor por cada unidade de moeda gasta.

A armadilha principal para novatos é a compra impulsiva de exploits sem verificação prévia de versões. A solução é simples: sempre execute nmap e scanrouter antes de abrir a Hack Shop. Com essa disciplina básica, você economiza recursos e acelera significativamente sua progressão no jogo.

Para jogadores avançados, o caminho natural é migrar de exploits comprados para scripts customizados. A compra de código-fonte (ao invés de versões compiladas) facilita esse aprendizado, transformando cada exploit em uma aula de GreyScript que eventualmente elimina a dependência da Hack Shop.
