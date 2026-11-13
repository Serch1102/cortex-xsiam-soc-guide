---
title: Laboratorio integral de identidad y detección
tags: [laboratorio, identidad, xsiam, xql]
estado: ejercicio-educativo
actualizado: 2026-11-13
---
# Laboratorio integral: muchos fallos y un inicio de sesión correcto

**Objetivo:** practicar investigación, diseño de query y criterio de detección con datos **100 % sintéticos**. No necesitas conectarte a producción para resolver el razonamiento.

## Contexto

Durante una mañana aparecen estos registros, normalizados hipotéticamente:

| Hora (UTC) | IP origen | Usuario | Resultado | Aplicación |
|---|---|---|---|---|
| 09:00 | 198.51.100.24 | ana@ejemplo.test | FAILED | Portal |
| 09:01 | 198.51.100.24 | bruno@ejemplo.test | FAILED | Portal |
| 09:02 | 198.51.100.24 | carla@ejemplo.test | FAILED | Portal |
| 09:03 | 198.51.100.24 | diego@ejemplo.test | FAILED | Portal |
| 09:04 | 198.51.100.24 | ana@ejemplo.test | FAILED | Portal |
| 09:06 | 198.51.100.24 | carla@ejemplo.test | SUCCESS | Portal |
| 09:07 | 203.0.113.30 | juan@ejemplo.test | FAILED | Portal |

Las dos IP pertenecen a rangos reservados para documentación. Las cuentas y la aplicación son inventadas.

## Parte A — Explicar sin utilizar XQL

1. ¿Cuántos fallos se registraron desde `198.51.100.24`?
2. ¿Cuántos **usuarios distintos** recibieron fallos desde esa IP?
3. ¿Qué usuario tuvo después una autenticación correcta?
4. ¿Qué información falta para atribuir el éxito al mismo actor que generó los fallos?
5. ¿Qué comprobarías si la IP fuera la salida de una VPN corporativa?

### Solución razonada

Hay cinco fallos de autenticación desde la IP y cuatro usuarios distintos afectados. Carla presenta un éxito posterior. Esto **no prueba** que el éxito se deba a un ataque: faltan dispositivo, sesión, MFA, contexto de red, identidad del cliente y la posibilidad de NAT o proxy.

## Parte B — Diseñar una query

Primero identifica en un tenant autorizado los nombres reales de dataset y campos de autenticación. Define:

- Campo de origen.
- Identidad.
- Resultado.
- Timestamp.
- Aplicación o proveedor.

**Pseudocódigo, no XQL ejecutable:**

~~~text
Filtrar eventos de autenticación fallidos
Agrupar por IP origen durante una ventana definida
Calcular:
  - total de fallos
  - usuarios distintos afectados
Mantener IP y cifras principales
Filtrar por umbral definido y justificado
Investigar éxitos posteriores por identidad y contexto temporal
~~~

## Parte C — ¿Qué JOIN sería peligroso?

Unir todos los fallos con todos los éxitos por `user_name` de un día entero produce coincidencias que podrían no pertenecer a una misma sesión. Unir solo por IP puede mezclar diferentes usuarios detrás de un NAT.

Para reducir errores, añade restricción temporal, aplicación, dispositivo o ID de sesión cuando esté disponible, y valida algunas coincidencias manualmente.

## Parte D — Diseñar la regla

Completa la [plantilla de detección](../Plantillas/Plantilla-deteccion.md):

- Hipótesis y cobertura.
- Recuento de usuarios distintos y fallos.
- Entidad de agrupación.
- Ventana y frecuencia.
- Campos de Issue y drill-down.
- Riesgo de falsos positivos.
- Supresión.
- Plan de pruebas.

## Parte E — Simulación de recepción tardía

Supón que el evento de las 09:02 llega a XSIAM a las 09:20. ¿Qué verá una regla que ejecutó a las 09:10? ¿Cómo diseñarías la ventana y las pruebas para no descartar eventos tardíos?

## Cómo evaluar el trabajo

Una buena respuesta:
- No confunde eventos, usuarios, intentos ni sesiones.
- Explica que el éxito posterior no confirma compromiso.
- Identifica condiciones para evitar un JOIN engañoso.
- Señala la dependencia de parsing, ingesta y cobertura.
- Diseña un Issue útil para el siguiente analista.

## Comparación con otras herramientas

| XSIAM | Google SecOps | Sentinel/Azure |
|---|---|---|
| XQL y Correlation Rule | UDM para buscar eventos, YARA-L para reglas cuando proceda | KQL sobre tablas de autenticación y Analytics Rules |
| XDM / campos reales de la fuente | `principal` / `target` y `metadata` según significado | ASIM Authentication o tablas como `SigninLogs`, cuando existan |

La equivalencia es de objetivo, no de sintaxis. No copies nombres de campos entre productos.

**Relacionadas:** [Tiempos](../01-XQL/06-Tiempos-y-ventanas.md) · [JOIN avanzado](../01-XQL/07-JOIN-y-correlaciones-engañosas.md) · [Ciclo de vida de detección](../03-Detecciones/05-Ciclo-de-vida-de-una-regla.md).
