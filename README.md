# Gentleman Programming — espacio de trabajo

Workspace para aprender y usar el stack de Gentle AI (Alan Buscaglia) en OpenCode y Cursor: harness agéntico, ODD, skills, memoria y review.

## Por dónde empezar

Si es tu primera vez acá, **no leas el resto de este archivo**: abrí `04-guia/00-mapa-mental.md`. Después seguí `04-guia/02-ruta-de-estudio.md` en orden.

## Capítulos de la guía

| # | Archivo | Qué resuelve |
|---|---|---|
| 00 | `04-guia/00-mapa-mental.md` | Cómo se conecta todo. El mapa |
| 01 | `04-guia/01-glosario.md` | Qué significa cada término |
| 02 | `04-guia/02-ruta-de-estudio.md` | En qué orden estudiarlo |
| 03 | `04-guia/03-como-funciona-el-agente.md` | Orquestador, subagentes, contexto |
| 04 | `04-guia/04-odd.md` | La metodología: los 7 pasos, tamaño de tarea, riesgo |
| 05 | `04-guia/05-engram.md` | Memoria persistente y proyectos |
| 06 | `04-guia/06-opencode.md` | Cómo usar el stack en OpenCode: agentes, comandos y review |
| 07 | `04-guia/07-cursor.md` | Cómo usar el mismo stack en Cursor: regla, skills y subagentes |

## Carpetas

| Carpeta | Qué guarda |
|---|---|
| `01-instalacion/` | Inventario verificado de qué hay instalado, versiones y comandos para reproducirlo |
| `02-plantillas/` | Plantillas reutilizables: documento de feature ODD, AGENTS.md |
| `03-proyectos/` | Cada experimento en su propia carpeta |
| `04-guia/` | La documentación desde cero: mapa mental, glosario, ruta de estudio y uso por entorno |

## Comandos del día a día

```bash
opencode                          # OpenCode con gentle-orchestrator
cursor .                          # Cursor; la regla gentle-ai se aplica sola
gentle-ai doctor                  # chequeo de salud del stack
gentle-ai review mode status      # estado del review por candidate
gentle-ai review start --focus risk
engram search "tema"              # buscar en memoria
```

En Cursor, el review adversarial se pide con `/judgment-day`. El detalle de cada entorno está en los capítulos 06 y 07.

## Las tres capas

Sirve para no confundir qué es requisito y qué es preferencia personal:

1. **Harness (el agente)** — gentle-ai, skills, Engram, review, ODD. Es la que importa.
2. **Editor / host** — OpenCode, Cursor, Neovim/LazyVim. Elección personal; no afecta al harness.
3. **Terminal** — Ghostty, tmux, ripgrep, fzf. Ergonomía; opcional.

Empezar por la capa 1 y no replicar la 2 y 3 de entrada.

## Estado

Ver `01-instalacion/inventario.md`. Última verificación: 2026-11-10, healthy.
