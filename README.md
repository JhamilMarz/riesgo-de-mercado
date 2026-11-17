# Riesgo de mercado del NASDAQ Composite en recesiones y expansiones de EE. UU. (1990-2026)

Evidencia estadística y evaluación de modelos de Valor en Riesgo (VaR) y Expected Shortfall (ES). Proyecto final del diplomado de estadística (análisis estadístico avanzado), desarrollado con la metodología CRISP-DM en Python.

<!-- Cuando subas el repositorio, agrega aquí el botón de Colab:
[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/<usuario>/<repositorio>/blob/main/analisis.ipynb) -->

## La pregunta

¿Es distinto el riesgo diario del NASDAQ Composite en las recesiones y en las expansiones de EE. UU.? ¿Qué tan grande es la diferencia y qué modelos de VaR y ES la capturan?

## Datos

Tres series públicas de [FRED](https://fred.stlouisfed.org/) (Banco de la Reserva Federal de St. Louis), descargadas el 8 de noviembre de 2026 con `fredapi`, del 2-ene-1990 al 30-oct-2026:

| Serie | Qué es | Frecuencia |
|---|---|---|
| `NASDAQCOM` | Cierre del índice NASDAQ Composite | Diaria |
| `VIXCLS` | Índice de volatilidad VIX (CBOE) | Diaria |
| `USREC` | Indicador de recesiones del NBER (0 = expansión, 1 = recesión) | Mensual |

Resultado: 9 276 retornos logarítmicos diarios, 753 de ellos (8.1 %) en los 36 meses de recesión de cuatro episodios (1990-91, 2001, 2008-09 y 2020). Las series pertenecen a sus fuentes originales (Nasdaq, CBOE y NBER) y se incluyen aquí solo para reproducir el análisis.

## Métodos

- **Auditoría y limpieza** documentadas (faltantes, atípicos, bitácora), con los datos originales intactos.
- **Exploración y bivariado:** descriptivos por estado del ciclo, Spearman, chi-cuadrado y regresión con errores de Newey-West.
- **Comparación entre estados:** t de Welch y Mann-Whitney sobre la log-volatilidad mensual, y razón de volatilidades diarias con intervalo *bootstrap* estratificado, frente a umbrales fijados de antemano.
- **Modelos de riesgo:** VaR y ES histórico, normal y GJR-GARCH(1,1) con errores t (implementado con `numpy` y `scipy`), evaluados fuera de muestra en 8 276 días con las pruebas de Kupiec, Christoffersen y una prueba binomial por estado del ciclo.

## Hallazgos principales

1. **La volatilidad es mayor en recesión:** razón diaria de 1.82 (IC 95 % [1.46, 2.18]); diferencia mensual significativa con Welch y Mann-Whitney (p < 0.001; d de Cohen = 1.27), robusta al recortar la muestra.
2. **El VIX anticipa la magnitud, no la dirección:** rho de Spearman = 0.385 con la magnitud del retorno del día siguiente; sin asociación con que el día sea de baja.
3. **Ningún modelo de VaR supera la prueba de cobertura de Kupiec:** los tres subestiman el riesgo.
4. **Los modelos simples fallan sobre todo en recesión:** al 99 %, el 30.5 % (histórico) y el 23.8 % (normal) de las excepciones ocurren en recesión, que es el 7.1 % de los días; en el GJR-GARCH-t, 8.4 %, y sus excepciones no se agrupan.

Los resultados describen diferencias estadísticas, no prueban causalidad, y se apoyan en solo cuatro recesiones.

## Estructura del repositorio

```
analisis.ipynb      Notebook ejecutable (CRISP-DM: secciones 1 a 5)
datos/              NASDAQCOM.csv, VIXCLS.csv, USREC.csv (columnas fecha, valor)
resultados/         Tablas generadas: diccionario, bitácora, descriptivos, backtest, métricas
figuras/            Gráficos del análisis (PNG)
informe/            Informe final en PDF
requirements.txt    Dependencias
```

## Cómo ejecutarlo

**En Google Colab:** abre `analisis.ipynb` y ejecuta *Entorno de ejecución → Ejecutar todo*. No hay que subir archivos ni crear carpetas. El notebook lee los datos desde los enlaces de la celda de configuración y, si no responden, desde `datos/`.

**En local:**
```bash
pip install -r requirements.txt
jupyter notebook analisis.ipynb
```
Ejecuta todas las celdas de arriba abajo (tarda cerca de un minuto). No se necesita clave de FRED: el notebook no descarga datos de la API.

## Autor

Yamil M. — La Paz, Bolivia. Maestría en Ciencia de Datos, Universidad Católica de Bolivia.
