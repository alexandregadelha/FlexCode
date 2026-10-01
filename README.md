<h1 align="center">FlexCode</h1>

<p align="center">
  <img src="assets/readme/mimocode-banner.png" alt="FlexCode" width="700">
</p>

<p align="center"><strong>FlexCode — Flexibilidade para seus projetos</strong></p>

<p align="center">
  <a href="README.en.md">English</a> | Português
</p>

<p align="center">
  <a href="https://github.com/alexandregadelha/FlexCode">GitHub</a> |
  Fork do <a href="https://github.com/XiaomiMiMo/MiMo-Code">MiMoCode</a>
</p>

---

O **FlexCode** é um fork do [MiMoCode](https://github.com/XiaomiMiMo/MiMo-Code) — um
assistente de código nativo de terminal que lê e escreve código, executa comandos,
gerencia Git e mantém memória persistente entre sessões.

Por cima da base do MiMoCode, o FlexCode adiciona a sua característica principal:
**o navegador integrado**, a mesma arquitetura de navegação que o
[Hermes Agent](https://github.com/NousResearch) utiliza — para que o assistente
navegue na web, inspecione páginas e interaja com interfaces no mesmo fluxo de
trabalho em que você programa.

---

## Navegador Integrado

O FlexCode dá ao agente a capacidade de controlar um navegador real, seguindo o
mesmo design de ferramentas de browser do Hermes Agent:

- **Navegação** — abrir URLs, voltar/avançar, recarregar e gerenciar abas.
- **Snapshot** — a página é lida como uma árvore de acessibilidade com refs
  estáveis, e o agente identifica onde clicar, digitar ou selecionar.
- **Interação** — clicks, digitação em campos, preenchimento de formulários,
  rolagem, drag, upload de arquivos e execução de JavaScript quando necessário.
- **Capturas** — screenshots (JPG/PNG) e export de páginas em Markdown.
- **Modos** — navegador local via CDP ou providers em nuvem plugáveis
  (Browserbase, Browser Use, …), com fallback e supervisão de sessões.

Isso permite fluxos como "abra esta issue no navegador, leia o console, reproduza
o bug na UI e suba o fix" sem sair do terminal.

> A integração de navegador é a feature distintiva do FlexCode. A base
> (agentes, memória, compose) permanece a do MiMoCode.

---

## Quick Start

```bash
# Instalar dependências
bun install

# Rodar em modo desenvolvimento
bun run dev

# Ou rodar o bin direto
mimo
```

No primeiro uso, o fluxo de configuração é guiado automaticamente. As opções
disponíveis de provedor incluem:

- **MiMo Auto (gratuito por tempo limitado)** — canal anônimo, zero configuração
- **Plataforma MiMo da Xiaomi** — login OAuth
- **Importar do Claude Code** — migra sua autenticação em um passo
- **Provedor custom** — adicione qualquer API compatível com OpenAI no TUI

> Como o FlexCode é um fork, você também pode usar os binários do MiMoCode
> originais como ponto de partida e aplicar o módulo de navegador por cima.

<details>
<summary><strong>WSL: problemas de clipboard</strong></summary>

Se o texto sair corrompido ao copiar no WSL, instale o <code>xsel</code>:
```bash
sudo apt install xsel
```
</details>

<details>
<summary><strong>Windows: saída CJK (chinês/japonês/coreano) corrompida no shell</strong></summary>

No Windows com locale de sistema não-UTF-8 (ex.: zh-CN, code page 936/GBK), a
saída de comandos com caracteres CJK pode sair em mojibake. O FlexCode força
saída UTF-8 para os subprocessos PowerShell/cmd que ele lança. Se ainda assim
houver saída corrompida em casos não cobertos, ative o suporte UTF-8 de sistema
do Windows:

**Configurações → Hora e idioma → Idioma e região → Configurações administrativas
de idioma → Alterar locale do sistema → marque "Beta: Use Unicode UTF-8 for
worldwide language support" → reinicie.**

Isso troca a code page ativa (ACP) para UTF-8 (65001) em todos os programas,
para que subprocessos não herdem mais a code page legada. É um toggle Beta de
sistema e pode fazer programas antigos não-Unicode exibirem incorretamente —
trate como workaround.
</details>

---

## Core Features

### Múltiplos Agentes

| Agente | Descrição |
|--------|------|
| **build** | Padrão. Permissões completas para desenvolvimento |
| **plan** | Modo de análise somente leitura para exploração e design de soluções |
| **compose** | Modo de orquestração para desenvolvimento guiado por specs e workflows por skill |

Pressione `Tab` para alternar entre os agentes principais. Subagentes são
criados pelo sistema conforme necessário.

### Memória Persistente

Memória entre sessões com SQLite FTS5 (full-text search):

- **Memória de projeto** (`MEMORY.md`) — conhecimento, regras e decisões de arquitetura
- **Checkpoint de sessão** (`checkpoint.md`) — snapshots estruturados mantidos pelo subagente checkpoint-writer
- **Notas rápidas** (`notes.md`) — área de rascunho dos agentes
- **Progresso de tarefa** (`tasks/<id>/progress.md`) — logs por tarefa

A memória é injetada automaticamente quando a sessão recomeça, então o agente
não precisa "aprender de novo" o contexto do projeto.

### Gestão Inteligente de Contexto

- **Checkpoints automáticos** — decide quando salvar o estado com base na janela de contexto do modelo
- **Reconstrução de contexto** — quando o contexto se aproxima do limite, reconstrói a partir do checkpoint mais recente, da memória, do progresso das tarefas e das mensagens recentes
- **Injeção sob orçamento** — controla com um orçamento de tokens quanto de checkpoint, memória e notas entra no contexto, com ranking de importância

### Rastreamento de Tarefas

Sistema de tarefas em árvore (`T1`, `T1.1`, `T1.2`, …) que integra
automaticamente com o sistema de checkpoints, então o progresso das tarefas é
preservado quando a sessão recomeça.

### Sistema de Subagentes

O agente principal cria subagentes sob demanda. Subagentes compartilham o
contexto da sessão atual, trabalham em paralelo, com rastreio de ciclo de
vida, cancelamento e execução em background.

### Meta / Condição de Parada

O comando `/goal` define uma condição de parada para a sessão. Quando o agente
tenta parar, um modelo julgador independente avalia a conversa para decidir se
a condição foi de fato satisfeita — prevenindo paradas "otimistas" prematuras
durante trabalho autônomo.

### Modo Compose

Modo Compose oferece um workflow estruturado para desenvolvimento guiado por
specs. Inclui skills embutidas de planejamento, execução, code review, TDD,
debug, verificação e merge — orquestrando o ciclo completo de spec até código
entregue.

### Entrada de Voz

Entrada de voz em streaming em tempo real com TenVAD e MiMo ASR. Ative com
`/voice` e fale — o áudio é segmentado por pausas e transcrito incrementalmente
na entrada. Disponível para usuários logados no MiMo. Requer `sox`
(`brew install sox` no macOS, em outras plataformas similar).

<details>
<summary><strong>Configuração de áudio WSLg</strong></summary>

```bash
sudo apt install -y sox pulseaudio libasound2-plugins
export PULSE_SERVER=unix:/mnt/wslg/PulseServer
```
</details>

<details>
<summary><strong>Áudio remoto via SSH (Mac → host remoto)</strong></summary>

```bash
# Mac (local)
brew install pulseaudio
pulseaudio --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1" --exit-idle-time=-1 --daemonize
# Adicione em ~/.ssh/config: RemoteForward 4713 127.0.0.1:4713

# Host remoto
apt install -y pulseaudio pulseaudio-utils sox
export PULSE_SERVER=tcp:127.0.0.1:4713
# Verifique: pactl info
```
</details>

### Dream & Distill

- **`/dream`** — varra os traços de sessões recentes, extraia conhecimento
  persistente para a memória do projeto e remova entradas obsoletas
- **`/distill`** — descobre workflows manuais repetidos no trabalho recente e
  empacota os candidatos de maior confiança em skills, subagentes ou comandos
  reutilizáveis

---

## Configuração

O FlexCode é configurado por `.mimocode/mimocode.json` no diretório do projeto
(ou `~/.config/mimocode/mimocode.json` globalmente). Opções principais incluem:

- Provedor e seleção de modelo
- Permissões de agente e agentes custom
- Comportamento de checkpoint e memória
- Conexões de servidores MCP
- Keybindings e tema

O Max Mode (raciocínio paralelo best-of-N com seleção por julgador) pode ser
ativado via `experimental.maxMode` na configuração.

<details>
<summary><strong>Permitir o diretório temporário do sistema (<code>/tmp</code>)</strong></summary>

Por padrão, ler ou escrever arquivos fora do diretório de trabalho do projeto
dispara um prompt de permissão <code>external_directory</code> — incluindo o
diretório temporário do sistema. Isso é intencional: o FlexCode não amplia
permissões silenciosamente, você controla o que o modelo pode tocar fora do
projeto.

O diretório temporário aparece com frequência porque a maioria dos modelos usa
ele como espaço de rascunho (ex.: um script rápido, um arquivo de dados
descartável). Se você confia no seu ambiente e prefere não ser perguntado toda
vez, opte por permitir na configuração:

```json title=".mimocode/mimocode.json"
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "external_directory": {
      "/tmp/**": "allow"
    }
  }
}
```

**Esta configuração tem riscos conhecidos — use por sua conta e risco.** O
diretório temporário é gravável por todos e compartilhado com qualquer outro
processo e usuário da máquina. Permitir automaticamente significa que o modelo
pode ler e gravar ali sem confirmação, o que amplia sua exposição a truques
previsíveis de temp-path / symlink (ex.: outro processo pre-cria
<code>/tmp/foo</code> como symlink para um arquivo sensível). Por isso, é
recomendado apenas para ambientes de usuário único, controlados, ou dentro de
um container. Mantenha a allowlist o mais estreita possível.

</details>

---

## Desenvolvimento

```bash
bun install              # Instala as dependências
bun run dev              # Roda em modo de desenvolvimento
bun turbo typecheck      # Checagem de tipos
```

---

## Relação com o MiMoCode

O FlexCode é um fork do [MiMoCode](https://github.com/XiaomiMiMo/MiMo-Code),
que por sua vez é um fork do
[OpenCode](https://github.com/anomalyco/opencode). Ele mantém todas as
capacidades core do OpenCode/MiMoCode (múltiplos provedores, TUI, LSP, MCP,
plugins) e adiciona o **navegador integrado** como diferencial — a mesma
arquitetura de navegação do Hermes Agent.

---

## Licença

Código-fonte licenciado sob a [Licença MIT](./LICENSE).

O uso do FlexCode também está sujeito às
[Restrições de Uso](./USE_RESTRICTIONS.md). O uso dos serviços hospedarizados
pela Xiaomi MiMo está sujeito aos [Termos de Serviço do MiMo](https://platform.xiaomimimo.com/docs/terms/user-agreement).
O uso do nome, logotipo e marcas do MiMo está sujeito à Política de Marca do
MiMo.
