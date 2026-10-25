# RetailSmart / Lumina Retail  
## Revisión consolidada de problemática, supuestos y fronteras de responsabilidad

## 1. Propósito

Antes de definir la arquitectura de datos, los mecanismos de procesamiento, o los componentes tecnológicos de Lumina Retail, es necesario delimitar con precisión qué problemas corresponden realmente a una plataforma de datos y cuáles deben ser resueltos por los sistemas operacionales del ecosistema.

El objetivo es evitar que Lumina se convierta en una solución compensatoria para deficiencias transaccionales o de integración que deberían resolverse en los sistemas responsables de pedidos, inventario, clientes, despacho, pagos o logística.

La premisa central que guía este proyecto es:

> **Lumina observa, integra, analiza, anticipa y recomienda. El ecosistema operacional ejecuta.**

Por tanto, Lumina no debe convertirse en el sistema que procesa una compra, reserva inventario, ejecuta un despacho, confirma un pago o mantiene la identidad maestra del cliente.

---

# 2. Supuestos y fronteras del escenario

El caso de RetailSmart describe una problemática empresarial amplia, pero no detalla todos los procesos y sistemas operacionales existentes. Para poder diseñar una solución coherente, se adoptan los siguientes supuestos.

## 2.1 Sistemas operacionales existentes

Se asume que RetailSmart dispone, o debería disponer, de sistemas operacionales responsables de:

- pedidos;
- inventario;
- reservas de stock;
- preparación de pedidos;
- despacho;
- entregas;
- pagos;
- gestión logística;
- clientes.

Estos sistemas continúan siendo los responsables de ejecutar las transacciones.

---

## 2.2 Autoridad sobre inventario y pedidos

El sistema operacional de inventario debe ser la fuente de verdad de disponibilidad y reservas.

Por ejemplo, ante la pregunta:

> ¿Existen actualmente tres televisores disponibles en una tienda o almacén?

la respuesta debe provenir del sistema operacional de inventario, no del Lakehouse.

De igual forma, la reserva definitiva de una unidad debe ejecutarse contra el sistema transaccional correspondiente.

Lumina puede analizar la evolución del stock, detectar tendencias y anticipar quiebres, pero **no debe sustituir la responsabilidad operacional de inventario**.

---

## 2.3 Lumina no es parte obligatoria de la transacción

Una venta debería poder completarse incluso si Lumina estuviera temporalmente indisponible.

La relación esperada es:

```text
Sistemas operacionales
        ↓
Ventas / inventario / pedidos / logística / clientes / mantenimiento
        ↓
Datos y eventos
        ↓
      Lumina
        ↓
Integración + análisis + predicción + recomendaciones
```

Y no:

```text
Venta
  ↓
Lumina
  ↓
Decisión obligatoria
  ↓
Continuación de la transacción
```

---

## 2.4 Identidad maestra de cliente

Se recomienda que RetailSmart cuente con una capacidad de **Master Data Management — MDM** para mantener una identidad de cliente unificada y confiable a nivel organizacional.

El MDM debería resolver funciones como:

- identificación de registros duplicados;
- definición de un identificador canónico de cliente;
- consolidación de datos maestros;
- resolución de inconsistencias entre fuentes;
- mantenimiento del registro maestro.

Lumina consumiría esa identidad consolidada para realizar análisis de comportamiento.

> **MDM unifica la identidad del cliente. Lumina unifica la visión analítica de su comportamiento.**

Lumina no reemplaza al MDM.

---

## 2.5 Supuestos explícitos frente a vacíos del caso

Ante la falta de claridad del caso, hemos decidido establecer supuestos razonables para completar el diseño. Estos supuestos forman parte de la solución propuesta y se documentan explícitamente como decisiones de diseño, no como hechos proporcionados por el caso.

---

# 3. Frontera funcional de Lumina

## 3.1 Alcance de Lumina

Puede:

- integrar información de múltiples fuentes;
- conservar histórico;
- cruzar señales provenientes de distintos dominios;
- identificar patrones;
- detectar tendencias;
- proyectar demanda;
- anticipar riesgos;
- generar alertas;
- producir recomendaciones;
- generar información analítica;
- apoyar decisiones de negocio y operación.

