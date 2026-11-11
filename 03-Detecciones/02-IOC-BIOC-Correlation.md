---
title: IOC, BIOC y Correlation Rules
tags: [xsiam, reglas, detecciones]
---
# Elegir el tipo de detección

| Tipo | Idea | Ejemplo |
|---|---|---|
| IOC | Indicador conocido | IP, dominio o hash relevante |
| BIOC | Comportamiento | Proceso con un patrón de ejecución sospechoso |
| Correlation Rule | Relación de eventos o umbral | Intentos fallidos en varias cuentas por una IP |

## Elección práctica
¿Necesitas reconocer un artefacto conocido? Piensa en IOC. ¿Un comportamiento de endpoint representado por campos del evento? Evalúa BIOC. ¿Agregaciones, relaciones de varias fuentes o JOIN? Evalúa una Correlation Rule programada.

**La herramienta de detección no sustituye el diseño del caso de uso.**

[Guía de diseño](01-Diseno-de-detecciones.md) · [Correlation Rules](03-Correlation-Rules.md).
