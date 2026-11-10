# 07 — Cursor: el mismo stack, otra puerta

Cursor no tiene un selector de agentes de Gentle AI. El stack entra por otro lado: una regla global que se aplica sola, skills que se descubren automáticamente, tres subagentes de Judgment Day y los MCP.

Si venís de OpenCode, la traducción es simple: donde allá elegís `gentle-orchestrator`, acá la regla `gentle-ai.mdc` hace ese papel. El detalle de OpenCode está en `06-opencode.md`.

## Lo que tenés instalado

| Pieza | Qué hay | Dónde vive |
|---|---|---|
| Regla global | Persona + Engram + orquestador + routing ODD | `~/.cursor/rules/gentle-ai.mdc` |
| Skills | 12 + `_shared` en `~/.cursor/skills` + las de `~/.agents/skills` | `~/.cursor/skills/`, `~/.agents/skills/` |
| Subagentes | 3: `jd-judge-a`, `jd-judge-b`, `jd-fix-agent` | `~/.cursor/agents/` |
| MCP | Engram, Context7, Playwright, CodeGraph + los que ya tenías | `~/.cursor/mcp.json` |
| Protocolo Engram | Copia del protocolo de memoria | `~/.cursor/engram-memory-protocol.md` |
| Comandos propios | Ninguno; se usan skills con `/` | — |

Cursor además trae sus propias skills y comandos en `~/.cursor/skills-cursor/` (`/review`, `/create-skill`, `/create-subagent`, etc.). No son de Gentle AI: sirven, pero no son el review 4R ni el ODD.

## Arrancar

1. Abrí el repo **dentro de tu home** (`/home/antonydev/...`).
2. Abrí el chat de **Agent** (no Ask, no Inline Edit).
3. Escribí el pedido como en OpenCode.

No hay `default_agent` ni un `Tab` que cambie de agente de Gentle. La regla entra sola y el Agent decide qué skills y subagentes usar. Si el proyecto está fuera del home, la regla global no se carga; el detalle está abajo.

## La regla global: cómo funciona y por qué no la ves

El archivo es `~/.cursor/rules/gentle-ai.mdc`. Tiene frontmatter:

```yaml
---
description: Gentle AI rules
alwaysApply: true
---
```

Eso significa que se inyecta en cada chat. Pero hay dos cosas que confunden:

**1. Puede que no aparezca en `Customize → Rules` ni en `Settings → Rules`.** Cursor no siempre lista la carpeta global `~/.cursor/rules`; solo las reglas del proyecto y las User Rules. Que no la veas en la UI no significa que no se aplique: es una limitación conocida, no un error de instalación.

**2. Cursor busca `.cursor/rules` hacia arriba desde el proyecto.** Por eso la regla global se aplica si el proyecto está dentro de tu home. Si abrís un repo en `/mnt`, `/opt` o por SSH remoto fuera del home, no la encuentra.

Si trabajás fuera del home, tenés dos salidas:

- Copiar o symlinkear la regla a `.cursor/rules/gentle-ai.mdc` en el proyecto.
- Armar un plugin local en `~/.cursor/plugins/local/gentle-ai/` con `rules/gentle-ai.mdc` y `.cursor-plugin/plugin.json`. Es la forma soportada de tener reglas file-based globales y que además se vean en la UI.

Para cloud agents, la regla tiene que estar en el repo: `~/.cursor/rules` no se sincroniza.

No edites `~/.cursor/rules/gentle-ai.mdc` a mano: es un archivo administrado por `gentle-ai` y un `sync` puede pisarlo. Las preferencias personales van en User Rules (texto plano, global) o en reglas del proyecto.

## ODD en Cursor

La regla contiene el mismo ODD de 7 pasos, la misma clasificación por tamaño, la misma lista de riesgo y los mismos triggers de delegación. En la práctica:

- Un pedido chico se resuelve inline.
- Un pedido grande crea `odd/tasks/<feature>.md` y su espejo en Engram.
- La implementación se agrupa en unidades y se commitea en la rama de feature.
- Al cerrar, reporta lo verificado, lo que falló y el próximo paso.

La diferencia es quién ejecuta:

| Pieza | OpenCode | Cursor |
|---|---|---|
| Orquestador | agente `gentle-orchestrator` | la regla `gentle-ai.mdc` |
| Exploración / escritura / verificación | `gentle-ai-explore`, `worker`, `verify` | subagentes nativos de Cursor |
| Review 4R | `review-*` | no existe |
| Judgment Day | `jd-*` | `jd-*` |

Si el Agent no sigue ODD, lo primero es confirmar que la regla esté cargada. Preguntale "¿qué reglas tenés cargadas?" o "¿cuál es el primer paso de ODD?". Si no la ve, revisá la ubicación del proyecto.

## Skills

Escribí `/` en el chat de Agent y aparece el menú. Algunas que vas a usar:

| Skill | Para qué |
|---|---|
| `/judgment-day` | Review adversarial con dos jueces ciegos |
| `/chained-pr` | Partir un cambio grande en PRs revisables |
| `/work-unit-commits` | Planear commits por unidad |
| `/impeccable` | Diseño y auditoría de interfaces |
| `/skill-creator` | Crear o actualizar una skill |
| `/skill-registry` | Reconstruir el índice de skills |