## 3.2 Ecosistema operacional

Los sistemas operacionales deben:

- registrar transacciones de venta;
- reservar inventario;
- ejecutar movimientos de stock;
- procesar pagos;
- asignar definitivamente recursos operacionales;
- ejecutar despachos;
- confirmar entregas;
- mantener la identidad maestra del cliente;
- controlar físicamente activos o equipos;
- sustituir sistemas transaccionales, MDM o sistemas de mantenimiento.

> **Lumina ayuda a decidir mejor qué hacer; los sistemas operacionales realizan la acción.**

---

# 4. Problemas de negocio consolidados

## Problemática

RetailSmart opera a través de tiendas físicas y canales de e-commerce, por lo que sus clientes pueden comprar desde distintos puntos de contacto. El principal problema aparece después de la compra: la empresa presenta dificultades para coordinar de manera eficiente el envío de los productos hasta el cliente.

Una de las causas es que el inventario puede encontrarse lejos del comprador. Esto obliga a realizar entregas desde ubicaciones poco convenientes, incrementando el costo de envío y haciendo menos eficiente la última milla. El problema se vuelve más crítico en las compras online, donde la disponibilidad y ubicación del stock condicionan directamente la posibilidad de atender una entrega de manera rápida y eficiente.

Además, durante fechas de alta demanda, como campañas comerciales o eventos especiales, el procesamiento de pedidos puede congestionarse y generar retrasos. En estos escenarios, una mayor cantidad de pedidos debe competir por la misma capacidad de inventario, preparación y distribución, afectando los tiempos de atención y entrega.

A esta situación se suma que conocer el stock disponible en un momento determinado no permite necesariamente anticipar dónde y cuándo podría agotarse, ni identificar con anticipación en qué zonas se concentrará la demanda. Esto dificulta posicionar adecuadamente el inventario y preparar la capacidad logística antes de que aparezcan los picos de consumo.

En síntesis, el problema no está únicamente en vender por diferentes canales, sino en lograr que el pedido, el inventario disponible y la capacidad logística trabajen de manera coordinada para realizar la entrega de forma eficiente. Desde la perspectiva de Lumina, el foco no será ejecutar directamente estas operaciones, sino integrar información, identificar patrones y anticipar demanda, riesgo de quiebre, desalineación geográfica del inventario y posibles situaciones de saturación, proporcionando información que permita al ecosistema operacional tomar mejores decisiones.

La problemática del caso contiene varios síntomas y consecuencias que se solapan entre sí. Para evitar duplicidad, se consolidan en cinco problemas principales.

---

# Problema 1 — Riesgo de quiebre de inventario

## Frase resumen

> **Sé cuánto stock tengo, pero no dónde ni cuándo se va a agotar.**

## Situación

Un sistema operacional puede conocer correctamente el stock disponible en un momento determinado.

Sin embargo, conocer la fotografía actual no necesariamente permite responder:

- ¿qué productos se están agotando más rápido de lo esperado?;
- ¿qué tiendas o almacenes presentan mayor riesgo de quiebre?;
- ¿cuándo podría agotarse determinado producto?;
- ¿qué combinación de demanda y consumo puede llevar a una ruptura de stock?;
- ¿en qué ubicaciones conviene anticipar reposición?

El problema no es necesariamente desconocer cuánto stock existe actualmente.

El problema es **no anticipar adecuadamente su evolución**.

## Ejemplo funcional

```text
Tienda A
08:00  Stock = 30
10:00  Stock = 22
12:00  Stock = 14
14:00  Stock = 7
16:00  Stock = 2
```

El sistema operacional responde:

> “Actualmente tengo 2 unidades.”

Lumina debería ayudar a responder:

> “Si esta velocidad de consumo continúa, esta ubicación tiene alta probabilidad de quedarse sin stock en las próximas horas.”

## Impacto de negocio

- quiebres de stock;
- ventas perdidas;
- reposiciones tardías;
- presión logística;
- incumplimiento de demanda;
- deterioro de experiencia del cliente.

## Responsabilidad de Lumina

Lumina puede integrar:

```text
Histórico de ventas
+
evolución del inventario
+
estacionalidad
+
señales de demanda
+
comportamiento digital
        ↓
Predicción de consumo
        ↓
Riesgo de quiebre
```

Su responsabilidad sería anticipar:

- qué producto;
- en qué ubicación;
- en qué horizonte temporal;

presenta mayor riesgo de agotamiento.

## Responsabilidad del ecosistema operacional

Los sistemas operacionales deben:

- mantener el stock transaccional;
- realizar reservas;
- ejecutar movimientos;
- confirmar disponibilidad real;
- ejecutar reposiciones o transferencias aprobadas.

## Fuera del alcance de Lumina

- ser la fuente informativa del stock;
- reservar productos;
- resolver concurrencia transaccional;
- bloquear unidades;
- garantizar consistencia de una venta.

## Resultado funcional esperado

Una visión predictiva del riesgo de quiebre que permita a la operación actuar antes de que se produzca la ruptura de inventario.

---

# Problema 2 — Desalineación geográfica entre inventario y demanda

## Frase resumen

> **Sé dónde está mi stock, pero no si está cerca de donde se concentrará la demanda.**

## Situación

RetailSmart puede disponer de inventario suficiente a nivel global, pero ese inventario puede encontrarse físicamente lejos de los clientes que probablemente lo demandarán.

Por ejemplo:

```text
Zona con demanda alta
        ↓
Stock cercano: bajo o inexistente
        ↓
Stock disponible: almacén distante
        ↓
Más kilómetros de entrega
        ↓
Mayor costo y mayor tiempo
```

El problema no es que no exista stock.

El problema es que **el stock puede estar mal posicionado respecto a la demanda esperada**.

## Impacto de negocio

- mayor costo de última milla;
- recorridos innecesariamente largos;
- menor capacidad de entrega rápida;
- presión sobre la red logística;
- mayor costo por pedido;
- menor competitividad frente a actores con stock más cercano al comprador.

## Responsabilidad de Lumina

Lumina puede combinar:

```text
Histórico de ventas
+
ubicación de inventario
+
zonas de demanda
+
estacionalidad
+
canales de compra
+
señales digitales
+
eventos locales
        ↓
Predicción geográfica de demanda
        ↓
Recomendación de posicionamiento de inventario
```

Lumina no necesita saber qué cliente exacto comprará mañana.

Debe estimar:

- qué productos;
- en qué zonas;
- con qué intensidad;

probablemente serán demandados.

## Responsabilidad del ecosistema operacional

La operación debe decidir y ejecutar:

- transferencias;
- reposiciones;
- movimientos entre tiendas y almacenes;
- estrategias de abastecimiento;
- asignación logística;
- política final de despacho.

## Fuera del alcance de Lumina

- ejecutar automáticamente movimientos físicos;
- sustituir la gestión de inventario;
- elegir de forma transaccional el almacén definitivo de cada pedido;
- despachar productos.

## Resultado funcional esperado

Planes o recomendaciones periódicas que permitan ubicar inventario más cerca de las zonas donde se espera la demanda y reducir costo y distancia de entrega.

---

# Problema 3 — Saturación de capacidad durante picos de demanda

## Frase resumen

> **Sé que la demanda está creciendo, pero no cuándo va a superar mi capacidad operativa.**

## Situación

Durante temporadas de alta demanda —por ejemplo Black Friday, Cyber Monday o campañas equivalentes— el volumen de pedidos puede aumentar rápidamente y generar congestión.

El retraso puede aparecer en diferentes etapas:

- preparación;
- picking;
- packing;
- despacho;
- capacidad de almacén;
- capacidad logística;
- transporte;
- entrega.

Existe una distinción importante:

### Si el sistema transaccional no soporta carga

Problemas como:

- base de datos saturada;
- API que no escala;
- bloqueos;
- caída del sistema de pedidos;
- falta de capacidad computacional transaccional;

corresponden al ecosistema operacional y deben resolverse mediante arquitectura y escalabilidad operacional.

### Si el problema es no anticipar la saturación

Ahí sí existe una oportunidad para Lumina.

## Ejemplo funcional

