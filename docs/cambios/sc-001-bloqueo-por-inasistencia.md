# SC-001: Bloqueo de cuenta por inasistencias recurrentes

---

## Estado

Propuesta · 15 de noviembre de 2026

## Quién la pide

Dupla de revisión 

## Qué se pide

Quiero que el sistema lleve un conteo de las veces que un cliente agenda una cita y simplemente no se presenta. Si un cliente me deja plantada 3 veces (acumula 3 inasistencias), quiero que el sistema lo bloquee automáticamente y ya no le permita agendar más citas por la página web.

## Por qué

Porque cuando la gente no llega a su cita y tampoco la cancela a tiempo, ese tiempo se pierde, no se le puede dar el espacio a otro paciente y la clínica pierde dinero. Necesitamos ponerles un límite tecnológico para que respeten el tiempo de la agenda.

## Qué pasa si no se hace

Los clientes mala paga seguirán agendando y faltando sin ninguna consecuencia, la agenda se llenará de "citas fantasma" que estorban a clientes reales, y la clínica seguirá perdiendo ingresos por los horarios desperdiciados.

## Tipo de mantenimiento

- [ ] **Correctivo** · algo está mal y hay que repararlo
- [ ] **Adaptativo** · el entorno cambió y hay que ajustarse
- [x] **Perfectivo** · funciona, pero puede ser mejor
- [ ] **Preventivo** · todavía no falla, pero va a fallar

**Justificación:** El sistema actual funciona bien y cumple con lo acordado (bloquear cancelaciones a menos de 24 horas), pero agregar una penalización mejora las reglas del negocio y protege los ingresos de la clínica.

## Criterio de aceptación

Cuando un cliente acumula 3 citas marcadas como "No asistió", el sistema debe ocultar o deshabilitar el botón de "Agendar cita" en su sesión y mostrarle un mensaje indicando que su cuenta está restringida y debe comunicarse por teléfono.

## Prioridad que sugiere el solicitante

Alta. Resuelve una fuga de dinero directa y un problema de logística diario en la clínica.

---
