# Sales Dashboard — Global Superstore (2011–2014)

Análisis de ventas y rentabilidad de una empresa global de suministros de oficina, con limpieza y validación en Python y un dashboard interactivo en Power BI.

**Pregunta de negocio:** ¿dónde se gana y dónde se pierde dinero, y qué papel juegan los descuentos?

🔗 **Dashboard publicado:** [https://app.powerbi.com/links/OaFPP843Ff?ctid=3374819b-3f7f-4153-ab6a-fd4afd7b386c&pbi_source=linkShare]

---

## Dashboard

![Dashboard de ventas](/images/dashboard_pagina1.png)

El dashboard incluye: 7 KPIs, tendencia mensual de ventas y ganancia, ventas y margen por categoría, margen por mercado, rentabilidad por subcategoría y margen por rango de descuento. Dos slicers (Año y Mercado) filtran todos los visuales.

---

## Hallazgos principales

| Hallazgo | Cifra |
|---|---|
| Ventas totales (2011–2014) | **$12,642,905** |
| Ganancia total | **$1,467,457** (margen de **11.6%**) |
| Crecimiento de ventas 2011 → 2014 | de $2.26 M a $4.30 M (**+90%**) |
| Margen sin descuento vs. descuento > 30% | **25.3%** vs. **−51.3%** |
| Transacciones con pérdida | 12,544 de 51,290 (**24.5%**) |
| Subcategoría con pérdida | *Tables*: ventas $757 mil, pérdida −$64 mil |
| Categoría con menor margen | *Furniture*: 6.9% (frente a 14.0% en *Technology* y 13.7% en *Office Supplies*) |
| Mercado con menor margen | *EMEA*: 5.5% |

### Conclusión: descuentos

El margen baja de forma continua al aumentar el descuento, y es negativo a partir de **21%**:

| Descuento | Filas | Ventas | Ganancia | Margen |
|---|---|---|---|---|
| 0% | 29,009 | 6,992,734 | 1,770,695 | 25.32% |
| 1–10% | 4,679 | 1,962,633 | 338,189 | 17.23% |
| 11–20% | 6,274 | 1,757,296 | 173,255 | 9.86% |
| 21–30% | 967 | 382,540 | −21,156 | −5.53% |
| >30% | 10,361 | 1,547,702 | −793,526 | −51.27% |

Las transacciones con más de 30% de descuento son el 20% de las filas y pierden $793 mil. Sin ellas, la ganancia total sería de unos $2.26 millones en lugar de $1.47 millones.

El problema no se concentra en una categoría. Cinco subcategorías de distintas áreas explican más de la mitad de esa pérdida con descuentos mayores a 30%:

- *Tables*: −$120,581
- *Bookcases*: −$89,880
- *Phones*: −$87,191
- *Machines*: −$72,125
- *Chairs*: −$65,478

**Recomendación:** establecer una política general de descuentos con tope alrededor de 20%, en lugar de ajustes por producto.

Otros puntos:
- El crecimiento de ventas no mejoró el margen: se mantuvo entre 11% y 12% cada año.
- El 28% del catálogo (1,066 de 3,788 productos) genera el 80% de las ventas.

---

### Tendencia anual

| Año | Ventas | Ganancia | Margen | Crecimiento de ventas |
|---|---|---|---|---|
| 2011 | 2,259,511 | 248,941 | 11.02% | — |
| 2012 | 2,677,493 | 307,415 | 11.48% | +18.50% |
| 2013 | 3,405,860 | 406,935 | 11.95% | +27.20% |
| 2014 | 4,300,041 | 504,166 | 11.72% | +26.25% |

Las ventas crecieron cada año, pero el margen se mantuvo entre 11% y 12%. El crecimiento vino de vender más, no de vender con mejor margen.

### Concentración de ventas (Pareto)

Solo **1,066 de 3,788 productos (28.1%)** generan el 80% de las ventas. Es una concentración moderada: no justifica enfocar el negocio en unos pocos productos, pero sí indica que el catálogo tiene una cola larga de artículos con poco volumen.

---

## Dataset

**Global Superstore** (Kaggle, versión limpia): 51,290 transacciones, 7 mercados, 147 países.

Decisiones de limpieza:
- Se eliminan `Unnamed: 0` (índice heredado) y `Record_Count` (constante = 1).
- Los clientes se cuentan por `Customer_ID`, porque `Customer_Name` no es único (795 nombres para 4,873 IDs).
- Las fechas se verifican: ningún envío es anterior a su orden.
- Sin nulos ni duplicados.

---

## Estructura del proyecto

```
sales-dashboard/
├── data/
│   ├── raw/                  # Dataset original
│   └── processed/            # Generado por el notebook (no se sube a Git)
├── notebooks/
│   └── 01_eda_global.ipynb   # Limpieza, validación, análisis y exportación
├── dashboards/
│   └── sales_dashboard.pbix  # Dashboard de Power BI
├── docs/
│   └── hallazgos_analisis.txt  # Desarrollo completo de cada hallazgo
├── images/
│   └── dashboard_pagina1.png
├── requirements.txt
└── README.md
```

---

## Proceso y problemas resueltos

El dataset se trabajó en tres etapas: limpieza y validación en Python, análisis con conclusiones, y construcción del dashboard en Power BI. Durante el proceso aparecieron problemas que conviene conocer:

**1. Dataset equivocado al inicio.** El archivo descargado primero era *Sample Superstore* (9,994 filas), no *Global Superstore* (51,290). Se detectó porque el conteo de filas no coincidía con el esperado en el paso de carga, y se reemplazó el archivo antes de continuar.

**2. Fechas interpretadas mal.** En una versión anterior del análisis, un promedio de envío de 107 días delató un formato de fecha invertido (día/mes). La conversión explícita con `format='%d/%m/%Y'` lo corrigió y el promedio quedó en 3.96 días.

**3. Configuración regional en Power BI.** Al importar el CSV, Power BI leyó el punto decimal como separador de miles con la configuración en español. El resultado fue que `Profit` sumaba $4,576 millones en lugar de $1.47 millones. Se corrigió convirtiendo las columnas decimales con *Cambiar tipo → Usando la configuración regional → Inglés (Estados Unidos)*.

**4. Descuentos redondeados a enteros.** Power Query detectó `Discount` como entero a partir de las primeras 1,000 filas, donde casi todos los valores son cero. Los descuentos de 0.20 quedaron en 0 y los de 0.85 en 1. El gráfico de descuentos mostraba solo dos rangos. Se corrigió con la misma conversión a decimal.

**5. Verificación con KPIs de control.** Antes de construir visuales, se calcularon en Python los totales de referencia (ventas, ganancia, margen, órdenes, clientes) y se compararon con cada medida de Power BI. Esto permitió detectar los errores 1 a 4 antes de que llegaran al dashboard.

**Lección principal:** en un dataset de 50 mil filas, una conversión automática basada en una muestra pequeña puede cambiar los resultados. Conviene verificar los totales después de cada transformación.

---

## Cómo reproducirlo

```bash
# 1. Crear el entorno
conda create -n sales python=3.11 -y
conda activate sales
pip install -r requirements.txt

# 2. Ejecutar el notebook (genera data/processed/global_superstore_clean.csv)
cd notebooks
jupyter notebook 01_eda_global.ipynb

# 3. Abrir dashboards/sales_dashboard.pbix en Power BI Desktop
#    y actualizar la fuente si la ruta cambió.
```

### Valores de control

Para comprobar que Power BI lee bien los datos:

| Medida | Valor |
|---|---|
| Ventas Totales | 12,642,905 |
| Ganancia Total | 1,467,457.29 |
| Margen de Ganancia | 11.61% |
| Órdenes Únicas | 25,035 |
| Clientes Únicos | 4,873 |
| Cantidad Vendida | 178,312 |
| Ticket Promedio | 505.01 |

---

## Notas técnicas

- **Formato decimal:** el CSV se exporta con punto decimal y sin índice. Si los totales de Power BI no coinciden, convierte `Sales`, `Profit`, `Shipping_Cost` y `Discount` con *Cambiar tipo → Usando la configuración regional → Número decimal, Inglés (Estados Unidos)*.
- **Medidas principales:** `Ventas Totales`, `Ganancia Total`, `Margen de Ganancia`, `Órdenes Únicas`, `Clientes Únicos`, `Ticket Promedio`, `Cantidad Vendida`.
- **Columna calculada:** `Rango Descuento`, con un orden auxiliar para que los rangos se muestren en su secuencia lógica.

---

## Limitaciones

- Los datos son de 2011 a 2014. Conviene verificar si la tendencia se mantiene con datos más recientes.
- El análisis de descuentos muestra asociación, no causalidad. Los productos con más descuento podrían también ser los de menor costo o menor demanda. Confirmarlo requiere datos de costos.
- Canadá tiene una muestra pequeña (67 mil en ventas), así que su margen de 26.6% no es concluyente.

---

**Autor:** Guillermo Adrianza · Ingeniero en Sistemas
