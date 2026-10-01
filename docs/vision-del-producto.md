# Visión del producto

---

**Autor: Noé EmmanueL Alvarado Rios**

**Fecha de la última versión: 30/09/2026**

**Repositorio: https://github.com/EmmanuelAlvarado-ai/Ingenieria-de-Software/blob/main/docs/vision-del-producto.md**

---

## 1. Descripción del sistema

**Nombre del sistema: Plataforma LavinPets**

**Descripción: Un sistema web para que los clientes agenden citas y compren productos 24/7, mientras la veterinaria automatiza su agenda y el envío de recordatorios.**

---

## 2. Problema y usuarios

**El problema: Administrar todo a mano quita mucho tiempo. Además, como no hay recordatorios automáticos, los clientes olvidan sus citas y la clínica pierde dinero. También se pierden ventas de productos porque la gente solo puede comprarlos si va físicamente al local.**

**Cómo se resuelve hoy sin el sistema: Los clientes tienen que llamar por teléfono o mandar mensajes de WhatsApp en horarios de atención para agendar una cita o preguntar por la existencia de productos. La dueña anota las citas en una libreta, y tiene que acordarse de mandar mensajes de texto manualmente un día antes para que los clientes no falten.**

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
| --- | --- | --- |
| **Dueño de mascota (Cliente)** | Poder ver horarios disponibles, agendar citas rápido, elegir el tipo de servicio y comprar productos desde su celular 24/7. | Que la plataforma sea difícil de usar, no saber si su cita realmente quedó confirmada, o pagar un producto y que no haya en existencia. |
| **Veterinaria (Administrador)** | Subir productos nuevos, ver una agenda que se llene sola, recibir alertas de compra y que el sistema mande recordatorios automáticos. | Que los clientes agenden citas que se empalmen, o que hagan citas falsas y le hagan perder tiempo y dinero. |
| **Soporte Técnico (Superusuario)** | Acceso total a bases de datos, código y configuraciones maestras para dar mantenimiento, instalar actualizaciones y solucionar errores. | Que el sistema sufra caídas (downtime), vulnerabilidades de seguridad, pérdida de datos o fallos críticos. |


**Un conflicto entre usuarios: El cliente quiere la máxima flexibilidad quiere poder cancelar su cita 5 minutos antes si le surge un imprevisto. Sin embargo, a la veterinaria esto le estorba porque pierde ese bloque de tiempo, dinero y la oportunidad de atender a otro paciente.**


---

## 3. Alcance


### Dentro del alcance

- **Registra** citas médicas y servicios estéticos asociándolos a un cliente y su mascota.
- **Bloquea** horarios en el calendario de la veterinaria automáticamente, dependiendo de la duración específica de cada tipo de servicio.
- **Procesa** compras de productos físicos mediante un catálogo en línea.
- **Envía** notificaciones automáticas de recordatorio (vía WhatsApp o SMS) a los clientes 24 horas antes de su cita.
- **Autentica** a tres tipos de usuarios con permisos distintos (Cliente, Veterinaria, Soporte Técnico).

### Explícitamente fuera del alcance

- No gestiona expedientes clínicos detallados, historias médicas, ni almacenamiento de radiografías de las mascotas.
- No controla el inventario físico de la clínica ni envía órdenes de reabastecimiento automáticas a proveedores.
- No procesa el cobro ni la facturación de las consultas médicas (el servicio médico se paga presencialmente en la clínica).

**Por qué queda fuera:**


La gestión de expedientes clínicos queda fuera porque la complejidad regulatoria y el volumen de datos médicos convertirían el proyecto en un software de salud completo. Esto no aporta al problema central de este proyecto, que es optimizar la agenda y habilitar las ventas en línea.

---

## 4. Tipo de sistema y restricciones


**Tipo de sistema:**

De información (con modelo de entrega Web y SaaS)


**Por qué es de ese tipo:**

Porque su objetivo principal es registrar, consultar y gestionar la información operativa y comercial de la clínica LavinPets para facilitar su trabajo diario y mejorar la comunicación con los clientes.

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| **Usabilidad** | Los dueños de mascotas buscan conveniencia al agendar y comprar desde su celular. | El sistema será abandonado y los clientes seguirán saturando el WhatsApp de la clínica. |
| **Integridad de los datos** | La agenda es compartida en tiempo real por la veterinaria y múltiples clientes entrando a la vez. | Se empalmarían citas (doble reserva) en el mismo horario o se vendería un producto sin stock físico. |
| **Control de acceso** | Hay tres tipos de usuarios y el sistema manejará datos de clientes y configuración del negocio. | Un cliente podría borrar la agenda, o modificar el catálogo de productos por error. |

**Reglas de negocio que ya identifiqué:**


1. Un servicio de "Vacunación" bloquea la agenda por 15 minutos, mientras que uno de "Estética" bloquea 45 minutos. El sistema debe calcular el tiempo a bloquear dinámicamente.
2. El cliente solo puede cancelar su cita desde el sistema si lo hace con al menos 24 horas de anticipación; de lo contrario, la opción se bloquea.
3. No todos los servicios se pueden agendar en línea. Por ejemplo, las cirugías están bloqueadas en el sistema web porque requieren una valoración médica presencial previa.

---

## 5. Ciclo de vida elegido


**Modelo elegido:**

Modelo Ágil (Iterativo e incremental)

**Por qué le conviene a este proyecto:**


Este proyecto tiene un riesgo principalmente de negocio (saber si los clientes realmente adoptarán la plataforma) y una cliente (la dueña) altamente disponible. Los requisitos de la interfaz y la agenda no son completamente estables, ya que la dueña descubrirá nuevas necesidades operativas al interactuar con el sistema. Entregar software funcionando en ciclos cortos nos permitirá ajustar el flujo de las citas y de la tienda con base en retroalimentación real del usuario.

### Alternativas descartadas

**Alternativa 1:** 

Modelo en Cascada.

*Por qué la descarté:* 

Asume que podemos conocer y congelar todos los requisitos hoy. En una clínica veterinaria hay excepciones operativas que la dueña recordará hasta que vea la primera versión de la agenda. Prohibirnos retroceder a la fase de especificación arruinaría la utilidad del software.

**Alternativa 2:**

Modelo en Espiral.

*Por qué la descarté:*
El modelo en Espiral es excelente para manejar la incertidumbre, pero está diseñado para proyectos donde el mayor riesgo es técnico o de factibilidad arquitectónica. En el caso de LavinPets, programar una agenda web y un catálogo es tecnología estándar y dominada (el riesgo técnico es bajo). Nuestro verdadero riesgo es de negocio (que los dueños de mascotas realmente adopten la plataforma).

---

## Antes de entregar

Reviso que el documento cumpla lo siguiente:

- [x] La descripción del apartado 1 se entiende sin ser del área
- [x] Hay al menos dos tipos de usuario con necesidades distintas
- [x] Identifiqué un conflicto real entre usuarios
- [x] El alcance dice qué queda fuera, no solo qué queda dentro
- [x] Las exclusiones son específicas, no genéricas
- [x] Identifiqué el tipo de sistema y al menos dos atributos de calidad
- [x] Anoté al menos tres reglas de negocio no obvias
- [x] Justifiqué el ciclo de vida contra dos alternativas descartadas
- [x] El documento está en mi repositorio y se puede leer desde el navegador
- [x] Borré todas las instrucciones en cursiva de la plantilla