```text
18:00  operación normal
18:10  crecimiento fuerte de interés
18:15  pedidos +40 %
18:20  aumenta cola de preparación
18:25  capacidad logística cerca del límite
18:30  empiezan retrasos
```

Lumina debería intentar advertir:

> “Si esta tendencia continúa, la capacidad de preparación o despacho será insuficiente dentro de un horizonte determinado.”

## Impacto de negocio

- retrasos;
- incumplimiento de promesas de entrega;
- acumulación de pedidos;
- mayores costos;
- saturación de almacenes;
- menor satisfacción del cliente.

## Responsabilidad de Lumina

Lumina puede combinar:

```text
Histórico de campañas
+
pedidos recientes
+
señales de interés
+
stock
+
capacidad de preparación
+
capacidad logística
+
tiempos históricos
        ↓
Análisis de tendencia
        ↓
Riesgo de saturación
```

Puede generar:

- alertas;
- predicciones;
- recomendaciones;
- indicadores de presión operacional;
- identificación de posibles cuellos de botella.

## Responsabilidad del ecosistema operacional

La operación debe ejecutar las acciones, por ejemplo:

- aumentar capacidad;
- reasignar personal;
- priorizar pedidos;
- ampliar ventanas;
- redistribuir carga;
- ajustar recursos;
- cambiar reglas de despacho.

## Fuera del alcance de Lumina

- escalar automáticamente el sistema transaccional;
- procesar pedidos;
- reservar stock;
- asignar recursos operacionales de forma definitiva;
- ejecutar despachos.

## Resultado funcional esperado

Detección anticipada de situaciones en las que la demanda probablemente superará la capacidad disponible.

---

# Problema 4 — Visión fragmentada del comportamiento del cliente

## Frase resumen

> **Sé quién es el cliente, pero no entiendo su comportamiento completo entre unidades y canales.**

## Situación

RetailSmart opera con múltiples unidades de negocio independientes —por ejemplo Moda, Hogar, Electrónica y otras— que no comparten adecuadamente información de cliente.

Esto puede provocar que cada unidad vea solo una fracción de la relación:

```text
Cliente
 ├── compra electrónica
 ├── compra hogar
 ├── navega móvil
 ├── compra en tienda física
 └── responde a campañas diferentes
```

Si cada unidad analiza únicamente su propio fragmento, la organización pierde una visión transversal del comportamiento.

## Distinción frente a MDM

Aquí existe una frontera crítica.

### MDM responde:

> ¿Quién es realmente este cliente?

Debe resolver identidad, duplicados y registro maestro.

### Lumina responde:

> Dado que sé que estas interacciones pertenecen al mismo cliente, ¿qué puedo aprender de su comportamiento?

Por tanto:

> **MDM consolida identidad. Lumina consolida análisis de comportamiento.**

## Impacto de negocio

- visión parcial del cliente;
- segmentaciones incompletas;
- campañas menos relevantes;
- dificultad para entender preferencias omnicanal;
- menor capacidad de identificar patrones de compra;
- decisiones aisladas entre unidades de negocio.

## Responsabilidad de Lumina

Lumina puede integrar analíticamente:

```text
Identidad canónica proveniente de MDM
+
compras
+
canales
+
navegación
+
categorías
+
frecuencia
+
preferencias
+
historial
        ↓
Vista analítica transversal
        ↓
Patrones / segmentos / tendencias
```

Puede ayudar a analizar:

- frecuencia de compra;
- categorías preferidas;
- afinidad entre unidades;
- canales preferidos;
- comportamiento físico vs. digital;
- patrones de recurrencia;
- segmentos;
- tendencias.

## Responsabilidad del ecosistema operacional / MDM

Debe existir una capacidad empresarial que gestione:

- identidad maestra;
- duplicados;
- customer ID canónico;
- atributos maestros;
- calidad del dato maestro;
- reglas de golden record.

## Fuera del alcance de Lumina

- convertirse en MDM;
- resolver de manera operacional la identidad;
- ser el repositorio maestro de cliente;
- sustituir CRM o sistemas transaccionales.

## Resultado funcional esperado

Una visión analítica unificada del comportamiento del cliente a través de múltiples unidades y canales, basada en una identidad confiable.

---

# Problema 5 — Riesgo de fallas en activos e infraestructura

