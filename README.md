# IndustrialIQ — Mantenimiento Industrial y Analítica Avanzada

Este dashboard es una herramienta interactiva para el cálculo, visualización y análisis de métricas de confiabilidad industrial. El objetivo es eliminar las "cajas negras" y proporcionar total transparencia matemática sobre cómo se evalúa la salud de los activos.

A continuación, se detalla la lógica de los motores de cálculo integrados en el sistema.

## 1. Indicadores Core (KPIs Básicos)
Estos son los cimientos del análisis, calculados dinámicamente a partir del historial de órdenes de trabajo (OTs).

* **MTBF (Tiempo Medio Entre Fallos):**
  * **Lógica:** Mide la confiabilidad básica; el tiempo que un equipo opera antes de fallar.
  * **Cálculo:** $MTBF=\frac{TiempoOperativoTotal-TiempoDeDowntimeTotal}{NumeroDeFallos}$
  * *Nota de seguridad:* Si no existen fallos correctivos registrados, el sistema asigna un valor por defecto o infinito para evitar divisiones por cero.
* **MTTR (Tiempo Medio de Reparación):**
  * **Lógica:** Mide la mantenibilidad, promediando el tiempo necesario para restituir la función del equipo.
  * **Cálculo:** $MTTR=\frac{\sum{TiempoDeReparacion}}{NumeroDeReparaciones}$

## 2. Modelos de Disponibilidad
El sistema evalúa distintos escenarios de la planta, desde el ideal hasta la realidad logística.

* **Disponibilidad Inherente (Ai):**
  * Asume un escenario donde los recursos de mantenimiento son inmediatos.
  * $Ai=\frac{MTBF}{MTBF+MTTR}$
* **Disponibilidad Operacional (Ao):**
  * Refleja la realidad operativa utilizando el Tiempo Medio de Inactividad (MDT) en lugar del MTTR puro.
  * $Ao=\frac{MTBF}{MTBF+MDT}$
* **Disponibilidad de Alcance (Aa):**
  * Considera el Tiempo Medio Entre Mantenimientos (MTBM), el Tiempo de Mantenimiento Activo (MAMT) y el Tiempo de Retraso Logístico (MLT).
  * $Aa=\frac{MTBM}{MTBM+MAMT+MLT}$

## 3. Ingeniería de Confiabilidad (Weibull y Bañera)
Modelado del ciclo de vida estadístico de los activos.

* **Curva de la Bañera (Bathtub Curve):**
  * El sistema grafica las etapas de vida alterando matemáticamente la tasa de fallos ($\lambda$) según el parámetro de forma ($\beta$):
    * **Mortalidad Infantil:** $\beta<1$ (Tasa decreciente).
    * **Vida Útil:** $\beta\approx1$ (Tasa constante/aleatoria).
    * **Zona de Desgaste:** $\beta>1$ (Tasa creciente por fatiga).
* **Análisis Weibull y RUL:**
  * Utiliza la distribución de Weibull y el parámetro de vida característica ($\eta$) para trazar la curva de supervivencia y estimar el RUL (Vida Útil Restante).

## 4. Herramientas de Diagnóstico Gerencial

* **Diagrama de Pareto (Bad Actors):**
  * **Lógica:** Principio 80/20.
  * **Cálculo:** Agrupa OTs por equipo, suma el costo/horas de parada, ordena de mayor a menor impacto y calcula el porcentaje acumulado para identificar los activos críticos.
* **Diagrama de Jack-Knife:**
  * **Lógica:** Mapa de dispersión bidimensional.
  * **Cálculo:** Promedia el MTBF y MTTR globales. Grafica cada equipo individual cruzando sus métricas contra la media global, dividiendo el parque en 4 cuadrantes de criticidad y acción.

## 5. Módulo de Machine Learning y Analítica Avanzada
Transición del mantenimiento descriptivo al prescriptivo.

* **Modelado Lineal (Regresión para RUL):**
  * Toma variables independientes históricas ($x$) para predecir la variable continua dependiente ($y$), estimando horas antes del próximo fallo.
  * Modelo base: $y=\beta_0+\beta_1x_1+\beta_2x_2+\epsilon$
* **Detección de Anomalías (Anomaly Detection):**
  * **Lógica:** Reconocimiento de patrones en series de tiempo.
 
