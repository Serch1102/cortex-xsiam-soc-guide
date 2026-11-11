---
title: Creación de reglas de correlación
tags: [xsiam, xql, detecciones, l3]
estado: nota-educativa
---
# Correlation Rules: de XQL a una detección útil

## Problema SOC
Una consulta encuentra una conducta sospechosa, pero queremos que el sistema la evalúe periódicamente y genere información investigable.

## Procedimiento
1. Definir hipótesis de amenaza y fuentes.
2. Validar datasets, campos y cobertura real.
3. Probar la consulta con datos históricos.
4. Revisar volumen, cardinalidad y falsos positivos.
5. Elegir Real Time o Scheduled según operaciones utilizadas y versión.
6. Configurar severidad, entidad, descripción y supresión adecuada.
7. Preparar una consulta de investigación o *drill-down*.
8. Habilitar solamente con autorización y monitorizar errores.

## Idea práctica
«La misma IP ha intentado acceder a muchas cuentas en una ventana corta» no significa necesariamente ataque. Revisa NAT, VPN, automatizaciones y resultados de acceso.

## Errores frecuentes
- Generar un Issue por cada registro en lugar de por entidad.
- Suprimir por una clave demasiado general.
- Confundir coincidencia temporal con causalidad.
- Usar JOIN en una regla Real Time sin verificar restricciones.

**Fuente:** [Documentación oficial de creación de reglas](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Create-a-correlation-rule).

[Diseño](01-Diseno-de-detecciones.md) · [Validación y tuning](04-Validacion-y-tuning.md).
