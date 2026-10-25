# Alineamiento Final de Servicios de Datos y Aplicaciones de Lumina Retail

## 1. Propósito

Luego de revisar la problemática de RetailSmart, la visión de Lumina Retail y la frontera de responsabilidades entre la plataforma de datos y los sistemas operacionales, se establece una lectura final de las cuatro verticales de servicios de datos.

El objetivo es asegurar que cada vertical responda a problemas concretos del negocio y que su diseño tecnológico derive de dicha necesidad, evitando que Lumina sea tratada como un sistema transaccional.

El principio rector es el siguiente:

> **Lumina observa, integra, analiza, aprende, predice y recomienda. El ecosistema operacional decide y ejecuta.**

Bajo este enfoque, Lumina no reemplaza a los sistemas de pedidos, inventario, almacenes, transporte, pagos o mantenimiento. Su función es transformar datos provenientes de estos dominios en señales, predicciones, alertas, métricas y recomendaciones que permitan tomar mejores decisiones.

---

## 2. Problemas de negocio consolidados

### P1. Riesgo de quiebre de stock

> **Sé cuánto stock tengo, pero no dónde ni cuándo se va a agotar.**

El sistema operacional debe conocer el stock actual. Lumina utiliza históricos, evolución del inventario, ventas, demanda y estacionalidad para anticipar el riesgo de agotamiento.

### P2. Desalineación geográfica entre stock y demanda

> **Sé dónde está mi stock, pero no si está cerca de donde se concentrará la demanda.**

El problema no es únicamente disponer de inventario, sino tenerlo en ubicaciones adecuadas para atender la demanda futura con menor costo y tiempo de entrega.

### P3. Saturación de la capacidad operativa

> **Sé que la demanda está creciendo, pero no cuándo va a superar mi capacidad operativa.**

Lumina debe ayudar a anticipar cuándo el volumen esperado de pedidos puede superar la capacidad de preparación, almacenamiento o distribución.

### P4. Visión fragmentada del comportamiento del cliente

> **Sé quién es el cliente, pero no entiendo su comportamiento completo entre unidades y canales.**

La identidad maestra del cliente debe ser resuelta por capacidades externas de MDM. Lumina utiliza esa identidad consolidada para analizar patrones de comportamiento entre unidades de negocio y canales.

### P5. Riesgo de degradación de activos

> **Sé que el equipo está funcionando ahora, pero no cuándo empezará a degradarse o fallar.**

Lumina utiliza telemetría e históricos para detectar anomalías, degradación y riesgo de falla, sin reemplazar a los sistemas de mantenimiento.

---

## 3. Interpretación final de la visión de Lumina Retail

La visión de Lumina Retail plantea una operación más integrada, eficiente y orientada por datos. Sin embargo, varias capacidades descritas originalmente —como picking, despacho, rutas, pagos y seguimiento operacional— pertenecen naturalmente al ecosistema transaccional.

Por ello, la visión se interpreta de la siguiente manera:

> **Lumina Retail es la capa inteligente de datos de RetailSmart, capaz de integrar señales provenientes de clientes, inventario, ventas, logística y activos físicos para generar análisis, predicciones y recomendaciones que permitan al ecosistema operacional tomar mejores decisiones.**

Esto implica que Lumina puede recomendar:

- qué zonas presentan mayor riesgo de quiebre;
- dónde se concentrará probablemente la demanda;
- qué ubicaciones de inventario resultan más convenientes;
- cuándo una campaña puede superar la capacidad disponible;
- qué pedidos o zonas presentan oportunidades de consolidación;
- qué activos presentan signos de degradación;
- qué tendencias operativas requieren intervención.

Pero Lumina no ejecuta directamente:

- reservas de inventario;
- creación o modificación de pedidos;
- picking físico;
- asignación de operarios;
- asignación de vehículos;
- despacho;
- procesamiento de pagos;
- mantenimiento físico de activos.

---

## 4. Alineamiento de las cuatro verticales

Las cuatro verticales no deben interpretarse como soluciones independientes. Cada una aporta una capacidad distinta y puede contribuir a uno o varios problemas de negocio.

### 4.1 Matriz general problema–vertical