### Detalle Técnico: Módulo de Machine Learning y Analítica

Este módulo simula el comportamiento de un motor de Machine Learning prescriptivo optimizado para ejecutarse localmente en el navegador, aplicando modelos estadísticos robustos sobre el historial de los activos.

#### 1. Predictor de Fallo por Equipo (Estimación de RUL)
Responde a la pregunta: *"¿Cuántas horas de operación quedan antes del próximo fallo?"*

* **Mecanismo:** Utiliza un modelo de **Regresión Múltiple** determinístico.
* **Matemática:** Aplica una ecuación lineal (y = β0 + β1X1 + β2X2 + ε), asignando "pesos" (β) a variables independientes históricas (X), como horas desde el último mantenimiento o el MTBF histórico.
* **Aplicación:** Proyecta la degradación hacia adelante basándose en la tasa de desgaste específica del equipo evaluado.

#### 2. Detección de Anomalías (Anomaly Detection)
Levanta alertas tempranas cuando el comportamiento de un equipo se desvía de su norma estadística, ideal para Mantenimiento Basado en Condición (CBM).

* **Mecanismo:** Actúa como una carta de control dinámico sobre la serie de tiempo de las paradas o frecuencias de fallo.
* **Matemática:** Utiliza el método de **Z-Score**. Calcula la media (μ) y la desviación estándar (σ) del historial. Si un nuevo evento cae fuera del rango de varianza normal (por ejemplo, μ ± 2σ), el algoritmo lo clasifica como una anomalía.
* **Aplicación:** Identifica picos o patrones ocultos de fallos crónicos que pasarían desapercibidos al observar simples promedios.

#### 3. Ranking de Riesgo por Equipo (Risk Score)
Es el motor prescriptivo que prioriza la toma de decisiones, indicando exactamente qué equipo requiere intervención inmediata.

* **Mecanismo:** Modelo de **Matriz de Ponderación Multicriterio**.
* **Matemática:** Normaliza variables dispares en una escala común (0 a 100) y aplica una ecuación de riesgo: `Riesgo = (w1 × Frecuencia) + (w2 × ImpactoMTTR) + (w3 × GravedadModoFallo)`, donde *w* representa los hiperparámetros que definen el peso de cada criterio.
* **Aplicación:** Convierte métricas complejas en un único "Score" de criticidad. El activo con el Score más cercano a 100 es identificado visualmente como el cuello de botella actual de la confiabilidad de la planta.

#### 4. Curva RUL (Remaining Useful Life)
Representación visual de la degradación del activo, conceptualmente alineada con la clásica **Curva P-F** (Punto Potencial a Punto Funcional de fallo).

* **Mecanismo:** Grafica la trayectoria de la degradación desde el estado de salud actual (100%) hasta la falla funcional (0%), consumiendo el resultado del Predictor de Fallo.
* **Matemática:** Dependiendo del modo de fallo, traza una función de decaimiento (generalmente exponencial) donde la pendiente se vuelve más pronunciada a medida que se acerca al límite predicho, simulando la aceleración progresiva del desgaste.
* **Aplicación:** Permite visualizar gráficamente la "ventana de oportunidad" para realizar el mantenimiento preventivo, justificando con datos en qué punto de la curva se encuentra el equipo hoy. 
  * **Cálculo:** Filtra horas de inactividad cronológicas. Busca desviaciones estadísticas fuera de la varianza normal que indiquen la gestación de fallos crónicos.
* **Risk Score (Matriz Ponderada):**
  * **Cálculo:** Función interna que combina estadísticamente el ratio de fallos, el tiempo promedio de inactividad y el impacto económico para emitir una calificación integral de riesgo de intervención.

## 6. ALERTAS ACTIVAS
### Sistema de Alertas Activas (Rule-Based System)

La sección de **Alertas Activas** no es un cálculo aislado, sino un sistema de filtrado basado en reglas lógicas. Funciona alimentándose directamente de los resultados arrojados por los motores de analítica.

Básicamente, el algoritmo itera sobre el listado de equipos actualizados y los hace pasar por un "colador" de condiciones. Si un equipo cumple con los criterios de peligro, se inyecta en la lista de Alertas Activas.

A continuación se detalla cómo el sistema determina qué mostrar:

