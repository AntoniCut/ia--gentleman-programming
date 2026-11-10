# Inventario del stack Gentle

Verificado el 2026-11-10 con `gentle-ai doctor` en esta carpeta.

**Estado global:** `8 passed, 0 failed, 0 warnings` — healthy.

## Instalado

| Componente | Versión / ruta | Para qué sirve |
|---|---|---|
| gentle-ai | 4.4.0 — `~/.local/bin/gentle-ai` | CLI que configura y sincroniza los agentes |
| OpenCode | `~/.opencode/bin/opencode` | Runtime del agente (entorno principal) |
| Cursor | 3.27.22 — `/usr/bin/cursor` | Editor con Agent (segundo entorno) |
| Claude Code | `~/.local/bin/claude` | Runtime alternativo |
| Engram | `~/.local/bin/engram` | Memoria persistente entre sesiones |
| CodeGraph | `~/.local/bin/codebase-memory-mcp` | Grafo de conocimiento del código |
| Context7 | MCP | Documentación actualizada de librerías |
| Playwright | MCP | Automatización de navegador |
| gga | `~/.local/bin/gga` | Asistente de Git de Gentle |
| node | v24.13.0 | |
| pnpm | 11.25.0 | |
| bun | 1.3.14 | |
| gh | instalado | GitHub CLI |

Agentes declarados en el estado de gentle-ai: `opencode`, `cursor`.

## Configuración activa (OpenCode)

- **Agente por defecto:** `gentle-orchestrator`
- **Agentes disponibles:** 14 (`gentle-ai-explore`, `gentle-ai-worker`, `gentle-ai-verify`, `review-risk`, `review-resilience`, `review-readability`, `review-reliability`, `review-refuter`, `review-validator`, `jd-judge-a`, `jd-judge-b`, `jd-fix-agent`, `gentleman`)
- **Skills:** 13 + `_shared`
- **MCP activos:** 4 (`codebase-memory-mcp`, `context7`, `engram`, `playwright`)
- **Review (RDD):** `on` — decidido por default, sin override global ni clone-local
- **Temas:** `gentleman.json`, `gentleman-cute.json`

## Qué tiene cada entorno

| Pieza | OpenCode | Cursor |
|---|---|---|
| Configuración | `~/.config/opencode/opencode.json` | `~/.cursor/rules/gentle-ai.mdc`, `~/.cursor/mcp.json` |
| Agentes | 14 (2 primarios + 12 subagentes) | 3 subagentes de Judgment Day |
| Skills | 13 + `_shared` en `~/.config/opencode/skills/` | 12 + `_shared` en `~/.cursor/skills/`; también lee `~/.agents/skills/` (ahí está `impeccable`) |
| Comandos | 3 (`/impeccable`, `/skill-creator`, `/skill-registry`) | no hay propios; se usan skills con `/` |
| MCP | 4: CodeGraph, Context7, Engram, Playwright | Engram, Context7, Playwright, CodeGraph + los previos |
| Review | 4 lentes RDD + Judgment Day | Judgment Day |

Notas de Cursor:

- La regla `~/.cursor/rules/gentle-ai.mdc` es global y `alwaysApply`. Cursor la busca hacia arriba desde el proyecto, así que se aplica a los proyectos que están dentro de `/home/antonydev`. Puede que no aparezca en `Customize → Rules` ni en `Settings → Rules`; es una limitación conocida de Cursor, no un error de instalación.
- Si el proyecto está fuera del home, o usás cloud agents, la regla global no se carga. En ese caso, copiala a `.cursor/rules/gentle-ai.mdc` del proyecto o armá un plugin local.
- `gentle-ai doctor` verifica la configuración de OpenCode. Para Cursor, revisá a mano las rutas de la tabla y los MCP en Settings → MCP.

## Falta instalar

| Componente | Comando | Prioridad |
|---|---|---|
| Gentle Shell | `npm i -g gentle-pi` → `gentle-shell` | Alta |
| Engram Cloud | `engram cloud` (configurar servidor) | Media |
| ripgrep, fd, fzf, bat | `sudo apt install ripgrep fd-find fzf bat` | Media |
| Pi (standalone) | `curl -fsSL https://pi.dev/install.sh \| sh` | Baja — viene con Gentle Shell |

Notas:

- El paquete de Gentle Shell todavía se llama `gentle-pi`; el producto se renombró a `gentle-shell`.
- Gentle Shell usa un home propio en `~/.gentle-shell/agent`. No toca `~/.pi` salvo que uses `--link`.
- En Debian/Ubuntu el binario de `fd-find` se llama `fdfind`.

## No hace falta para el flujo del agente

Capa de editor y terminal que usa Alan. Es ergonomía personal, no requisito:

- Neovim / LazyVim / Gentleman.Dots
- tmux / zellij
- Ghostty / WezTerm / Kitty
- Go

## Cómo re-verificar

```bash
gentle-ai doctor
gentle-ai review mode status
ls ~/.config/opencode/skills
ls ~/.config/opencode/commands
ls ~/.cursor/rules ~/.cursor/skills ~/.cursor/agents
jq '.mcpServers | keys' ~/.cursor/mcp.json
```
