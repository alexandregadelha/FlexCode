<h1 align="center">FlexCode</h1>

<p align="center"><strong>FlexCode — Flexibilidade para seus projetos</strong></p>

<p align="center">
  <a href="https://github.com/alexandregadelha/FlexCode">GitHub</a> |
  Fork do <a href="https://github.com/XiaomiMiMo/MiMo-Code">MiMoCode</a>
</p>

---

FlexCode é um fork do MiMoCode com a adição do **navegador integrado** — a
mesma arquitetura de navegação do Hermes Agent. O agente lê/escreve código,
executa comandos, gerencia Git e mantém memória persistente entre sessões, e
agora também navega na web: abre páginas, lê a árvore de acessibilidade,
interage com formulários, tira screenshots e exporta conteúdo — tudo dentro
do fluxo de desenvolvimento.

- **Múltiplos Agentes** — build (padrão), plan (análise somente leitura),
  compose (orquestração por specs); pressione `Tab` para alternar
- **Memória Persistente** — conhecimento do projeto entre sessões, checkpoints
  e progresso de tarefas via SQLite FTS5
- **Gestão Inteligente de Contexto** — checkpoints automáticos, reconstrução
  de contexto e injeção sob orçamento de tokens
- **Rastreamento de Tarefas** — sistema de tarefas em árvore integrado aos
  checkpoints
- **Sistema de Subagentes** — subagentes paralelos com rastreio de ciclo de
  vida, cancelamento e execução em background
- **Meta / Condição de Parada** — modelo julgador evita paradas prematuras em
  trabalho autônomo
- **Modo Compose** — workflow estruturado para desenvolvimento guiado por
  specs, com skills embutidas
- **Entrada de Voz** — entrada de voz em streaming (TenVAD + MiMo ASR)
- **Dream & Distill** — extraia conhecimento para memória (`/dream`) e
  descubra workflows reutilizáveis (`/distill`)
- **Navegador Integrado** — navegação, snapshots, interação, screenshots e
  export; local via CDP ou providers em nuvem (Browserbase, Browser Use, …)

Para a documentação completa, configuração e solução de problemas, veja o
[repositório no GitHub](https://github.com/alexandregadelha/FlexCode).
