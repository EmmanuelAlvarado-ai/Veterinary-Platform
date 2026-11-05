# Diseño de Clases 

**Sistema:** Plataforma LavinPets  
**Autor:** Noé Emmanuel Alvarado Ríos  
**Fecha de la última actualización:** 05/11/2026  

---

## 1. Sustantivos identificados (Lista cruda)

**Origen de los datos:**  
Extraídos directamente de la Especificación de Requisitos y los Casos de Uso del sistema LavinPets.

**Sustantivos subrayados:**  
- Sistema, Cliente, Administrador, Mascota, Nombre, Especie, Raza, Cita, Vacunación, Estética, Fecha, Hora, Historial, Producto, Stock, Precio, Pedido, Pasarela de Pagos, WhatsApp, Urgencia, Botón de pánico, Correo.

---

## 2. Filtro de descartes

**Criterios aplicados:**  
Se aplicaron las cuatro reglas estándar para descartar sustantivos que no califican como clases del sistema.

- **Nombre, Especie, Raza:** *Descartados (Es atributo).* Son datos internos que le pertenecen a la entidad Mascota, no clases independientes.
- **Fecha, Hora:** *Descartados (Es atributo).* Son datos que definen a la Cita.
- **Precio, Stock:** *Descartados (Es atributo).* Son valores numéricos que le pertenecen al Producto.
- **Vacunación, Estética:** *Descartados (Es sinónimo/Herencia).* Conceptualmente se agrupan bajo la clase abstracta "Servicio" (implementando el patrón Strategy para calcular bloqueos de tiempo).
- **Sistema:** *Descartado (Es vago).* No tiene límites claros ni guarda datos concretos por sí solo.
- **Urgencia, Botón de pánico:** *Descartados (Es vago / Es interfaz).* Representan acciones, estados o elementos de la interfaz de usuario, no estructuras de datos persistentes.
- **Pasarela de Pagos, WhatsApp, Correo:** *Descartados (Está fuera).* Son actores o sistemas externos gestionados mediante patrones de diseño (Adapter y Observer), pero no entidades propias del dominio de datos de la clínica.

---

## 3. Clases candidatas finales

**Propósito:**  
Definir las responsabilidades principales (qué sabe y qué hace) de cada entidad validada para el diseño orientado a objetos.

**1. Usuario (Cliente / Administrador)**  
- **Qué sabe (Atributos):** ID, nombre completo, teléfono, correo electrónico, rol de acceso.  
- **Qué hace (Operaciones):** Iniciar sesión, registrar cuenta, gestionar información de perfil.

**2. Mascota**  
- **Qué sabe (Atributos):** ID, nombre, especie, raza, peso, ID del dueño (Usuario).  
- **Qué hace (Operaciones):** *Nota de diseño:* En la arquitectura actual, esta clase actúa como una entidad pasiva (DTO) que transporta información. Las reglas de validación sobre la mascota se delegan al módulo de Agenda.

**3. Cita**  
- **Qué sabe (Atributos):** ID, fecha, hora de inicio, hora de fin, estado actual (activa/completada/cancelada), ID de la mascota, ID del servicio.  
- **Qué hace (Operaciones):** Confirmar agendamiento, ejecutar política de cancelación (validar margen > 24 hrs), actualizar estado.

**4. Servicio (Clase abstracta / Interfaz)**  
- **Qué sabe (Atributos):** Nombre del servicio, descripción, precio base.  
- **Qué hace (Operaciones):** `calcularTiempoBloqueo()` (Aplicación del patrón Strategy según el tipo de servicio).

**5. Producto**  
- **Qué sabe (Atributos):** ID, nombre, descripción, precio, cantidad en stock, categoría.  
- **Qué hace (Operaciones):** Descontar existencias del inventario, validar disponibilidad antes de la compra.

**6. Pedido**  
- **Qué sabe (Atributos):** ID, fecha de creación, ID del cliente, lista de productos (carrito), monto total a pagar, estado del pago (pendiente/pagado).  
- **Qué hace (Operaciones):** Calcular monto total, confirmar pago exitoso, actualizar estado del pedido.
