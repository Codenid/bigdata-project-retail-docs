# JUSTIFICACIÓN ECONÓMICA Y ESTIMACIÓN DE COSTOS
## Arquitectura TO-BE — Lumina Retail

**RetailSmart Global**  
**Plataforma analítica y de datos para operación omnicanal**

> **Objetivo del apartado:** estimar el costo de referencia de la arquitectura TO-BE agrupando los componentes por función, diferenciando precios oficiales, supuestos de dimensionamiento y costos estimados.

> **Nota de estimación:** los precios unitarios usados corresponden a tarifas públicas de Google Cloud consultadas al **24 de octubre de 2026**. Los volúmenes, workers y patrones de consulta son **supuestos de dimensionamiento** del proyecto y no representan datos productivos reales de RetailSmart. Los montos son **estimaciones referenciales**. La facturación real puede variar por configuración, operaciones auxiliares, transferencia de datos, servicios complementarios, descuentos por compromiso y acuerdos comerciales. Dado que RetailSmart representa una empresa de gran escala y crecimiento exponencial, un convenio empresarial o compromiso de consumo con Google Cloud podría reducir los precios efectivos frente a las tarifas públicas.

---

# 1. Criterio de agrupación de costos

Para facilitar la estimación económica, los costos de Lumina Retail se agrupan por capacidad arquitectónica:

| Grupo de costo | Propósito | Componentes principales |
|---|---|---|
| **A. Ingesta y mensajería** | Recibir y desacoplar eventos operacionales | Pub/Sub |
| **B. Procesamiento streaming** | Procesar eventos de baja latencia | Dataflow / Apache Beam |
| **C. Almacenamiento Data Lake** | Conservar Bronze y Silver | Cloud Storage + Parquet |
| **D. Analítica y consumo Gold** | Consultas, indicadores y productos analíticos | BigQuery |
| **E. Operación y observabilidad** | Logs, métricas, monitoreo y soporte | Cloud Logging, Monitoring, discos y servicios auxiliares |
| **F. Procesamiento batch** | Cargas históricas, planes diarios y reprocesos | Jobs batch bajo demanda |
| **G. Reserva de crecimiento** | Absorber expansión del negocio y campañas | Autoscaling, mayor volumen y consultas |

Esta agrupación permite identificar qué parte del costo es fija, variable o dependiente del crecimiento.

---

# 2. Supuestos de dimensionamiento

La estimación base corresponde a un escenario inicial de validación de la arquitectura.

| Variable | Supuesto |
|---|---:|
| Carga nominal | **20 eventos/s** |
| Eventos mensuales nominales | **51.84 millones** |
| Payload raw mensual | **74.2 GiB** |
| Pico de campaña | **400 eventos/s** |
| Duración del pico | **2 h/día durante 3 días** |
| Dataflow nominal | **2 workers streaming** |
| Dataflow pico | **hasta 8 workers** |
| Worker de referencia | **4 vCPU + 15 GiB RAM** |
| Silver | **50 % de Bronze** |
| Gold | **20 % de Bronze** |
| Consultas BigQuery | **2 TiB/mes** |

> **Importante:** estos valores no constituyen una predicción del volumen definitivo de una organización con más de 9 millones de clientes. Deben recalibrarse con telemetría real antes de producción.

---

# 3. Precios unitarios de referencia

| Servicio | Unidad | Tarifa pública usada | Clasificación |
|---|---|---:|---|
| Pub/Sub | Throughput después de 10 GiB/mes gratuitos | **USD 40/TiB** | Precio oficial |
| Dataflow streaming | vCPU | **USD 0.069/h** | Precio oficial |
| Dataflow streaming | Memoria | **USD 0.003557/GiB-h** | Precio oficial |
| Dataflow Streaming Engine | Unidad de cómputo | **USD 0.089/unidad** | Precio oficial |
| Cloud Storage Standard | Almacenamiento regional | **≈ USD 0.020/GiB-mes** | Referencia pública |
| BigQuery on-demand | Datos analizados después de 1 TiB/mes gratuito | **USD 6.25/TiB** | Precio oficial |
| BigQuery active logical storage | Después de 10 GiB/mes gratuitos | **≈ USD 0.023/GiB-mes** | Referencia pública |

