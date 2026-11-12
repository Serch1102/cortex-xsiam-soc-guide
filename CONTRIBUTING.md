# Cómo colaborar con esta guía

## Objetivo
Cada contribución debe permitir que un analista entienda una función de XSIAM y la aplique **sin interpretar más de lo que demuestra la evidencia**.

## Estructura de una nota
1. Problema real de SOC.
2. Explicación sencilla.
3. Funcionamiento técnico.
4. Pasos en consola o consulta XQL.
5. Cómo interpretar el resultado.
6. Falsos positivos, limitaciones y permisos.
7. Preguntas para el analista y fuentes.

## Analogías y comparaciones entre plataformas

Cuando sirvan para aprender, añade una sección **«Analogía y equivalencias con otras herramientas»**. Prioriza **Google SecOps (Chronicle)** y **Microsoft Sentinel/Azure**. Parte de una situación real del SOC, compara finalidad y diferencias técnicas y aclara los casos en los que no existe equivalencia directa. No uses una tabla de productos como sustituto de una explicación del funcionamiento de XSIAM. Conserva documentación del fabricante cuando el comportamiento exacto sea relevante.

## Control de cambios
Trabaja en una rama y abre una Pull Request. Revisión por otra persona antes de fusionar. Indica versión de XSIAM, fuentes consultadas y si la query se ha probado en un entorno autorizado.

## Seguridad y confidencialidad
**Este repositorio es público mientras el propietario no cambie su visibilidad.** No incluir eventos de producción, datos personales, direcciones internas, nombres de cliente, capturas, tokens, reglas operativas sensibles ni procedimientos privados. Usa datos sintéticos. Para contenido interno, considera un repositorio privado corporativo distinto.

## Nomenclatura
Redacción en español; conserva palabras oficiales como XQL, dataset, preset, IOC, BIOC, Issue, Case o JOIN cuando faciliten encontrar el mismo elemento en la consola.

## Antes de aceptar una PR
- [ ] Los enlaces Markdown funcionan.
- [ ] La nota no expone información sensible.
- [ ] Queda claro qué datos son supuestos.
- [ ] No se presenta una coincidencia como prueba concluyente.
- [ ] Se incluye una referencia oficial cuando se explica el comportamiento del producto.
