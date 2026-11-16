# Análisis de impacto de SC-001

---

## Solicitud que se analiza

Referencia a la solicitud: `sc-001-bloqueo-por-inasistencia.md`, solicitada por la dupla de revisión (actuando como dueña de la clínica) el 15 de noviembre de 2026.

## Traducción a términos del sistema

La petición de "conteo de inasistencias" significa que la clase `Usuario` necesita un nuevo atributo numérico para almacenar las faltas. La "cita a la que no se presenta" significa que la máquina de estados de la clase `Cita` necesita un nuevo estado llamado `NoAsistio`. Finalmente, el "bloqueo automático" significa que el CU-01 (Agendar cita) requiere una nueva validación (una guarda) al inicio de su diagrama de secuencia para impedir el paso si el contador del `Usuario` es mayor o igual a 3.

## Impacto directo

Lo que hay que modificar obligatoriamente para que el cambio exista.

| Artefacto | Unidad | Qué hay que cambiar |
|---|---|---|
| Diagrama de clases | 4 | Agregar el atributo `inasistencias: int` a la clase `Usuario` y el método `sumarInasistencia()`. |
| Máquina de estados | 4 | Formalizar la transición del estado `Agendada` al estado `NoAsistio` en la clase `Cita`. |
| Diagrama de secuencia | 4 | Agregar un fragmento `alt` al inicio del CU-01 verificando si `inasistencias < 3` antes de mostrar los horarios. |

## Impacto indirecto

Lo que se rompe o deja de ser cierto por culpa del cambio directo.

| Artefacto | Unidad | Por qué se ve afectado |
|---|---|---|
| Requisito RF-xx | 2 | Se debe crear el RF-014 (Penalización por inasistencia) para darle trazabilidad legal a este bloqueo. |
| Caso de uso | 2 | El CU-01 (Agendar cita) se ve afectado porque gana una nueva precondición y un flujo alterno (Rechazo por cuenta bloqueada). |
| Registro de decisión ADR-001 | 3 | Sin impacto. La arquitectura del monolito no se ve afectada. |
| Diagrama de contenedores C4 | 3 | Sin impacto. Siguen siendo los mismos contenedores web, API y BD. |

## Lo que queda sin tocar

Todo el módulo de Catálogo y Ventas (CU-03, CU-04, clase `Producto`, `Pedido` y la Pasarela de Pagos) queda completamente intacto. El módulo de perfiles de `Mascota` tampoco sufre alteraciones.

## Riesgos

El riesgo principal es que los usuarios bloqueados intenten evadir el castigo creando una nueva cuenta con otro correo electrónico. Si la clínica no verifica físicamente la identidad, la medida podría volverse inútil a largo plazo. 

## Alternativas consideradas

1. **Opción A (La solicitada):** Bloqueo de cuenta tras 3 inasistencias. Es fácil de programar, solo requiere un contador entero en la base de datos y un condicional en el frontend.
2. **Opción B (Alternativa económica):** Obligar a pagar una "tarifa de reserva" no reembolsable desde la página web usando la pasarela de pagos al momento de agendar. 

**Me inclino por la Opción A**, porque la Opción B nos obligaría a meter la pasarela de pagos (que ahorita es exclusiva de productos físicos) en el módulo de la agenda, afectando gravemente los casos de uso, las bases de datos y la complejidad arquitectónica (y rompiendo el alcance original).

## Estimación de esfuerzo

| Concepto | Esfuerzo |
|---|---|
| Cambios de diseño y documentación | 2 horas |
| Implementación | 4 horas |
| Pruebas | 2 horas |
| **Total** | 8 horas |

**De dónde sale este número:** Lo comparé con el tiempo que me tomó hacer el control de estado de las citas y la validación de 24 horas (RF-007). Agregar una columna de tipo entero a la base de datos y un `if` en el frontend es una tarea de baja complejidad técnica.

## Recomendación

**Aceptar.** Es un cambio de tipo perfectivo con un costo muy bajo de implementación (8 horas) que ataca de frente el problema principal del negocio (la fuga de ingresos por tiempos muertos).
