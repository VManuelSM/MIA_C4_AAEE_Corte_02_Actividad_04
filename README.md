# Actividad 04 · Tres formas de recombinar y tres de mutar en un algoritmo genético para fijar precios y descuentos

| | |
|---|---|
| **Alumno** | Víctor Manuel Santos Martínez — matrícula 253220020 |
| **Materia** | Algoritmos Evolutivos — Maestría en Inteligencia Artificial |
| **Docente** | Dr. Jaime Aguilar Ortiz |
| **Actividad** | Actividad 04 — Comparación de tres formas de realizar dos de las siete etapas de un algoritmo genético |
| **Modalidad** | Individual |
| **Periodo** | Septiembre–diciembre de 2026 · Corte 2 |
| **Institución** | Universidad Politécnica Metropolitana de Hidalgo |

## Descripción de la actividad

> Desarrollar una aplicación de algoritmo genético aplicando y comparando tres formas de realizar por lo menos dos de las 7 etapas de un algoritmo genético y justificar la elección.

El problema es la **fijación de precios de temporada** de una categoría de productos en una cadena de tiendas de conveniencia. Hoy los precios salen de una regla de costo más 45 %. Cada gen es el precio de un producto, un número real, con un piso de margen mínimo y un techo de alza máxima. Hay cuatro reglas comerciales: existencias limitadas, demanda no negativa, una escalera de precios entre marca propia, estándar y premium, y un tope de 3 % al alza del índice de precios de la categoría. La demanda es lineal, con efectos cruzados entre sustitutos.

Se comparan **tres formas de recombinar y tres de mutar** en un diseño factorial completo 3 × 3. Las otras cinco etapas quedan fijas.

| Etapa | Método | Por qué |
|---|---|---|
| 1. Inicialización | **M03** hipercubo latino | Recorre el rango completo de cada precio con N = 100 |
| 2. Evaluación | **M08** prioridad de factibilidad | Todas las formas compiten con la misma vara, sin coeficiente de penalización |
| 3. Selección | **M12** torneo, `k = 2` | Compara claves lexicográficas sin convertirlas en probabilidades |
| 4. Recombinación | **M14** uniforme · **M15** aritmética · **M16** BLX-α | Intercambiar, interpolar o extrapolar: tres mecanismos distintos de combinar dos precios |
| 5. Mutación | **M18** reinicio · **M19** gaussiana · **M20** polinomial | Reiniciar, perturbar y recortar, o perturbar respetando las cotas |
| 6. Reemplazo | **M22** generacional con elitismo, `e = 2` | La mejor solución factible nunca se pierde |
| 7. Paro | **M26** presupuesto de 20 000 evaluaciones | Las nueve configuraciones gastan exactamente lo mismo |

Los parámetros propios de cada operador (`α` de M16, `σ` de M19 y `η` de M20) se calibraron para cada configuración en una instancia que no se usa para evaluar, con una regla fijada antes de ver los resultados.

## Resultados

Cuatro instancias de evaluación (12 y 36 productos, con sustitución débil y fuerte), nueve configuraciones y treinta semillas emparejadas: 1 080 corridas. El óptimo global de cada instancia se conoce y se certifica con las condiciones de Karush-Kuhn-Tucker, porque la utilidad es estrictamente cóncava.

| Instancia | Mejora posible sobre los precios actuales | Brecha del ajuste parejo | Menor rango medio (brecha mediana) | M16+M20 (brecha mediana) | Peor configuración (brecha mediana) |
|---|---:|---:|---|---:|---|
| E12-D | 5.68 % | 3.84 % | M14+M20 (0.032 %) | 0.041 % | M15+M18 (0.911 %) |
| E12-F | 8.67 % | 1.97 % | M16+M19 (0.073 %) | 0.079 % | M15+M18 (0.878 %) |
| E36-D | 5.30 % | 2.64 % | M14+M19 (0.079 %) | 0.098 % | M15+M18 (0.890 %) |
| E36-F | 8.62 % | 1.86 % | M16+M20 (0.331 %) | 0.331 % | M14+M18 (1.408 %) |

- **La mutación pesa más que la recombinación.** M18, que reinicia el precio en todo su intervalo, es la peor mutación en las cuatro instancias. Sus configuraciones capturan entre el 82 % y el 94 % de la mejora posible; las de M19 y M20, entre el 91 % y el 99 %.
- **La mejor recombinación depende del problema.** Con sustitución débil gana o empata M14. En la instancia grande con sustitución fuerte gana sola M16. M15 genera hijos factibles casi siempre, porque el conjunto factible es convexo, pero contrae la población y nunca entra al grupo superior.
- **Elección final.** Cuatro configuraciones no difieren entre sí en el ranking global de 120 bloques (Friedman y prueba posterior de Demšar con Holm). La regla fijada de antemano elige la de menor brecha máxima: **M16+M20 con α = 0.3 y η = 20**, cuya peor corrida queda a 0.575 % del óptimo.
- **Líneas base.** La búsqueda aleatoria con el mismo presupuesto no encuentra ninguna solución factible con 36 productos, y con 12 queda a 7–8 % del óptimo. Subir todos los precios 3 % recupera entre el 28 % y el 77 % de la mejora.

