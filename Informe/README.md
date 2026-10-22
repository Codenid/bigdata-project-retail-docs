# Lumina Retail | Seguimiento del documento

## Documento principal

**Lumina Retail: Plataforma de datos para una operación integrada**  
Propuesta ejecutiva de arquitectura empresarial para RetailSmart Global.

El informe presenta el diagnóstico de negocio, la arquitectura objetivo, los controles de datos y resiliencia, y una hoja de ruta de implementación. Los análisis de costos quedan fuera del alcance de esta edición.

`Lumina_Retail_Informe_anterior.pdf` se conserva como referencia histórica y no es la versión vigente.

## Criterios de Arquitectura de Solución

El proyecto sigue los [Criterios de Arquitectura de Solución](../CRITERIOS_ARQUITECTURA_SOLUCION.md) para registrar decisiones, mantener alineados el modelo y el diseño aprobado, y guiar cualquier cambio técnico.

## Compilación

Desde esta carpeta, ejecutar:

```sh
make
```

El comando genera `Lumina_Retail_Informe.pdf` a partir de `Lumina_Retail_Informe.tex`.
Las siete imágenes y las macros LaTeX se mantienen en `figuras/conceptuales/` e `integracion/` dentro de esta carpeta.

## Seguimiento de cambios

| Fecha | Cambio |
|---|---|
| 21 de octubre de 2026 | Se adopta un título y enfoque ejecutivo orientados a RetailSmart Global. Se retira el control de versiones del informe y se conserva este seguimiento en el README. |
| 21 de octubre de 2026 | Se reemplazan los hitos con fechas fijas por fases, dependencias y criterios de avance de negocio. |
| 21 de octubre de 2026 | Se retiran el análisis y las referencias de costos para retomarlos en una etapa posterior. |
| 21 de octubre de 2026 | Se corrigen los nombres de la fuente LaTeX, el Makefile, los parámetros y los diagramas de aplicaciones AS-IS/TO-BE. |
| 21 de octubre de 2026 | Se agrega la figura ejecutiva de responsabilidades Bronze, Silver y Gold. |
| 21 de octubre de 2026 | Se integran seis ilustraciones conceptuales en el cuerpo y una en portada; se alinea la terminología con la Arquitectura de Solución. |
