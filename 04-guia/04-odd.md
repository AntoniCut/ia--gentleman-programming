# 04 — ODD: cómo trabaja el agente

ODD es **la metodología** que el agente sigue por defecto. No se activa, no se invoca, no hay comando. Corre en cada pedido que hacés.

## Qué significa

**Organic Driven Development.** La palabra "organic" es literal: el flujo crece según el tamaño de lo que pediste. Un typo no genera ceremonia; una feature de tres días sí.

## El problema que resuelve

Un agente sin metodología hace **cualquier cosa que le parezca razonable**. Eso produce tres desastres clásicos:

1. **Te escribe código sin entender el problema.** Empieza por la solución porque es lo más fácil de generar.
2. **Sobrescribe cosas que no debía tocar.** Sin alcance definido, todo el repo es su jurisdicción.
3. **Te dice "listo" sin haber verificado nada.** Porque nadie le pidió pruebas.

ODD es el conjunto de reglas que hace que no pase eso.

## Los 7 pasos

Corren en este orden, en cada pedido:

| # | Paso | Qué significa en la práctica |
|---|---|---|
| 1 | **Autorizar** | Primero decide si pediste un cambio o sólo una explicación. Si preguntás "¿cómo funciona X?", NO escribe nada |
| 2 | **Explorar** | Mira el código existente antes de proponer. Proporcional al pedido |
| 3 | **Resolver incertidumbre** | Si hay una decisión de producto real, te pregunta **una** cosa y espera. Sólo una |
| 4 | **Clasificar** | Decide si la tarea es chica o grande (ver abajo) |
| 5 | **Documentar antes de escribir** | Si es grande, crea el documento de feature **antes** de tocar código |
| 6 | **Implementar tarea por tarea** | Cada tarea con su check. Se marca `hecho` sólo con evidencia observada |
| 7 | **Cerrar** | Reporta lo verificado, lo que falló y el próximo paso |

El paso 1 es el que más te va a sorprender si venís de otros agentes: **preguntar no autoriza escribir.** Podés preguntar cómo funciona algo y el agente no va a tocar un archivo. Eso es ODD funcionando.

## Chico o grande: el resume test

La clasificación no se hace contando archivos ni líneas. Se hace con una sola pregunta:

> **Si la sesión se corta ahora, ¿alguien puede terminar esto con tu pedido original y un `git diff`?**

| Respuesta | Tamaño | Consecuencia |
|---|---|---|
| Sí | **Chica** | Trabaja inline. Sin documento, sin commits de unidad, sin ceremonia |
| No | **Grande** | Documento de feature, commits por unidad, seguimiento |

Contar archivos, comandos o tests **nunca** decide el tamaño. Un cambio de 300 líneas en un archivo entendido es chico. Un cambio de 10 líneas en un sistema que no entendés es grande.

## El documento de feature

Para trabajo grande se crea automáticamente **uno** en `odd/tasks/<nombre>.md`, con orden fijo:

```
Encabezado     → objetivo, rama, estrategia de entrega, runner
## Specs       → S1, S2, S3... con criterios de aceptación y checks
## Tasks       → una línea por tarea: ID, S# vinculadas, ruta, commit
## Log         → L1 = tu pedido original, textual. Después correcciones y evidencia
```

Tres cosas que tenés que entender de ese documento:

**Los `S#` citan tus palabras textuales.** Si dijiste "el error debe decir `usuario no encontrado`", eso va literal, no parafraseado. Las paráfrasis pierden intención y el agente termina implementando otra cosa.

**El `## Log` va al final a propósito.** Mientras el documento crece, el log queda abajo. Así quien lo lee (o un subagente) llega primero a lo estable.

**`L1` es tu pedido original, sin editar.** Es la fuente de verdad cuando alguien pregunta "¿esto era lo que se pidió?".

Tenés la plantilla lista en `02-plantillas/feature-odd.md`.

## Riesgo alto: la lista

Algunos cambios no se pueden tratar como "chicos" aunque parezcan chicos. Estos son los seis casos:

1. **Datos irrecuperables** — migraciones, borrar datos existentes, cambiar formatos persistidos
2. **Seguridad** — autenticación, permisos, credenciales, secretos, sandbox
3. **Contratos públicos** — API pública, flags de CLI, formatos de configuración, exports
4. **Concurrencia** — locks, condiciones de carrera, orden asíncrono
5. **Entrega o entorno** — instaladores, release, CI, deploy, dependencias
6. **Sin test que lo cubra** — si ningún test atraparía una regresión

Cuando un cambio toca uno de estos, se cierra con una línea de riesgo:

```
Risk: item 2 (toca validación de permisos de admin)
```
o
```
Risk: none
```

Esa línea no es burocracia: es lo que decide si hace falta un verificador independiente.

## Test-first es el default

Cuando hay un test determinista que se puede escribir y un resultado esperado claro:

```
RED    → escribís el test, lo ves fallar
GREEN  → implementás hasta que pase
REFACTOR → limpiás con el test en verde
```

Si no se puede (no hay runner, es documentación, no hay forma de testear), **se explica la excepción** y se corren checks proporcionales igual. Lo que no se permite es inventar un resultado.

Un detalle fino: por cada comando o opción existente que el cambio toca, se agrega **un test que pruebe que su comportamiento anterior sigue igual**. Eso es lo que evita que arreglar A rompa B en silencio.

## Unidades de trabajo

Para trabajo grande, el agente agrupa cambios en **unidades revisables** de ~400 líneas y commitea cada una en la rama de feature.

Ojo con esto, porque se malinterpreta: **las 400 líneas son una heurística de planificación, no un límite.** Si la solución correcta son 600 líneas coherentes, se hacen las 600 y se explica por qué. Nunca se recorta un test ni se borran líneas en blanco para "entrar en el número".

Una unidad cuyo riesgo es alto va en **su propio commit**, para que el review la pueda tomar sola.

Y esto es importante: **push, PR y merge no son parte de ODD.** Nunca. Son decisiones tuyas.

## Qué ves vos en la práctica

**Pedido chico:**
```
VOS: "cambiá el texto del botón a 'Guardar cambios'"
AGENTE: [lo hace, corre el check, responde]
```
Nada de documento. Nada de commits raros.

**Pedido grande:**
```
VOS: "agregá autenticación con JWT"
AGENTE: "Creé odd/tasks/auth-jwt.md con 6 tareas. ¿Arranco por S1?"
         ↑ una línea, antes de escribir código
```

## ODD no es RDD

Esta confusión es universal, así que la dejo explícita:

| | ODD | RDD |
|---|---|---|
| Qué es | La **metodología** de trabajo | El **sistema de review** |
| Cuándo | Siempre, en cada pedido | Sólo en candidatos de riesgo alto |
| Se apaga | No | Sí: `gentle-ai review mode disable` |
| Qué produce | Documento de feature, tests, commits | Un veredicto sobre un cambio congelado |

ODD es cómo trabajás. RDD es cómo se revisa lo que hiciste. Son cosas distintas que suenan parecido — igual que `gentle-ai` y `Gentle Shell`.

## Errores comunes

**1. Pedir "tracking" o "que planifique".** No hace falta. Esa es justamente la gracia: no tenés que acordarte de invocar nada.

**2. Creer que un pedido chico no pasa por ODD.** Pasa. Sólo que el paso 4 lo clasifica como chico y no genera artefactos.

**3. Enojarte porque pregunta en vez de hacer.** Si preguntó, es porque hay una decisión de producto que no le corresponde tomar. Es una feature, no un bug.

**4. Marcar tareas a mano como hechas.** El checkbox lo marca el agente con evidencia observada. Si lo marcás vos, rompés la cadena de prueba.

## Dónde verlo funcionando

El documento de feature se crea en el repo donde estás trabajando, no en este workspace:

```
tu-proyecto/odd/tasks/nombre-feature.md
```

Y una copia espejo en Engram, bajo el tema `odd/<nombre-feature>/tasks`, para poder retomar en otra sesión. De eso trata el capítulo siguiente.
