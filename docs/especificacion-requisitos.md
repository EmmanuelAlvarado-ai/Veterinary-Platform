# Especificación de requisitos

**Sistema:** Plataforma LavinPets  
**Autor:** Noé Emmanuel Alvarado Ríos  
**Fecha de la última actualización:** 30/09/2026  

---

## 1. Propósito y alcance

**Propósito del documento:**  
Especificar los requisitos funcionales y no funcionales, así como los casos de uso principales para la construcción de la Plataforma LavinPets, sirviendo como guía verificable para el diseño, desarrollo y validación del sistema.

**Alcance del sistema:**  
- Registra citas médicas y servicios estéticos asociándolos a un cliente y su mascota.
- Bloquea horarios en el calendario de la veterinaria automáticamente, dependiendo de la duración específica de cada tipo de servicio.
- Procesa compras y el cobro en línea de productos físicos mediante la integración con una pasarela de pagos externa (ej. Stripe o Mercado Pago).
- Envía notificaciones automáticas de recordatorio (vía WhatsApp o SMS) a los clientes 24 horas antes de su cita.
- Autentica a tres tipos de usuarios con permisos distintos (Cliente, Veterinaria, Soporte Técnico).
- Permite a la Veterinaria cancelar o suspender en bloque las citas del resto del día con un solo botón en caso de urgencia médica, disparando notificaciones de reagendación automáticas a los clientes afectados.

**Fuera del alcance:**  
- No gestiona expedientes clínicos detallados, historias médicas, ni almacenamiento de radiografías de las mascotas.
- No controla el inventario médico ni de insumos operativos de la clínica (jeringas, medicamentos de uso en consultorio). El control de existencias en el sistema se limita exclusivamente a los artículos publicados en el catálogo de venta en línea, y no emite órdenes de reabastecimiento a proveedores.
- No procesa el cobro ni la facturación de las consultas médicas (el servicio médico se paga presencialmente en la clínica).
- No gestiona envíos a domicilio ni cobro de paquetería para las compras en línea (la entrega de productos es estrictamente mediante recolección física en la clínica).
- No almacena ni procesa directamente datos sensibles de tarjetas de crédito o débito. Toda la transacción financiera y la seguridad de los datos bancarios se delegan a la pasarela de pagos externa.

---

## 2. Usuarios y su contexto

| Usuario | Qué hace hoy sin el sistema | Qué espera del sistema |
| :--- | :--- | :--- |
| **Dueño de mascota (Cliente)** | Tienen que llamar por teléfono o mandar mensajes de WhatsApp en horarios de atención para agendar una cita o preguntar por la existencia de productos. | Poder ver horarios disponibles, agendar citas rápido, elegir el tipo de servicio y comprar productos desde su celular 24/7. |
| **Veterinaria (Administrador)** | Anota las citas en una libreta, y tiene que acordarse de mandar mensajes de texto manualmente un día antes para que los clientes no falten. | Subir productos nuevos, ver una agenda que se llene sola, recibir alertas de compra y que el sistema mande recordatorios automáticos. |
| **Soporte Técnico (Superusuario)** | N/A | Acceso total a bases de datos, código y configuraciones maestras para dar mantenimiento, instalar actualizaciones y solucionar errores. |

**Conflictos identificados entre usuarios:**  
El cliente quiere la máxima flexibilidad quiere poder cancelar su cita 5 minutos antes si le surge un imprevisto. Sin embargo, a la veterinaria esto le estorba porque pierde ese bloque de tiempo, dinero y la oportunidad de atender a otro paciente.

---

## 3. Requisitos funcionales

### 3.1 Resumen

