# RATIO

*Guía personal para organizar el contexto que recibe un modelo de lenguaje.*

| | |
|---|---|
| **Autor** | Enmanuel Damas Reyes |
| **Fecha** | 12 de septiembre de 2026 |
| **Estatus** | Final. Sin sucesor. |
| **Sustituye a** | v3.0 (2026-07-04) y todas las versiones anteriores |

---

## Estatus del documento

Este documento es terminal. Las secciones 1 a 5 constituyen el núcleo y no se revisan. El apéndice está fechado y caduca por diseño: describe los canales de una interfaz concreta en un momento concreto, y esa interfaz cambia sin avisar.

La regla de mantenimiento está en la cláusula final. Léela antes de modificar nada.

---

## 1. Qué es

RATIO es una guía para decidir dónde colocar cada pieza de configuración cuando una interfaz ofrece varios canales para dar contexto a un modelo.

No es software. No es un archivo que el modelo cargue. No es una técnica de formulación de instrucciones: esas operan dentro de un mensaje, y aquí se decide qué información va en qué canal, de los cuales el mensaje es uno. El modelo nunca sabe que RATIO existe; recibe el resultado de haberlo aplicado.

**Esquema e instanciación.** RATIO define dónde va cada tipo de instrucción. El contenido concreto de esas instrucciones para un usuario o un dominio es una instanciación. El esquema es estable; las instanciaciones se reescriben sin tocar el esquema. Si una modificación exige cambiar este documento, casi siempre es que se está escribiendo instanciación en el sitio equivocado.

---

## 2. El núcleo

Tres hechos sobre cómo un modelo consume contexto. No dependen de qué producto se use ni de qué canales exponga.

**Un canal condicional no sostiene una garantía.** Si un canal se carga solo cuando algo lo dispara, todo lo que contenga es inexistente en los turnos en que no se dispara. Una convención que debe cumplirse siempre no puede vivir ahí, por mucho que temáticamente parezca pertenecer a esa tarea. Este es el criterio que más errores de asignación evita, y el único que produce fallos silenciosos: la instrucción no falla, simplemente no está.

**La posición dentro de la ventana modula el peso efectivo.** Lo que ocupa el principio y el final de la ventana pesa más que lo que queda en el centro, y el efecto se agrava conforme la ventana crece. De aquí salen dos consecuencias prácticas: dentro de cada canal, lo prioritario va primero; y el mensaje, por ocupar el extremo final, funciona como mecanismo de rescate de lo que se haya diluido en las capas permanentes.

**El coste de una instrucción lo paga el usuario, no el modelo.** La capacidad de seguimiento de los modelos actuales excede con holgura cualquier configuración razonable de un usuario individual. Lo que no escala es la capacidad de quien mantiene la configuración para recordar qué hay dentro, detectar contradicciones y explicarse un comportamiento raro. La restricción es de verificación, no de capacidad.

---

## 3. El criterio de asignación

Una sola pregunta, y sus respuestas están ordenadas de más persistente a más efímera:

1. ¿Debe estar activa en todos los turnos, en cualquier conversación? Canal global.
2. ¿Debe estar activa en todos los turnos de un dominio o proyecto? Canal de dominio.
3. ¿Basta con que se active cuando la tarea la invoque, y su ausencia intermitente es tolerable? Canal condicional.
4. ¿Vale solo para este turno? Mensaje.

La opción 3 exige una pregunta adicional antes de elegirla: qué pasa si no se dispara. Si la respuesta es que el resultado queda mal de una forma que importa, la instrucción sube a 1 o 2 aunque parezca ligada a la tarea. Una convención tipográfica o un estilo de citación "parecen" propios del entregable y no lo son.

**El criterio es funcional, no ontológico.** Cuando dos canales admiten una instrucción igual de bien, la elección es indiferente y no merece deliberación. No existe una frontera natural entre estructura y formato, ni entre procedimiento y convención. Lo que importa es que la instrucción esté en algún sitio donde se active cuando hace falta, con la prioridad adecuada. Perseguir una asignación ontológicamente correcta es tiempo que no mejora ningún resultado.

---

## 4. Cómo se redacta una instrucción

**Atomicidad.** Una instrucción por frase. Las compuestas se parten.

**Formulación positiva.** "Haz Y en lugar de X" antes que "no hagas X". La forma negativa se reserva para exclusiones que no admiten reformulación. El principio está respaldado por la documentación de Anthropic y por experiencia propia; cualquier explicación sobre lo que ocurre dentro del modelo al procesar una prohibición es especulación y no pertenece aquí.

