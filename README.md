---
title: Guía SOC de Cortex XSIAM
aliases: [Inicio XSIAM, Base de conocimiento de XSIAM]
tags: [xsiam, soc, indice]
estado: vigente
actualizado: 2026-11-11
---
# Cortex XSIAM · Guía práctica para analistas SOC

Documentación **en castellano**, orientada al trabajo de analistas SOC L1, L2 y L3. Primero explicamos el problema operativo; después, el concepto, la consulta y cómo interpretar el resultado.

> [!important] Alcance
> Los ejemplos no están probados en vuestro tenant. Comprueba siempre los datasets, campos, permisos y rangos temporales antes de ejecutar. **No publicar información real de clientes, identificadores internos, capturas o secretos.**

## Itinerario recomendado
1. [Fundamentos y flujo](00-Fundamentos/01-Arquitectura-y-flujo.md)
2. [Datasets, presets y XDM](00-Fundamentos/02-Datasets-Presets-y-XDM.md)
3. [De evento a caso](00-Fundamentos/03-Alertas-Issues-y-Cases.md)
4. [Introducción a XQL](01-XQL/01-Introduccion-XQL.md)
5. [Filtros y operadores](01-XQL/02-Filtros-y-operadores.md)
6. [Agregaciones con COMP](01-XQL/03-Agregaciones-COMP.md)
7. [JOIN entre datasets](01-XQL/04-JOIN-entre-datasets.md)
8. [Investigación SOC](02-Investigacion/01-Procesos-y-parent-child.md)
9. [Diseño de detecciones](03-Detecciones/01-Diseno-de-detecciones.md)
10. [Laboratorio guiado](06-Laboratorios/01-Laboratorio-XQL.md)

## Módulos
| Módulo | Contenido |
|---|---|
| [00 · Fundamentos](00-Fundamentos/00-MOC-Fundamentos.md) | Arquitectura, datasets, alertas, issues y cases |
| [01 · XQL](01-XQL/00-MOC-XQL.md) | Consultar, filtrar, agregar, unir y depurar datos |
| [02 · Investigaciones](02-Investigacion/01-Procesos-y-parent-child.md) | Procesos, red, identidad y SaaS/IA |
| [03 · Detecciones](03-Detecciones/01-Diseno-de-detecciones.md) | IOC, BIOC, reglas de correlación y tuning |
| [04 · Automatización](04-Automatizacion/01-Playbooks-y-automatizacion.md) | Playbooks y respuesta segura |
| [05 · Casos](05-Casos/01-PsExec-PSEXESVC.md) | Casos razonados de PsExec, PowerShell y password spraying |
| [06 · Laboratorios](06-Laboratorios/01-Laboratorio-XQL.md) | Ejercicios y checklist |
| [07 · Recursos](07-Recursos/Fuentes-oficiales.md) | Glosario y fuentes |

## Uso en Obsidian
Clona este repositorio y abre su carpeta raíz como **vault**. Se usan enlaces Markdown relativos para que funcionen en Obsidian y en GitHub, más propiedades YAML para filtrar notas. En Obsidian configura los enlaces nuevos como Markdown en lugar de Wikilinks si vas a compartirlos en GitHub.

## Niveles de validación
- **Documentación oficial:** afirmación apoyada en referencia del fabricante.
- **Consulta base:** código didáctico que puede requerir adaptación.
- **Ejemplo adaptable:** nombres hipotéticos o no confirmados en el tenant.
- **Pseudocódigo:** no ejecutable.

**Colaboración:** [normas](CONTRIBUTING.md) · [plantilla de nota](Plantillas/Plantilla-nota.md) · [fuentes oficiales](07-Recursos/Fuentes-oficiales.md).