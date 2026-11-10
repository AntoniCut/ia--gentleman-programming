# 03 — Cómo funciona el agente por dentro

Este capítulo explica qué pasa entre que escribís un pedido y ves el resultado. No hace falta memorizarlo, pero sin esto no se entiende por qué el agente a veces delega, a veces pregunta, y a veces hace todo solo.

Está escrito sobre OpenCode, que es donde el modelo de agentes se ve completo. En Cursor la entrada es una regla global y los subagentes son otros; el detalle está en `07-cursor.md`.

## Los dos niveles

Gentle-ai declara 14 agentes en tu configuración. Se dividen en dos clases:

| Clase | Quién | Qué hace |
|---|---|---|
| **Primario** | `gentle-orchestrator` (tu default), `gentleman` | Es el que te habla. Decide, coordina y responde |
| **Subagente** | Los otros 12 | Trabajan para el primario. No te hablan a vos |

Vos siempre hablás con el primario. Los subagentes son invisibles salvo que mires qué está pasando.

## Por qué con `Tab` ves 4 y no 14

`Tab` cicla **solo agentes primarios**, y esa lista mezcla dos orígenes que no conviene confundir:

```text
gentle-ai declara .......... 14 agentes
  primarios ................ 2   gentleman, gentle-orchestrator
  subagentes ............... 12  explore, worker, verify, review-*, jd-*
OpenCode suma (visibles) .... 2   build, plan
                            ──
visibles con Tab ........... 4
```

OpenCode además trae built-ins internos (`compaction`, `summary`, `title`) que no aparecen en el selector porque los usa el runtime, no vos.

Los 12 subagentes no aparecen porque no se invocan a mano: los lanza el orquestador con la tool `task`, y en tu config esa tool está cerrada con allow explícito solo para esos 12.

| Agente | Origen | Qué instrucciones carga | Para qué |
|---|---|---|---|
| `gentle-orchestrator` | gentle-ai | ODD completo, reglas de delegación y review | Trabajo real. Es el default (`default_agent`) |
| `gentleman` | gentle-ai | `AGENTS.md` como prompt del agente | Charlar, discutir arquitectura, aprender |
| `build` | OpenCode | `AGENTS.md` global, sin ODD | Cambio puntual que ya sabés hacer |
| `plan` | OpenCode | Igual, con la edición de archivos denegada | Pensar un plan sin tocar código |

Dos detalles que explican casi todas las dudas:

**Los cuatro usan el mismo modelo.** Ningún agente declara `model` en la config: lo que cambia son las instrucciones y los permisos, no el cerebro.

**Solo el orquestador tiene ODD.** Build, Plan y Gentleman no clasifican la tarea, no crean `odd/tasks/`, no delegan y no cierran con línea de riesgo. Si querés la disciplina, es el orquestador.

## El detalle de Plan

Plan viene de fábrica con la edición denegada, justamente para planear sin riesgo de que toque archivos. Pero ese default se aplica **antes** del permiso global y gana la última regla: un `"edit": "*": "allow"` global lo anula en silencio, sin avisar.

En esta máquina está cerrado con un bloque explícito en `~/.config/opencode/opencode.json`:

```json
"agent": {
  "plan": {
    "permission": {
      "bash": "ask",
      "edit": "deny"
    }
  }
}
```

Si algún día ves a Plan editando archivos, ese bloque desapareció.

El costo de `bash: "ask"`: como gana la última regla, los `deny` específicos del permiso global (por ejemplo `git push --force*`) quedan en `ask` para Plan. Sigue pidiendo confirmación, pero ya no los bloquea solo.

## El ciclo de un pedido

```
escribís algo
   ↓
el orquestador clasifica
   ├─ ¿autoriza cambio o es sólo consulta?  → si es consulta, no escribe nada
   ├─ ¿chico o grande?                      → chico: inline. grande: documento de feature
   └─ ¿toca algo de riesgo alto?            → sí: verificación independiente
   ↓
trabaja (inline, o delega a un subagente)
   ↓
verifica
   ↓
responde
```

Nada de esto lo manejás vos. Pero cuando veas "delegando a `gentle-ai-explore`", ya sabés en qué parte del ciclo estás.

## El problema que resuelven los subagentes

Un agente tiene una **ventana de contexto**: una cantidad finita de información que puede tener presente a la vez.

Si le pedís que explore un módulo de 40 archivos, esos archivos se le meten en el contexto. Cuando termina de explorar ya gastó media cabeza en ruido, y le queda menos para pensar en tu problema real.

**Leer ensucia el contexto.**