| ID | Nombre | Prioridad | Origen |
| :--- | :--- | :--- | :--- |
| RF-001 | Bloqueo dinámico de agenda | Imprescindible | Entrevista (confirmado) |
| RF-002 | Restricción de cirugías web | Imprescindible | Entrevista (confirmado) |
| RF-003 | Suspensión de agenda por urgencia | Importante | Entrevista (hallazgo inesperado) |
| RF-004 | Notificación de cancelación masiva | Importante | Supuesto derivado de RF-003 |
| RF-005 | Restricción de cobro a productos | Imprescindible | Entrevista (confirmado) |
| RF-006 | Recordatorio automático de cita | Imprescindible | Entrevista (dolor del negocio) |
| RF-007 | Restricción de cancelación por tiempo | Imprescindible | Entrevista (confirmado) |
| RF-008 | Bloqueo individual por mascota | Importante | Entrevista (confirmado) |
| RF-009 | Deducción automática de stock | Imprescindible | Derivado de CU-03 |
| RF-010 | Control de acceso a catálogo | Imprescindible | Derivado de CU-04 |

### 3.2 Fichas

**RF-001 - Bloqueo dinámico de agenda**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema bloquea el tiempo en la agenda dependiendo del servicio: 20 minutos para "Vacunación" y 60 minutos para "Estética". |
| **Origen** | Entrevista con la dueña, 30 de septiembre (Regla ajustada). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al guardar una cita de "Vacunación" a las 10:00 AM, el sistema muestra el horario de 10:00 a 10:20 ocupado.<br>- Al guardar "Estética", ocupa de 10:00 a 11:00 AM. |
| **Relacionado con** | RNF-INT-001 |

**RF-002 - Restricción de cirugías web**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema impide que un usuario tipo Cliente agende el servicio "Cirugía" de manera directa. |
| **Origen** | Entrevista con la dueña, 30 de septiembre (Confirmado). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al intentar seleccionar "Cirugía" en el catálogo de servicios web, el botón de confirmar se deshabilita y se muestra el mensaje: "Requiere agendar Revisión General previa". |
| **Relacionado con** | N/A |

**RF-003 - Suspensión de agenda por urgencia**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema cambia a estado "Cancelado" todas las citas posteriores a la hora actual en el día en curso al presionar el botón de suspensión de urgencia. |
| **Origen** | Entrevista con la dueña, 30 de septiembre (Hallazgo inesperado). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - Si son las 2:00 PM y el administrador presiona la suspensión, todas las citas entre las 2:01 PM y el cierre del día cambian su estado a cancelado en un solo clic. |
| **Relacionado con** | RF-004 |

**RF-004 - Notificación de cancelación masiva**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema envía un correo electrónico y un mensaje de WhatsApp de aviso de reagendación a los clientes cuyas citas fueron afectadas por la suspensión de urgencia. |
| **Origen** | Supuesto propio derivado de la necesidad de urgencias. |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - Al ejecutarse el RF-003, el sistema despacha correos y mensajes a los contactos registrados de los afectados en un máximo de 2 minutos. |
| **Relacionado con** | RF-003, RNF-REN-001 |

**RF-005 - Restricción de cobro a productos**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema procesa pagos en línea exclusivamente para los carritos que contienen artículos del catálogo de productos físicos. |
| **Origen** | Entrevista con la dueña, 30 de septiembre (Confirmado). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Si el usuario tiene una consulta en el carrito, el flujo salta a "Confirmar cita" sin pedir tarjeta.<br>- Si tiene croquetas, el sistema exige el pago mediante la pasarela antes de confirmar el pedido. |
| **Relacionado con** | N/A |

**RF-006 - Recordatorio automático de cita**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema envía una notificación de recordatorio (vía WhatsApp o SMS) al Cliente exactamente 24 horas antes de la hora programada para su cita. |
| **Origen** | Entrevista con la dueña, 30 de septiembre (Confirmado - Dolor principal). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Si una cita está agendada para el jueves a las 16:00, el sistema dispara la notificación el miércoles a las 16:00 sin intervención humana. |
| **Relacionado con** | Regla de Negocio 2 (Política de 24 horas) |

**RF-007 - Restricción de cancelación por tiempo**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema bloquea la option de cancelar una cita en el portal del Cliente si la diferencia entre la hora actual y la hora programada es menor a 24 horas. |
| **Origen** | Entrevista con la dueña, 30 de septiembre (Regla para evitar pérdidas por inasistencia). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Un cliente intenta cancelar el miércoles a las 11:00 AM una cita programada para el jueves a las 09:00 AM. El sistema oculta el botón de cancelar y muestra el texto: "Cancelación no disponible con menos de 24 horas". |
| **Relacionado con** | CU-02 |

