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
  * **Cálculo:** Filtra horas de inactividad cronológicas. Busca desviaciones estadísticas fuera de la varianza normal que indiquen la gestación de fallos crónicos.
* **Risk Score (Matriz Ponderada):**
  * **Cálculo:** Función interna que combina estadísticamente el ratio de fallos, el tiempo promedio de inactividad y el impacto económico para emitir una calificación integral de riesgo de intervención.