**Especificidad operativa.** Una instrucción ordena una acción sobre la información, no constata que la información existe. "Las convenciones están en el archivo X" no es una instrucción. "Carga y aplica el archivo X antes de producir el output" sí lo es. Los modelos recientes interpretan literalmente y no infieren peticiones que no se han hecho: un archivo nombrado pero no ordenado puede sencillamente no cargarse.

**Parsimonia.** Antes de añadir una instrucción, comprobar que el comportamiento por defecto es inadecuado. Una instrucción que reproduce lo que el modelo ya hace bien no cambia el output y sí consume presupuesto de mantenimiento.

---

## 5. Cómo se diseña un override

Cuando una capa más específica debe anular a una más general, la anulación es fiable solo si la contradicción es cuantificable y explícita: un máximo contra un mínimo, un formato contra otro formato, una inclusión contra una exclusión. El modelo identifica la contradicción y aplica la precedencia.

Cuando las dos instrucciones son cualitativas y tiran en direcciones opuestas sin contradecirse de forma detectable ("sé conciso" contra "incluye todos los mecanismos"), no hay anulación: hay transacción, y el resultado es impredecible. La corrección no consiste en reforzar una de las dos, sino en reescribirlas para que la contradicción se vuelva explícita, o en eliminar una.

---

## 6. Lo que este documento no gobierna

Trabajar desde una interfaz de consumo impone límites que conviene tener escritos para no volver a chocarse con ellos:

La precedencia real entre canales la determina la plataforma. Se observa, no se configura, y puede cambiar entre versiones del modelo.

El prompt de sistema que inyecta el proveedor no es visible ni modificable, contribuye con más instrucciones que toda la configuración del usuario junta, y es la fuente principal de tensiones con las instrucciones propias. Cuando algo se comporta de forma inexplicable, esta es la primera hipótesis.

La memoria derivada de conversaciones anteriores se inyecta en cada inferencia sin ser instrucción autorada. Puede reforzar o contradecir lo configurado. Si un comportamiento depende de que esté en un estado determinado, hay que comprobarlo con la memoria en ese estado.

Los parámetros de muestreo no son accesibles.

---

## Apéndice fechado: claude.ai, septiembre de 2026

**Este apéndice caduca.** Describe una interfaz concreta y no forma parte del núcleo.

Canales autorados disponibles, de más persistente a más efímero:

| Canal | Alcance | Activación |
|---|---|---|
| Preferencias de usuario | Todas las conversaciones | Siempre |
| Instrucciones de proyecto | Conversaciones del proyecto | Siempre dentro del proyecto |
| Skills | Todas las conversaciones | Condicional, por coincidencia semántica o invocación explícita |
| Mensaje | Un turno | Ese turno |

Notas vigentes en esta fecha:

El canal de estilo ha desaparecido; sus contenidos se reparten entre las preferencias de usuario, si deben regir siempre, y las skills, si son propios de un entregable.

Dentro de una skill activa, los archivos de referencia entran en contexto solo cuando el archivo principal ordena cargarlos. Una convención que deba aplicarse a todo output de la skill va en el cuerpo del archivo principal, o en un archivo de referencia que se cargue mediante orden explícita e incondicional. Nombrar la ubicación no basta.

Mi instanciación actual ocupa tres bloques en las preferencias de usuario: perfil de interlocutor, políticas epistémicas y convenciones de salida. Los proyectos se corresponden con asignaturas. Hay una skill para elaboración de material de estudio. El resto va en el mensaje.

---

## Cláusula de caducidad

Cuando algo de este documento parezca necesitar revisión, la acción depende de qué parte sea:

Si es el apéndice, porque la plataforma ha cambiado, se reescribe el apéndice. El núcleo no se toca y la fecha del documento no cambia.

Si es una instanciación concreta, se cambia la instanciación. Este documento no la contiene y no se entera.

Si es el núcleo, la acción correcta es dejar de usar el documento, no revisarlo. Un núcleo de tres hechos y cuatro principios que deje de ser útil no se arregla con un quinto principio; significa que la herramienta ha terminado su vida, y eso es un final aceptable.

No hay versión siguiente. La existencia de una v5.0 sería, por sí sola, la prueba de que el documento dejó de servir a su propósito y pasó a ser su propio propósito.
