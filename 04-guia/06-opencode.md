# 06 — OpenCode: el día a día

Este capítulo es práctico. Si todavía no tenés claro qué es un orquestador, un subagente o una skill, leé primero `03-como-funciona-el-agente.md`. Acá vemos cómo se usa todo eso en OpenCode, que es el entorno donde el stack está completo.

## Qué es OpenCode en este stack

OpenCode es el **runtime**: el programa que ejecuta al agente. Gentle AI no lo reemplaza; lo configura. Le instala la persona, los agentes, las skills, los comandos y los MCP.

Es el entorno donde tenés el paquete completo:

- Los 14 agentes de Gentle AI.
- Las 4 lentes de review (RDD).
- Los comandos de OpenCode.
- La TUI y los temas.

En Cursor vas a encontrar casi lo mismo, pero con otra puerta de entrada y algunas piezas menos. El detalle está en `07-cursor.md`.

## Lo que tenés instalado

| Pieza | Qué hay | Dónde vive |
|---|---|---|
| Agentes | 14: 2 primarios (`gentle-orchestrator`, `gentleman`) + 12 subagentes | `~/.config/opencode/opencode.json` |
| Skills | 13 + `_shared` | `~/.config/opencode/skills/` |
| Comandos | 3: `/impeccable`, `/skill-creator`, `/skill-registry` | `~/.config/opencode/commands/` |
| MCP | 4: CodeGraph, Context7, Engram, Playwright | `~/.config/opencode/opencode.json` |
| Review | 4 lentes + refuter + validator + Judgment Day | agentes `review-*` y `jd-*` |

OpenCode además suma dos agentes propios que no son de Gentle AI: `build` y `plan`. Por eso con `Tab` ves cuatro y no dos.

## Arrancar

```bash
cd /ruta/al/repo
opencode
```

También podés hacer `opencode .` o `opencode /ruta/al/repo`.

**Abrí siempre desde la raíz del repo.** Engram detecta el proyecto desde el directorio de trabajo y el remoto de git. Si abrís desde una carpeta padre, las memorias pueden terminar en otro proyecto. Es el error más común y el más difícil de detectar después.

Al arrancar, el agente por defecto es `gentle-orchestrator`. No tenés que elegirlo: ya está configurado en `default_agent`.

## Los 4 agentes que ves con Tab

`Tab` cicla **solo agentes primarios**. Los 12 subagentes no aparecen porque no se invocan a mano.

| Agente | Origen | Cuándo usarlo |
|---|---|---|
| `gentle-orchestrator` | Gentle AI | Trabajo real. Es el default y el único que aplica ODD completo |
| `gentleman` | Gentle AI | Charlar, discutir arquitectura, aprender. No está pensado para implementar |
| `build` | OpenCode | Un cambio puntual que ya sabés hacer y no necesita ceremonia |
| `plan` | OpenCode | Planificar sin tocar archivos. Tiene la edición denegada |

Si querés la disciplina de ODD, usá `gentle-orchestrator`. `gentleman` es para conversar y no implementa. `build` y `plan` son atajos de OpenCode: no clasifican la tarea, no crean `odd/tasks/`, no delegan y no cierran con línea de riesgo.

Ojo con `plan`: su permiso de edición se puede anular sin aviso si un `"edit": "allow"` global gana la última regla. En esta máquina hay un bloque explícito en `~/.config/opencode/opencode.json` para que no pase. Si alguna vez lo ves editando, ese bloque desapareció.

## Qué pasa cuando escribís un pedido

```
escribís
  → lo recibe gentle-orchestrator
      → ¿autoriza cambio o es solo consulta?
      → ¿chico o grande?
          → chico: trabaja inline
          → grande: documento de feature + commits por unidad
      → ¿toca riesgo alto? → verificador independiente
      → guarda decisiones en Engram
      → review si el candidate lo pide
      → te responde con lo verificado y lo pendiente
```

No memorices el diagrama. Sirve para que cuando veas "delegando a `gentle-ai-explore`" sepas en qué parte del ciclo estás.

## Subagentes

Los 12 subagentes son invisibles en la TUI, pero hacen el trabajo pesado:

| Subagente | Rol |
|---|---|
| `gentle-ai-explore` | Exploración read-only. Mapea archivos y devuelve un resumen |
| `gentle-ai-worker` | Escritura acotada. Implementa una unidad con tests y docs |
| `gentle-ai-verify` | Verificación read-only. Re-corre checks e inspecciona el diff |
| `review-risk` | Lente de seguridad, permisos, datos y contratos |
| `review-resilience` | Lente de fallos, retries, timeouts y degradación |
| `review-readability` | Lente de nombres, complejidad y mantenibilidad |
| `review-reliability` | Lente de tests, edge cases y regresiones |
| `review-refuter` | Refuta hallazgos del review |
| `review-validator` | Valida checks puntuales del review |
| `jd-judge-a`, `jd-judge-b` | Jueces ciegos de Judgment Day |
| `jd-fix-agent` | Corrige solo los hallazgos confirmados |

