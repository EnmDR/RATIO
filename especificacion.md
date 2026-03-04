# RATIO (RATIOnal Architecture for Tailored Interaction and Output)

## Especificación 

## Alcance

RATIO estructura la información configurable por el usuario de Claude en claude.ai. Organiza esa información en cinco capas correspondientes a los cinco canales de la plataforma (user preferences, project instructions, style, skills, prompt), cada una con un catálogo de facetas que determina qué instrucciones admite.

## Definiciones

**Capa.** Canal de la plataforma al que se asignan instrucciones. Cada capa tiene una persistencia (en qué conversaciones son efectivas las instrucciones) y un modo de activación (bajo qué condición entran en el contexto de inferencia).

**Faceta.** Categoría temática de instrucciones dentro de una capa. 

**Configurable.** Aspecto del comportamiento del modelo que admite especificación por el usuario en la instanciación. Tres formas lingüísticas posibles:

- Declarativo: "X es Y".
- Política: "Cuando X, haz Y" o "Haz X de manera Y".
- Restricción: forma preferida "Haz Y en lugar de X". Forma negativa ("No hagas X") reservada para exclusiones que no admiten reformulación positiva.

## Arquitectura de capas

### Tabla de capas

| Capa | Interfaz | Persistencia | Activación |
|------|----------|-------------|------------|
| L₁ · User preferences | Ajustes → Preferencias personales | Todas las conversaciones | Siempre activa |
| L₂ · Project instructions | Proyecto → Instrucciones del proyecto | Conversaciones del proyecto | Siempre activa |
| L₃ · Style | Personalización → Estilos | Conversaciones con estilo activo | Siempre activa |
| L₄ · Skills | Personalización → Skills | Todas las conversaciones | Condicional (coincidencia semántica) |
| L₅ · Prompt | Mensaje del usuario | Mensaje individual | Una inferencia |

### Asignación de capas

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


## Arquitectura de facetas

### Tabla de facetas

| ID | Nombre | Capa | Obligatoriedad |
|----|--------|------|----------------|
| F₁ | Interlocutor profiles | L₁ | Obligatoria |
| F₂ | Epistemic policies | L₁ | Obligatoria |
| F₃ | Global constraints | L₁ | Obligatoria |
| F₄ | Domain profile | L₂ | Obligatoria |
| F₅ | Project objective | L₂ | Obligatoria |
| F₆ | Source policies | L₂ | Obligatoria |
| F₇ | Project constraints | L₂ | Opcional |
| F₈ | Lexical-semantic rules | L₃ | Obligatoria |
| F₉ | Morphosyntactic rules | L₃ | Obligatoria |
| F₁₀ | Pragmatic-tonal rules | L₃ | Obligatoria |
| F₁₁ | Discourse rules | L₃ | Obligatoria |
| F₁₂ | Style examples | L₃ | Obligatoria |
| FS₁ | Trigger | L₄ | Obligatoria por skill |
| FS₂ | Procedure | L₄ | Obligatoria por skill |
| FS₃ | Resources | L₄ | Opcional por skill |
| FS₄ | Skill Examples | L₄ | Opcional por skill |
| F₁₃ | Task specification | L₅ | Obligatoria |
| F₁₄ | Task scope | L₅ | Obligatoria |
| F₁₅ | Task constraints | L₅ | Opcional |
| F₁₆ | Task examples | L₅ | Opcional |

### Asignación de facetas

**L₁ · User preferences**

```
¿Define quién es el usuario o cómo se posiciona el modelo ante él?
├── Sí → F₁ (Interlocutor profiles)
└── No
    ├── ¿Regula cómo el modelo trata la evidencia, la incertidumbre o el error?
    │   ├── Sí → F₂ (Epistemic policies)
    │   └── No → F₃ (Global constraints)
```

**L₂ Project instructions**

```
¿Es una restricción específica del proyecto?
├── Sí → F₇ (Project constraints)
└── No
    ├── ¿Define el dominio, sus convenciones o el rol del modelo en el proyecto?
    │   ├── Sí → F₄ (Domain profile)
    │   └── No
    │       ├── ¿Define el resultado global esperado del proyecto?
    │       │   ├── Sí → F₅ (Objective)
    │       │   └── No → F₆ (Knowledge policies)
```

