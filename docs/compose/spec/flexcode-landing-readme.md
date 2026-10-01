---
feature: flexcode-landing-readme
status: in-progress
updated: 2026-01-11
branch: main
commits:
---

# FlexCode Landing README

## Report

**O que foi feito** — Reescrevi o README de landing do fork FlexCode (XiaomiMiMo/MiMo-Code) para exibi-lo na página inicial do GitHub de `alexandregadelha`. O novo `README.md` é em pt-BR e destaca a característica distintiva do fork — o **navegador integrado** (mesma arquitetura de navegação do Hermes Agent) — sobre a base do MiMoCode (agentes, memória persistente, subagentes, compose, dream/distill). Adicionei `README.en.md` (espelho em inglês) e atualizei o `README_npm.md` curto. Ambos os READMEs se referenciam via link no topo.

**Verificação** — Evidência fresca (11/01/2026, no workspace `/home/alesef/projetos/FlexCode`):
- Links externos (5): todos 200 (`anomalyco/opencode`, `NousResearch`, `XiaomiMiMo/MiMo-Code`, `platform.xiaomimimo.com/terms`, `alexandregadelha/FlexCode`).
- Links locais: `./LICENSE` e `./USE_RESTRICTIONS.md` existem; `assets/readme/mimocode-banner.png` existe.
- Balanceamento estrutural: `<details>`/`</details>` = 5/5 (PT e EN), 0/0 (npm); code fences paritários (12 em PT, 12 em EN); sem fences órfãos.
- `bun install` (frozen): resolveu 317 pacotes com sucesso — toolchain usável para quem quiser desenvolver no fork.
- markdownlint-cli: apenas avisos de estilo (MD013/MD031/MD033/MD060) compatíveis com o estilo do MiMoCode original; nenhum erro de sintaxe.

**Journey log** —
1. A branch `main` do fork **não contém** código de navegador — é um mirror do upstream. A seção do README descreve a feature em termos de design/contratos (a ser implementada em branch futura); decisão registrada na [S2].
2. A "integração de navegador do Hermes" não está versionada no fork; os fontes do Hermes Agent estão em `~/.hermes/hermes-agent-current/tools/browser_*.py` (CDP, Camofox, Lightpanda, cloud providers) — usei-os para descrever a arquitetura com precisão sem copiar código.
3. `gh auth` está ativo para `alexandregadelha` com token `repo` — push direto para `main` é possível sem credential setup adicional.

## [S1] Problem
O fork [FlexCode](https://github.com/alexandregadelha/FlexCode) (XiaomiMiMo/MiMo-Code)
exibe no GitHub o README original do MiMoCode — com nome, links e identidade da Xiaomi —
que não reflete o projeto FlexCode nem sua característica distintiva: a integração do
navegador usado pelo Hermes Agent.

## [S2] Design
- **README.md (pt-BR)** — README principal em português, com:
  - Banner existente (`assets/readme/mimocode-banner.png`) + título "FlexCode"
  - Tagline "Flexibilidade para seus projetos" (espelha a descrição do repo)
  - Descrição: fork do MiMoCode + navegador integrado
  - Seção **Navegador Integrado** destacando a feature do Hermes
    (navegação, snapshots, interações, screenshots; local CDP + cloud providers)
  - Quick Start (build do fork via bun + uso)
  - Feature grid condensado (agentes, memória, subagents, compose...)
  - Seção Development + referência ao upstream
- **README.en.md** — versão inglesa espelhada, linkada a partir do README principal
- **README_npm.md** — versão curta do npm, atualizada para FlexCode
- O código da integração de navegador **ainda não** existe na main; a seção do
  navegador descreve a feature em termos de design/contratos (a ser implementada)

## [S3] Out of Scope
- Implementação do código da integração de navegador (trabalho futuro, branch separada)
- Criar novo banner/logo FlexCode (usar o existente por ora)
- Publicação do fork no npm

## Tasks
- [x] T1: Escrever README.md em pt-BR com foco no navegador integrado — acceptance: arquivo lê-se como landing page da página inicial do GitHub do repo (covers: S2)
- [x] T2: Escrever README.en.md (espelho em inglês) e linkar ambas versões — acceptance: os dois READMEs se referenciam; conteúdo espelhado (covers: S2; depends: T1)
- [x] T3: Atualizar README_npm.md para a identidade FlexCode — acceptance: versão curta em pt-BR coerente com o README principal (covers: S2; depends: T1)
- [x] T4: Verificar renderização (markdown lint + links) e commit — acceptance: nenhum link quebrado; markdown válido; commit em main (covers: S2; depends: T2, T3)