**RF-008 - Bloqueo individual por mascota**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema impide asignar más de un perfil de mascota a un mismo bloque de tiempo durante el proceso de reserva. |
| **Origen** | Entrevista con la dueña, 30 de septiembre (Corrección sobre citas múltiples). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - El cliente selecciona dos perros en el formulario de la cita. El botón de confirmar se deshabilita y aparece una alerta indicando que debe reservar un espacio por cada mascota. |
| **Relacionado con** | RF-001, CU-01 |

**RF-009 - Deducción automática de stock**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema descuenta de las existencias del catálogo en línea la cantidad exacta de artículos comprados en cuanto la pasarela de pago confirma la transacción exitosa. |
| **Origen** | Supuesto derivado del flujo de compras web (CU-03). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Se compran 2 bultos de croquetas. Al recibir el "OK" de la pasarela, el stock del producto en la base de datos baja inmediatamente de 10 a 8. |
| **Relacionado con** | RF-005 |

**RF-010 - Control de acceso a catálogo**
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema restringe el acceso a la vista de agregar, editar o eliminar productos del catálogo exclusivamente a los usuarios autenticados con rol de Administrador. |
| **Origen** | Supuesto de seguridad del sistema (CU-04). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Un usuario tipo Cliente ingresa manualmente la URL de gestión de catálogo. El sistema rechaza la petición y lo redirige a la página principal mostrando el error "Acceso denegado". |
| **Relacionado con** | N/A |

---

## 4. Requisitos no funcionales

### 4.1 Resumen

| ID | Atributo | Nombre | Prioridad | Origen |
| :--- | :--- | :--- | :--- | :--- |
| RNF-USA-001 | Usabilidad | Límite de clics para agendar | Imprescindible | Derivado de tipo de sistema |
| RNF-INT-001 | Integridad | Prevención de empalmes | Imprescindible | Entrevista (dolor del negocio) |
| RNF-DIS-001 | Disponibilidad | Uptime del portal web | Importante | Derivado de tipo de sistema |

### 4.2 Fichas

**RNF-USA-001 - Límite de clics para agendar**
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Usabilidad |
| **Descripción** | Un usuario tipo Cliente completa el flujo de agendar una cita en un máximo de cuatro clics desde la pantalla principal. |
| **Métrica** | Número absoluto de clics u toques en pantalla (máximo 4) para un paciente previamente registrado. |
| **Origen** | Derivado del tipo de sistema. |
| **Prioridad** | Imprescindible |
| **Por qué importa** | Si el sistema es más largo o confuso que mandar un WhatsApp, los clientes lo abandonarán y seguirán saturando el teléfono de la clínica de madrugada. |
| **Afecta a** | RF-001 |

**RNF-INT-001 - Prevención de empalmes concurrentes**
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Integridad de los datos |
| **Descripción** | El sistema rechaza las peticiones concurrentes para el mismo bloque de horario con un tiempo de respuesta menor a 2 segundos. |
| **Métrica** | Tiempo de validación de disponibilidad en base de datos al momento de guardar (menor a 2000 ms). |
| **Origen** | Entrevista (las citas duplicadas o mal anotadas generan caos en piso). |
| **Prioridad** | Imprescindible |
| **Por qué importa** | Al ser un entorno web, dos dueños pueden dar clic al mismo tiempo. Si el sistema guarda ambas, habrá dos pacientes para un solo consultorio. |
| **Afecta a** | RF-001 |

**RNF-DIS-001 - Uptime del portal web**
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Disponibilidad |
| **Descripción** | El portal de agendamiento y catálogo está accesible el 99.9% del tiempo fuera de ventanas de mantenimiento programadas. |
| **Métrica** | Porcentaje de tiempo de actividad mensual medido por herramientas de monitoreo. |
| **Origen** | Derivado del tipo de sistema. |
| **Prioridad** | Importante |
| **Por qué importa** | El mayor dolor de la veterinaria son los mensajes a las 11:00 PM. El sistema existe para operar justamente cuando la clínica física está cerrada. |
| **Afecta a** | N/A |
---