No los lances a mano. El orquestador usa la tool `task`, que en esta configuración está cerrada con allow explícito solo para esos 12. Si necesitás una revisión adversarial, pedí "hacé judgment day" y el orquestador carga la skill y arma el flujo.

## Comandos y skills

Los comandos son pocos y explícitos:

| Comando | Qué hace |
|---|---|
| `/impeccable` | Diseño y auditoría de interfaces: UX, jerarquía, tipografía, accesibilidad |
| `/skill-creator` | Crea o actualiza una skill |
| `/skill-registry` | Reconstruye `.atl/skill-registry.md` |

Las skills no se invocan con `/`. El agente las carga solo, con la tool `skill`, cuando el pedido coincide. Si querés forzar una, pedí "usá la skill X".

Las skills viven en `~/.config/opencode/skills/`, pero OpenCode también lee `~/.agents/skills/` y `~/.claude/skills/`. Para una skill que quieras compartir con Cursor, `~/.agents/skills/` es la carpeta común.

## Review

Hay dos sistemas distintos, y conviene no mezclarlos.

**RDD (4R).** Revisa un candidato congelado con las lentes que correspondan. Está encendido por default, pero es tuyo: lo apagás y lo prendés cuando quieras.

```bash
gentle-ai review mode status          # ver si está on u off
gentle-ai review start --focus risk   # arrancar un review enfocado en riesgo
```

También podés enfocar en `resilience`, `readability` o `reliability`. El review pide consentimiento por candidato y no autoriza commit, push ni merge.

**Judgment Day.** Dos jueces ciegos e independientes, un agente de fix y como máximo dos rondas. Se dispara con la skill `judgment-day`; pedile "hacé judgment day" o "juzgá este cambio".

Si el review está apagado, no lo prendas por inercia: es una decisión tuya. `gentle-ai review mode status` te dice el estado sin cambiarlo.

## Memoria y CodeGraph

Engram es automático: el agente guarda decisiones, bugs y descubrimientos sin que se lo pidas. Vos podés mirar lo que guardó:

```bash
engram tui                 # interfaz completa
engram search "auth"       # búsqueda rápida
engram projects list       # proyectos y cantidad de memorias
```

CodeGraph es el otro MCP que vale la pena tener presente. Si el grafo del proyecto está vacío o desactualizado:

```bash
gentle-ai codegraph init --cwd .
```

Context7 y Playwright también están disponibles como tools del agente, pero no se usan desde la terminal.

## Cheat sheet

| Quiero... | Hago... |
|---|---|
| Arrancar el agente | `cd repo && opencode` |
| Cambiar de agente primario | `Tab` |
| Ver los agentes y sus permisos | `jq '.agent' ~/.config/opencode/opencode.json` |
| Ver los comandos | `ls ~/.config/opencode/commands` |
| Ver las skills | `ls ~/.config/opencode/skills` |
| Ver el estado del review | `gentle-ai review mode status` |
| Arrancar un review de riesgo | `gentle-ai review start --focus risk` |
| Ver la memoria | `engram tui` |
| Chequear el stack | `gentle-ai doctor` |

## Errores comunes

**1. "Con Tab solo veo 4 agentes".** Es correcto. Los otros 12 son subagentes y los lanza el orquestador.

**2. "No veo a `gentle-ai-worker`".** No se invoca a mano. Si querés que delegue, pedí una tarea que lo justifique.

**3. "Plan está editando archivos".** Un `"edit": "allow"` global le ganó al deny. Revisá el bloque de `plan` en `opencode.json`.

**4. "Engram guardó en otro proyecto".** Abriste el agente desde una carpeta padre. Abrí desde la raíz del repo.

**5. "El review no corre".** Mirá `gentle-ai review mode status`. Si está off, es una decisión tuya, no un bug.

**6. "Creé una skill y no aparece".** Corré `/skill-registry` y revisá que el `SKILL.md` esté en una carpeta que OpenCode lea.

**7. "El comando `/x` no existe".** Solo hay tres comandos propios. Las skills no son comandos.

## Verificación

```bash
gentle-ai doctor
ls ~/.config/opencode/skills
ls ~/.config/opencode/commands
jq '.agent | keys' ~/.config/opencode/opencode.json
```

Si `gentle-ai doctor` da 8/8 y las rutas existen, el entorno está sano. El resto es práctica.
