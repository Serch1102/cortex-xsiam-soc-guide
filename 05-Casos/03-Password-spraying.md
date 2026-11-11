---
title: Caso de password spraying
tags: [soc, identidad, detecciones]
---
# Password spraying: contar cuentas diferentes

## Qué queremos detectar
Una IP o entidad intentando autenticarse contra múltiples cuentas con pocos intentos por cuenta.

## Diseño
- Identificar origen, cuenta, resultado y tiempo.
- Contar intentos y usuarios únicos por ventana.
- Revisar éxitos posteriores, MFA y políticas.
- Controlar NAT, VPN, proxies y servicios autorizados.
- Definir supresión e investigación posterior.

## Limitación
Muchos fallos desde una IP común no bastan para diagnosticar un ataque.

## Práctica
Describe un umbral inicial y cómo validarlo frente al volumen habitual del cliente. No uses datos reales en este repositorio.

[Agregaciones](../01-XQL/03-Agregaciones-COMP.md).
