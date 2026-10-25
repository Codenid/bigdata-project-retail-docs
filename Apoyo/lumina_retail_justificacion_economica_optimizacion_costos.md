# Justificación económica y optimización de costos

## 1. Objetivo

La justificación económica de Lumina Retail se basa en un principio simple: **la arquitectura debe consumir recursos en función de la carga real y del valor que genera, evitando infraestructura permanente que no sea necesaria**.

La arquitectura TO-BE propuesta utiliza un modelo gestionado y desacoplado sobre GCP:

```text
Fuentes operacionales y eventos
        ↓
Pub/Sub
        ↓
Apache Beam en Dataflow
        ↓
Bronze — Cloud Storage
        ↓
Silver — Cloud Storage / Parquet
        ↓
Gold — BigQuery
        ↓
Consultas, indicadores, alertas y servicios de datos
```

Adicionalmente, los históricos de pedidos, inventario y catálogo ingresan mediante procesos batch y se integran con los eventos operativos en las capas Silver y Gold.

La evaluación económica no busca justificar el costo por la cantidad de componentes desplegados, sino por la capacidad de Lumina para **anticipar quiebres de inventario, reducir desalineaciones geográficas entre stock y demanda, detectar saturación operativa, mejorar la comprensión del comportamiento del cliente y anticipar degradaciones de activos**.

---

## 2. Principio económico de la arquitectura TO-BE

Lumina no forma parte obligatoria del camino transaccional de una venta. Los sistemas operacionales continúan siendo responsables de pedidos, reservas, inventario, despacho, pagos y mantenimiento.

Esta separación tiene una consecuencia económica importante:

> **La plataforma analítica puede escalar, reducir capacidad o incluso degradarse temporalmente sin obligar a sobredimensionarla para garantizar la continuidad de la transacción comercial.**

Por tanto, la arquitectura se diseña con una combinación de servicios administrados y consumo bajo demanda, evitando mantener capacidad de cómputo dedicada cuando no existe procesamiento que la justifique.

---

# 3. Decisiones de arquitectura que reducen costo

## 3.1 Pub/Sub como capa de desacoplamiento

Los productores publican eventos en Pub/Sub sin requerir infraestructura propia de brokers.

### Justificación económica

El uso de un servicio administrado evita mantener permanentemente:

- nodos de mensajería;
- servidores de coordinación;
- almacenamiento dedicado para brokers;
- tareas de parcheo y actualización;
- capacidad reservada para absorber picos;
- personal operativo especializado en la plataforma de mensajería.

La arquitectura paga principalmente por el volumen efectivamente procesado y conserva la posibilidad de aumentar el flujo durante campañas sin desplegar anticipadamente un clúster permanente.

### Optimización

- mantener mensajes compactos;
- publicar únicamente eventos necesarios para los casos de uso;
- evitar replicaciones o tópicos redundantes;
- definir una retención acorde con la necesidad real de recuperación;
- controlar reintentos para evitar tráfico duplicado.

---

## 3.2 Dataflow para procesamiento streaming bajo demanda

La arquitectura utiliza Apache Beam ejecutado en Dataflow para validar, normalizar y evaluar reglas o ventanas sobre eventos.

### Justificación económica

Se evita desplegar un clúster permanente de procesamiento distribuido solo para atender cargas variables.

La carga de RetailSmart presenta dos comportamientos distintos:

- operación nominal;
- picos asociados a campañas como Black Friday o Cyber Monday.

Por ello, resulta económicamente más conveniente ajustar capacidad a la carga observada que dimensionar toda la plataforma para el peor escenario durante todo el mes.

### Optimización

- configurar autoscaling;
- establecer mínimos de workers compatibles con la carga nominal;
- permitir expansión temporal durante campañas;
- reducir capacidad cuando desaparece el backlog;
- revisar claves calientes y lógica intensiva antes de agregar workers;
- evitar mantener jobs streaming que no respondan a una necesidad real de baja latencia.

