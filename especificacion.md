
# RATIO (RATIOnal Architecture for Tailored Interaction and Output)
## Especificaciones

## §1 Alcance

RATIO estructura la información configurable por el usuario de Claude en claude.ai. Organiza esa información en cinco capas correspondientes a los cinco canales de la plataforma (user preferences, project instructions, style, skills, prompt), cada una con un catálogo de facetas que determina qué instrucciones admite.

## §2 Definiciones

**Capa.** Canal de la plataforma al que se asignan instrucciones. Cada capa tiene una persistencia (en qué conversaciones son efectivas las instrucciones) y un modo de activación (bajo qué condición entran en el contexto de inferencia).

**Faceta.** Categoría temática de instrucciones dentro de una capa. Es la unidad organizativa del framework. No se comunica al modelo: es una herramienta para que el usuario decida dónde poner cada instrucción.

**Configurable.** Aspecto del comportamiento del modelo que admite especificación por el usuario. Tres formas lingüísticas posibles:

- Declarativo: "X es Y".
- Política: "Cuando X, haz Y" o "Haz X de manera Y".
- Restricción: forma preferida "Haz Y en lugar de X". Forma negativa ("No hagas X") reservada para exclusiones que no admiten reformulación positiva.

## §3 Arquitectura de capas

### 3.1 Tabla de capas

| Capa | Interfaz | Persistencia | Activación |
|------|----------|-------------|------------|
| L₁ · User preferences | Ajustes → Preferencias personales | Todas las conversaciones | Siempre activa |
| L₂ · Project instructions | Proyecto → Instrucciones del proyecto | Conversaciones del proyecto | Siempre activa |
| L₃ · Style | Personalización → Estilos | Conversaciones con estilo activo | Siempre activa |
| L₄ · Skills | Personalización → Skills | Todas las conversaciones | Condicional (coincidencia semántica) |
| L₅ · Prompt | Mensaje del usuario | Mensaje individual | Una inferencia |

### 3.2 · Asignación de capas

```
¿Cambia entre mensajes?
├── Sí → L₅
└── No
    ├── ¿Cambia entre proyectos?
    │   ├── Sí → L₂
    │   └── No
    │       ├── ¿Es un procedimiento contingente a un tipo de tarea?
    │       │   ├── Sí → L₄
    │       │   └── No
    │       │       ├── ¿Regula la forma lingüística del output?
    │       │       │   ├── Sí → L₃
    │       │       │   └── No → L₁
```

### 3.3 Asignación de facetas

Una vez determinada la capa, la faceta se identifica con la pregunta discriminante de cada una (§4). El procedimiento es:

```
1. Determinar la capa con el árbol de §3.2.
2. Recorrer las preguntas discriminantes de las facetas de esa capa.
3. Asignar a la primera faceta cuya pregunta se responda afirmativamente.
4. Si ninguna pregunta se responde afirmativamente, revisar si la capa es correcta.
```

## §4 Catálogo de facetas

### 4.1 Tabla de facetas

| ID | Nombre | Capa | Obligatoriedad |
|----|--------|------|----------------|
| F₁ | Context | L₁ | Obligatoria |
| F₂ | Epistemic policies | L₁ | Obligatoria |
| F₃ | Global constraints | L₁ | Obligatoria |
| F₄ | Domain profile | L₂ | Obligatoria |
| F₅ | Objective | L₂ | Obligatoria |
| F₆ | Knowledge policies | L₂ | Obligatoria |
| F₇ | Project constraints | L₂ | Opcional |
| F₈ | Lexical-semantic rules | L₃ | Obligatoria |
| F₉ | Morphosyntactic rules | L₃ | Obligatoria |
| F₁₀ | Pragmatic-tonal rules | L₃ | Obligatoria |
| F₁₁ | Discourse rules | L₃ | Obligatoria |
| F₁₂ | Style examples | L₃ | Obligatoria |
| FS₁ | Trigger | L₄ | Obligatoria por skill |
| FS₂ | Procedure | L₄ | Obligatoria por skill |
| FS₃ | Resources | L₄ | Opcional por skill |
| FS₄ | Examples | L₄ | Opcional por skill |
| F₁₃ | Task specification | L₅ | Obligatoria |
| F₁₄ | Scope | L₅ | Obligatoria |
| F₁₅ | Task constraints | L₅ | Opcional |
| F₁₆ | Task examples | L₅ | Opcional |


### L₁ User preferences

Instrucciones globales que aplican a todas las conversaciones con independencia del proyecto, el estilo o la tarea.

**F₁ — Context**

Información declarativa estable sobre el usuario y sobre la postura del modelo.

**F₂ — Epistemic policies**

Políticas que regulan cómo el modelo gestiona la evidencia, la incertidumbre y los errores del usuario. 

**F₃ — Global constraints**

Restricciones permanentes sobre el output que aplican a toda conversación y todo proyecto.

### L₂ · Project instructions

Instrucciones que aplican a todas las conversaciones dentro de un proyecto. 

**F₄ — Domain profile**

Identidad disciplinar del proyecto.

**F₅ — Objective**

Resultado que el proyecto debe producir.

**F₆ — Knowledge policies**

Políticas de gestión de las fuentes de conocimiento disponibles en el proyecto.

**F₇ — Project constraints** *(opcional)*

Restricciones persistentes dentro del proyecto que complementan F₃.

### L₃ · Style

Conjunto de reglas que regulan la forma lingüística y visual del output. 

**F₈ — Lexical-semantic rules**

Reglas sobre la selección y el uso de unidades léxicas.

**F₉ — Morphosyntactic rules**

Reglas sobre la construcción de oraciones.

**F₁₀ — Pragmatic-tonal rules**

Reglas sobre la actitud discursiva del modelo.

**F₁₁ — Discourse rules**

Reglas sobre la cohesión y la coherencia a nivel supraoracional.

**F₁₂ — Style examples**

Muestras de output que instancian simultáneamente F₈–F₁₂.

### L₄ · Skills

Instrucciones procedurales, reutilizables entre proyectos y contigenes a un tipo de tarea que se activan por coincidencia semántica.

```
skill-name/
├── SKILL.md          # obligatorio
├── scripts/          # opcional
├── references/       # opcional
└── assets/           # opcional
```

**FS₁ — Trigger**

Activación de la skill en el campo `description` del frontmatter YAML de `SKILL.md`. 

**FS₂ — Procedure**

Instrucciones en el cuerpo de `SKILL.md` tras el frontmatter. 

**FS₃ — Resources** *(opcional)*

Archivos adicionales en el directorio de la skill. 

**FS₄ — Examples** *(opcional)*

Pares input-output que demuestran el resultado esperado de la skill.

### L₅ · Prompt

Instrucciones efímeras que aplican a un único mensaje. 

**F₁₃ — Task specification**

Operación concreta que el modelo ejecuta en respuesta al mensaje.

**F₁₄ — Scope**

Alcance temático de la tarea.

**F₁₅ — Task constraints** *(opcional)*

Restricciones efímeras que aplican exclusivamente al mensaje actual.

**F₁₆ — Task examples** *(opcional)*

Pares input-output que ejemplifican la tarea del mensaje.

*Condición de instanciación.* Se instancia cuando la tarea es ambigua o novedosa y la combinación de otras capas no especifica suficientemente el output.
