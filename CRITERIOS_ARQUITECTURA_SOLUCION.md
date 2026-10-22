# Criterios de la Arquitectura de Solución

## Responsabilidad del diseño

Las decisiones pertenecen al diseño de la **Arquitectura de Solución**. El diseño aprobado documenta los componentes de solución, sus responsabilidades, flujos, contratos, tecnologías, restricciones y decisiones arquitectónicas.

ArchiMate puede utilizarse como notación de modelado, y Archi como herramienta, para representar vistas del modelo de solución. Una notación o herramienta no decide el diseño ni constituye el entregable rector. La representación debe mantenerse consistente con las decisiones documentadas.

## Lenguaje que se utiliza

- Arquitectura de Solución.
- diseño aprobado.
- componente de solución.
- decisión arquitectónica.
- modelo de solución.

En pies de figuras y referencias puede utilizarse **«Modelo de solución elaborado por el equipo»**. Si resulta útil identificar la notación, escribir **«Vista del modelo de solución representada con notación ArchiMate»**.

## Lenguaje que debe corregirse

| Expresión a revisar | Redacción según el contexto |
| --- | --- |
| «según ArchiMate» | «de acuerdo con el diseño aprobado de la Arquitectura de Solución» |
| «ArchiMate define» | «la Arquitectura de Solución define» |
| «ArchiMate es la fuente de verdad» | «el diseño aprobado documenta las decisiones arquitectónicas y sus versiones vigentes» |
| «las vistas ArchiMate sustentan los componentes y las relaciones» | «el modelo de solución documenta los componentes y las relaciones derivados de las decisiones arquitectónicas» |
| «sin sustituir el detalle de ArchiMate» | «sin sustituir las vistas detalladas de la Arquitectura de Solución» |
| «Vistas completas de ArchiMate» | «Vistas de la Arquitectura de Solución» |

Las sustituciones requieren lectura del contexto. Se conservan las menciones a la notación o herramienta cuando describen cómo se elaboró o representó una vista.

## Secuencia para modificar la implementación

Si la implementación requiere cambiar un componente, flujo, contrato o tecnología, se respeta este orden:

1. **Registrar la decisión arquitectónica:** problema, alternativas, decisión y consecuencias; identificar los elementos afectados.
2. **Actualizar la Arquitectura de Solución:** modelo, vistas y especificaciones afectadas, con su versión.
3. **Actualizar el catálogo y la documentación:** componentes, contratos, diccionario, recursos, configuración y trazabilidad que correspondan.
4. **Modificar la implementación:** código, infraestructura o configuración, vinculando el cambio con la decisión y las versiones anteriores.

La verificación posterior comprueba que implementación, modelo de solución y documentación correspondan a la misma decisión. Si el diseño aprobado y el código difieren, se registra la diferencia y se resuelve siguiendo esta secuencia.

## Alcance de esta integración visual

La incorporación de las ilustraciones y la corrección de términos son cambios editoriales. No justifican introducir o sustituir un servicio, alterar un contrato o crear un flujo técnico. Cualquier necesidad de ese tipo sigue la secuencia anterior.

## Registro breve de una decisión

- **ID y fecha:** identificador estable de la decisión.
- **Contexto:** problema o restricción que exige decidir.
- **Alternativas:** opciones consideradas y compromisos principales.
- **Decisión arquitectónica:** opción adoptada y justificación.
- **Impacto:** componentes, flujos, contratos y tecnologías afectados.
- **Actualización de la Arquitectura de Solución:** ubicación y versión.
- **Actualización documental:** catálogo y documentos afectados.
- **Implementación y validación:** referencia al cambio y evidencia de correspondencia.