La arquitectura considera como escenario de validación una operación nominal con **dos workers** y una expansión de hasta **ocho workers durante una ventana limitada de pico**. Esta cifra se utiliza como hipótesis de capacidad y deberá validarse mediante pruebas de rendimiento antes de convertirse en una reserva permanente de recursos.

---

## 3.3 Usar streaming únicamente cuando la demora cambia la decisión

Uno de los principales controles económicos del diseño es evitar convertir todos los datos en streaming.

La regla adoptada es:

> **Streaming se utiliza cuando la demora modifica el valor o la decisión; batch se utiliza cuando el resultado puede obtenerse mediante consolidación periódica.**

Ejemplos:

| Necesidad | Procesamiento | Razón económica |
|---|---|---|
| Riesgo inmediato de quiebre | Streaming | La demora puede impedir actuar a tiempo |
| Telemetría anómala de un activo | Streaming | La oportunidad de la alerta es relevante |
| Plan diario de inventario | Batch | No requiere procesamiento continuo |
| Histórico de ventas | Batch | Puede consolidarse por ventanas |
| Indicadores ejecutivos | Batch / OLAP | El valor no depende de segundos de latencia |

Esto evita pagar procesamiento continuo para información cuya frecuencia natural es diaria o periódica.

---

# 4. Optimización del almacenamiento

## 4.1 Bronze y Silver en Cloud Storage

Cloud Storage se utiliza para conservar:

- datos originales en Bronze;
- datos curados y normalizados en Silver.

Esta elección permite mantener grandes volúmenes de información fuera de motores analíticos de mayor costo de procesamiento.

### Beneficio económico

- almacenamiento desacoplado del cómputo;
- posibilidad de reprocesar sin volver a consultar la fuente;
- menor dependencia de motores de consulta para conservar históricos;
- capacidad de aplicar políticas de ciclo de vida.

---

## 4.2 Uso de Parquet en Silver

Silver utiliza Parquet como formato principal para información estructurada.

### Impacto económico

Parquet reduce:

- volumen físico almacenado mediante compresión;
- bytes leídos en consultas selectivas;
- tiempo de lectura;
- tráfico innecesario entre procesamiento y almacenamiento.

Además, facilita mantener portabilidad fuera de un motor propietario.

---

## 4.3 Retención diferenciada por capa

Para el piloto se propone:

| Capa | Retención inicial | Motivo |
|---|---:|---|
| **Bronze** | 30 días | Recuperación y evidencia reciente |
| **Silver** | 90 días | Reproceso, conciliación y análisis |
| **Gold** | 12 meses | Comparación histórica e indicadores |

La política evita conservar indefinidamente la misma información en todas las capas.

Antes de ampliar una retención se deberá demostrar una necesidad de:

- auditoría;
- regulación;
- reproceso;
- entrenamiento;
- análisis histórico.

> **La retención no debe crecer por defecto; debe crecer por necesidad de negocio.**

---

# 5. Optimización de BigQuery y capa Gold

BigQuery se utiliza para los productos analíticos Gold y las consultas de consumo.

## Justificación económica

Gold contiene información preparada para consulta, no una copia indiscriminada de todas las fuentes.

Esta separación permite utilizar BigQuery únicamente para información que genera valor mediante:

- indicadores;
- planes;
- alertas consolidadas;
- análisis operativo;
- productos de consumo.

## Controles de costo

### Particionamiento

Las tablas Gold deben particionarse por una dimensión temporal compatible con las consultas.

Esto evita explorar períodos completos cuando el consumidor requiere únicamente una ventana concreta.

### Selección de columnas

Las consultas deben recuperar únicamente los atributos necesarios.

Debe evitarse como práctica habitual:

```sql
SELECT *
```

sobre tablas de gran volumen.

### Agregaciones

Los indicadores utilizados repetidamente pueden prepararse previamente en Gold, evitando recalcular grandes históricos en cada consulta.

### Granularidad

Cada producto Gold debe conservar la granularidad necesaria para su caso de uso. Mantener detalle innecesario aumenta almacenamiento y bytes procesados sin agregar valor.

