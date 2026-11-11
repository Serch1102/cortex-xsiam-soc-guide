---
title: Diseñar detecciones XSIAM
tags: [detecciones, l2, l3]
---
# Antes de crear una regla, define qué quieres detectar

## Ficha mínima
- **Amenaza o hipótesis:** ¿qué comportamiento sería sospechoso?
- **Datos necesarios:** ¿qué fuente lo observa?
- **Lógica:** ¿qué combinación de eventos y umbrales dispara?
- **Entidad:** usuario, dispositivo, IP, proceso, cuenta de servicio.
- **Acción:** ¿qué debe investigar el SOC al recibirla?
- **Falsos positivos:** ¿qué actividad legítima se parece?
- **Validación:** cobertura, volumen, pruebas positivas y negativas.

## Ejemplo
«Una misma IP registra fallos contra muchos usuarios en un periodo corto» es una hipótesis de password spraying; el umbral exacto debe ajustarse al comportamiento real de la organización.

No actives una detección solo porque la query devuelve filas. Antes valida recuentos históricos, campos disponibles y qué consecuencias tendría generar cientos de issues.

[IOC, BIOC y correlación](02-IOC-BIOC-Correlation.md) · [Plantilla](../Plantillas/Plantilla-deteccion.md).
