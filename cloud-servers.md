# Servidores Alugados no Grey Hack: Guia Completo para Iniciantes

Servidores alugados são a **infraestrutura essencial** que separa hackers amadores de operadores profissionais no Grey Hack. Eles funcionam como VPS (Virtual Private Servers) legítimos dentro do jogo, permitindo que você execute ataques sem expor seu IP real, hospede serviços 24/7 e crie cadeias de anonimização praticamente inrastreáveis. Para qualquer jogador sério no modo multiplayer, investir em pelo menos um servidor alugado logo após o tutorial é a decisão estratégica mais importante que você pode tomar.

---

## O que são servidores alugados e por que você precisa de um

Servidores alugados são máquinas virtuais compradas através de sites de ISP (Internet Service Provider) dentro do jogo. Diferentemente do seu computador doméstico que só funciona quando você está online, eles permanecem **ativos 24 horas por dia**, mesmo quando você está desconectado. Esta persistência é o diferencial que torna possível hospedar websites, manter backdoors em funcionamento e, principalmente, proteger sua identidade real.

A diferença fundamental entre as três opções de infraestrutura disponíveis no jogo determina seu nível de segurança operacional:

| Tipo de Servidor | Persistência | Administradores NPC | Nível de Risco |
|------------------|--------------|---------------------|----------------|
| **Servidor Alugado** | 24/7, mesmo offline | Nenhum | Baixíssimo |
| **PC Doméstico** | Apenas quando jogando | N/A | Máximo - expõe IP real |
| **Servidor NPC Hackeado** | Reset a cada 15-20 min | Sim, iniciam traces | Médio-Alto |

A ausência de administradores NPC em servidores alugados é crítica: significa que você pode deletar **todos os logs** sem preocupação, pois não há ninguém monitorando. Em servidores NPC hackeados, deletar logs suspeitosamente pode iniciar um trace passivo que eventualmente leva até você.

---

## Como adquirir seu primeiro servidor

O processo de aquisição mudou em atualizações recentes — servidores não estão mais em lojas comuns, mas em **sites de ISP** que você precisa encontrar.

**Passo a passo para alugar:**

1. Abra o **Browser.exe** no seu computador doméstico
2. Pesquise por "internet" ou "server" na barra de busca
3. Navegue **profundamente nos resultados** (página 30+ é recomendado em multiplayer para encontrar ISPs menos conhecidos)
4. Acesse um site de ISP e clique na aba **"Services"**
5. Escolha a velocidade de conexão — **3MB/s (Small Pack)** é suficiente para a maioria dos usos
6. Clique em **"Rent Server"** e confirme a compra

Após a compra, você receberá um **email** contendo o IP do servidor, credenciais root e instruções. Não se esqueça de baixar o **ConfigLan.exe** do mesmo site ISP — esta ferramenta é essencial para gerenciar todos os seus servidores.

⚠️ **AVISO CRÍTICO**: Os pagamentos são **mensais e recorrentes**, debitados automaticamente da sua conta bancária no jogo. Se o pagamento falhar, **todos os seus servidores são desativados simultaneamente**. Mantenha sempre saldo suficiente.

---

## Configuração técnica inicial

Servidores alugados não possuem interface gráfica — você trabalha exclusivamente via terminal SSH. A conexão segue a sintaxe:

```
ssh root@[senha] [IP_do_servidor]
```

**Exemplo prático:**
```
ssh root@1234abc 87.125.43.12
```

Após conectar, sua primeira tarefa é criar a estrutura de diretórios para suas ferramentas:

```bash
cd /root
mkdir rkit
cd rkit
```

O diretório `/root/rkit` será seu "kit de ferramentas" onde você armazenará exploits, bibliotecas e scripts. As bibliotecas essenciais que você **precisa** fazer upload são:

- **metaxploit.so** — necessária para todos os exploits (salve em `/lib`)
- **crypto.so** — necessária para quebra de senhas (salve em `/lib`)
- **decipher** — ferramenta de descriptografia de senhas

