# Inventario del stack Gentle

Verificado el 2026-11-10 con `gentle-ai doctor` en esta carpeta.

**Estado global:** `8 passed, 0 failed, 0 warnings` — healthy.

## Instalado

| Componente | Versión / ruta | Para qué sirve |
|---|---|---|
| gentle-ai | 4.4.0 — `~/.local/bin/gentle-ai` | CLI que configura y sincroniza los agentes |
| OpenCode | `~/.opencode/bin/opencode` | Runtime del agente (el que usás) |
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

## Configuración activa

- **Agente por defecto:** `gentle-orchestrator`
- **Agentes disponibles:** 14 (`gentle-ai-explore`, `gentle-ai-worker`, `gentle-ai-verify`, `review-risk`, `review-resilience`, `review-readability`, `review-reliability`, `review-refuter`, `review-validator`, `jd-judge-a`, `jd-judge-b`, `jd-fix-agent`, `gentleman`)
- **Skills:** 13 + `_shared`
- **MCP activos:** 4 (`codebase-memory-mcp`, `context7`, `engram`, `playwright`)
- **Review (RDD):** `on` — decidido por default, sin override global ni clone-local
- **Temas:** `gentleman.json`, `gentleman-cute.json`

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
```