| Problema de negocio | V1. Decisiones en Tiempo Real | V2. Planes Diarios de Inventario | V3. Informes Operativos | V4. Mantenimiento Proactivo |
|---|---|---|---|---|
| **P1. Riesgo de quiebre de stock** | Apoya con señales recientes de stock y demanda | **Principal**: predicción de quiebre y necesidad de reposición | Mide quiebres, disponibilidad y evolución | — |
| **P2. Stock lejos de la demanda** | Apoya con señales de demanda y ubicación | **Principal**: predicción geográfica y posicionamiento de inventario | Mide costo, distancia y tiempos de entrega | — |
| **P3. Saturación operativa** | **Principal** para detectar presión y cambios rápidos | Apoya con previsiones de campañas y demanda | **Principal** para analizar capacidad, tiempos y costos | Apoya cuando la infraestructura reduce capacidad |
| **P4. Comportamiento fragmentado del cliente** | Apoya con clics e interacción reciente | Puede enriquecer el forecast de demanda | **Principal** para análisis transversal por cliente/canal | — |
| **P5. Degradación de activos** | Apoya con telemetría reciente | — | Mide desempeño y fallas históricas | **Principal**: anomalías y mantenimiento predictivo |

La relación no es de tipo **un problema = una vertical**. Los problemas atraviesan diferentes capacidades de datos y procesamiento.

---

## 5. Vertical 1: Decisiones en Tiempo Real

### Objetivo

Responder a la pregunta:

> **¿Qué está ocurriendo ahora o qué está cambiando rápidamente?**

Esta vertical utiliza procesamiento en streaming o baja latencia para captar señales recientes provenientes de la operación.

### Fuentes principales

- sensores IoT;
- GPS de repartidores;
- clics e interacciones digitales;
- eventos recientes de stock;
- telemetría;
- señales operativas.

### Problemas relacionados

Principalmente:

- P1: riesgo de quiebre;
- P2: desalineación stock-demanda;
- P3: saturación operativa.

Como apoyo:

- P5: degradación de activos.

### Resultados esperados de Lumina

- alertas de caída acelerada de stock;
- detección de incrementos abruptos de demanda;
- identificación de zonas con mayor presión logística;
- señales de congestión;
- detección de comportamiento anómalo;
- actualización de contexto para modelos y recomendaciones.

### Fuera de alcance

La vertical no debe:

- reservar stock;
- bloquear inventario;
- modificar pedidos;
- asignar vehículos;
- despachar;
- procesar pagos.

> **Streaming proporciona conciencia situacional; no reemplaza la operación.**

---

## 6. Vertical 2: Planes Diarios de Inventario

### Objetivo

Responder a la pregunta:

> **¿Qué creemos que va a ocurrir y dónde necesitaremos inventario?**

Esta vertical combina procesamiento batch con modelos predictivos.

### Fuentes principales

- histórico de ventas;
- evolución del inventario;
- estacionalidad;
- tendencias digitales;
- eventos locales;
- ubicación;
- patrones de demanda;
- históricos de campañas.

### Problemas relacionados

Principalmente:

- P1: riesgo de quiebre;
- P2: stock lejos de la demanda.

Como apoyo:

- P3: saturación futura de capacidad.

### Resultados esperados de Lumina

- forecast de demanda;
- riesgo de quiebre por producto y zona;
- necesidad estimada de inventario;
- identificación de déficit o exceso relativo;
- recomendación de reposición o redistribución;
- proyección de presión futura sobre la operación.

### Ejemplo

En lugar de ejecutar:

> “Mover 50 unidades al almacén X”.

Lumina debería producir algo equivalente a:

> “Para la zona X se proyecta una demanda de 120 unidades y existe riesgo de déficit de 35 unidades para el siguiente periodo”.

La decisión y ejecución de reposición o transferencia corresponde a los sistemas operacionales y a la gestión de inventario.

---

## 7. Vertical 3: Informes Operativos

### Objetivo

Responder a la pregunta:

> **¿Cómo está funcionando el negocio y qué tendencias debemos entender?**

Esta vertical se soporta principalmente en procesamiento analítico y OLAP.

### Fuentes principales

- ventas;
- pedidos;
- inventario;
- logística;
- costos;
- clientes;
- tiempos de entrega;
- conversiones;
- ingresos.

### Problemas relacionados

Principalmente:

- P2: stock y demanda geográficamente desalineados;
- P3: capacidad operativa;
- P4: visión fragmentada del cliente.

También permite medir transversalmente los resultados asociados a P1 y P5.

### Resultados esperados de Lumina

- costo promedio por envío;
- tiempo promedio de entrega;
- distancia media entre inventario y cliente;
- porcentaje de pedidos entregados dentro del SLA;
- utilización de capacidad;
- conversión por canal;
- ingresos por región o unidad;
- evolución de quiebres de stock;
- comportamiento de clientes entre canales;
- tendencias históricas y comparaciones.

### Consideración sobre clientes

El problema P4 requiere una identidad confiable de cliente. Por ello, se asume la existencia de una capacidad externa de MDM o resolución de identidad.

Lumina no actúa como MDM. Consume una identidad canónica para realizar análisis de comportamiento transversal.

---

## 8. Vertical 4: Mantenimiento Proactivo