## Qué es un subagente

Un agente hijo que arranca con el contexto **vacío**.

```
VOS → orquestador → "andá a mapear cómo funciona el auth"
                         ↓
                    subagente (contexto en 0)
                    lee 40 archivos, se ensucia él solo
                         ↓
                    devuelve: "3 archivos, estas líneas"
                         ↓
VOS ← orquestador (contexto intacto, 10 líneas de resumen)
```

El orquestador manda al subagente a ensuciarse y recibe de vuelta un resumen limpio. El ruido se descarta con el subagente.

## La propiedad crítica: contexto vacío

**El subagente no ve tu conversación.** Arranca de cero. No sabe qué venías hablando, no conoce tu código, no sabe qué decisiones tomaste antes.

Dos consecuencias que muerden si no las tenés claras:

1. **Todo lo que necesita hay que pasárselo explícitamente.** Si no le dicen "usá la función `addEntry` que ya existe", va a inventar otra. No por malo: por ignorante.
2. **Lo que devuelve es todo lo que el padre va a saber.** Si el subagente se guarda un detalle, ese detalle se pierde.

Es un jefe que manda a un empleado nuevo a hacer un relevamiento: si no le explica el contexto, vuelve con algo inútil. Y si el empleado no escribe todo en el informe, el jefe nunca se entera.

## Tus 12 subagentes

### Pueden escribir

| Subagente | Para qué |
|---|---|
| `gentle-ai-worker` | Escribe código en un alcance acotado y devuelve evidencia |
| `jd-fix-agent` | Corrige hallazgos confirmados de Judgment Day |

### Sólo leen (verificado en tu config: `edit=deny`, `write=deny`)

| Subagente | Para qué |
|---|---|
| `gentle-ai-explore` | Mapear código y devolver `archivo:línea` |
| `gentle-ai-verify` | Verificar técnicamente un cambio |
| `review-risk` | Lente R1: seguridad, permisos, datos expuestos |
| `review-readability` | Lente R2: nombres, complejidad, intención |
| `review-reliability` | Lente R3: tests, edge cases, regresiones |
| `review-resilience` | Lente R4: fallos, reintentos, degradación |
| `review-refuter` | Refutar hallazgos del review |
| `review-validator` | Validar correcciones puntuales |
| `jd-judge-a` | Juez ciego A de Judgment Day |
| `jd-judge-b` | Juez ciego B de Judgment Day |

Fijate el detalle de diseño: **los revisores no pueden tocar código**, están configurados para no poder. Un revisor que arregla lo que encuentra deja de ser independiente.

## Cuándo se delega y cuándo no

Vos no invocás subagentes. El orquestador decide, y sólo delega cuando se cumple una condición concreta:

| Se delega cuando… | Subagente | Por qué |
|---|---|---|
| entender algo necesita más de ~5 búsquedas seguidas | `gentle-ai-explore` | el ruido de leer no debe quedar en el padre |
| hay 2+ unidades de trabajo independientes, cada una de varios minutos | `gentle-ai-worker` | se pueden hacer en paralelo |
| el cambio toca seguridad, datos irrecuperables, contratos públicos o concurrencia | `gentle-ai-verify` | hace falta un ojo que no escribió el código |
| un candidato de riesgo alto va a review | `review-*` | cuatro lentes independientes |

Si no se cumple ninguna, **trabaja inline**. Un cambio chico no se delega: delegar cuesta más que hacerlo.

Por eso si le pedís "arreglá este typo", no vas a ver ningún subagente. Y si le pedís "armá este proyecto con tests y docs", vas a ver varios.

## Errores comunes

**1. Creer que el subagente sabe lo que hablaste.** No lo sabe. Si el resultado vuelve mal, la causa suele ser un pedido incompleto, no un agente tonto.

**2. Delegar cosas chicas.** Arrancar un subagente cuesta tiempo de lectura. Para un cambio de 20 líneas sale más caro que hacerlo inline.

**3. Pedirle a un revisor que arregle.** Los revisores son read-only a propósito. Si encontró algo, se corrige en otra instancia, y después se revalida.

**4. Leer cada mensaje de delegación como si fuera tuyo.** No lo es: es el padre coordinando. Vos sólo importa el resultado final y, si algo salió mal, el motivo.

## Cómo verificarlo en tu máquina

```bash
node -e 'const c=require(process.env.HOME+"/.config/opencode/opencode.json");
for (const [k,v] of Object.entries(c.agent)) console.log(k, "→", v.mode||"default")'
```

Eso te lista los agentes reales, no los que diga la documentación.
