# ADR-001: Monolito modular en lugar de microservicios

## Estado
Propuesta, 29 de octubre de 2026

## Contexto
El sistema de la Plataforma LavinPets cuenta con tres módulos principales de negocio con fronteras lógicas muy claras: Agenda Médica, Catálogo y Ventas, y Perfiles e Identidad. El desarrollo y mantenimiento del sistema lo realiza un único programador. La clínica veterinaria tiene una sola sucursal y el volumen de tráfico esperado (citas diarias y ventas locales) es predecible. 

Se evaluó separar el sistema en microservicios, especialmente para aislar la pasarela de pagos, pero según la Ley de Conway, gestionar múltiples despliegues y servicios en red representaría una sobrecarga técnica inmanejable y excesiva para un solo desarrollador.

## Decisión
Construimos un monolito modular: un solo sistema desplegable, con los tres módulos separados internamente por carpetas e interfaces lógicas. Se establece la regla estricta de que el módulo de Ventas y el módulo de Agenda no pueden hablar directamente entre sí ni leer las tablas de base de datos del otro; cualquier consulta cruzada debe pasar a través del módulo de Perfiles.

## Consecuencias

**Lo que ganamos:**
- Mantenemos un solo despliegue, un solo repositorio y un solo registro de errores, lo cual es perfectamente operable para un equipo de una sola persona.
- Si en un futuro la clínica crece o se convierte en franquicia y necesitamos extraer el módulo de Ventas, las fronteras lógicas y las interfaces ya están trazadas, facilitando el corte.

**El costo y los riesgos:**
- No podemos escalar los módulos por separado. Si la tienda en línea genera un pico masivo de tráfico, consumirá los recursos del mismo servidor donde corre la Agenda Médica.
- El riesgo real es la falta de disciplina: que por ahorrar tiempo un módulo lea directamente las tablas de base de datos de otro, rompiendo el aislamiento. Esto obligará a revisar manualmente el código en cada actualización para asegurar que no se crucen las fronteras.
