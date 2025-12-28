# Grey Hack: Guia Completo de Sobrevivência e Evasão

**Sobreviver em Grey Hack exige dominar uma regra fundamental: nunca deixe rastros que possam ser rastreados até você.** O sistema de detecção do jogo opera em dois níveis — rastreamento ativo (quando o admin está online e inicia uma busca em tempo real) e rastreamento passivo (quando o admin descobre logs suspeitos posteriormente). Sem proxy, você tem aproximadamente **100 segundos** antes do game over quando um trace ativo começa. Com bounce chains de 5+ servidores e limpeza adequada de logs, o rastreamento se torna praticamente impossível. A chave é sempre executar o AdminMonitor.exe, verificar se há admins ativos com o comando `ps`, deletar apenas as entradas suspeitas do log (nunca o arquivo inteiro), e usar servidores alugados como proxies. Este guia documenta cada mecânica, comando e procedimento necessário para hackear sem ser pego.

---

## Seção 1: Tabela de referência de risco por comando

| Comando/Ação | Nível de Risco | Condição de Trigger | Janela de Tempo | Contramedida |
|--------------|----------------|---------------------|-----------------|--------------|
| `nmap <IP>` | BAIXO | Nenhum log criado | N/A | Nenhuma necessária |
| `metaxploit.scan()` | BAIXO | Nenhum log criado | N/A | Nenhuma necessária |
| `ps` | BAIXO | Nenhum log criado | N/A | Nenhuma necessária |
| `ls`, `cat`, `cd` | BAIXO | Operações read-only | N/A | Nenhuma necessária |
| WiFi cracking (airmon/aireplay/aircrack) | BAIXO | Operação local apenas | N/A | Deletar file.cap após uso |
| `scp -d` (download) | BAIXO-MÉDIO | Pode criar log no alvo | N/A | Verificar logs do alvo |
| Conexão via proxy | MÉDIO | Cria "connection routed" | Até admin revisar | Deletar entrada + limpar proxy |
| `rm <arquivo>` | ALTO | Cria "file deleted" no log | Até admin revisar | Deletar entrada do log |
| Exploit com shell | ALTO | Cria "shell obtained" no log | Até admin revisar | **Sempre deletar esta entrada** |
| Deletar TODO o log | ALTO | Deixa apenas "disconnected" (suspeito) | Trace passivo garantido | Deletar apenas entradas específicas |
| `shell.start_terminal` (admin online) | CRÍTICO | Inicia trace ativo imediato | ~100 segundos | Sair imediatamente |
| Transferência bancária não autorizada (v0.8.5+) | CRÍTICO | Trace bancário automático | Automático | Impossível evitar completamente |

---

## Seção 2: Mecânicas de rastreamento em profundidade

### Como o sistema de logs funciona

O arquivo `/var/system.log` é o coração do sistema de detecção. Cada ação significativa no sistema gera uma entrada neste arquivo, e admins NPCs verificam estas entradas quando iniciam seu turno de trabalho. O arquivo só pode ser editado com a ferramenta **LogViewer.exe** e requer **acesso root**.

**Tipos de entrada que exigem deleção:**
- **"Shell obtained"** — Prova definitiva de acesso não autorizado. Esta é a entrada mais crítica para remover.
- **"Connection routed"** ou **"IP redirected"** — Indica uso de proxies e permite rastrear a cadeia de bounce.
- **"File deleted"** — Demonstra atividade maliciosa no sistema.

**Entradas que devem permanecer:**
- **"Connection established"** — Tráfego normal de rede.
- **"Connection closed"** / **"IP disconnected"** — Criada automaticamente quando você desconecta.

A razão para manter as entradas de conexão é crítica: se o admin encontrar um log contendo **apenas** uma entrada de desconexão sem a entrada de conexão correspondente, isso é altamente suspeito e automaticamente dispara um trace passivo. O sistema detecta esta inconsistência através de uma verificação simples: se o arquivo existe, tem conteúdo, e contém apenas uma ação de "close" sem "connect" correspondente, o trace passivo é iniciado.

