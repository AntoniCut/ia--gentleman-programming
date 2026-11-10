# 01 — Glosario

Orden alfabético. Una línea por término, más por qué te importa cuando corresponde.

Cada entrada dice si el término es **concepto**, **pieza instalada** o **pieza que falta**.

---

**AGENTS.md**
Archivo de instrucciones que el agente lee al arrancar. En `~/.config/opencode/AGENTS.md` está tu persona y las reglas del ecosistema. *Concepto — ya lo tenés.*

**Agente (runtime)**
El programa con el que hablás: OpenCode, Claude Code, Gentle Shell. Ejecuta el loop de pensar → usar herramienta → responder. *Concepto.*

**Candidato (candidate)**
La foto inmutable de un cambio que se manda a review: un commit o un slice de PR. El review nunca mira el working tree vivo, mira el candidato congelado. *Concepto — clave para entender RDD.*

**CodeGraph**
Grafo del código del proyecto. Permite preguntar "quién llama a esta función" sin leer archivos. Herramienta `codebase-memory-mcp`. *Pieza instalada.*

**Compactación**
Cuando la conversación se acerca al límite de contexto, el agente resume lo viejo para seguir. Peligroso: se pierde información. Por eso conviene guardar en Engram **antes** de compactar. *Concepto.*

**Engram**
Memoria persistente entre sesiones. Lo que el agente aprende hoy, lo puede recuperar mañana. Corre local; la nube (`Engram Cloud`) está sin configurar. *Pieza instalada.*

**gga (Gentleman Guardian Angel)**
Review de código automático en el hook `pre-commit` de git. Corre antes de que el commit exista. Versión 2.10.1. *Pieza instalada.*

**gentle-ai**
El CLI del ecosistema. Instala, sincroniza y verifica la configuración de los agentes. Comandos: `install`, `sync`, `update`, `doctor`, `review`, `codegraph`, `skill-registry`. *Pieza instalada — 4.4.0.*

**Gentle Shell**
La TUI agéntica: workspace con agentes en paralelo, tareas en vivo, diffs, memoria y permisos. Corre sobre Pi. Antes se llamaba "Gentle Pi". *Pieza que falta.*

**gentle-pi**
Dos cosas a la vez, de ahí el lío: (1) el nombre viejo del producto Gentle Shell; (2) el nombre **actual** del paquete npm y del repo. Si buscás en npm, es `gentle-pi`. *Pieza que falta.*

**Harness**
El "arnés" que le da estructura al modelo: herramientas, permisos, contexto, flujo. Un modelo sin harness es un chat; con harness es un agente que trabaja. *Concepto — es LA palabra del ecosistema.*

**Judgment Day**
Review adversarial con dos jueces ciegos e independientes (A y B) que no se comunican entre sí, más rondas acotadas de corrección. Se activa sólo si lo pedís explícitamente. *Pieza instalada.*

**MCP (Model Context Protocol)**
Protocolo para conectar el agente con herramientas externas. Tenés 4 activos: Engram, CodeGraph, Context7, Playwright. *Concepto.*

**ODD (Organic-Driven Development)**
La metodología de trabajo de Gentle: explorar antes de escribir, dimensionar la tarea, documentar features grandes, verificar antes de cerrar. Es el flujo que corre por defecto. *Concepto — activo.*

**Persona**
El "carácter" con el que te habla el agente: tono, idioma, criterio. En tu caso es un arquitecto senior que te corrige con argumentos. *Concepto.*

**Pi**
Harness minimalista de pi.dev (Earendil Inc., MIT). Trae lo mínimo y deja afuera a propósito: subagentes, plan mode, permisos, to-dos. Es el motor sobre el que corre Gentle Shell. *Pieza que falta.*

**Provider**
El proveedor del modelo: Anthropic, OpenAI, Google, Groq, OpenRouter, Ollama. Es lo que se factura. El harness es lo que lo usa. *Concepto.*

**RDD (Receipt-Driven Development)**
El sistema de review: cada candidato con riesgo alto pasa por lentes independientes antes de darse por terminado. Está `on` por default. El switch es tuyo: `gentle-ai review mode enable|disable|status`. *Concepto — activo.*

**4R (las cuatro lentes)**
Los cuatro ángulos de review: **r**isk (seguridad), **r**esilience (fallos), **r**eadability (claridad), **r**eliability (comportamiento y tests). Sólo corre la lente que corresponde al cambio. *Pieza instalada.*

**SDD (Spec-Driven Development)**
Escribir la especificación antes del código. Primo hermano de ODD; ODD agrega el dimensionado de tarea y la verificación por riesgo. *Concepto.*

**Skill**
Paquete de instrucciones y herramientas que se carga sólo cuando hace falta, sin inflar el contexto. Tenés 13: `judgment-day`, `chained-pr`, `work-unit-commits`, etc. *Pieza instalada.*

**Subagente**
Agente hijo con contexto fresco al que el orquestador le delega una unidad de trabajo. No ve tu conversación; sólo lo que se le pasa. *Pieza instalada — 14 agentes.*

**TDD (Test-Driven Development)**
Test primero, rojo, después implementación hasta verde. ODD lo aplica por defecto cuando hay un runner determinista disponible. *Concepto.*

**Token**
La unidad en la que se mide el contexto y el costo. No es lo mismo que "palabra". *Concepto.*

**Worktree**
Copia de trabajo separada de un mismo repo git. Se usa para que dos agentes en paralelo no se pisen los archivos. *Concepto.*