#### 1. Cruce del Umbral de Riesgo (Risk Score)
El principal disparador es el motor del **Risk Score**, basado en límites (thresholds) de tolerancia programados.
* El algoritmo verifica el Score (de 0 a 100) de cada equipo.
* Si el Score supera un valor crítico (por ejemplo, `RiskScore > 80`), el sistema levanta automáticamente una bandera roja, asumiendo que la combinación de alta frecuencia de fallos y alto impacto económico requiere atención inmediata.

##### 1.1.-Motor de Decisión: Cálculo del Risk Score (0 - 100)

El cálculo del Risk Score es **100% automático**. Como usuario, no es necesario definir, calcular ni estimar este parámetro a mano. La única entrada requerida es el historial de Órdenes de Trabajo (OTs), ya sea mediante registro manual o importando un archivo Excel.

A nivel de código, el motor responsable es la función `calcRiskScore(stats)`, que se ejecuta en segundo plano al actualizar los datos. Funciona bajo el principio de una matriz de ponderación multicriterio (*weighted combination of failure rate, avg downtime, cost*).

Así es como el algoritmo construye el indicador final, asegurando total transparencia:

* **1. Frecuencia de Fallos Relativa (Failure Rate):** El sistema no evalúa los fallos de forma aislada. Primero, filtra las OTs para procesar únicamente las de tipo "Correctivo". Luego, identifica el equipo con la mayor cantidad de fallos históricos globales (`maxFallos`). Finalmente, compara la cantidad de fallos del equipo analizado contra ese "peor escenario" para obtener un índice de criticidad normalizado.
* **2. Impacto Operativo (Avg Downtime):** Aunque un equipo falle con poca frecuencia, el algoritmo penaliza la gravedad de sus paradas. Calcula cuántas horas en promedio pasa inactivo el activo por cada evento. Si un fallo menor genera un tiempo de inactividad prolongado, el Score aumenta drásticamente.
* **3. Impacto Económico (Cost):** Incorpora la variable financiera al modelo, evaluando el impacto monetario total asociado al historial de reparaciones y tiempos de parada del activo.

#### La Ponderación Final
El algoritmo toma estos tres factores normalizados y aplica una **fórmula de suma ponderada** (multiplicando cada factor por un peso específico de importancia). El resultado se escala matemáticamente para entregar un valor estandarizado entre **0 y 100**.

> **Aplicación Práctica:** El sistema lee este Score resultante para alimentar el panel de **Alertas Activas**. Si el número cruza los umbrales de tolerancia, el equipo se marca visualmente en Rojo (Crítico) o Amarillo (Advertencia), convirtiendo cientos de registros de Excel en una lista de prioridades de acción inmediata para el planificador.
#### 2. Límite de Vida Útil Restante (RUL Crítico)
El segundo disparador proviene del modelo de **Regresión Lineal**.
* El sistema lee la cantidad de horas predichas que le quedan al equipo antes de fallar.
* Si el valor cae por debajo de un margen de seguridad preestablecido (por ejemplo, `RUL < 48 horas`, o menos del 10% de su MTBF histórico), se genera una alerta. Funciona como la luz de reserva de combustible en un tablero.

#### 3. Detección de Anomalías Recientes
El tercer disparador está conectado al motor estadístico de **Z-Score**.
* Evalúa la última orden de trabajo o parada del equipo. Si detecta que su duración o comportamiento cae fuera de la varianza normal (desviaciones estándar $\mu \pm 2\sigma$), lo marca inmediatamente.
* **Significado:** *"No sabemos si el equipo fallará mañana, pero su última parada de mantenimiento fue estadísticamente anormal y debe ser investigada"*.

#### Representación Visual
Cuando el motor detecta que un equipo cumple una o varias condiciones, crea un objeto de alerta asignándole:
* **Nivel de severidad:** Si cumple múltiples condiciones (ej. RUL bajo + Anomalía), se clasifica como **Crítico** (Rojo). Si solo se acerca al umbral, se clasifica como **Advertencia** (Naranja/Amarillo).
* **Mensaje descriptivo:** Extrae el dato clave (ej. *"Motor Principal - RUL crítico: 24h"*).

> **En resumen:** Las Alertas Activas son la consecuencia visual de los motores matemáticos en segundo plano. Es la forma en que el dashboard procesa datos complejos para entregarle al planificador una lista de tareas clara, priorizada e inmediata.