Cursor lee `~/.cursor/skills/` y `~/.agents/skills/`. La segunda es la carpeta compartida con OpenCode: si creás una skill ahí, la ven los dos. Las skills se descubren solas; el Agent puede usarlas sin que escribas `/`.

No confundas las skills de Gentle con las de Cursor: `/review` es el review de Cursor, no las 4 lentes. Para el 4R, OpenCode.

## Subagentes

En `~/.cursor/agents/` hay tres:

| Subagente | Rol |
|---|---|
| `jd-judge-a` | Juez ciego A, read-only |
| `jd-judge-b` | Juez ciego B, read-only |
| `jd-fix-agent` | Corrige solo los hallazgos confirmados |

Se usan al correr Judgment Day. El Agent los delega automáticamente; vos no los lanzás a mano. El flujo tiene como máximo dos rondas de fix y re-juzgamiento.

No están `gentle-ai-explore`, `worker`, `verify` ni las lentes `review-*`. Para delegación genérica, Cursor usa sus subagentes nativos. Para el review 4R, OpenCode.

## MCP

La configuración global está en `~/.cursor/mcp.json`. Los servidores de Gentle son:

- `engram` — memoria persistente.
- `context7` — documentación de librerías.
- `playwright` — automatización de navegador.
- `codebase-memory-mcp` — grafo del código.

También están los que ya tenías: `filesystem`, `git`, `mysql`, `postman`, `supabase`, `time`, `console-ninja`. Verificalos en **Settings → MCP**; si un servidor no arranca, Cursor muestra el error y el comando que intentó.

Engram usa `--tools=agent`, igual que en OpenCode. Context7 es remoto. Playwright se instala con `npx`. CodeGraph apunta al binario `~/.local/bin/codebase-memory-mcp`.

## Review

En Cursor el review disponible es **Judgment Day**:

```
/judgment-day
```

o pedile "hacé judgment day sobre este cambio". Dos jueces ciegos, un fix agent, máximo dos rondas. No hay refuter ni validator en Cursor.

Para el review RDD con las 4 lentes (risk, resilience, readability, reliability), usá OpenCode. No es que Cursor no pueda: es que los agentes `review-*` no están instalados ahí.

Y otra vez: `/review` de Cursor es otra cosa. No lo confundas con el review de Gentle.

## Memoria y CodeGraph

Engram es la misma base en los dos entornos. Si guardaste una decisión en OpenCode, Cursor la puede recuperar; si no la trae sola, pedile "buscá en memoria X".

La regla de higiene es la misma: abrí el proyecto desde la raíz del repo. Si abrís desde una carpeta padre, el proyecto detectado puede ser otro y las memorias se parten.

CodeGraph también es el mismo MCP. Si el proyecto no está indexado:

```bash
gentle-ai codegraph init --cwd .
```

## Cheat sheet

| Quiero... | Hago... |
|---|---|
| Usar el stack | Abrir el repo bajo el home → Agent chat |
| Invocar una skill | `/` y elegir |
| Review adversarial | `/judgment-day` |
| Partir un PR | `/chained-pr` |
| Planear commits | `/work-unit-commits` |
| Ver los MCP | Settings → MCP |
| Ver la memoria | `engram tui` |
| Chequear el stack | `gentle-ai doctor` |

## Errores comunes

**1. "No veo la regla en Customize".** Es normal. `~/.cursor/rules` no se lista en la UI, pero se aplica si el proyecto está bajo el home.

**2. "ODD no se activa".** Proyecto fuera del home, cloud agent, o la regla no está en contexto. Movelo bajo el home o agregá `.cursor/rules/gentle-ai.mdc` al proyecto.

**3. "No veo `gentle-orchestrator`".** Cursor no tiene ese agente. El orquestador es la regla.

**4. "No veo las lentes `review-*`".** Solo están en OpenCode. En Cursor usá `/judgment-day`.

**5. "`/review` no hace 4R".** Es el review de Cursor. Para 4R, OpenCode.

**6. "Engram guarda en otro proyecto".** Abrí desde la raíz del repo.

**7. "Edité la regla y se volvió a cambiar".** Es un archivo administrado. Usá reglas del proyecto o User Rules.

**8. "El Agent no sigue la regla".** Confirmá que la ve; si el proyecto está fuera del home, no la va a cargar.

## Cómo convivir con OpenCode

- Mismo repo, misma memoria, mismo grafo, mismo git.
- Un agente por sesión. No corras OpenCode y Cursor sobre el mismo working tree a la vez; si necesitás paralelo, usá worktrees.
- OpenCode para el circuito completo con review 4R. Cursor para editar y para Judgment Day.
- Después de actualizar: `gentle-ai sync`, `gentle-ai doctor`, y revisá a mano `~/.cursor/rules`, `~/.cursor/skills` y `~/.cursor/agents`.
- Si creás una skill para los dos, ponela en `~/.agents/skills` y corré `gentle-ai skill-registry refresh`.

## Verificación

```bash
ls ~/.cursor/rules
ls ~/.cursor/skills
ls ~/.cursor/agents
jq '.mcpServers | keys' ~/.cursor/mcp.json
gentle-ai doctor
```

En Cursor, abrí un repo bajo el home y preguntá: "¿Qué reglas y skills tenés cargados?". Si la regla aparece y el menú `/` muestra las skills de Gentle, está todo bien.