## Frase resumen

> **Sé que el equipo está funcionando ahora, pero no cuándo empezará a degradarse o fallar.**

## Situación

El caso define una necesidad de mantenimiento proactivo soportada por:

- telemetría de cámaras frigoríficas;
- sensores de almacén;
- señales de infraestructura.

No se afirma que exista actualmente una determinada tasa de fallas; por tanto, este problema se formula como una **necesidad derivada de la capacidad requerida por el caso**.

Un sistema operacional puede conocer el estado actual:

```text
Equipo: operativo
Temperatura: normal
Sensor: activo
```

Pero esto no necesariamente responde:

- ¿la temperatura se está desviando progresivamente?;
- ¿la vibración está aumentando?;
- ¿hay un patrón parecido al observado antes de una falla?;
- ¿qué activo presenta mayor riesgo?;
- ¿conviene intervenir antes de que falle?

## Ejemplo funcional

```text
08:00  4.5 °C
10:00  4.7 °C
12:00  5.4 °C
14:00  6.6 °C
16:00  8.1 °C
```

La operación puede decir:

> “El equipo continúa funcionando.”

Lumina debería ayudar a identificar:

> “La señal se está alejando progresivamente del comportamiento esperado y existe riesgo de degradación.”

## Impacto de negocio

- fallas inesperadas;
- interrupciones operativas;
- pérdida de productos sensibles;
- tiempos muertos;
- mantenimiento reactivo;
- mayores costos de reparación;
- reducción de capacidad disponible.

## Responsabilidad de Lumina

Lumina puede integrar:

```text
Telemetría actual
+
histórico del activo
+
histórico de mantenimiento
+
histórico de fallas
+
patrones de comportamiento
        ↓
Análisis predictivo
        ↓
Riesgo de degradación o falla
```

Puede generar:

- alertas;
- score de riesgo;
- detección de anomalías;
- recomendaciones de revisión;
- priorización de activos.

## Responsabilidad del ecosistema operacional

La operación debe:

- inspeccionar el activo;
- generar o gestionar órdenes de trabajo;
- programar mantenimiento;
- reparar;
- sustituir componentes;
- controlar físicamente los equipos.

## Fuera del alcance de Lumina

- encender o apagar equipos;
- controlar cámaras frigoríficas;
- ejecutar mantenimiento;
- sustituir sistemas de mantenimiento;
- reemplazar sistemas IoT, SCADA o equivalentes.

## Resultado funcional esperado

Detección anticipada de degradación y priorización del mantenimiento antes de que una falla afecte la operación.

---

# 5. Elementos del caso que no se consideran problemas independientes

## 5.1 Problemas en compras online por disponibilidad de stock

Se consideran una manifestación de los problemas 1 y 2:

- riesgo de quiebre;
- inventario mal posicionado respecto de la demanda.

No se crea un problema adicional.

---

## 5.2 Dispersión de canales y fragmentación de inventario

La fragmentación de inventario está cubierta principalmente por:

- Problema 1;
- Problema 2.

La dispersión de canales se considera una dimensión transversal de la demanda:

```text
Tienda física
+
e-commerce
+
móvil
+
otros canales
        ↓
Demanda omnicanal
```

Lumina debe analizarla de manera integrada, pero no requiere un problema adicional.

---

## 5.3 Desajuste demográfico y digital

El caso indica diferencias entre:

- clientes que prefieren atención física;
- clientes que prefieren compra móvil y entrega inmediata.

Esta situación se incorpora principalmente en el Problema 4 como parte del análisis de preferencias de cliente y canal.

La parte relacionada con disponibilidad limitada de stock urbano se incorpora en el Problema 2.

---

## 5.4 Capacidad logística subutilizada

No se considera un problema independiente.

Se interpreta como una consecuencia combinada de:

- Problema 2: inventario mal posicionado;
- Problema 3: incapacidad para anticipar dónde y cuándo se requiere capacidad.

La organización puede disponer de una gran red logística, pero utilizarla de manera ineficiente si no anticipa correctamente demanda, ubicación y presión operacional.

---

## 5.5 Cuellos de botella logísticos

Se consideran principalmente una extensión del Problema 3.