---

# 6. Escalabilidad económica durante campañas

La arquitectura no debe dimensionarse permanentemente para Cyber Monday o Black Friday.

El escenario de referencia considera:

- operación nominal de **20 eventos/s**;
- aproximadamente **51,84 millones de eventos mensuales**;
- alrededor de **74,2 GiB de payload raw mensual**;
- un escenario de pico de **400 eventos/s durante dos horas al día por tres días**;
- aproximadamente **60,05 millones de eventos en el mes que contiene el pico**.

Estas cifras son **parámetros de diseño y prueba**, no una estimación de facturación definitiva.

La estrategia económica consiste en:

```text
Carga nominal
   ↓
capacidad reducida

Pico de campaña
   ↓
escalamiento temporal

Fin del pico
   ↓
reducción de capacidad
```

Esto evita pagar durante todo el año por una capacidad que solo se necesita durante ventanas excepcionales.

---

# 7. Batch sin clúster permanente

La arquitectura no propone desplegar desde el inicio un clúster batch permanente.

El motor batch definitivo debe seleccionarse de acuerdo con el volumen observado.

Esta decisión evita:

- capacidad ociosa;
- administración permanente;
- sobredimensionamiento prematuro;
- costos operativos asociados a clústeres siempre encendidos.

La regla será:

> **No desplegar capacidad persistente hasta que la carga demuestre que resulta necesaria y económicamente conveniente.**

---

# 8. Costo operacional frente a costo tecnológico

La evaluación económica no debe limitarse al costo mensual de GCP.

La arquitectura busca reducir costos del negocio en cuatro frentes principales.

| Problema | Impacto económico que Lumina busca reducir |
|---|---|
| Quiebre de stock | Ventas perdidas y reposiciones tardías |
| Stock lejos de la demanda | Kilómetros, tiempo y costo de última milla |
| Saturación operativa | Horas adicionales, retrasos y uso ineficiente de capacidad |
| Información fragmentada | Decisiones comerciales menos efectivas |
| Fallas de activos | Mantenimiento reactivo, tiempo fuera de servicio y pérdidas |

Por tanto, el beneficio esperado debe compararse contra el costo incremental de operar Lumina.

La ecuación conceptual es:

```text
Valor económico de Lumina
=
costos operativos evitados
+ pérdidas evitadas
+ capacidad mejor utilizada
+ decisiones mejoradas
-
costo total de la plataforma
```

---

# 9. Relación directa entre verticales y consumo

Cada vertical genera un patrón de costo diferente.

| Vertical | Principal consumo | Estrategia de optimización |
|---|---|---|
| **V1. Decisiones en Tiempo Real** | Pub/Sub + Dataflow | Autoscaling, ventanas eficientes, procesamiento solo de señales útiles |
| **V2. Planes Diarios de Inventario** | Batch + almacenamiento + analítica | Ejecución programada, sin cómputo permanente |
| **V3. Informes Operativos** | BigQuery | Particionamiento, agregados y control de bytes consultados |
| **V4. Mantenimiento Proactivo** | Streaming + almacenamiento histórico + modelos | Procesar telemetría necesaria y conservar la granularidad útil |

De esta forma, la estrategia de costos está alineada con las necesidades reales de cada vertical y no con una única configuración tecnológica para todo el sistema.

---

# 10. Controles FinOps propuestos

La optimización económica debe convertirse en una práctica continua y no en una actividad posterior al despliegue.

Se proponen los siguientes controles:

1. etiquetar recursos por entorno, dominio, componente y responsable;
2. definir presupuesto y alertas de consumo;
3. revisar semanalmente costos durante el piloto;
4. identificar recursos sin utilización;
5. medir costo por vertical;
6. medir costo por volumen de eventos procesados;
7. medir bytes almacenados y consultados;
8. controlar crecimiento de Bronze, Silver y Gold;
9. revisar jobs streaming permanentes;
10. validar que cualquier aumento de capacidad responda a una métrica observable;
11. eliminar recursos temporales después de pruebas;
12. documentar cambios que incrementen el costo base.