---

# 4. Estimación de costos agrupados

## 4.1 Grupo A — Ingesta y mensajería

### Pub/Sub

Se considera una publicación y una entrega principal al consumidor streaming.

**Volumen estimado:**

```text
74.2 GiB publicados
+ 74.2 GiB entregados
= 148.4 GiB/mes
```

**Estimación:**

```text
(148.4 - 10) / 1024 × USD 40
≈ USD 5.41/mes
```

| Concepto | Estimado mensual |
|---|---:|
| Pub/Sub | **USD 5.41** |

**Lectura económica:** el costo de mensajería es bajo frente al cómputo y crece principalmente con el volumen procesado.

---

## 4.2 Grupo B — Procesamiento streaming

### Dataflow nominal

```text
2 workers × 730 h ×
[(4 vCPU × 0.069) + (15 GiB × 0.003557)]
≈ USD 480.86/mes
```

### Dataflow adicional de campaña

```text
6 workers adicionales × 6 h ×
[(4 vCPU × 0.069) + (15 GiB × 0.003557)]
≈ USD 11.86
```

| Concepto | Estimado mensual |
|---|---:|
| Dataflow nominal: CPU + RAM | **USD 480.86** |
| Capacidad adicional de pico | **USD 11.86** |
| **Subtotal grupo B** | **USD 492.72** |

**No incluido:** Streaming Engine, Persistent Disk y otros consumos dependientes de la ejecución real.

**Lectura económica:** este es el principal componente cuantificable de la solución. Por ello, el streaming debe utilizarse únicamente cuando la baja latencia aporta valor de negocio.

---

# 5. Grupo C — Almacenamiento Data Lake

## Bronze

```text
74.2 GiB × USD 0.020
≈ USD 1.48/mes
```

## Silver

Asumiendo 50 % del volumen Bronze:

```text
37.1 GiB × USD 0.020
≈ USD 0.74/mes
```

| Capa | Volumen estimado | Costo mensual |
|---|---:|---:|
| Bronze | 74.2 GiB | **USD 1.48** |
| Silver | 37.1 GiB | **USD 0.74** |
| **Subtotal grupo C** | 111.3 GiB | **USD 2.22** |

**Lectura económica:** el almacenamiento no representa el principal costo del escenario base. El uso de Parquet en Silver ayuda a reducir volumen y lectura.

---

# 6. Grupo D — Analítica y consumo Gold

## Almacenamiento Gold

Asumiendo 20 % del volumen Bronze:

```text
14.84 GiB
```

Con 10 GiB gratuitos:

```text
(14.84 - 10) × USD 0.023
≈ USD 0.11/mes
```

## Consultas BigQuery

Escenario de referencia:

```text
2 TiB consultados/mes
- 1 TiB gratuito
= 1 TiB facturable
```

```text
1 × USD 6.25
= USD 6.25/mes
```

| Concepto | Estimado mensual |
|---|---:|
| Gold storage | **USD 0.11** |
| Consultas BigQuery | **USD 6.25** |
| **Subtotal grupo D** | **USD 6.36** |

**Lectura económica:** el costo depende más de los bytes consultados que del almacenamiento. Particionamiento, selección de columnas y agregados son los principales controles.

---

# 7. Grupo E — Operación y observabilidad

Estos costos no se incluyen todavía en el subtotal porque dependen de la configuración real.

| Componente | Estado |
|---|---|
| Streaming Engine | **Precio oficial conocido; consumo pendiente de medición** |
| Persistent Disk | **Dependiente del job final** |
| Cloud Logging | **Dependiente del volumen de logs** |
| Cloud Monitoring | **Dependiente de métricas y retención** |
| Transferencia de red | **Dependiente de flujo entre regiones/servicios** |
| Operaciones Cloud Storage | **Dependiente de cantidad de requests** |