Para transferir arquivos, use o comando `scp`:
```bash
scp -d /etc/passwd    # Download de arquivo
scp -u /root/tool.exe # Upload de arquivo
```

---

## Configurando port forwarding e firewall

Para que serviços funcionem no seu servidor (SSH, rshell, HTTP), você precisa configurar o **port forwarding** no roteador. Primeiro, descubra o IP do gateway:

```bash
ifconfig
```

Procure o campo "gateway" (geralmente **192.168.0.1** ou similar). Abra o Browser.exe e acesse:

```
[gateway_ip]:8080
```

Na interface do roteador, vá em **Port Forwarding** e adicione entradas:

| Porta Externa | Porta Interna | IP LAN do Servidor |
|---------------|---------------|---------------------|
| 22 | 22 | 192.168.0.2 (SSH) |
| 1222 | 1222 | 192.168.0.2 (rshell) |
| 80 | 80 | 192.168.0.2 (HTTP) |

Para o **firewall**, acesse a aba correspondente no mesmo painel. Configure regras para bloquear IPs indesejados, mas **cuidado para não bloquear seu próprio acesso**.

---

## Usando servidores como bounce point para anonimização

A aplicação mais importante de um servidor alugado é servir como **bounce point** — um intermediário que mascara seu IP real. O conceito é simples: em vez de atacar diretamente do seu PC doméstico, você conecta primeiro ao servidor alugado e executa os ataques de lá.

**Fluxo de ataque correto:**
```
Seu PC → Servidor Alugado → Alvo
```

Quando o alvo verifica os logs, ele vê apenas o IP do servidor alugado, não o seu. Se houver um trace, ele para no servidor alugado — que não tem administradores NPC para continuar a investigação.

**Regra de Ouro**: Nunca conecte diretamente do seu PC doméstico para atacar um alvo. Sempre use pelo menos um bounce.

Para conexões encadeadas com múltiplos servidores (aumentando ainda mais o tempo de trace):

```
Seu PC → Servidor 1 → Servidor 2 → Servidor 3 → Alvo
```

Cada "salto" adiciona tempo ao trace. Jogadores avançados reportam usar cadeias de **10-15 servidores** para operações de alto risco.

---

## Infraestrutura de reverse shell (rshell)

O rshell permite que você receba conexões de máquinas comprometidas, criando backdoors persistentes. Configure no seu servidor alugado — **nunca no PC doméstico**, pois os serviços podem ter vulnerabilidades exploráveis.

**Instalação do rshell server:**

1. Baixe `rshell-server` e `rshell_interface` da hackshop
2. SSH no servidor alugado
3. Execute `sudo rshell-server` para instalar
4. Configure port forwarding para porta **1222**
5. Execute `rshell_interface` para monitorar conexões

O rshell é especialmente útil com **engenharia social**. Usando o template "Funny Game" no Mail.exe, você pode enviar emails para NPCs que, ao executarem o "jogo", enviam um shell para seu servidor. Verifique novas conexões no `rshell_interface`.

---

## Análise econômica e momento ideal de investimento

Os custos de servidores aumentam **exponencialmente** conforme você aluga mais unidades. O pagamento é global — se falhar, todos desativam simultaneamente.

**Estrutura de custos recomendada:**

| Estágio do Jogo | Recomendação | Justificativa |
|-----------------|--------------|---------------|
| Tutorial/Início | Nenhum servidor | Aprenda mecânicas básicas |
| Primeiras Missões | 1 servidor (3MB/s) | Proteção básica de IP |
| Operações Sérias | 2-3 servidores | Cadeia de bounces |
| Jogo Avançado | 3-5 servidores | Redundância + serviços |

O investimento se paga rapidamente: uma única operação de hacking bancário bem-sucedida pode financiar meses de aluguel. NPCs recebem depósitos mensais — salve credenciais bancárias comprometidas e **revisit-as mensalmente** para saques recorrentes.