## Contenido del repositorio

| Ruta | Contenido |
|---|---|
| `Actividad_04_Precios_Descuentos.ipynb` | Cuadernillo autocontenido y ejecutado: modelo, óptimo certificado, siete etapas, dieciséis pruebas, calibración, experimento factorial, estadística, figuras y lectura de resultados |
| `resultados/instancias.csv` | Óptimos, líneas base, reglas activas y medidas de dificultad de las cinco instancias |
| `resultados/calibracion.csv`, `calibracion_elegidos.csv` | 350 corridas de calibración y parámetro elegido por configuración |
| `resultados/corridas.csv` | Una fila por corrida de evaluación (1 080) |
| `resultados/historiales.csv` | Trayectoria generación a generación de las 1 080 corridas |
| `resultados/busqueda_aleatoria.csv`, `historial_busqueda_aleatoria.csv` | 120 búsquedas aleatorias con presupuesto idéntico |
| `resultados/resumen.csv`, `pruebas_estadisticas.csv`, `ranking_global.csv` | Agregados por configuración, pruebas de Friedman y Wilcoxon-Holm, ranking global |
| `resultados/mecanismo_medianas.csv` | Dispersión, hijos factibles y genes en cota por generación |
| `resultados/caso_principal_*.csv` | Precios de las doce bebidas de E12-F y alternativas comparadas |
| `resultados/manifiesto.json` | Versiones, configuración, semillas y huellas SHA-256 de cada archivo |
| `figuras/` | Ocho figuras PNG generadas desde los datos de la ejecución |

## Reproducir

Desde esta carpeta, con el entorno conda `science`:

```bash
conda run -n science jupyter nbconvert --to notebook --execute --inplace \
  --ExecutePreprocessor.timeout=1800 Actividad_04_Precios_Descuentos.ipynb
```

El cuadernillo no importa módulos `.py` propios y no descarga nada. La primera celda falla si el kernel no pertenece al entorno `science`. Las dieciséis pruebas se ejecutan antes de cualquier experimento, y el cuadernillo se detiene si una falla. Dos de ellas contrastan la referencia exacta con problemas resueltos a mano.

Entorno comprobado: Python 3.11.16, NumPy 2.4.6, pandas 3.0.6, SciPy 1.17.1 y Matplotlib 3.11.2, en macOS arm64. La ejecución completa tarda unos 35 segundos. Dos ejecuciones dan archivos idénticos salvo en las columnas de tiempo.

## Alcance y límites

- **Los datos son sintéticos.** Precios, costos, elasticidades y existencias son una escala de trabajo, no estimaciones de una empresa real.
- **El algoritmo genético no es la herramienta necesaria para este modelo.** La utilidad es cóncava y las reglas son lineales, así que un solucionador de programación cuadrática encuentra el óptimo en 0.11 s o menos. Esa propiedad es la que permite medir la brecha exacta de cada configuración; el algoritmo genético no la usa. Tendría sentido práctico con una demanda no cóncava, estimada por simulación o con reglas no lineales, y entonces sólo cambiaría la etapa 2.
- **No se generalizan los resultados.** Describen cuatro instancias y una calibración en una quinta. Las hipótesis se escribieron después de un piloto de diseño con parámetros por defecto e instancias preliminares; el experimento formal usa instancias y semillas nuevas.
- **La utilidad es plana cerca del óptimo.** Soluciones casi igual de buenas pueden tener precios bastante distintos; la brecha mide la utilidad, no la cercanía de los precios.

## Procedencia y autoría

Las siete etapas, los treinta métodos y su nomenclatura M01–M30 proceden del material de la materia (Dr. Jaime Aguilar Ortiz). BLX-α es de Eshelman y Schaffer (1993), la mutación polinomial acotada de Deb y Goyal (1996) y Deb y Deb (2014), la prioridad de factibilidad de Deb (2000) y el hipercubo latino de McKay, Beckman y Conover (1979). El modelo de precios, los datos sintéticos, el certificado de optimalidad, el diseño factorial, la calibración y el análisis son decisiones de este desarrollo.

Se empleó asistencia de inteligencia artificial para la programación y la redacción. Las cifras no proceden de esa asistencia: se obtienen al ejecutar el cuadernillo y sus dieciséis pruebas.