## 5. Casos de uso


### Lista de Casos de Uso del Sistema
1. **CU-01:** Agendar cita de servicio
2. **CU-02:** Cancelar cita programada
3. **CU-03:** Comprar productos físicos
4. **CU-04:** Administrar catálogo de productos
5. **CU-05:** Suspender agenda por urgencia
6. **CU-06:** Registrar perfil de mascota
7. **CU-07:** Consultar agenda del día
8. **CU-08:** Consultar historial de citas

---

### Detalle de los Casos de Uso

**CU-01 · Agendar cita de servicio**
*   **Actor principal:** Cliente (Dueño de mascota).
*   **Objetivo:** Reservar un bloque de tiempo específico en la clínica veterinaria para un servicio.
*   **Precondición:** El Cliente tiene sesión iniciada y al menos una mascota registrada.
*   **Escenario principal:**
    1. El Cliente selecciona a la mascota que recibirá la atención.
    2. El Cliente elige el tipo de servicio deseado del catálogo (ej. Vacunación).
    3. El sistema calcula dinámicamente el tiempo de bloqueo requerido para ese servicio.
    4. El sistema consulta la base de datos y muestra los horarios libres.
    5. El Cliente selecciona una fecha y hora específica.
    6. El sistema verifica internamente que el espacio no se haya ocupado en ese instante.
    7. El sistema registra la cita, bloquea el tiempo y muestra la pantalla de confirmación.
*   **Flujos alternos:**
    *   **2a. Servicio restringido:** Si elige "Cirugía", el sistema bloquea el calendario y muestra "Requiere agendar Revisión General previa".
    *   **5a. Múltiples mascotas:** El Cliente intenta agrupar mascotas; el sistema notifica que cada mascota requiere un bloque individual y lo redirige.
    *   **6a. Concurrencia:** El horario fue ocupado por otro usuario; el sistema lanza el error "Horario no disponible" y recarga las horas libres.
*   **Postcondición:** El bloque de tiempo queda oficialmente ocupado y visible para la Veterinaria.
*   **Requisitos que realiza:** RF-001, RF-002, RNF-USA-001, RNF-INT-001.

**CU-02 · Cancelar cita programada**
*   **Actor principal:** Cliente (Dueño de mascota).
*   **Objetivo:** Liberar un espacio de la agenda que el cliente ya no podrá utilizar.
*   **Precondición:** El Cliente tiene sesión iniciada y cuenta con una cita futura registrada.
*   **Escenario principal:**
    1. El Cliente accede a su sección de "Mis Citas".
    2. El Cliente selecciona la cita que desea cancelar y presiona "Cancelar cita".
    3. El sistema verifica el tiempo restante entre la hora actual y la hora de la cita.
    4. El sistema cambia el estado de la cita a "Cancelada por cliente" y libera el horario.
    5. El sistema notifica la cancelación en el panel de la Veterinaria.
*   **Flujos alternos:**
    *   **3a. Violación de política de tiempo:** Faltan menos de 24 horas para la cita. El sistema oculta el botón de cancelación y muestra: "Las citas con menos de 24 horas de proximidad no pueden cancelarse por sistema".
*   **Postcondición:** La cita se anula y el bloque de tiempo vuelve a estar disponible.
*   **Requisitos que realiza:** Regla de Negocio 2 (Política de 24 horas).

**CU-03 · Comprar productos físicos**
*   **Actor principal:** Cliente.
*   **Objetivo:** Pagar en línea artículos del catálogo para asegurar su disponibilidad y pasar a recogerlos.
*   **Precondición:** El Cliente tiene artículos agregados en su carrito de compras.
*   **Escenario principal:**
    1. El Cliente abre el carrito y presiona "Proceder al pago".
    2. El sistema verifica las existencias disponibles en el catálogo en línea para cada producto.
    3. El sistema redirige al Cliente a la pasarela de pagos externa.
    4. La pasarela confirma la transacción exitosa al sistema.
    5. El sistema genera el comprobante de compra con estatus "Pendiente de recolección en tienda" y resta la cantidad comprada de las existencias del catálogo.