### Os dois tipos de rastreamento

**Trace Ativo** ocorre quando um administrador está logado no sistema que você está hackeando. Se você executar `shell.start_terminal` em uma máquina onde o processo `dsession` do root está ativo, o admin pode iniciar um rastreamento em tempo real. Sem proteção de proxy, você tem aproximadamente **100 segundos** até o game over. Com VPN (como Abuseware), esse tempo estende para **várias horas**. Com uma cadeia de 5+ servidores bounce, o trace se torna praticamente impossível se você limpar os logs corretamente.

**Trace Passivo** começa quando o admin descobre evidências depois do fato. Isso acontece quando: (1) um trace ativo falha porque você escapou, (2) o admin faz login posteriormente e encontra logs suspeitos, ou (3) você deixou apenas uma entrada de desconexão no log. O trace passivo pode levar ao game over mesmo quando você não está conectado ao multiplayer.

### Sistema de bounce chains

Bounce chains funcionam conectando-se através de múltiplos servidores intermediários antes de alcançar o alvo. Cada conexão cria entradas de "IP redirected" nos logs, mas seu IP real é substituído pelo IP do último proxy na cadeia. Para o alvo, apenas o IP do hop imediatamente anterior é visível.

**Para quebrar a cadeia**, você precisa deletar as entradas "IP redirected" ou corromper o `/var/system.log` em **qualquer** máquina da cadeia. Se o link for quebrado em qualquer ponto, o admin não consegue rastrear de volta até você. A estratégia recomendada é limpar os logs de trás para frente enquanto desconecta de cada hop.

### Comportamento dos admins NPCs

Administradores NPCs seguem horários de trabalho — tipicamente de segunda a sexta-feira, com turnos matutinos e noturnos (nem todos os computadores têm trabalhadores noturnos). **O melhor momento para hackear é fora do horário de trabalho do admin.** Finais de semana são primariamente para atividades de lazer e sono dos NPCs. Quando um admin inicia seu turno, ele restaura servidores NPC e verifica logs em busca de atividade suspeita.

---

## Seção 3: Checklist pré-hack

Execute esta sequência antes de cada sessão de hacking:

**Fase 1 — Configuração de infraestrutura**
1. Alugue pelo menos **1-2 servidores** como proxies bounce (mais servidores = mais tempo de trace)
2. Faça upload de suas ferramentas para os servidores alugados (metaxploit.so, crypto.so, decipher, etc.)
3. Certifique-se de ter o **AdminMonitor.exe** instalado (disponível na Hack Shop → Tools)

**Fase 2 — Inicialização da sessão**
4. Execute `AdminMonitor.exe` em background **antes** de conectar a qualquer sistema remoto
5. Conecte ao seu primeiro servidor proxy
6. Inicie terminal no proxy de endpoint: `shell.start_terminal` — isso garante que o IP do proxy aparece nos logs do alvo, não o seu

**Fase 3 — Reconhecimento do alvo**
7. Execute `nmap <IP_ALVO>` para identificar portas abertas e versões de bibliotecas
8. Após obter shell no alvo, **imediatamente** execute `ps` para verificar processos ativos
9. Procure por `dsession` ao lado do usuário root — se presente, admin está ativo

**Fase 4 — Verificação de segurança**
10. Se AdminMonitor mostrar aviso ou dsession do root estiver ativo, **aborte ou proceda com extrema cautela**
11. Prepare mentalmente a rota de escape através de cada hop da sua cadeia

---

## Seção 4: Resposta de emergência — "Fui detectado, e agora?"

### Fluxograma de decisão

