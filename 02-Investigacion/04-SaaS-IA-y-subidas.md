---
title: Investigación de subidas a herramientas de IA
tags: [soc, ia, saas, dlp]
---
# ¿Se puede saber el nombre del archivo subido a una IA?

## La limitación
Ver tráfico HTTPS hacia un servicio de IA **no implica** conocer el nombre del archivo. Para atribuir un documento pueden necesitarse eventos de DLP, CASB, auditoría SaaS o navegador, además de endpoint.

## Procedimiento
1. Identifica qué sensor generó el evento.
2. Comprueba si detectó navegación, solicitud de subida o subida completada.
3. Revisa campos del dataset: usuario, servicio, acción, archivo, tamaño, DLP y hora.
4. Si el archivo no aparece en el log, busca una fuente con visibilidad de objeto.
5. Correlaciona solo si tienes una clave sólida y una ventana temporal coherente.

## Redacción defendible
> Se ha observado actividad hacia el servicio. Esta fuente no muestra actualmente el nombre del fichero; por tanto no podemos afirmar qué documento se transfirió.

## Ejercicio
Construye una tabla de cuatro columnas: **fuente**, **qué observa**, **qué no puede observar**, **siguiente consulta**.

[JOIN](../01-XQL/04-JOIN-entre-datasets.md).