*   **Flujos alternos:**
    *   **2a. Falta de stock (Empalme físico):** El sistema detecta que el producto se agotó físicamente. Bloquea el cobro y pide al Cliente sacarlo del carrito.
    *   **4a. Pago declinado:** La pasarela rechaza la tarjeta. El sistema regresa al Cliente a la pantalla de pago mostrando el error de la pasarela.
*   **Postcondición:** El producto queda pagado, apartado, y la Veterinaria recibe la orden.
*   **Requisitos que realiza:** RF-005.

**CU-04 · Administrar catálogo de productos**
*   **Actor principal:** Veterinaria (Administrador).
*   **Objetivo:** Dar de alta, dar de baja o actualizar precios y existencias.
*   **Precondición:** La Veterinaria inició sesión con credenciales de administrador.
*   **Escenario principal:**
    1. La Veterinaria accede a "Gestión de Catálogo".
    2. Selecciona "Agregar/Modificar Producto".
    3. Ingresa o edita los datos (nombre, precio, stock, foto).
    4. Presiona "Guardar cambios".
    5. El sistema valida los datos y actualiza la vista pública.
*   **Flujos alternos:**
    *   **3a. Datos inconsistentes:** Ingresa un precio negativo. El sistema impide guardar y marca el campo con error.
*   **Postcondición:** Base de datos actualizada y visible para clientes.
*   **Requisitos que realiza:** Control de Acceso (Implícito), RF-005.

**CU-05 · Suspender agenda por urgencia**
*   **Actor principal:** Veterinaria (Administrador).
*   **Objetivo:** Cancelar en bloque las citas restantes del día por urgencia médica en piso.
*   **Precondición:** Sesión iniciada y citas programadas para las horas siguientes.
*   **Escenario principal:**
    1. La Veterinaria ingresa a la vista principal de la agenda del día.
    2. Presiona "Suspender agenda del día".
    3. El sistema despliega una alerta advirtiendo el número de citas afectadas.
    4. La Veterinaria confirma la acción.
    5. El sistema cambia a estatus "Cancelada por urgencia" todas las citas restantes.
    6. El sistema envía automáticamente mensajes de whatsapp y correos electrónicos a los clientes afectados.
*   **Flujos alternos:**
    *   **3a. Sin citas futuras:** El sistema detecta que ya no hay citas pendientes hoy, bloquea el botón y notifica.
*   **Postcondición:** Agenda bloqueada por el resto del día y notificaciones enviadas.
*   **Requisitos que realiza:** RF-003, RF-004.

**CU-06 · Registrar perfil de mascota**
*   **Actor principal:** Cliente (Dueño de mascota).
*   **Objetivo:** Crear un expediente básico del animal para vincularlo a reservas.
*   **Precondición:** Cliente con sesión iniciada.
*   **Escenario principal:**
    1. El Cliente accede a "Mis Mascotas" y selecciona "Agregar mascota".
    2. El sistema muestra el formulario de registro.
    3. El Cliente ingresa nombre, especie, raza y edad.
    4. El sistema valida campos obligatorios.
    5. El sistema vincula la mascota al cliente.
*   **Flujos alternos:**
    *   **4a. Campos faltantes:** El Cliente olvida la especie. El sistema detiene el registro y exige el dato.
*   **Postcondición:** Mascota disponible para reservas.
*   **Requisitos que realiza:** Estructura de base de datos relacional (Implícito).

**CU-07 · Consultar agenda del día**
*   **Actor principal:** Veterinaria (Administrador).
*   **Objetivo:** Revisar la lista de pacientes y servicios de la fecha actual.
*   **Precondición:** Sesión de administrador iniciada.
*   **Escenario principal:**
    1. La Veterinaria entra al sistema y selecciona la vista "Hoy".
    2. El sistema recupera las citas ordenadas por hora.
    3. El sistema muestra nombre del dueño, mascota y servicio programado.
