---
title: Validación y ajuste de detecciones
tags: [xsiam, detecciones, tuning]
---
# Una detección no termina al publicarla

## Qué medir
Coincidencias diarias, entidades únicas, falsos positivos, casos útiles, retrasos y esfuerzo de análisis.

## Flujo de revisión
1. Muestrear resultados históricos.
2. Identificar qué causa el ruido.
3. Revisar claves, agrupación, umbrales y supresión.
4. Comprobar falsos negativos potenciales.
5. Probar cambios con datos históricos.
6. Documentar el antes y después y solicitar revisión.

## Advertencia
Excluir todas las alertas de una cuenta de administración sin distinguir el comportamiento puede ocultar actividad maliciosa. Preferir excepciones justificadas, específicas y revisables.

[Plantilla de detección](../Plantillas/Plantilla-deteccion.md).