```
AdminMonitor mostra aviso?
    │
    ├── SIM → Quanto tempo resta no countdown?
    │         │
    │         ├── >60 segundos → LIMPAR LOGS E SAIR
    │         │   1. cd /var
    │         │   2. LogViewer.exe system.log
    │         │   3. Delete "shell obtained" (PRIORIDADE 1)
    │         │   4. Delete "connection routed" (PRIORIDADE 2)
    │         │   5. exit
    │         │   6. Repetir limpeza em cada proxy da cadeia
    │         │
    │         └── <60 segundos → ABANDONO IMEDIATO
    │             1. exit (desconectar imediatamente)
    │             2. Limpar apenas o primeiro proxy se possível
    │             3. Trocar IP (conectar a novo WiFi ou crackear novo roteador)
    │
    └── NÃO → Verifique `ps` - dsession do root existe?
              │
              ├── SIM → Admin está logado mas não iniciou trace ainda
              │         - Complete tarefa rapidamente
              │         - Limpe logs meticulosamente
              │         - Saia antes que ele perceba
              │
              └── NÃO → Prossiga com cautela normal
                        - Ainda limpe logs antes de sair
```

### Sequência de limpeza de emergência (comandos exatos)

**Situação: Trace ativo detectado, ~60+ segundos restantes**
```bash
# No sistema alvo
cd /var
LogViewer.exe system.log
# Navegue e delete: "shell obtained", "connection routed", "file deleted"
# NÃO delete "connection established"
exit

# No proxy 3 (mais próximo do alvo)
cd /var
rm system.log
exit

# No proxy 2
cd /var
rm system.log
exit

# No proxy 1 (seu servidor alugado - pode deletar tudo)
cd /var
rm system.log
exit
```

**Situação: Countdown crítico (<30 segundos)**
```bash
exit
exit
exit
# Apenas escape - aceite o trace passivo como consequência
# Depois: crackear novo roteador para obter novo IP
```

### Procedimento pós-escape

Após uma sessão de hacking com múltiplos alvos, especialmente se houve detecção:
1. Crackear um novo roteador WiFi para obter novo IP
2. Qualquer jogador tentando PvP encontrará apenas um endereço morto nos logs
3. Considere abandonar proxies comprometidos e alugar novos servidores

---

## Seção 5: Referência rápida de práticas seguras

### Comandos de limpeza de log
```bash
# Visualizar/editar logs (requer root)
cd /var
LogViewer.exe system.log

# Obter root para deletar logs
scp -d /etc/passwd           # Download do arquivo de senhas
decipher passwd              # Crackear senha do root
sudo -s                      # Mudar para shell root
[digite a senha crackeada]

# Método alternativo de corrupção de log
rm /var/system.log
touch /var system.log        # Cria arquivo vazio
# AVISO: arquivo regenera no próximo evento de log
```

### O que deletar vs. manter
| DELETAR | MANTER |
|---------|--------|
| Shell obtained | Connection established |
| Connection routed | Connection closed |
| File deleted | IP disconnected |
| IP redirected | — |

### Ferramentas essenciais (obter na Hack Shop)
- **AdminMonitor.exe** — Avisa sobre admins e traces (OBRIGATÓRIO)
- **decipher** — Crackear senhas
- **metaxploit.so** — Biblioteca para exploits
- **crypto.so** — Biblioteca para WiFi cracking
- **LogViewer.exe** — Editar logs do sistema

### Tempos de trace por configuração
| Configuração | Tempo até Game Over |
|--------------|---------------------|
| Conexão direta, admin online | ~100 segundos |
| Com VPN (Abuseware) | Várias horas |
| Com 5+ proxy chain (limpo) | Praticamente impossível |

---

## Seção 6: Erros fatais que garantem game over

### Erro #1: Deletar o log inteiro
**O que acontece:** Quando você deleta `/var/system.log` completamente, o arquivo regenera automaticamente quando você desconecta, criando apenas uma entrada "disconnected". Um log contendo somente uma desconexão sem conexão correspondente é **extremamente suspeito** e dispara trace passivo imediato.

**Prevenção:** Sempre use LogViewer.exe para deletar apenas entradas específicas ("shell obtained", "connection routed", "file deleted"). Mantenha as entradas de conexão normais intactas.

### Erro #2: Conectar diretamente ao alvo
**O que acontece:** Seu IP real fica registrado nos logs do alvo. Se o admin iniciar trace ativo, você tem apenas ~100 segundos. Sem bounce chain, o trace vai direto para você.

