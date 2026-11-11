---
title: Playbooks y automatización
tags: [xsiam, soar, automatizacion]
---
# Qué automatizar y qué reservar a una persona

Los playbooks permiten enriquecer datos y ejecutar acciones repetitivas bajo un proceso establecido.

## Prioridad
1. Enriquecimiento: reputación, contexto de usuario, host y eventos relacionados.
2. Validación: comprobar resultados, errores y cobertura.
3. Escalado o notificación con contexto suficiente.
4. Contención solo con autorizaciones, condiciones y mecanismos de reversión.

## Ejemplo
Ante una ejecución sospechosa de PowerShell, recopilar árbol de procesos, hash, firma y red puede acelerar la investigación. Aislar un equipo únicamente por el nombre del proceso sería una respuesta arriesgada.

## Revisión
Registrar aprobaciones, fallos, efectos colaterales y procedimiento de reversión.

[Investigación de procesos](../02-Investigacion/01-Procesos-y-parent-child.md).