> Este grupo debe incorporarse al TCO final una vez desplegada y medida la solución.

---

# 8. Grupo F — Procesamiento batch

La arquitectura no contempla inicialmente un clúster batch permanente.

Los procesos batch se ejecutarán únicamente para:

- carga histórica;
- planes diarios de inventario;
- conciliaciones;
- reprocesos;
- preparación de datasets.

**Decisión económica:**

> **No mantener cómputo batch permanente mientras la carga no demuestre que es necesario.**

Por tanto, este grupo se contabilizará por ejecución real y no se incorpora al subtotal base.

---

# 9. Consolidado mensual

| Grupo | Estimado mensual |
|---|---:|
| A. Ingesta y mensajería | **USD 5.41** |
| B. Procesamiento streaming | **USD 492.72** |
| C. Data Lake | **USD 2.22** |
| D. Gold / Analítica | **USD 6.36** |
| E. Operación y observabilidad | Pendiente de medición |
| F. Batch | Variable por ejecución |
| **Subtotal cuantificable mensual** | **USD 506.71** |

### Referencia anual

```text
USD 506.71 × 12
≈ USD 6,080.50/año
```

> **No debe interpretarse como TCO definitivo.** Es el subtotal de los componentes actualmente cuantificables con los supuestos definidos.

---

# 10. Sensibilidad al crecimiento

Dado que RetailSmart opera a gran escala y presenta crecimiento exponencial, se evalúan escenarios de crecimiento del volumen.

| Escenario | Eventos/s | Eventos/mes | Bronze raw/mes |
|---|---:|---:|---:|
| Base | 20 | 51.84 M | 74.2 GiB |
| **5x** | 100 | 259.2 M | 371 GiB |
| **10x** | 200 | 518.4 M | 742 GiB |
| Pico campaña de referencia | 400 | Variable | Variable |

**Interpretación:**

- Pub/Sub y Storage crecerán aproximadamente con el volumen.
- Dataflow no debe extrapolarse linealmente: depende de autoscaling, ventanas, estado y lógica del pipeline.
- BigQuery depende de bytes realmente consultados.
- Para una organización de este tamaño, las pruebas de carga deben preceder cualquier aumento permanente de capacidad.

---

# 11. Impacto de acuerdos comerciales

La estimación anterior utiliza precios públicos.

Para una empresa del tamaño de RetailSmart podrían existir:

- committed use discounts;
- contratos empresariales;
- descuentos por volumen;
- créditos de consumo;
- condiciones negociadas con Google Cloud.

Como referencia, los compromisos públicos de Dataflow pueden ofrecer aproximadamente:

| Modalidad | Descuento de referencia | CPU + RAM nominal estimado |
|---|---:|---:|
| Sin compromiso | 0 % | **USD 480.86/mes** |
| Compromiso 1 año | ~20 % | **USD 384.69/mes** |
| Compromiso 3 años | ~40 % | **USD 288.51/mes** |

Estos valores son ilustrativos y no sustituyen una cotización empresarial.

---

# 12. Indicadores de eficiencia

## Eficiencia tecnológica

```text
Costo tecnológico por millón de eventos
= USD 506.71 / 51.84
≈ USD 9.77
```

Este indicador permite comparar el costo relativo de la plataforma a medida que aumenta el volumen.

## Eficiencia operacional

```text
Costo operativo por pedido
=
(preparación
+ almacenamiento
+ distribución
+ última milla)
/
pedidos entregados
```

Lumina busca reducir este indicador mediante:

- menor distancia de última milla;
- menos reposiciones urgentes;
- mejor posicionamiento de inventario;
- anticipación de saturación;
- mantenimiento proactivo.

---

# 13. Controles FinOps

Se propone monitorear:

| Indicador | Uso |
|---|---|
| Costo mensual total | Control presupuestario |
| Costo por vertical | Identificar capacidades costosas |
| Costo por millón de eventos | Medir eficiencia de procesamiento |
| GiB almacenados por capa | Controlar crecimiento Bronze/Silver/Gold |
| TiB consultados en BigQuery | Controlar costo analítico |
| Costo por campaña | Medir impacto de picos |
| Workers promedio/máximo | Evaluar autoscaling |
| Costo operativo por pedido | Conectar plataforma con valor de negocio |