**Para maximizar ROI:**
- Use o plano **3MB/s (Small Pack)** — velocidades maiores raramente são necessárias
- Delete servidores não utilizados via ConfigLan (aba "unused servers") para evitar cobranças fantasma
- Mantenha saldo bancário suficiente para **pelo menos 2 meses** de pagamentos

---

## Segurança operacional (OPSEC) e proteção do servidor

Seu servidor alugado pode ser hackeado por outros jogadores em multiplayer. Proteja-o imediatamente após a configuração.

**Comandos essenciais de proteção:**

```bash
# Remover todas as permissões de guests
sudo chmod -R o-rwx /

# Proteger diretório /etc especificamente  
sudo chmod o-rwx /etc
sudo chmod o-rwx /etc/passwd

# Remover permissões de grupo
sudo chmod -R g-rwx /
```

**Gerenciamento de logs** é crítico. Em servidores alugados, você pode deletar tudo sem consequências. Mas entenda a diferença:

**Logs que devem ser DELETADOS:**
- ✅ "shell obtained"
- ✅ "connection routed"
- ✅ "file deleted"

**Logs que NUNCA devem ser deletados em servidores NPC:**
- ❌ "connection established" (parece normal)
- ❌ "disconnected from server" (não pode ser deletado)

Use `LogViewer.exe /var/system.log` para visualizar e editar logs.

---

## Erros fatais que iniciantes cometem

Os cinco erros mais comuns que levam iniciantes a serem rastreados:

1. **Atacar diretamente do PC doméstico** — sem bounce, você é rastreado em minutos
2. **Deletar arquivos de log inteiros** — cria entrada "file deleted" que é rastreável; delete apenas entradas específicas
3. **Ignorar o AdminMonitor.exe** — execute sempre em background para alertas de traces ativos
4. **Não verificar comando `ps` antes de operar** — se root com "dsession" aparecer, um admin está online; desconecte imediatamente
5. **Deixar Bank.txt e Mail.txt acessíveis** — outros jogadores podem roubar suas credenciais

⚠️ **AVISO**: Scripts de terceiros podem conter backdoors. Ferramentas populares da comunidade são geralmente seguras, mas sempre verifique o código antes de executar em servidores importantes.

---

## Alternativas gratuitas: hackeando servidores NPC

Se você não pode pagar por servidores alugados, pode usar servidores NPC hackeados como bounces temporários. O processo:

1. Use `nmap [IP]` para escanear portas e versões de serviços
2. Procure exploits correspondentes na hackshop
3. Execute o exploit e obtenha acesso
4. Use como bounce **temporariamente**

**Trade-offs críticos:**

| Aspecto | Servidor Alugado | Servidor NPC Hackeado |
|---------|------------------|------------------------|
| Custo | Mensal | Grátis |
| Administradores | Nenhum | Sim — iniciam traces |
| Persistência | Permanente | Reset em 15-20 min |
| Limpeza de logs | Pode deletar tudo | Apenas entradas suspeitas |
| Confiabilidade | 100% | Imprevisível |

Servidores NPC **resetam periodicamente** — suas modificações, backdoors e serviços instalados são removidos. Eles são úteis para operações rápidas e descartáveis, mas não substituem a confiabilidade de servidores alugados para operações sérias.

---

## Conclusão e recomendações por perfil

Para **jogadores casuais em single player**: servidores alugados são úteis mas opcionais. Use NPCs hackeados para bounces e aprenda as mecânicas sem pressão financeira.

Para **jogadores de multiplayer**: invista em pelo menos **um servidor alugado** imediatamente após o tutorial. O custo é mínimo comparado ao risco de perder todo seu progresso por um trace bem-sucedido.

Para **operadores avançados**: mantenha **2-3 servidores** em cadeia, com um dedicado para rshell/serviços e outros para bouncing. Configure permissões restritivas, execute AdminMonitor.exe constantemente, e limpe logs religiosamente.

A infraestrutura de servidores alugados é o alicerce de todas as operações profissionais no Grey Hack. Dominar sua configuração e uso operacional transforma você de um script kiddie vulnerável em um hacker metódico e virtualmente inrastreável.
