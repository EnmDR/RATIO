# RATIO (RATIOnal Architecture for Tailored Interaction and Output)

## Especificación 

## Alcance

RATIO es una **guía** que estructura la información configurable por el usuario de Claude en claude.ai., en cinco capas correspondientes a los cinco canales de la plataforma (user preferences, project instructions, style, skills, prompt), cada una con un catálogo de facetas que determina qué instrucciones admite.

## Definiciones

**Capa.** Canal de la plataforma al que se asignan instrucciones. Cada capa tiene una persistencia (en qué conversaciones son efectivas las instrucciones) y una activación (bajo qué condición entran en el contexto de inferencia).

**Faceta.** Categoría temática de instrucciones dentro de una capa. Cada faceta tiene una obligatoriedad. Las instrucciones pueden tomar tres formas lingüísticas posibles: 
- Declarativa: "X es Y".
- Política: "Cuando X, haz Y" o "Haz X de manera Y".
- Restricción: forma preferida "Haz Y en lugar de X". Forma negativa ("No hagas X") reservada para exclusiones que no admiten reformulación positiva.

## Arquitectura de capas

### Tabla de capas

Fundamentada. Depende de Claude. 
| Capa | Descripción | Persistencia | Activación |
|------|-------------|-------------|------------|
| L₁ o user preferences | Instrucciones globales que aplican a todas las conversaciones. | Todas las conversaciones | Siempre activa |
| L₂ o project instructions | Instrucciones que aplican a todas las conversaciones dentro de un proyecto. | Conversaciones del proyecto | Siempre activa |
| L₃ o style | Reglas que regulan la forma lingüística del output agrupadas dentro de una unidad autónoma. | Conversaciones con estilo activo | Siempre activa |
| L₄ o skills | Instrucciones procedurales, reutilizables entre proyectos y contingentes a un tipo de tarea que se activan por coincidencia semántica. | Todas las conversaciones | Condicionalmente activa (coincidencia semántica) |
| L₅ o prompt | Instrucciones efímeras que aplican a un único mensaje. | Mensaje individual | Una inferencia |

### Asignación de facetas por capas

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

Arbitraria. Depende del usuario. 
| Faceta | Descripción | Capa | Obligatoriedad |
|------|-------------|------|----------------|
| F₁ o interlocutor profiles | Información declarativa estable sobre el usuario (preferencias contextuales) y sobre la postura del modelo (preferencias conductuales). | L₁ | Obligatoria |
| F₂ o epistemic policies | Políticas que regulan cómo el modelo gestiona la evidencia, la incertidumbre, así como los errores del usuario. | L₁ | Obligatoria |
| F₃ o global constraints | Restricciones permanentes sobre el output que aplican a toda conversación y todo proyecto. | L₁ | Obligatoria |
| F₄ o domain profile | Identidad disciplinar del proyecto. | L₂ | Obligatoria |
| F₅ o project objective | Resultado que el proyecto debe producir. | L₂ | Obligatoria |
| F₆ o source policies | Políticas de gestión de las fuentes de conocimiento disponibles en el proyecto. | L₂ | Obligatoria |
| F₇ o project constraints | Restricciones persistentes dentro del proyecto que complementan F₃. | L₂ | Opcional |
| F₈ o lexical-semantic rules | Reglas sobre la selección y el uso de unidades léxicas. | L₃ | Obligatoria |
| F₉ o morphosyntactic rules | Reglas sobre la construcción de oraciones. | L₃ | Obligatoria |
| F₁₀ o pragmatic-tonal rules | Reglas sobre la actitud discursiva del modelo. | L₃ | Obligatoria |
| F₁₁ o discourse rules | Reglas sobre la cohesión y la coherencia a nivel supraoracional. | L₃ | Obligatoria |
| F₁₂ o style examples | Muestras de output que instancian simultáneamente F₈–F₁₁. | L₃ | Obligatoria |
| FS₁ o SKILL.md | | L₄ | Obligatoria |
| FS₂ o scripts | | L₄ | Opcional |
| FS₃ o references | | L₄ | Opcional |
| FS₄ o assets | | L₄ | Opcional |
| F₁₃ o task specification | Operación concreta que el modelo ejecuta en respuesta al mensaje. | L₅ | Obligatoria |
| F₁₄ o task scope | Alcance temático de la tarea. | L₅ | Opcional |
| F₁₅ o task constraints | Restricciones efímeras que aplican exclusivamente al mensaje actual y que complementan F₃ y F₇. | L₅ | Opcional |
| F₁₆ o task examples | Muestras de output. | L₅ | Opcional |

### Asignación de instrucciones por facetas 

**L₁**

```
¿Define quién es el usuario o cómo se posiciona el modelo ante él?
├── Sí → F₁ (Interlocutor profiles)
└── No
    ├── ¿Regula cómo el modelo trata la evidencia, la incertidumbre o el error?
    │   ├── Sí → F₂ (Epistemic policies)
    │   └── No → F₃ (Global constraints)
```

**L₂ o project instructions**

```
¿Es una restricción específica del proyecto?
├── Sí → F₇ (Project constraints)
└── No
    ├── ¿Define el dominio, sus convenciones o el rol del modelo en el proyecto?
    │   ├── Sí → F₄ (Domain profile)
    │   └── No
    │       ├── ¿Define el resultado global esperado del proyecto?
    │       │   ├── Sí → F₅ (Objective)
    │       │   └── No → F₆ (Source policies)
```

**L₃**

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

**L₅**

```
¿Es un ejemplo de output para esta tarea?
├── Sí → F₁₆ (Task examples)
└── No
    ├── ¿Es una restricción puntual para este mensaje?
    │   ├── Sí → F₁₅ (Task constraints)
    │   └── No
    │       ├── ¿Define qué acción ejecuta el modelo?
    │       │   ├── Sí → F₁₃ (Task specification)
    │       │   └── No → F₁₄ (Scope)
```
