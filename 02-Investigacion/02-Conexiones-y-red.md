---
title: Investigación de red en XSIAM
tags: [soc, red, firewall]
---
# Investigar actividad de red

## Pregunta SOC
¿Un equipo estableció realmente una conexión con un destino sospechoso? ¿Fue permitida o bloqueada? ¿Podemos atribuirla a un proceso?

## Secuencia
1. Identifica fuente: firewall, proxy o EDR.
2. Verifica IP origen/destino, puerto, protocolo, acción y timestamp.
3. Confirma NAT, VPN, proxy de salida o CDN cuando afecten a la atribución.
4. Busca repetición, volumen, respuestas y contexto del host.
5. Si hay telemetría endpoint, intenta relacionar la conexión con proceso y usuario.

Una conexión a un dominio de riesgo no prueba exfiltración. Un evento de firewall no siempre contiene el proceso que la originó.

**Ejercicio:** toma una conexión de laboratorio bloqueada y redacta qué sabemos y qué no sabemos.

[JOIN para enriquecer](../01-XQL/04-JOIN-entre-datasets.md).
