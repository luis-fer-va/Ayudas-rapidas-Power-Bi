# 📊 Análisis de Pareto en Power BI

## ✅ ¿Qué es y para qué sirve?

El Análisis de Pareto es una técnica que nos ayuda a identificar cuáles productos, categorías o clientes generan la mayor parte de los ingresos o beneficios de una compañía. Basado en el principio 80/20, permite **enfocar los esfuerzos en ese pequeño porcentaje que produce la mayor parte de los resultados**.

Esta herramienta es clave para tomar decisiones estratégicas sobre productos, clientes o líneas de negocio.

---

## ❓ ¿Qué podemos responder con este análisis?

- ¿Cuántos productos o clientes generan el 80% de los ingresos?
- ¿Qué productos o categorías **no** hacen parte de ese 80%?
- ¿Cuántos elementos representan el 20% restante (baja participación)?
- ¿Cómo se distribuyen las ventas dentro de una categoría, cliente o zona específica?

Este tipo de preguntas ayuda a tomar decisiones más informadas y estratégicas.

---

## 🎯 ¿Qué decisiones se pueden tomar a partir del Análisis de Pareto?

- **Sobre clientes con baja participación (el 20% restante):**
  - Implementar campañas o promociones específicas para incentivarlos.
  - Revisar patrones de consumo y analizar si es posible aumentar el ticket promedio.

- **Sobre clientes de alto valor (dentro del 80%):**
  - Ofrecer beneficios exclusivos, programas de fidelización o ventas cruzadas.
  - Diseñar estrategias comerciales basadas en sus preferencias.

- **Sobre gestión de inventario y compras:**
  - Garantizar stock constante para los productos que conforman el 80%.
  - Evaluar si es necesario mantener productos de baja rotación o reemplazarlos por nuevas opciones.

---

## ⚙️ Metodología utilizada

Para realizar este análisis utilizamos **Power BI**, lo que nos permite que los resultados se actualicen dinámicamente al aplicar filtros por producto, categoría, cliente o cualquier otra variable.

### 📝 Pasos generales del análisis:

1. **Ordenar los productos/clientes por ventas descendentes.**
2. **Calcular el acumulado de ingresos** a medida que se recorren los productos desde el que más vende al que menos.
3. **Obtener el porcentaje acumulado sobre el total**, para visualizar cómo se concentra el ingreso.
4. **Resaltar visualmente** el 80%, el siguiente 10% y el 10% restante con diferentes colores para facilitar la interpretación.
5. **Contar cuántos elementos conforman ese 80%** y analizar cuántos productos/clientes tienen una baja participación.

1️⃣ Crear un ranking de productos (o categorías/clientes)
Primero, necesitamos ordenar los elementos (productos, clientes, etc.) de mayor a menor según la métrica que vamos a analizar (por ejemplo, ventas).

Para eso, creamos una medida que genera un ranking descendente, tomando en cuenta el contexto de filtros aplicado:

```DAX
Rank_Productos =
IF (
    [Selected metric] = BLANK (),
    BLANK (),
    RANK (
        DENSE,
        ALLSELECTED ( Dim_Producto[Nombre] ),
        ORDERBY ( [Selected metric], DESC, Dim_Producto[Nombre] )
    )
)
```
✅ ¿Qué logramos con esto?
Tener una lista ordenada para recorrer los elementos del más vendido al menos vendido, que es la base del Pareto.
![image](https://github.com/user-attachments/assets/e8de37b3-8cef-46b5-805e-425ad570c4a3)


2️⃣ Calcular el ingreso acumulado según el ranking
Luego, obtenemos el ingreso acumulado a medida que se avanza por el ranking, utilizando la función TOPN para ir sumando los productos en orden.

2. Obtener el ingreso acumulado  con ayuda del ranking que creamos antes
3. 
```
Pareto_producto $ =
CALCULATE (
    [Venta], 
    TOPN ( [Rank1], ALLSELECTED ( Dim_Producto[Product] ), [Venta], DESC )
)
```

✅ ¿Para qué sirve?
Nos permite ver cuánto ingreso se ha acumulado hasta cada punto del ranking.

![image](https://github.com/user-attachments/assets/848617f9-a1bb-4eb9-83ea-a1a4a1759e10)

3️⃣ Expresar el acumulado como porcentaje del total
Ahora transformamos ese ingreso acumulado en un porcentaje del total, lo que nos permite identificar cuándo alcanzamos el famoso 80%.

```
Pareto_producto % =
DIVIDE ( [Pareto_producto $], CALCULATE ( [Venta], ALLSELECTED () ) )
```

✅ ¿Por qué es importante?
Porque con el porcentaje acumulado podemos saber en qué punto se alcanza el 80% del ingreso total.

![image](https://github.com/user-attachments/assets/fdf0eba1-38d9-41df-8d55-cb136450719a)

4️⃣ Visualización con colores para una lectura rápida
Para facilitar el análisis visual, creamos una medida que asigna un color según el rango acumulado:

```
Color pareto =
IF (
    [Pareto %] <= 0.8, 
    "DarkSeaGreen",  -- pinta en verde el rango del pareto del 1% hasta el 80%
    IF ( [Pareto producto%] <= 0.90, "#FFE485",-- Pinta en amarillo el rango del pareto del 81% al 90%
    "lIGHTSALMON" )   -- Pinta el resto de valores en Salmon
)

```

✅ ¿Qué conseguimos?
Identificamos rápidamente qué productos están dentro del Pareto (verde), los que están en el rango medio (amarillo), y los que aportan poco (salmón).

📌 Dato importante:
El cálculo se actualiza dinámicamente si filtramos por una categoría, cliente o cualquier otra dimensión.
Ejemplo aplicado con Categoría = Fruver 👇

![image](https://github.com/user-attachments/assets/10b34aef-dbb0-4d76-9321-02adf0b676f2)

5️⃣ ¿Cuántos productos conforman ese 80%?
Para complementar el análisis, creamos una medida que cuenta cuántos productos forman parte del 80% acumulado:

``` Pdtos_Pareto =
VAR _tabla_pareto =
    FILTER (
        ADDCOLUMNS (
            SELECTCOLUMNS ( VALUES ( Dim_Producto ), Dim_Producto[Nombre] ),
            "@pareto", [Pareto_producto %]
        ),
        [Venta] <> BLANK ()
            && [Pareto_producto %] <= 0.8
    )
RETURN
    COUNTROWS ( _tabla_pareto )

```

✅ ¿Por qué es útil?
Porque no solo sabemos cuánto representan en ingresos, sino también cuántos productos específicos lo están generando.

6️⃣ Ejemplo aplicado: ¿Qué descubrimos?
Total de productos vendidos: 82

Productos que generan el 80% de los ingresos: 15

➡️ Resultado: 67 productos (82%) apenas aportan el 20% restante.
Esto confirma el principio de Pareto y resalta la necesidad de enfocar esfuerzos en los productos clave.

![image](https://github.com/user-attachments/assets/8ec392a8-91c7-4f66-b402-05e4caaf8b9f)


## 📌 Conclusiones del análisis

- El Análisis de Pareto **permite tomar decisiones informadas sobre dónde enfocar recursos, tiempo y estrategias**.
- La visualización con colores facilita que cualquier persona —incluso fuera del área de análisis— comprenda rápidamente la información.
- **El enfoque dinámico en Power BI** hace que sea adaptable para diferentes productos, categorías o períodos de tiempo.
- Es una herramienta esencial para:
  - **Optimizar campañas de ventas.**
  - **Gestionar inventarios.**
  - **Priorizar clientes o zonas geográficas.**
  - **Guiar a la alta dirección en la toma de decisiones.**

---

> **Documento elaborado por: Luis Vallejo  
> **Versión:** 1.0  
> **Fecha:** Junio 2025