*   **Flujos alternos:**
    *   **2a. Agenda vacía:** No hay citas programadas, el sistema muestra "Día libre de citas programadas".
*   **Postcondición:** La Veterinaria tiene visibilidad de su carga de trabajo.
*   **Requisitos que realiza:** Visualización de agenda (Implícito).

**CU-08 · Consultar historial de citas**
*   **Actor principal:** Cliente.
*   **Objetivo:** Ver el registro de visitas pasadas de su mascota.
*   **Precondición:** Sesión iniciada.
*   **Escenario principal:**
    1. El Cliente entra a "Mis Mascotas" y selecciona el perfil de un animal.
    2. El sistema despliega una lista cronológica de las citas pasadas con estatus "Completado".
*   **Flujos alternos:**
    *   **2a. Sin historial:** La mascota es nueva y no tiene citas previas, el sistema indica "Aún no hay registros de visitas".
*   **Postcondición:** El Cliente conoce las fechas de las atenciones previas.
*   **Requisitos que realiza:** Consulta de información (Implícito).

---

## 6. Trazabilidad

| Requisito | Origen | Caso de uso | Pantalla del prototipo | Estado |
| :--- | :--- | :--- | :--- | :--- |
| RF-001 | Entrevista 30 sep | CU-01 Agendar cita | Pantalla de Selección de Horario | Vigente |
| RF-002 | Entrevista 30 sep | CU-01 Agendar cita (Flujo Alt 2a) | Pantalla de Selección de Servicio | Vigente |
| RF-003 | Entrevista 30 sep | CU-05 Suspender agenda por urgencia | Pantalla del Administrador (Botón Pánico)| Vigente |
| RF-004 | Supuesto propio | CU-05 Suspender agenda por urgencia | N/A (Proceso backend) | Vigente |
| RF-005 | Entrevista 30 sep | CU-03 Comprar productos físicos | Pantalla de Checkout de Tienda | Vigente |
| RF-006 | Entrevista 30 sep | N/A (Proceso automático) | N/A (Proceso backend) | Vigente |
| RNF-USA-001 | Derivado del sistema | CU-01 Agendar cita | Flujo completo de Nueva Cita | Vigente |
| RNF-INT-001 | Entrevista 30 sep | CU-01 Agendar cita (Flujo Alt 6a)| Pantalla de Confirmación | Vigente |
| RNF-DIS-001 | Derivado del sistema | Todos los Casos de Uso (Global) | N/A (Infraestructura) | Vigente |
| RF-007 | Entrevista 30 sep | CU-02 Cancelar cita programada | Pantalla de Mis Citas | Vigente |
| RF-008 | Entrevista 30 sep | CU-01 Agendar cita (Flujo Alt 4a) | Pantalla de Selección de Servicio | Vigente |
| RF-009 | Derivado de CU-03 | CU-03 Comprar productos físicos | N/A (Proceso backend) | Vigente |
| RF-010 | Derivado de CU-04 | CU-04 Administrar catálogo | Pantalla de Gestión de Catálogo | Vigente |

---

## 7. Registro de cambios

| Fecha | Requisito | Qué cambió | Por qué |
| :--- | :--- | :--- | :--- |
| 30/09/2026 | RF-001 | Tiempo de vacunación a 20 min | Confirmación en entrevista con Veterinaria |
| 30/09/2026 | Alcance | Eliminación de entregas/paquetería | Confirmación en entrevista de recolección física |
| 30/09/2026 | RF-003 | Se agregó requisito de botón de pánico | Hallazgo inesperado en entrevista sobre caos en urgencias |
| 30/09/2026 | Alcance y CU-03 | Clarificación de control de existencias de catálogo vs. inventario médico | Corrección de contradicción detectada en inspección de requisitos |
| 30/09/2026 | RF-006 | Se agregó requisito funcional de recordatorios | Para cubrir la promesa hecha en el Alcance del sistema |
| 30/09/2026 | RF-007 a RF-010 | Se agregaron cuatro requisitos funcionales adicionales | Formalización de reglas de negocio ya descritas en los Casos de Uso para alcanzar el umbral mínimo de diseño |