---

# 14. Conclusión económica

La estimación muestra que, bajo el escenario inicial, el principal componente cuantificable de costo es el **procesamiento streaming**, mientras que mensajería, almacenamiento y analítica representan una proporción menor.

La estrategia económica de la arquitectura TO-BE se resume en:

> **almacenar de forma eficiente, utilizar streaming únicamente cuando la baja latencia genera valor, ejecutar batch bajo demanda, escalar temporalmente durante campañas y medir el costo unitario de forma continua.**

Para una organización grande y en crecimiento exponencial como RetailSmart, el objetivo no debe ser mantener una infraestructura mínima, sino una plataforma **elástica, observable y económicamente controlable**, capaz de crecer sin sobredimensionar permanentemente la capacidad.

El presupuesto definitivo deberá construirse con métricas reales de producción y con las condiciones comerciales negociadas entre RetailSmart y Google Cloud.


---

# 15. Resumen ejecutivo de costo del proyecto

A partir de los componentes que actualmente pueden cuantificarse con los supuestos definidos, el costo de operación de la plataforma en el escenario base es:

| Concepto | Estimación |
|---|---:|
| **Costo mensual cuantificable** | **USD 506.71/mes** |
| **Costo anual cuantificable** | **USD 6,080.52/año** |
| Reserva de planeamiento propuesta (20 %) | USD 1,216.10/año |
| **Presupuesto anual de referencia para infraestructura** | **≈ USD 7,296.62/año** |

La reserva del 20 % no corresponde a una tarifa de Google Cloud. Es una **holgura presupuestaria propuesta para planeamiento** mientras no se hayan medido en producción los consumos de Streaming Engine, discos, logging, operaciones de almacenamiento, procesos batch y otros servicios auxiliares.

Por tanto, para efectos del proyecto se propone comunicar el siguiente valor:

> **Presupuesto inicial de infraestructura cloud: aproximadamente USD 600–610 mensuales, equivalente a unos USD 7.3 mil anuales para el escenario base.**

Este monto representa el **costo operativo tecnológico estimado de la plataforma**, no el costo total de implementación del proyecto.

## Qué no está incluido

El presupuesto anterior no incorpora:

- horas de desarrollo e implementación;
- arquitectura, ingeniería de datos y QA;
- soporte operativo del equipo;
- licencias de herramientas externas;
- capacitación;
- migración de datos;
- costos organizacionales;
- eventuales servicios profesionales de Google Cloud o terceros.

Por ello, el **TCO completo del proyecto** deberá calcularse posteriormente como:

```text
TCO del proyecto
=
Infraestructura Cloud
+ Implementación
+ Operación y soporte
+ Licencias externas
+ Evolución / mantenimiento
```

## Lectura para RetailSmart

El valor aproximado de **USD 7.3 mil anuales** debe entenderse como un presupuesto inicial para validar y operar el escenario base de la arquitectura TO-BE. Debido al tamaño de RetailSmart y a su crecimiento exponencial, este valor no debe proyectarse linealmente para escenarios 5x o 10x.

Antes de ampliar capacidad de forma permanente se deberán ejecutar pruebas de carga y medir:

- volumen real de eventos;
- utilización promedio de workers;
- bytes procesados en BigQuery;
- crecimiento de Bronze, Silver y Gold;
- consumo de Streaming Engine;
- costo de logging y observabilidad;
- comportamiento en campañas.

Con estas métricas podrá construirse un presupuesto empresarial más preciso y negociar descuentos por volumen o compromisos de consumo con Google Cloud.

> **Conclusión ejecutiva:** para el escenario inicial de la arquitectura, RetailSmart debería considerar un presupuesto cloud del orden de **USD 7,000–7,500 por año**, sujeto a recalibración con datos productivos y a posibles descuentos corporativos con GCP.