---

# 11. Indicadores económicos recomendados

Además del costo total mensual, se recomienda seguir indicadores que permitan relacionar gasto tecnológico con valor de negocio.

| Indicador | Propósito |
|---|---|
| **Costo mensual de plataforma** | Control del gasto total |
| **Costo por millón de eventos procesados** | Medir eficiencia del streaming |
| **Costo por GB procesado / consultado** | Observar eficiencia analítica |
| **Costo de procesamiento por campaña** | Medir impacto de picos |
| **Costo por producto Gold** | Identificar productos de información costosos |
| **Costo por vertical** | Relacionar consumo con capacidad de negocio |
| **Costo promedio por pedido** | Relacionar plataforma con eficiencia operacional |
| **Costo de última milla** | Validar beneficio de P2 |
| **Pérdidas por quiebre** | Validar beneficio de P1 |
| **Costo de mantenimiento reactivo** | Validar beneficio de P5 |

---

# 12. Criterio para ampliar capacidad

Un aumento permanente de capacidad deberá justificarse cuando exista evidencia de que alguna de estas condiciones se cumple:

- crecimiento sostenido del backlog;
- incumplimiento recurrente del objetivo de latencia;
- aumento estable del volumen;
- consultas Gold que no cumplen el nivel de servicio aun después de optimización;
- ventanas batch que exceden el tiempo disponible;
- modelos predictivos cuyo tiempo de ejecución impide el uso esperado.

Antes de aumentar infraestructura se deberá verificar que el problema no corresponda a:

- particionamiento incorrecto;
- consultas ineficientes;
- archivos excesivamente pequeños;
- duplicados;
- claves calientes;
- retención innecesaria;
- procesamiento repetido;
- reglas que pueden resolverse mediante batch.

---

# 13. Justificación económica final

La arquitectura TO-BE de Lumina Retail prioriza un modelo **pay-per-use, gestionado y elástico**.

Las principales decisiones que sustentan económicamente la propuesta son:

- Pub/Sub evita operar infraestructura propia de mensajería;
- Dataflow permite aumentar y reducir capacidad de procesamiento;
- streaming se restringe a decisiones cuya oportunidad lo requiere;
- batch se utiliza para cargas periódicas y planes diarios;
- Cloud Storage separa almacenamiento de cómputo;
- Parquet reduce almacenamiento y lectura;
- Bronze, Silver y Gold tienen retenciones diferenciadas;
- BigQuery se reserva para productos analíticos y se optimiza mediante particionamiento y agregación;
- no se propone un clúster batch permanente sin evidencia de necesidad;
- la capacidad de pico no se mantiene durante períodos normales;
- el consumo se controla mediante prácticas FinOps y métricas de negocio.

La propuesta no pretende demostrar que la plataforma tiene el menor costo tecnológico posible de forma aislada. Busca demostrar que:

> **el costo de Lumina es proporcional al volumen procesado y puede justificarse por la reducción de costos e ineficiencias operativas que sus capacidades analíticas, predictivas y prescriptivas permiten atacar.**

---

# 14. Conclusión

Desde la perspectiva económica, la arquitectura TO-BE busca evitar dos extremos:

1. **subdimensionar la solución**, comprometiendo las capacidades de inteligencia requeridas; y
2. **sobredimensionarla**, manteniendo capacidad permanente para escenarios que ocurren solo de forma excepcional.

Por ello, la optimización de costos se integra al propio diseño:

> **almacenar barato, procesar cuando sea necesario, escalar cuando la carga lo requiera y consultar únicamente los datos que generan valor.**

La estimación monetaria final deberá realizarse con la calculadora de consumo utilizando las volumetrías y ventanas de operación validadas. Hasta contar con esas mediciones, las cifras de eventos, almacenamiento y workers deben considerarse **supuestos de dimensionamiento y no compromisos de facturación**.