Lumina puede identificar tendencias y anticipar congestión, pero la ejecución de cambios operacionales corresponde al ecosistema logístico.

---

# 6. Matriz consolidada de problemas y responsabilidades

| # | Problema | Frase resumen | Qué aporta Lumina | Qué ejecuta el ecosistema operacional |
|---|---|---|---|---|
| **1** | Riesgo de quiebre de inventario | **Sé cuánto stock tengo, pero no dónde ni cuándo se va a agotar.** | Predicción de consumo, riesgo de quiebre, alertas y recomendaciones | Stock transaccional, reservas, movimientos y reposición |
| **2** | Desalineación geográfica inventario-demanda | **Sé dónde está mi stock, pero no si está cerca de donde se concentrará la demanda.** | Predicción geográfica y recomendaciones de posicionamiento | Transferencias, abastecimiento, asignación logística y despacho |
| **3** | Saturación de capacidad en picos de demanda | **Sé que la demanda está creciendo, pero no cuándo va a superar mi capacidad operativa.** | Predicción de saturación y detección de cuellos de botella | Escalamiento, priorización, asignación de recursos y operación |
| **4** | Visión fragmentada del comportamiento del cliente | **Sé quién es el cliente, pero no entiendo su comportamiento completo entre unidades y canales.** | Visión analítica transversal, patrones, segmentos y tendencias | MDM/CRM/sistemas fuente mantienen identidad y operación |
| **5** | Riesgo de falla de activos | **Sé que el equipo está funcionando ahora, pero no cuándo empezará a degradarse o fallar.** | Anomalías, riesgo de falla, predicción y recomendación de mantenimiento | Inspección, mantenimiento, reparación y control físico |

---

# 7. Dependencias externas de Lumina

Lumina depende de que el ecosistema mantenga ciertas capacidades básicas correctamente resueltas.

| Dependencia | Responsabilidad |
|---|---|
| Sistema de pedidos | Registrar y administrar pedidos |
| Sistema de inventario | Mantener stock real y reservas |
| Sistema de despacho/logística | Ejecutar asignaciones y entregas |
| MDM | Mantener identidad maestra de cliente |
| CRM / canales | Gestionar interacción operacional con cliente |
| IoT / sistemas de sensores | Capturar telemetría confiable |
| Gestión de mantenimiento | Ejecutar intervenciones sobre activos |
| Plataformas operacionales | Proveer eventos y datos confiables |

Estas dependencias no deben interpretarse como componentes que Lumina deba sustituir.

---

# 8. Criterio para decidir si una necesidad pertenece a Lumina

Para cada nueva necesidad se aplicará la siguiente pregunta:

> **¿Estamos intentando ejecutar una transacción o generar inteligencia sobre la operación?**

Si la respuesta es:

### “Ejecutar una transacción”

Probablemente corresponde al ecosistema operacional.

Ejemplos:

- reservar;
- cobrar;
- despachar;
- modificar stock;
- crear un pedido;
- mover físicamente inventario;
- controlar un equipo.

### “Generar inteligencia”

Probablemente puede corresponder a Lumina.

Ejemplos:

- anticipar;
- predecir;
- detectar;
- comparar;
- segmentar;
- recomendar;
- identificar tendencias;
- medir;
- optimizar mediante análisis.

---

# 9. Lectura integrada de la problemática

Los cinco problemas forman una cadena coherente:

```text
¿Dónde y cuándo se agotará el stock?
             ↓
¿Está el inventario cerca de donde aparecerá la demanda?
             ↓
¿Podrá la operación absorber esa demanda?
             ↓
¿Entendemos suficientemente al cliente que genera esa demanda?
             ↓
¿La infraestructura física estará disponible para soportar la operación?
```

Esto permite posicionar a Lumina no como un reemplazo de los sistemas operacionales, sino como una **plataforma de inteligencia transversal** que combina histórico, señales recientes y múltiples fuentes para anticipar decisiones.

---

# 10. Conclusión de alcance

La propuesta de Lumina debe evitar convertirse en:

- sistema transaccional;
- sistema de inventario;
- OMS;
- MDM;
- CRM;
- sistema de mantenimiento;
- controlador IoT.

