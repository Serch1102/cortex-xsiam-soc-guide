---
title: Optimizar y depurar consultas XQL
tags: [xql, rendimiento, l2]
---
# Queries lentas o resultados engañosos

Empieza con **poco tiempo**, filtros de entidad, eventos concretos y columnas necesarias. Evalúa cardinalidad antes de hacer `join`.

## Protocolo
1. Ejecutar el dataset de origen.
2. Añadir un filtro y comprobar cuántas filas se pierden.
3. Añadir campos de salida.
4. Añadir agregación si es necesaria.
5. Añadir `join` solo cuando ambas consultas por separado funcionen.
6. Ordenar, limitar salida y documentar rendimiento.

> [!warning] `limit` no sustituye filtros
> Limitar 50 filas de salida no significa que el motor solo haya procesado 50 registros.

## Fallos típicos
- Campo inexistente: confirmar esquema en los resultados.
- Cero filas: revisar periodo, fuente, mayúsculas, claves, nulos.
- Demasiadas filas: múltiples matches por clave.
- Timeout: reducir rango, agregar antes y eliminar campos innecesarios.

[JOIN](04-JOIN-entre-datasets.md).