**L₃ Style**

```
¿Es una muestra concreta de output?
├── Sí → F₁₂ (Style examples)
└── No
    ├── ¿Regula qué palabras se usan o se evitan?
    │   ├── Sí → F₈ (Lexical-semantic rules)
    │   └── No
    │       ├── ¿Regula cómo se construyen las oraciones?
    │       │   ├── Sí → F₉ (Morphosyntactic rules)
    │       │   └── No
    │       │       ├── ¿Regula la actitud discursiva (formalidad, tono, cortesía)?
    │       │       │   ├── Sí → F₁₀ (Pragmatic-tonal rules)
    │       │       │   └── No → F₁₁ (Discourse rules)
```

**L₅ Prompt**

```
¿Es un ejemplo de input-output para esta tarea?
├── Sí → F₁₆ (Task examples)
└── No
    ├── ¿Es una restricción puntual para este mensaje?
    │   ├── Sí → F₁₅ (Task constraints)
    │   └── No
    │       ├── ¿Define qué acción ejecuta el modelo?
    │       │   ├── Sí → F₁₃ (Task specification)
    │       │   └── No → F₁₄ (Scope)
```
### L₁ User preferences

Instrucciones globales que aplican a todas las conversaciones con independencia del proyecto, el estilo o la tarea.

**F₁ — Interlocutor profiles.** Información declarativa estable sobre el usuario (preferencias contextuales) y sobre la postura del modelo (preferencias conductuales).

**F₂ — Epistemic policies.** Políticas que regulan cómo el modelo gestiona la evidencia, la incertidumbre y los errores del usuario.

**F₃ — Global constraints.** Restricciones permanentes sobre el output que aplican a toda conversación y todo proyecto.

### L₂ Project instructions

Instrucciones que aplican a todas las conversaciones dentro de un proyecto.

**F₄ — Domain profile.** Identidad disciplinar del proyecto.

**F₅ — Project Objective.** Resultado que el proyecto debe producir.

**F₆ — Source policies.** Políticas de gestión de las fuentes de conocimiento disponibles en el proyecto.

**F₇ — Project constraints** *(opcional)***.** Restricciones persistentes dentro del proyecto que complementan F₃.


### L₃ Style

Reglas que regulan la forma lingüística del output. Cada estilo es una unidad autónoma. Un mismo usuario puede mantener múltiples estilos y alternar entre ellos sin modificar L₁ ni L₂.

**F₈ — Lexical-semantic rules.** Reglas sobre la selección y el uso de unidades léxicas.

**F₉ — Morphosyntactic rules.** Reglas sobre la construcción de oraciones.

**F₁₀ — Pragmatic-tonal rules.** Reglas sobre la actitud discursiva del modelo.

**F₁₁ — Discourse rules.** Reglas sobre la cohesión y la coherencia a nivel supraoracional.

**F₁₂ — Style examples.** Muestras de output que instancian simultáneamente F₈–F₁₁.


### L₄ Skills

Instrucciones procedurales, reutilizables entre proyectos y contingentes a un tipo de tarea que se activan por coincidencia semántica: 

```
skill-name/
├── SKILL.md          # obligatorio
├── scripts/          # opcional
├── references/       # opcional
└── assets/           # opcional
```

### L₅ Prompt

Instrucciones efímeras que aplican a un único mensaje.

**F₁₃ — Task specification.** Operación concreta que el modelo ejecuta en respuesta al mensaje.

**F₁₄ — Task scope.** Alcance temático de la tarea.

**F₁₅ — Task constraints** *(opcional)***.** Restricciones efímeras que aplican exclusivamente al mensaje actual.

**F₁₆ — Task examples** *(opcional)***.** Pares input-output que ejemplifican la tarea del mensaje. Se instancia cuando la tarea es ambigua o novedosa y la combinación de otras capas no especifica suficientemente el output.
