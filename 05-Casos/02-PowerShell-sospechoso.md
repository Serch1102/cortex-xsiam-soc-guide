---
title: Investigación de PowerShell
tags: [soc, endpoint, powershell]
---
# PowerShell: del proceso a una conclusión

## Caso
Una aplicación de ofimática inicia PowerShell. Puede ser relevante, pero no prueba ejecución maliciosa.

## Comprueba
1. Proceso padre, usuario y línea de comandos.
2. Si el sensor observó el intento o la ejecución efectiva.
3. Procesos hijos, conexiones y escritura de archivos.
4. Firma, hash, reputación y persistencia.
5. Existencia de comportamiento similar en otros hosts.

> Hecho: «se observó el inicio del proceso». Hipótesis: «se ejecutó un script malicioso». Solo la evidencia adicional permite confirmar la segunda.

[Árbol de procesos](../02-Investigacion/01-Procesos-y-parent-child.md).
