Formulario de Probabilidad y Estadística para Python
Este repositorio contiene un resumen de funciones y fórmulas clave en Python (scipy.stats) para resolver problemas de probabilidad y estadística. Es un recurso rápido para estudiantes y profesionales que trabajan con análisis de datos y modelado estadístico. 📊

Tabla de Contenidos
Distribuciones de Probabilidad

Distribución Binomial

Distribución de Poisson

Distribución Normal

Intervalos de Confianza

Para la Media (μ) - Varianza Poblacional (σ²) Conocida

Para la Media (μ) - Varianza Poblacional (σ²) Desconocida

Para una Proporción (p) - Muestra Grande

Para la Varianza (σ²) - Distribución Chi-cuadrado

Lógica de Decisión para Intervalos de Confianza de la Media

1. Distribuciones de Probabilidad
a) Distribución Binomial
Propósito: Modela el número de éxitos k en n ensayos independientes, con una probabilidad de éxito p en cada ensayo.

Función de Probabilidad (PMF):

Python (scipy.stats):

Python

from scipy.stats import binom

# Probabilidad de k éxitos en n ensayos
binom.pmf(k, n, p)
Parámetros:

k: Número de éxitos.

n: Número de ensayos.

p: Probabilidad de éxito.

b) Distribución de Poisson
Propósito: Modela el número de eventos k que ocurren en un intervalo fijo de tiempo o espacio, dado un promedio de ocurrencia μ (lambda).

Función de Probabilidad (PMF):

Python (scipy.stats):

Python

from scipy.stats import poisson

# P(X = k): Probabilidad de exactamente k eventos
poisson.pmf(k, mu)

# P(X <= k): Probabilidad acumulada (a lo sumo k eventos)
poisson.cdf(k, mu)

# P(X > k): Probabilidad de supervivencia (más de k eventos)
poisson.sf(k, mu)
Parámetros:

k: Número de eventos.

mu: Tasa promedio de ocurrencia (μ o λ).

c) Distribución Normal
Propósito: Describe datos continuos que se agrupan simétricamente alrededor de una media.

Python (scipy.stats):

Python

from scipy.stats import norm

# P(X <= x): Probabilidad acumulada hasta el valor x
norm.cdf(x, loc, scale)

# Valor x que corresponde a una probabilidad acumulada p
norm.ppf(p, loc, scale)

# Altura de la curva de densidad en el punto x (raramente usado para probabilidad)
norm.pdf(x, loc, scale)
Parámetros:

x: Valor de la variable.

p: Probabilidad acumulada.

loc: Media (μ) de la distribución.

scale: Desviación estándar (σ) de la distribución.

2. Intervalos de Confianza
a) Para la Media (μ) - Varianza Poblacional (σ²) Conocida (Distribución Z)
Condiciones: La población es normal o la muestra es grande (n > 30). Se conoce σ.

Fórmula:

Función Python:

Python

from scipy.stats import norm

def intervaloParaMedia_z(x_media, std, alfa, n):
    error_estandar = std / (n**(1/2))
    z = norm.ppf(1 - alfa/2)
    lim_inf = x_media - z * error_estandar
    lim_sup = x_media + z * error_estandar
    return lim_inf, lim_sup
b) Para la Media (μ) - Varianza Poblacional (σ²) Desconocida (Distribución t)
Condiciones: La población es aproximadamente normal y/o la muestra es pequeña (n ≤ 30). Se usa la desviación estándar muestral s.

Fórmula:

Función Python:

Python

from scipy.stats import t

def intervaloParaMedia_t(x_media, std_muestra, alfa, n):
    error_estandar = std_muestra / (n**(1/2))
    t_student = t.ppf(1 - alfa/2, n - 1) # n-1 son los grados de libertad
    lim_inf = x_media - t_student * error_estandar
    lim_sup = x_media + t_student * error_estandar
    return lim_inf, lim_sup
c) Para una Proporción (p) - Muestra Grande
Condiciones: La muestra es grande (np ≥ 5 y n(1-p) ≥ 5).

Fórmula:

Función Python:

Python

from scipy.stats import norm

def intervaloParaProporcion_z(p_gorro, alfa, n):
    error_estandar = (p_gorro * (1 - p_gorro) / n)**(1/2)
    z = norm.ppf(1 - alfa/2)
    lim_inf = p_gorro - z * error_estandar
    lim_sup = p_gorro + z * error_estandar
    return lim_inf, lim_sup
d) Para la Varianza (σ²) - Distribución Chi-cuadrado
Condiciones: La población debe ser normal.

Fórmula:

Función Python:

Python

from scipy.stats import chi2

def intervaloParaVarianza_chi2(varianza_muestra, alfa, n):
    gl = n - 1 # Grados de libertad
    chi_val1 = chi2.ppf(1 - alfa/2, gl)
    chi_val2 = chi2.ppf(alfa/2, gl)
    lim_inf = (gl * varianza_muestra) / chi_val1
    lim_sup = (gl * varianza_muestra) / chi_val2
    return lim_inf, lim_sup
Nota: Para el intervalo de confianza de la desviación estándar (σ), simplemente se calcula la raíz cuadrada de los límites del intervalo de la varianza.

3. Lógica de Decisión para Intervalos de Confianza de la Media
El siguiente diagrama ayuda a decidir qué estadístico usar (Z o t) al estimar la media poblacional.

Resumen de la Lógica
¿La población es normal?

Sí:

¿Conoces la varianza de la población (σ²)?

Sí: Usa Z.

No: ¿La muestra es grande (n > 30)?

Sí: Usa Z (Teorema del Límite Central).

No: Usa t.

No:

¿La muestra es grande (n > 30)?

Sí: Usa Z (Teorema del Límite Central).

No: No se pueden usar Z ni t directamente (se requieren métodos no paramétricos).