### Objetivo

Responder a la pregunta:

> **¿Qué activo puede empezar a degradarse o fallar antes de que la operación sea afectada?**

Esta vertical utiliza telemetría, históricos y modelos predictivos de mantenimiento.

### Fuentes principales

- cámaras frigoríficas;
- sensores de almacén;
- telemetría de infraestructura;
- históricos de incidentes;
- históricos de mantenimiento;
- patrones normales de funcionamiento.

### Problemas relacionados

Principalmente:

- P5: degradación y falla de activos.

Como apoyo:

- P3: cuando una degradación reduce capacidad operativa.

### Resultados esperados de Lumina

- detección de anomalías;
- identificación de tendencias de degradación;
- riesgo estimado de falla;
- priorización de activos;
- recomendaciones de inspección o mantenimiento;
- alertas tempranas.

### Fuera de alcance

Lumina no:

- controla físicamente equipos;
- reemplaza sistemas SCADA, BMS o IoT;
- genera directamente órdenes de trabajo;
- ejecuta reparaciones.

La ejecución corresponde al sistema de mantenimiento y a los equipos operativos.

---

## 9. Relación entre tipo de procesamiento y pregunta de negocio

| Tipo de procesamiento | Pregunta que responde | Horizonte | Ejemplo en Lumina |
|---|---|---|---|
| **Streaming** | ¿Qué está ocurriendo ahora? | Segundos / minutos | Stock cayendo, demanda creciendo, telemetría anómala |
| **Batch** | ¿Qué información debe procesarse de forma consolidada? | Horas / días | Históricos de ventas, inventario, preparación de datasets |
| **Analítico / OLAP** | ¿Cómo estamos funcionando? | Histórico / consolidado | Costos, tiempos, conversión, utilización |
| **Predictivo** | ¿Qué probablemente ocurrirá? | Futuro | Demanda, quiebre, saturación, falla |
| **Prescriptivo** | ¿Qué convendría hacer? | Futuro / decisión | Reposición, redistribución, priorización, alternativa recomendada |

La capa prescriptiva no se plantea como una quinta vertical, sino como una evolución natural de las capacidades predictivas y analíticas de Lumina.

---

## 10. Modelo conceptual de funcionamiento

```text
PROBLEMAS DE NEGOCIO
P1 · P2 · P3 · P4 · P5
        ↓
FUENTES OPERACIONALES
ventas · pedidos · inventario · logística
clientes · clics · IoT · telemetría
        ↓
CAPACIDADES DE DATOS
Streaming · Batch · Analítica · Predictiva
        ↓
LUMINA RETAIL
integración
+ análisis
+ predicción
+ optimización
        ↓
SALIDAS
alertas
insights
forecast
riesgos
recomendaciones
        ↓
ECOSISTEMA OPERACIONAL
decide y ejecuta
```

---

## 11. Principio de arquitectura

La arquitectura de Lumina debe diseñarse a partir de los problemas de negocio y no a partir de la tecnología.

Por ello, cada componente técnico deberá justificar su existencia mediante una relación explícita con una vertical y un problema.

Ejemplos:

- el procesamiento streaming se justifica cuando existe necesidad de detectar cambios recientes relacionados con P1, P2, P3 o P5;
- el procesamiento batch se justifica para consolidar históricos y alimentar modelos de demanda e inventario;
- la capa analítica se justifica para medir comportamiento, desempeño y evolución de los problemas;
- la capa predictiva se justifica para anticipar quiebres, demanda, saturación y degradación;
- la capa prescriptiva se justifica cuando se requiere recomendar alternativas sin ejecutar directamente la transacción.

---

## 12. Conclusión

Las cuatro verticales de Lumina Retail se mantienen, pero su interpretación se redefine a partir de una frontera clara entre inteligencia y operación.

La arquitectura no busca reemplazar los sistemas de pedidos, inventario, logística, pagos o mantenimiento. Busca integrar los datos generados por estos sistemas para convertirlos en información accionable.

La relación final puede resumirse de la siguiente manera:

> **Streaming permite saber qué está ocurriendo.**  
> **Batch permite consolidar y preparar información.**  
> **Analítica permite entender cómo estamos funcionando.**  
> **Predicción permite anticipar qué ocurrirá.**  
> **Prescripción permite recomendar qué convendría hacer.**

Sobre estas capacidades, Lumina aporta inteligencia al ecosistema de RetailSmart, mientras que los sistemas operacionales conservan la responsabilidad de ejecutar las decisiones y mantener la integridad de las transacciones del negocio.

La arquitectura futura deberá mantener este principio como criterio de diseño:

> **Lumina observa, integra, aprende, predice y recomienda. El ecosistema operacional decide y ejecuta.**
