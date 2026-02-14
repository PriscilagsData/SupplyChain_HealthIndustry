# 🌍 Análisis de Supply Chain – Envíos internacionales de medicamentos
Health Industry

## 🎯 Objetivo del proyecto
Análisis de la cadena de suministros de medicación para malaria y HIV a nivel mundial.

El objetivo es realizar un proceso de ETL y modelado de los datos asegurando su calidad y estructura para una óptima extracción de insights y KPIs de valor para el análisis.

---

## 🌍 Contexto de negocio
El acceso a medicamentos esenciales a nivel mundial depende de procesos complejos de Supply Chain envolviendo a múltiples skateholders, regiones y procesos logísticos. 

El entendimiento y preparación de estos datos es un paso crítico para identificar ineficiencias, mejorar la distribución y tomar mejores decisiones en la salud pública.

---

## 📊 Descripción de Dataset
Base de datos pública del SCMS **(Supply Chain Management System)** sobre envíos internacionales de medicamentos de *Malaria y HIV* entre **2006 y 2015** en formato .csv, incluyendo:
- presupuestos y órdenes,
- países de origen y destino,
- fechas y métodos de envío,
- características y categorización de productos sanitarios,
- cantidades y costos,
- múltiples campos logísticos y administrativos.
(33 *columnas o atributos* y 10.324 *filas o registros*)

[*Fuente web*]( https://catalog.data.gov/dataset/supply-chain-shipment-pricing-data-07d29](https://www.kaggle.com/datasets/princehobby/supply-chain-shipment-dataset?select=Suppy_Chain_Shipment_Data.csv )

*Medicamentos:
ARV: antirretrovirales (tratamiento HIV),
ACT: combinación basada en artemisinina (tratamiento malaria),
ANTM: antimalarias;*

*Tests para diagnóstico:
HRDT: tests HIV,
MRDT: tests malaria.*

📁 El [dataset original Supply Chain Shipment csv](https://github.com/PriscilagsData/SupplyChain_HealthIndustry/blob/main/Supply_Chain_Shipment_csv.csv) se encuentra cargado en el repositorio.

---

## 🧹 Proceso de Data Cleaning (Power Query / M)
La transformación y limpieza de datos se realizaron en Power Query de Excel.

Key steps included:

- Removal of irrelevant or redundant columns  
- Standardization of country names and categorical fields  
- Data type corrections (dates, numeric fields)  
- Handling of missing and null values  
- Creation of derived columns for improved analysis  

📄 El código de Power Query (M) está incluido en este repositorio para transparencia y reproductibilidad.

---

## 🛠️ Herramientas Utilizadas
- Microsoft Excel  
- Power Query (M language)
- SQL (modeling)
- Microsoft Power BI (M, DAX, dashboard)

---

## 📁 Estructura del Repositorio