**Prevenção:** Sempre conecte através de pelo menos um servidor proxy alugado. Inicie terminal no proxy de endpoint antes de prosseguir para garantir que o IP do proxy aparece nos logs, não o seu.

### Erro #3: Não verificar dsession antes de agir
**O que acontece:** Você executa `shell.start_terminal` em uma máquina onde o admin root está ativamente logado. Isso dispara trace ativo instantâneo.

**Prevenção:** Sempre execute `ps` imediatamente após obter shell. Procure por "dsession" ao lado do usuário root. Se presente, o admin está online — proceda com extrema cautela ou aborte.

### Erro #4: Ignorar o AdminMonitor
**O que acontece:** Você perde completamente os avisos de trace. O countdown começa e você não percebe até ser tarde demais.

**Prevenção:** Execute AdminMonitor.exe em background **antes** de conectar a qualquer sistema remoto. Mantenha visível durante toda a sessão de hacking.

### Erro #5: Deixar "shell obtained" nos logs
**O que acontece:** Esta é a prova definitiva de acesso não autorizado. Quando o admin verifica logs e encontra esta entrada, trace passivo é iniciado contra o IP registrado.

**Prevenção:** Esta entrada deve ser deletada **sempre**, sem exceção. É a prioridade #1 na limpeza de logs.

### Erro #6: Não limpar logs em TODOS os proxies
**O que acontece:** Você limpa perfeitamente o alvo, mas esquece de limpar um proxy intermediário. O trace segue a cadeia e te encontra através do proxy não limpo.

**Prevenção:** Trabalhe de trás para frente ao desconectar. Limpe logs em cada hop da cadeia. Em servidores alugados (que são descartáveis), você pode deletar o log inteiro.

### Erro #7: Deixar computador desprotegido (Multiplayer)
**O que acontece:** No multiplayer, seu PC in-game está exposto 24/7, mesmo quando você está offline. Outros jogadores podem hackear você enquanto não está jogando.

**Prevenção:** Configure firewall adequado, mude senhas regularmente, e mantenha seu sistema seguro antes de sair do jogo.

---

## Considerações sobre versões e confiabilidade

**Informações confirmadas (alta confiança):**
- Localização do log: `/var/system.log`
- Tipos de entrada de log e comportamento
- Tempo de trace ~100 segundos sem proxy
- Função do AdminMonitor.exe
- Mecânica de bounce chains

**Informações prováveis (média confiança):**
- Horários exatos de trabalho dos admins (varia por NPC)
- Thresholds específicos de detecção

**Mudanças de versão importantes:**
- **v0.7+:** Ferramentas como sshcrack, sshguest, shellmail foram removidas. Use exploits pré-gerados da Hack Shop ou scripts customizados.
- **v0.8.5+:** Traces bancários automáticos para transações não autorizadas.

**Diferenças Single-player vs Multiplayer:**
- Single-player: Universo para quando você sai; PC só acessível enquanto joga
- Multiplayer: Mundo contínuo 24/7; seu PC está exposto mesmo offline; trace passivo pode causar game over quando não conectado

---

## Conclusão

A sobrevivência em Grey Hack depende de disciplina operacional consistente. Os três pilares fundamentais são: **infraestrutura de proteção** (sempre usar bounce chains), **vigilância constante** (AdminMonitor ativo e verificação de dsession), e **higiene de logs** (deletar seletivamente, nunca o arquivo inteiro). 

O erro mais comum entre novatos é subestimar a importância da limpeza de logs — uma única entrada "shell obtained" esquecida pode resultar em game over horas depois. Jogadores experientes tratam cada sessão de hacking como uma operação em três fases: preparação meticulosa, execução com monitoramento constante, e limpeza de trás para frente através de toda a cadeia de conexão.

Para maximizar segurança, adote a mentalidade de que **qualquer ação que cria log é uma bomba-relógio** até ser neutralizada. Com os procedimentos documentados neste guia, você pode hackear agressivamente enquanto mantém um perfil de risco aceitável.
