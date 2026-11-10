# Plantilla — Documento de feature ODD

> Copiar a `odd/tasks/<nombre-feature>.md` dentro del proyecto, o a `03-proyectos/<proyecto>/odd/tasks/`.
> El orden es fijo: primero lo estable (Specs, Tasks), al final lo que crece (Log).
> Este archivo es la especificación que leen los subagentes. Se lee hasta `## Log`.

---

**Objetivo:** <una línea: qué tiene que existir cuando esto termine>
**Rama:** <feature/nombre> · **Entrega:** ask-on-risk · **Runner:** <pnpm test / go test ./...>

## Specs

Requisitos numerados. Cada uno con su alcance, criterio de aceptación y check.

- **S1** — <requisito>. Las cadenas, mensajes de error, comandos y ejemplos del usuario van **textuales**, sin parafrasear.
  - Aceptación: <cómo se comprueba>
  - Check: <comando o test concreto>

- **S2** — <requisito>.
  - Aceptación: <cómo se comprueba>
  - Check: <comando o test concreto>

> No agregar requisitos que el usuario no pidió. Si algo es una suposición, marcarlo como suposición.

## Tasks

Una línea por tarea. ID estable, specs vinculadas, ruta, estado y evidencia.

| ID | Specs | Ruta | Estado | Evidencia |
|---|---|---|---|---|
| T1 | S1 | inline | pendiente | — |
| T2 | S2 | delegado | pendiente | — |

Estados válidos: `pendiente`, `en curso`, `hecho`, `bloqueado`.
Se marca `hecho` solo con el resultado observado y su check corrido.

## Log

El libro. Lo más nuevo abajo. `L1` es siempre el pedido original del usuario, textual.

- **L1** — <pedido original del usuario, verbatim>
- **L2** — <correcciones del usuario, textuales, con fecha>
- **L3** — <decisiones, evidencia de verificación, próximo paso>
