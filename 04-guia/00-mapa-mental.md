# 00 — Mapa mental del ecosistema

Este es el archivo que hay que leer primero. Si algo no cierra, el problema casi nunca es una pieza suelta: es que falta este mapa.

## La idea en una frase

Gentle AI no es un agente. Es una **capa de disciplina** que se instala encima del agente que ya usás, para que no haga cualquier cosa.

## Cómo se conecta todo

```
VOS
 └─ hablás con un RUNTIME (el agente)
     ├─ OpenCode ........ tu entorno principal (cap. 06)
     ├─ Cursor .......... tu segundo entorno (cap. 07)
     ├─ Claude Code ..... también instalado
     └─ Gentle Shell .... la TUI de Alan
          └─ Pi ......... harness minimalista sobre el que corre

Sobre cualquier runtime, gentle-ai instala:
   ├─ persona y reglas .... AGENTS.md / regla de Cursor
   ├─ skills .............. ~/.config/opencode/skills/ · ~/.cursor/skills/ · ~/.agents/skills/
   ├─ subagentes .......... OpenCode: explore, worker, verify, review-* · Cursor: jd-*
   └─ review (RDD) ........ 4 lentes en OpenCode + Judgment Day en los dos

Herramientas satélite (funcionan aparte del runtime):
   ├─ Engram ....... memoria persistente entre sesiones
   ├─ CodeGraph .... grafo de conocimiento del código
   ├─ gga .......... review automático en pre-commit
   └─ MCPs ......... Context7 (docs), Playwright (navegador)
```

La clave: **gentle-ai es horizontal**. No reemplaza a OpenCode, Cursor ni a Pi; los configura. Podés cambiar de runtime y llevarte casi todo el stack. La excepción importante: el review 4R con las lentes `review-*` vive en OpenCode. Cursor tiene Judgment Day y sus propios subagentes.

## Por qué te estás haciendo lío (es razonable)

Tres razones concretas, ninguna es culpa tuya:

1. **Tres cosas distintas comparten el prefijo "Gentle"**: el ecosistema (Gentle AI), el CLI (`gentle-ai`) y el producto (`Gentle Shell`). Suenan igual, hacen cosas distintas.
2. **Un producto cambió de nombre a mitad de camino**: `gentle-pi` → `gentle-shell`. El repo y el paquete npm siguen con el nombre viejo. Los videos de mayo dicen uno, los de noviembre dicen el otro.
3. **Los videos no están en orden pedagógico**: el curso de mayo explica conceptos, el stream de esta semana asume que ya los sabés.

## Tabla de nombres (la que mata la confusión)

| Nombre | Qué es | Estado |
|---|---|---|
| **Gentle AI** | El ecosistema completo / la marca | — |
| **gentle-ai** | El CLI que instala y sincroniza todo. Lo que corre `doctor`, `review`, `sync` | ✅ instalado, 4.4.0 |
| **Gentle Shell** | El producto TUI: workspace agéntico. Antes se llamaba "Gentle Pi" | ❌ falta |
| **gentle-pi** | Nombre viejo del producto Y nombre **actual** del paquete npm/repo | ❌ falta |
| **Pi** | Harness minimalista (pi.dev). No es un modelo. Es el motor | ❌ falta |
| **OpenCode** | Runtime de agente. El entorno principal | ✅ instalado |
| **Cursor** | Editor con Agent. El segundo entorno | ✅ instalado |
| **ODD** | Organic-Driven Development: la metodología de trabajo | ✅ activa por defecto |
| **RDD** | Receipt-Driven Development: el sistema de review por candidate | ✅ on por default |
| **4R** | Las cuatro lentes de review: risk, resilience, readability, reliability | ✅ instaladas |
| **Judgment Day** | Review adversarial con dos jueces ciegos e independientes | ✅ instalada |
| **Engram** | Memoria persistente del agente | ✅ local (cloud ❌) |
| **gga** | Gentleman Guardian Angel: review de código en el hook pre-commit | ✅ 2.10.1 |
| **CodeGraph** | Grafo de conocimiento del código (`codebase-memory-mcp`) | ✅ instalado |

## Las tres capas (y por qué importa)

| Capa | Qué incluye | ¿Requisito? |
|---|---|---|
| **1. Harness** | gentle-ai, skills, Engram, review, ODD | **Sí. Es el 90% del valor** |
| **2. Editor / host** | OpenCode, Cursor, Neovim / LazyVim | No. Elección personal |
| **3. Terminal** | Ghostty, tmux, zellij, rg, fzf | No. Ergonomía |

Alan muestra las tres juntas en los videos, pero solo la primera cambia cómo trabaja el agente. Vos ya tenés la capa 1 completa y sana.

## Tus dos runtimes

Tenés el stack instalado en OpenCode y en Cursor. No son dos stacks: es el mismo, con dos puertas de entrada.

| | OpenCode | Cursor |
|---|---|---|
| Entrada | `opencode` en la raíz del repo | Agent chat |
| ODD | agente default `gentle-orchestrator` | regla `~/.cursor/rules/gentle-ai.mdc` |
| Agentes propios | 2 primarios + 12 subagentes | 3 subagentes de Judgment Day |
| Skills | tool `skill`, automáticas | menú `/`, automáticas |
| Comandos | `/impeccable`, `/skill-creator`, `/skill-registry` | no hay propios; skills con `/` |
| Review 4R | sí | no |
| Judgment Day | sí | sí |
| MCP | `~/.config/opencode/opencode.json` | `~/.cursor/mcp.json` |

El detalle práctico de cada uno está en `06-opencode.md` y `07-cursor.md`. La regla para convivir: un agente por sesión, misma raíz de repo y la memoria de Engram como puente.

## Qué está pasando cuando usás OpenCode

```
escribís un pedido
  → lo recibe gentle-orchestrator (tu agente por defecto)
      → clasifica: ¿chico o grande? ¿riesgo alto?
      → si hace falta, delega a un subagente
      → guarda decisiones en Engram
      → al terminar, corre las lentes de review que correspondan
```

No tenés que memorizar esto. Es para que cuando veas "delegando a gentle-ai-worker" sepas qué está pasando y por qué.

En Cursor el ciclo es el mismo, pero lo dispara la regla global y los subagentes son los nativos de Cursor. Si el Agent no clasifica, explora o verifica, lo primero es confirmar que la regla esté en contexto: el proyecto tiene que estar bajo tu home. El detalle está en `07-cursor.md`.
