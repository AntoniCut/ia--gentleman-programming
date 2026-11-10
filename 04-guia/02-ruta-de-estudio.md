# 02 — Ruta de estudio

El error a evitar: ver los cuatro videos seguidos y no tocar nada. Vas a terminar con más vocabulario y la misma confusión.

La regla de esta ruta es una sola: **cada etapa termina en algo que hiciste, no en algo que viste.**

---

## Etapa 0 — Despejar el vocabulario

**Tiempo:** 20 min · **Sin videos**

- [ ] Leer `00-mapa-mental.md` completo
- [ ] Leer `01-glosario.md` de corrido, sin memorizar
- [ ] Volver al mapa y explicar en voz alta la diferencia entre `gentle-ai`, `Gentle Shell` y `Pi`

**Criterio de salida:** podés decir en una frase qué es cada uno de esos tres sin mirar el archivo. Si no podés, releer el mapa. No avances.

---

## Etapa 1 — Entender qué es un harness

**Tiempo:** 30 min

- [ ] Ver `Building pi in a World of Slop — Mario Zechner` (18 min)
      https://www.youtube.com/watch?v=RjfbvDXpFls
- [ ] Anotar en Engram, no en un papel: qué problema resuelve un harness minimalista

**Por qué este primero y no el de Alan:** porque es el autor de Pi explicando el porqué. Sin esto, "minimalista" suena a limitación en vez de a decisión de diseño.

**Criterio de salida:** podés explicar por qué Pi *no* trae subagentes a propósito, y no como carencia.

---

## Etapa 2 — Practicar con lo que YA tenés

**Tiempo:** 1 a 2 h · **Acá se juega todo**

No instales nada todavía. Ya tenés el 90% del stack funcionando.

- [ ] Abrir `03-proyectos/` y crear un proyecto choto pero real: algo de 50 líneas
- [ ] Pedirle a OpenCode que lo haga, **sin dar instrucciones de metodología**
- [ ] Mirar qué hizo: ¿exploró antes de escribir? ¿te preguntó algo? ¿verificó?
- [ ] Ahora repetir con un pedido que fuerce el flujo: una feature chica con specs claras
- [ ] Ver el documento de feature que generó en `odd/tasks/`
- [ ] Repetir el mismo pedido en Cursor y comparar: la regla global debería disparar el mismo ODD. Si no lo hace, revisá `07-cursor.md`
- [ ] Leer `06-opencode.md` y `07-cursor.md` para el detalle de cada entorno

```bash
cd 03-proyectos/mi-experimento
opencode
```

**Por qué acá:** ODD y los subagentes ya están activos. No hace falta instalar Gentle Shell para verlos funcionar. La mayoría de la gente instala la TUI primero y no entiende por qué "no pasa nada".

**Criterio de salida:** viste con tus ojos un subagente trabajar y un documento de feature aparecer. Si no pasó, el problema es el pedido, no la herramienta — reformulá con specs y criterio de aceptación.

---

## Etapa 3 — El ecosistema completo

**Tiempo:** 42 min de video + práctica

- [ ] Ver `El PADRE de todos los CURSOS de IA` (42 min)
      https://www.youtube.com/watch?v=5Q7jV8TpMXA
- [ ] Anotar los 3 conceptos que no tenías en el glosario y agregarlos

**Ojo:** este video es de mayo y habla de "Gentle Pi". Es el mismo producto que hoy se llama Gentle Shell. No te confundas, no son dos cosas.

**Criterio de salida:** podés mapear cada concepto del video a una pieza que ya tenés instalada.

---

## Etapa 4 — Instalar Gentle Shell

**Tiempo:** 30 min · **Recién acá**

- [ ] `npm i -g gentle-pi`
- [ ] `gentle-shell` (crea su home aislado en `~/.gentle-shell/agent`)
- [ ] Recorrer la TUI: barra de estado, árbol de archivos, git graph
- [ ] Ver `Te muestro la EVOLUCIÓN de Gentle Shell` (2 h — ponelo de fondo, mirá los primeros 40 min)
      https://www.youtube.com/watch?v=KTRoVbZeo3s

**Criterio de salida:** lo usaste para una tarea real, no para pasear por la interfaz.

---

## Etapa 5 — Lo avanzado

**Tiempo:** abierto

- [ ] Entender el review: `gentle-ai review mode status`
- [ ] Probar las 4 lentes en un cambio con riesgo real
- [ ] Probar Judgment Day en un cambio que te dé miedo: en OpenCode pidiendo "hacé judgment day"; en Cursor con `/judgment-day`
- [ ] Configurar Engram Cloud si querés memoria sincronizada
- [ ] Usar `gga` como hook pre-commit

Bloques de la video que ya viste que valen la pena (timestamps verificados):
`7:10` cuándo compactar · `10:21` guardar en Engram antes de compactar · `24:10` subagentes y worktrees

---

## Regla de oro

Si en cualquier etapa sentís que "no entendés nada", **volvé a la Etapa 0**. El 90% de las veces el problema no es la pieza nueva: es que perdiste el mapa.

No agregues herramientas para resolver confusión. Agregar una pieza más a un mapa que no entendés la empeora siempre.
