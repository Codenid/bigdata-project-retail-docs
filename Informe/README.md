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
El proyecto usa pdfLaTeX mediante `latexmk`; compilar desde `Informe/` mantiene válidas las rutas relativas.

## Fuentes y recursos para futuras modificaciones

- `Lumina_Retail_Informe.tex`, `parametros.tex` y `integracion/figuras_conceptuales.tex` son las fuentes LaTeX.
- Las siete ilustraciones conceptuales existentes están en `figuras/conceptuales/`.
- Las aperturas y viñetas utilizadas están en `../Lumina_Retail_Imagenes_Editoriales_Actual/01_portadas/` y `03_vinetas/`.
- Los SVG editables de `02_apoyo/` y `05_anexos_originales/` son las fuentes de los diagramas. La compilación incorpora los PDF vectoriales guardados en sus subcarpetas `derivados_pdf/`.
- `05_anexos_originales/derivados_svg/08_integracion_detallada_tobe_encuadrado.svg` conserva el encuadre editorial de la vista detallada; el SVG original no se modifica.

Se retiraron PNG de diagramas que ya tienen fuente SVG y derivado PDF, copias idénticas de las ilustraciones de `figuras/conceptuales/`, catálogos de vista previa e instrucciones de integración ya aplicadas. No son necesarios para compilar ni para editar las fuentes vigentes. Los derivados PDF permiten compilar sin ejecutar una conversión; para actualizarlos desde los SVG se utilizó CairoSVG.

## Seguimiento de cambios

| Fecha | Cambio |
|---|---|
| 21 de octubre de 2026 | Se adopta un título y enfoque ejecutivo orientados a RetailSmart Global. Se retira el control de versiones del informe y se conserva este seguimiento en el README. |
| 21 de octubre de 2026 | Se reemplazan los hitos con fechas fijas por fases, dependencias y criterios de avance de negocio. |
| 21 de octubre de 2026 | Se retiran el análisis y las referencias de costos para retomarlos en una etapa posterior. |
| 21 de octubre de 2026 | Se corrigen los nombres de la fuente LaTeX, el Makefile, los parámetros y los diagramas de aplicaciones AS-IS/TO-BE. |
| 21 de octubre de 2026 | Se agrega la figura ejecutiva de responsabilidades Bronze, Silver y Gold. |
| 21 de octubre de 2026 | Se integran seis ilustraciones conceptuales en el cuerpo y una en portada; se alinea la terminología con la Arquitectura de Solución. |
| 22 de octubre de 2026 | Se añaden aperturas de capítulos y anexos, nueve gráficos vectoriales y seis viñetas; se convierten los ocho diagramas SVG de anexos a PDF, se mejora la jerarquía de aperturas y el flujo de figuras, y se encuadra la vista detallada en una única lámina vectorial personalizada. |
| 22 de octubre de 2026 | Se depuran copias idénticas, PNG redundantes, catálogos de vista previa e instrucciones ya aplicadas; se conservan las fuentes editables y todos los recursos necesarios para compilar. |
