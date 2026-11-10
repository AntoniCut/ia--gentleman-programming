# 05 — Engram: la memoria

## El problema

Cada sesión el agente arranca en cero. No se acuerda de nada: ni de las decisiones de ayer, ni de qué probaste y no funcionó, ni de por qué elegiste esa librería.

Vos tampoco te acordás de todo. Y lo que anotaste en un archivo, el agente no lo lee salvo que se lo indiques.

## Qué es Engram

Una base de datos local donde el agente guarda lo que vale la pena recordar, y consulta cuando le sirve. Corre en tu máquina, sin internet, en `~/.engram/engram.db`.

Se conecta por MCP (Model Context Protocol), y en tu configuración ya está activo con el perfil `agent`.

## Quién lo usa

**El agente, no vos.** Esto es lo primero que hay que entender, porque mucha gente abre Engram, lo ve vacío de su lado, y concluye que no funciona.

El agente tiene herramientas para eso:

| Herramienta | Qué hace |
|---|---|
| `mem_save` | Guarda una conclusión, decisión o descubrimiento |
| `mem_search` | Busca en la memoria por keywords |
| `mem_context` | Trae el historial reciente de la sesión |
| `mem_get_observation` | Lee el contenido completo de una memoria por ID |

Vos no las invocás. El agente las invoca cuando corresponde: decisiones de arquitectura, bugs resueltos, descubrimientos no obvios, convenciones.

## Qué guarda y qué no

**Sí guarda** cosas caras de reconstruir:

- Por qué se eligió una librería y qué se descartó
- Un bug y su causa raíz
- Una convención del equipo
- Una decisión de arquitectura
- Un gotcha que te hizo perder tres horas

**No debería guardar** lo que ya está en el código o en git. Si el nombre de una función está en el archivo, guardarlo en memoria es ruido.

La regla tácita: si se puede releer del repo, no va a memoria. Si costó pensarlo, sí.

## Cómo lo usás vos

```bash
engram tui              # abrir la interfaz, buscar, filtrar, ver todo
engram search "auth"    # buscar sin abrir la TUI
engram projects list    # ver los proyectos y cuántas memorias tiene cada uno
```

`engram tui` es el que más vale la pena. Es la forma más rápida de ver qué estuvo guardando el agente cuando no mirabas.

## El problema real: los proyectos

Engram agrupa las memorias por **proyecto**, y lo detecta solo desde el repo git. Ahí está el detalle que te va a morder.

Así se ve tu máquina hoy:

```
Projects (5):
  curso-practico-wordpress-con-ia   50 obs    20 sessions
  antonydev                         12 obs     2 sessions
  wordpress                          6 obs     2 sessions
  curso-wordpress-con-ia             4 obs     1 session
  gentle-ai                          0 obs     1 session
```

Dos cosas para leer ahí:

**1. Hay duplicados.** `curso-practico-wordpress-con-ia` y `curso-wordpress-con-ia` son el mismo curso. `antonydev` y `wordpress` probablemente también. Las memorias quedaron partidas en dos lados.

**2. Engram promueve carpetas hijas.** Si estás parado en una carpeta que no es repo pero contiene una subcarpeta que sí lo es, toma esa como proyecto. Eso explica el caso raro de arriba y significa que **si no cuidás desde dónde abrís el agente, tus memorias se van a otro lado**.

Arreglo de duplicados:

```bash
engram projects consolidate     # merge interactivo
```

Y la regla de higiene: **abrí el agente desde la raíz del repo del proyecto en el que trabajás**, no desde una carpeta padre ni desde el home.

## El espejo de ODD

Cuando ODD crea un documento de feature, guarda una copia en Engram bajo el tema:

```
odd/<nombre-feature>/tasks
```

Eso permite retomar el trabajo en otra sesión aunque el archivo se pierda. El agente lee la copia de memoria y el archivo real, y reconcilia.

Ojo: **las dos copias no se escriben de forma atómica.** Si una falla, la otra queda desactualizada. Por eso al retomar se leen las dos y se compara. No es un detalle teórico: es la razón por la que retomar una feature nunca es "leer el archivo y listo".

## Compartir con el equipo

Las memorias viven locales. Para compartirlas por git:

```bash
engram sync             # exporta a .engram/ en el repo
engram sync --import    # importa al clonar
```

Commitear `.engram/` hace que quien clona herede el contexto acumulado. Para onboarding es demoledor: el que entra arranca con el conocimiento del equipo en vez de preguntando todo.

## Cloud: no configurado

```
Cloud status: not configured (no effective server URL)
```

Engram Cloud sincroniza memorias entre máquinas sin pasar por git. Está **sin configurar** en tu máquina, y no lo necesitás todavía. Si algún día trabajás en dos máquinas y no querés commitear `.engram/`, ahí sí.

Antes de configurarla, corré `engram cloud status` para ver qué le falta.

## Errores comunes

**1. Abrirlo y verlo vacío.** No está vacío: lo que ves es la vista de tu proyecto actual. Cambiá de proyecto en la TUI, o mirá `engram projects list`.

**2. Guardar a mano lo mismo que ya anotaste en un `.md`.** Dos lugares para el mismo conocimiento es cómo terminás sin saber cuál es el bueno.

**3. Asumir que se acuerda de todo.** No es una grabación de la conversación. Guarda lo que decide guardar, y eso depende de lo que le pediste y de si lo consideró relevante.

**4. Abrir el agente desde una carpeta padre.** Las memorias se van a otro proyecto. Es el error más silencioso de todos.

## Chequeo rápido

```bash
engram projects list     # ¿tus memorias están donde creés?
engram tui               # ¿qué sabe el agente de tu trabajo?
```

Si esos dos no te cierran, el resto de Engram es ruido. Arreglá primero el orden de los proyectos.
