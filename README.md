---
title: Guía SOC de Cortex XSIAM
aliases: [Inicio XSIAM, Base de conocimiento de XSIAM]
tags: [xsiam, soc, indice]
estado: vigente
actualizado: 2026-11-13
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
10. [Calidad de ingesta y visibilidad](00-Fundamentos/05-Visibilidad-y-calidad-de-ingesta.md)
11. [Marcas de tiempo y ventanas](01-XQL/06-Tiempos-y-ventanas.md)
12. [Triage de alertas](02-Investigacion/05-Triage-de-alertas-y-evidencias.md)
13. [Ciclo de vida de una regla](03-Detecciones/05-Ciclo-de-vida-de-una-regla.md)
14. [Supresión y excepciones](03-Detecciones/06-Supresion-y-excepciones.md)
15. [Laboratorio guiado](06-Laboratorios/03-Caso-integral-identidad-y-deteccion.md)

## Módulos
| Módulo | Contenido |
|---|---|
| [00 · Fundamentos](00-Fundamentos/00-MOC-Fundamentos.md) | Arquitectura, datasets, alertas, issues, cases y salud de ingesta |
| [01 · XQL](01-XQL/00-MOC-XQL.md) | Consultar, filtrar, agregar, unir y depurar datos, tiempos y correlaciones engañosas |
| [02 · Investigaciones](02-Investigacion/01-Procesos-y-parent-child.md) | Procesos, red, identidad, SaaS/IA y triage de evidencias |
| [03 · Detecciones](03-Detecciones/01-Diseno-de-detecciones.md) | IOC, BIOC, correlación, tuning, ciclo de vida y excepciones |
| [04 · Automatización](04-Automatizacion/01-Playbooks-y-automatizacion.md) | Playbooks y respuesta segura |
| [05 · Casos](05-Casos/01-PsExec-PSEXESVC.md) | Casos razonados de PsExec, PowerShell y password spraying |
| [06 · Laboratorios](06-Laboratorios/01-Laboratorio-XQL.md) | Ejercicios, caso integral y checklist |
| [07 · Recursos](07-Recursos/Fuentes-oficiales.md) | Glosario y fuentes |

## Novedades: investigación avanzada y detecciones
- [Calidad de ingesta: cuando faltan logs](00-Fundamentos/05-Visibilidad-y-calidad-de-ingesta.md)
- [Tiempo del evento frente a ingesta y ejecución](01-XQL/06-Tiempos-y-ventanas.md)
- [JOIN: cardinalidad e inferencias erróneas](01-XQL/07-JOIN-y-correlaciones-engañosas.md)
- [Triage con evidencias defendibles](02-Investigacion/05-Triage-de-alertas-y-evidencias.md)
- [De hipótesis a regla mantenible](03-Detecciones/05-Ciclo-de-vida-de-una-regla.md)
- [Supresión, excepciones y riesgo de falsos negativos](03-Detecciones/06-Supresion-y-excepciones.md)
- [Laboratorio integral de identidad](06-Laboratorios/03-Caso-integral-identidad-y-deteccion.md)

## Uso en Obsidian
Clona este repositorio y abre su carpeta raíz como **vault**. Se usan enlaces Markdown relativos para que funcionen en Obsidian y en GitHub, más propiedades YAML para filtrar notas. En Obsidian configura los enlaces nuevos como Markdown en lugar de Wikilinks si vas a compartirlos en GitHub.

## Niveles de validación
- **Documentación oficial:** afirmación apoyada en referencia del fabricante.
- **Consulta base:** código didáctico que puede requerir adaptación.
- **Ejemplo adaptable:** nombres hipotéticos o no confirmados en el tenant.
- **Pseudocódigo:** no ejecutable.

**Colaboración:** [normas](CONTRIBUTING.md) · [plantilla de nota](Plantillas/Plantilla-nota.md) · [fuentes oficiales](07-Recursos/Fuentes-oficiales.md).