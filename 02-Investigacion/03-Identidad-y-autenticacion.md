---
title: Investigación de identidad y autenticación
tags: [soc, identidad, l2]
estado: borrador
---
# Investigar un inicio de sesión sospechoso

## Recoge los hechos
Identidad, hora, IP, resultado, método de autenticación, MFA, dispositivo, aplicación y posibles políticas de acceso condicional.

## Distingue
- **Password spraying:** una fuente prueba contraseñas contra varias cuentas.
- **Brute force:** múltiples intentos contra una cuenta.
- **Credential stuffing:** intentos con combinaciones de usuario/contraseña previamente obtenidas.
- **Impossible travel:** alerta basada en ubicaciones incompatibles; revisar VPN, proxy, GPS/IP y sincronización de eventos.

## Hipótesis vs pruebas
Muchos fallos de autenticación no confirman compromiso. Un éxito posterior tampoco prueba por sí solo que sea la misma sesión o el mismo actor.

## Práctica
Diseña una consulta de dos fases: primero cuenta fallos por IP y usuarios distintos; luego investiga si alguna cuenta tuvo accesos correctos desde el mismo origen. No unas solo por nombre de usuario ignorando el tiempo.

[Diseño de detecciones](../03-Detecciones/01-Diseno-de-detecciones.md).
