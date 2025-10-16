# 📝 Librería de Funciones Estadísticas en Python

Este repositorio contiene una colección de funciones y explicaciones fundamentales de estadística implementadas en Python, utilizando principalmente la librería `scipy.stats`. El objetivo es proporcionar una guía clara y concisa sobre distribuciones de probabilidad e intervalos de confianza.

## 📊 Contenido

- [Distribuciones de Probabilidad](#distribuciones-de-probabilidad)
- [Intervalos de Confianza](#intervalos-de-confianza)
- [Lógica de Decisión para Intervalos de Confianza](#lógica-de-decisión-para-intervalos-de-confianza)

---

## 1. Distribuciones de Probabilidad

### a) Distribución Binomial

**Propósito:** Modela el número de éxitos k en n ensayos independientes, con una probabilidad de éxito p en cada ensayo.

**Función de Probabilidad (PMF):**

**Uso en Python (scipy.stats):**

```python
from scipy.stats import binom
# Probabilidad de k éxitos en n ensayos
probabilidad = binom.pmf(k, n, p)
```

**Parámetros:**
- `k`: Número de éxitos que se desea observar.
- `n`: Número total de ensayos independientes.
- `p`: Probabilidad de éxito en un solo ensayo.

---

### b) Distribución de Poisson

**Propósito:** Modela el número de eventos k que ocurren en un intervalo fijo de tiempo o espacio, dado un promedio de ocurrencia μ (lambda).

**Función de Probabilidad (PMF):**

**Uso en Python (scipy.stats):**

```python
from scipy.stats import poisson
# P(X = k): Probabilidad de exactamente k eventos
prob_exacta = poisson.pmf(k=k, mu=mu)
# P(X <= k): Probabilidad acumulada (a lo sumo k eventos)
prob_acumulada = poisson.cdf(k=k, mu=mu)
# P(X > k): Probabilidad de supervivencia (más de k eventos)
prob_supervivencia = poisson.sf(k=k, mu=mu)
```

**Parámetros:**
- `k`: Número de eventos cuya probabilidad se quiere calcular.
- `mu`: Tasa promedio de ocurrencia ($\mu$ o $\lambda$) de los eventos en el intervalo.

---

### c) Distribución Normal

**Propósito:** Describe datos continuos que se agrupan simétricamente alrededor de una media.

**Uso en Python (scipy.stats):**

```python
from scipy.stats import norm
# P(X <= x): Probabilidad acumulada hasta el valor x
prob_acumulada = norm.cdf(x, loc=media, scale=desviacion)
# Valor x que corresponde a una probabilidad acumulada p
valor_x = norm.ppf(p, loc=media, scale=desviacion)
```

**Parámetros:**
- `x`: Valor de la variable continua.
- `p`: Probabilidad acumulada (un valor entre 0 y 1).
- `loc`: La media ($\mu$) de la distribución.
- `scale`: La desviación estándar ($\sigma$) de la distribución.

---

## 2. Intervalos de Confianza

### a) Para la Media (μ) - Varianza Poblacional (σ²) Conocida (Distribución Z)

**Condiciones:** La población es normal o la muestra es grande (n > 30), y se conoce la desviación estándar poblacional σ.

**Fórmula:**

**Función en Python:**

```python
from scipy.stats import norm

def intervaloParaMedia_z(x, std, alfa, n):
    error_estandar = std / (n**(1/2))
    z = norm.ppf(1 - alfa/2, loc=0, scale=1)
    linferior = x - z * error_estandar
    lsuperior = x + z * error_estandar
    return linferior, lsuperior
```

**Parámetros:**
- `x`: La media muestral ($\bar{x}$).
- `std`: La desviación estándar poblacional ($\sigma$).
- `alfa`: El nivel de significancia ($\alpha$).
- `n`: El tamaño de la muestra.

**Ejemplo de Uso:**

```python
li, ls = intervaloParaMedia_z(x, std, alfa, n)
print(f"El intervalo de confianza es [{li}, {ls}]")
```

---

### b) Para la Media (μ) - Varianza Poblacional (σ²) Desconocida (Distribución t)

**Condiciones:** La población es aproximadamente normal. Se usa la desviación estándar muestral s, típicamente con muestras pequeñas (n ≤ 30).

**Fórmula:**

**Función en Python:**

```python
from scipy.stats import t

def intervaloParaMedia_t(x, std, alfa, n):
    error_estandar = std / (n**(1/2))
    t_student = t.ppf(1 - alfa/2, n - 1)
    linferior = x - t_student * error_estandar
    lsuperior = x + t_student * error_estandar
    return linferior, lsuperior
```

**Parámetros:**
- `x`: La media muestral ($\bar{x}$).
- `std`: La desviación estándar muestral ($s$).
- `alfa`: El nivel de significancia ($\alpha$).
- `n`: El tamaño de la muestra.

**Ejemplo de Uso:**

```python
li, ls = intervaloParaMedia_t(x, std, alfa, n)
print(f"El intervalo de confianza para la media poblacional es [{li}, {ls}]")
```

---

### c) Para una Proporción (p) - Muestra Grande

**Condiciones:** La muestra es suficientemente grande (np ≥ 5 y n(1-p) ≥ 5).

**Fórmula:**

**Función en Python:**

```python
from scipy.stats import norm

def intervaloParaProporcion_z(p, alfa, n):
    error_estandar = (p * (1 - p) / n)**(1/2)
    z = norm.ppf(1 - alfa/2, loc=0, scale=1)
    linferior = p - z * error_estandar
    lsuperior = p + z * error_estandar
    return linferior, lsuperior
```

**Parámetros:**
- `p`: La proporción muestral ($\hat{p}$).
- `alfa`: El nivel de significancia ($\alpha$).
- `n`: El tamaño de la muestra.

**Ejemplo de Uso:**

```python
li, ls = intervaloParaProporcion_z(p, alfa, n)
print(f"El intervalo de confianza para la proporción poblacional está entre [{li}, {ls}]")
```

---

### d) Para la Varianza (σ²) - (Distribución Chi-cuadrado)

**Condiciones:** La población de origen debe ser normal.

**Fórmula:**

**Función en Python:**

```python
from scipy.stats import chi2

def intervaloParaVarianza_chi2(varianza, alfa, n):
    gl = n - 1 # Grados de libertad
    chi_variable1 = chi2.ppf(1 - alfa/2, gl)
    chi_variable2 = chi2.ppf(alfa/2, gl)
    linferior = (gl * varianza) / chi_variable1
    lsuperior = (gl * varianza) / chi_variable2
    return linferior, lsuperior
```

**Parámetros:**
- `varianza`: La varianza muestral ($s^2$).
- `alfa`: El nivel de significancia ($\alpha$).
- `n`: El tamaño de la muestra.

**Ejemplo de Uso:**

```python
li, ls = intervaloParaVarianza_chi2(varianza, alfa, n)
print(f"El intervalo de confianza para la varianza poblacional puede estar entre [{li}, {ls}]")
```

**Nota:** Para obtener el intervalo de confianza de la desviación estándar (σ), simplemente calcula la raíz cuadrada de los límites del intervalo de la varianza.

---

## 3. Lógica de Decisión para Intervalos de Confianza de la Media

Para decidir si usar la distribución Z o t para el intervalo de confianza de la media, sigue este flujo:

### ¿La población es normal?

#### **SÍ**
- **¿Conoces la varianza poblacional (σ²)?**
  - **SÍ** → Usa **Z**.
  - **NO**
    - **¿La muestra es grande (n > 30)?**
      - **SÍ** → Usa **Z** (por el Teorema del Límite Central).
      - **NO** → Usa **t**.

#### **NO**
- **¿La muestra es grande (n > 30)?**
  - **SÍ** → Usa **Z** (por el Teorema del Límite Central).
  - **NO** → No se pueden usar Z ni t directamente. Considera métodos no paramétricos.
