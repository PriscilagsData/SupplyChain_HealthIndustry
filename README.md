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



📄 Código de Power Query (M) 

```m
-- let
    Origen = Csv.Document(File.Contents("C:\Users\prigu\OneDrive\Documentos\Data Analyst - Coder\Data Analytics\Entregas\Entrega Proyecto final\Supply Chain Shipment csv.csv"),[Delimiter=",", Columns=33, Encoding=65001, QuoteStyle=QuoteStyle.None]),
    #"Encabezados promovidos" = Table.PromoteHeaders(Origen, [PromoteAllScalars=true]),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Encabezados promovidos",{{"id", Int64.Type}, {"project code", type text}, {"pq #", type text}, {"po / so #", type text}, {"asn/dn #", type text}, {"country", type text}, {"managed by", type text}, {"fulfill via", type text}, {"vendor inco term", type text}, {"shipment mode", type text}, {"pq first sent to client date", type text}, {"po sent to vendor date", type text}, {"scheduled delivery date", type text}, {"delivered to client date", type text}, {"delivery recorded date", type text}, {"product group", type text}, {"sub classification", type text}, {"vendor", type text}, {"item description", type text}, {"molecule/test type", type text}, {"brand", type text}, {"dosage", type text}, {"dosage form", type text}, {"unit of measure (per pack)", Int64.Type}, {"line item quantity", Int64.Type}, {"line item value", type text}, {"pack price", type text}, {"unit price", type text}, {"manufacturing site", type text}, {"first line designation", type logical}, {"weight (kilograms)", type text}, {"freight cost (usd)", type text}, {"line item insurance (usd)", type text}}),
    #"Valor reemplazado8" = Table.ReplaceValue(#"Tipo cambiado",".",",",Replacer.ReplaceText,{"line item value", "pack price", "unit price","freight cost (usd)","line item insurance (usd)"}),
    #"Tipo cambiado2" = Table.TransformColumnTypes(#"Valor reemplazado8",{{"line item value", type number}, {"pack price", type number}, {"unit price", type number}}),
    #"Duplicados quitados" = Table.Distinct(#"Tipo cambiado2"),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Duplicados quitados",{{"id", "ID"}, {"project code", "CodProyecto"}, {"pq #", "Num Cotización"}, {"po / so #", "Num OP/PE"}, {"asn/dn #", "Num Envío"}, {"country", "País Destino"}, {"managed by", "Administrado por"}, {"fulfill via", "Método de envío"}, {"vendor inco term", "INCOterm"}, {"shipment mode", "Modo de envío"}, {"pq first sent to client date", "Fecha primer presupuesto"}, {"po sent to vendor date", "Fecha envío OP a proveedor"}, {"delivered to client date", "Fecha entega"}, {"scheduled delivery date", "Fecha entrega programada"}, {"delivery recorded date", "Fecha entrega registrada"}, {"product group", "Grupo producto"}, {"sub classification", "Subclasificación"}, {"vendor", "Proveedor"}, {"item description", "Descripción producto"}, {"molecule/test type", "Droga activa o Tipo de test"}, {"brand", "Marca"}, {"dosage", "Dosis"}, {"dosage form", "Forma dosificación"}, {"unit of measure (per pack)", "Unidades por pack"}, {"line item quantity", "Cant de packs por línea producto"}, {"line item value", "Valor línea producto"}, {"pack price", "Valor del pack"}, {"unit price", "Precio unitario"}, {"manufacturing site", "Lugar de fabricación"}, {"first line designation", "Costo flete y peso detallado"}, {"weight (kilograms)", "Peso (kg)"}, {"freight cost (usd)", "Costo flete por envío (USD)"}, {"line item insurance (usd)", "Tasa anual aseguramiento línea producto (USD)"}}),
    #"Columnas quitadas" = Table.RemoveColumns(#"Columnas con nombre cambiado",{"Método de envío","Costo flete y peso detallado", "Peso (kg)"}),
    #"Valores reemplazados" = Table.TransformColumns(#"Columnas quitadas", {
    {"País Destino", each Text.Replace(_, "Côte d'Ivoire", "Ivory Coast"), type text},
    {"Fecha envío OP a proveedor", each Text.Trim(Text.Replace(Text.Replace(Text.Replace(_, "Date Not Captured", ""), "N/A - From RDC", ""), "/", "-")), type text},
    {"Fecha primer presupuesto", each Text.Replace(Text.Replace(_, "Pre-PQ Process", ""), "Date Not Captured", ""), type text},
    {"Costo flete por envío (USD)", each Text.Replace(Text.Replace(_, "Freight Included in Commodity Cost", ""), "Invoiced Separately", ""), type text}}),
    #"Fecha corregida" = Table.AddColumn(#"Valores reemplazados", "Fecha envío OP a proveedor1", each 
    // 1. CORRECCIÓN FECHA ENVÍO OP A PROVEEDOR
    let
    // 1. Verifico si la celda es nula o está vacía
    SourceValue = [#"Fecha envío OP a proveedor"],
    Resultado = if SourceValue = null or SourceValue = "" then null 
    else 
        let
            // 2. Si tiene datos divido por guion y extraigo Mes, Día y Año
            Partes = Text.Split(SourceValue, "-"),
            Mes = Partes{0},
            Dia = Partes{1},
            AnioTexto = Partes{2},
            // 3. Corrijo el año (2 o 4 dígitos)
            AnioLargo = if Text.Length(AnioTexto) = 2 then "20" & AnioTexto else AnioTexto,
            // 4. Nuevo texto en formato dd-MM-yyyy
            TextoFormateado = Dia & "-" & Mes & "-" & AnioLargo
        in
            TextoFormateado
in
    Resultado),
    #"Columnas reordenadas" = Table.ReorderColumns(#"Fecha corregida",{"ID", "CodProyecto", "Num Cotización", "Num OP/PE", "Num Envío", "País Destino", "Administrado por", "INCOterm", "Modo de envío", "Fecha primer presupuesto", "Fecha envío OP a proveedor", "Fecha envío OP a proveedor1", "Fecha entrega programada", "Fecha entega", "Fecha entrega registrada", "Grupo producto", "Subclasificación", "Proveedor", "Descripción producto", "Droga activa o Tipo de test", "Marca", "Dosis", "Forma dosificación", "Unidades por pack", "Cant de packs por línea producto", "Valor línea producto", "Valor del pack", "Precio unitario", "Lugar de fabricación", "Costo flete por envío (USD)", "Tasa anual aseguramiento línea producto (USD)"}),
    #"Columnas quitadas1" = Table.RemoveColumns(#"Columnas reordenadas",{"Fecha envío OP a proveedor"}),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Columnas quitadas1",{{"Fecha envío OP a proveedor1", "Fecha envío OP a proveedor"}}),
    #"Fecha corregida1" = Table.AddColumn(#"Columnas con nombre cambiado1", "Fecha primer presupuesto1", each 
    // 1. CORRECCIÓN PRIMER FECHA PRESUPUESTO
let
    FechaOriginal = [Fecha primer presupuesto],
    // 1. Dejo nulos
    Resultado = if FechaOriginal = null or FechaOriginal = "" then null 
    else 
        let
            // 2. CON SLASH (/): Formato MM/dd/yyyy -> dd-MM-yyyy
            Transformacion = if Text.Contains(FechaOriginal, "/") then 
                let
                    Partes = Text.Split(FechaOriginal, "/"),
                    Mes = Text.PadStart(Partes{0}, 2, "0"),
                    Dia = Text.PadStart(Partes{1}, 2, "0"),
                    Anio = Partes{2}
                in
                    Dia & "-" & Mes & "-" & Anio
            // 3. CON GUION (-): Formato dd-MM-yy -> dd-MM-yyyy
            else if Text.Contains(FechaOriginal, "-") then
                let
                    Partes = Text.Split(FechaOriginal, "-"),
                    Dia = Text.PadStart(Partes{0}, 2, "0"),
                    Mes = Text.PadStart(Partes{1}, 2, "0"),
                    AnioCorto = Partes{2},
                    // Si el año tiene 2 dígitos, le ponemos "20", si no, lo dejamos igual
                    AnioLargo = if Text.Length(AnioCorto) = 2 then "20" & AnioCorto else AnioCorto
                in
                    Dia & "-" & Mes & "-" & AnioLargo
            else FechaOriginal // Por si hay algún dato que no cumple ninguna (ej. ya corregido)
        in
            Transformacion
in
    Resultado),
    #"Columnas reordenadas1" = Table.ReorderColumns(#"Fecha corregida1",{"ID", "CodProyecto", "Num Cotización", "Num OP/PE", "Num Envío", "País Destino", "Administrado por", "INCOterm", "Modo de envío", "Fecha primer presupuesto", "Fecha primer presupuesto1", "Fecha envío OP a proveedor", "Fecha entrega programada", "Fecha entega", "Fecha entrega registrada", "Grupo producto", "Subclasificación", "Proveedor", "Descripción producto", "Droga activa o Tipo de test", "Marca", "Dosis", "Forma dosificación", "Unidades por pack", "Cant de packs por línea producto", "Valor línea producto", "Valor del pack", "Precio unitario", "Lugar de fabricación", "Costo flete por envío (USD)", "Tasa anual aseguramiento línea producto (USD)"}),
    #"Columnas quitadas2" = Table.RemoveColumns(#"Columnas reordenadas1",{"Fecha primer presupuesto"}),
    #"Columnas con nombre cambiado2" = Table.RenameColumns(#"Columnas quitadas2",{{"Fecha primer presupuesto1", "Fecha primer presupuesto"}}),
    #"Tipo cambiado con configuración regional" = Table.TransformColumnTypes(#"Columnas con nombre cambiado2", {{"Fecha entrega programada", type date}, {"Fecha entega", type date}, {"Fecha entrega registrada", type date}}, "en-US"),
    #"Tipo cambiado1" = Table.TransformColumnTypes(#"Tipo cambiado con configuración regional",{{"Fecha primer presupuesto", type date}, {"Fecha envío OP a proveedor", type date}, {"Tasa anual aseguramiento línea producto (USD)", type number}}),
    // Transformar -> Recortar a columnas de texto
    #"Texto recortado1" = Table.TransformColumns(#"Tipo cambiado1",{{"CodProyecto", Text.Trim, type text}, {"Num Cotización", Text.Trim, type text}, {"Num OP/PE", Text.Trim, type text}, {"Num Envío", Text.Trim, type text}, {"País Destino", Text.Trim, type text}, {"Administrado por", Text.Trim, type text}, {"INCOterm", Text.Trim, type text}, {"Modo de envío", Text.Trim, type text}, {"Grupo producto", Text.Trim, type text}, {"Subclasificación", Text.Trim, type text}, {"Proveedor", Text.Trim, type text}, {"Descripción producto", Text.Trim, type text}, {"Droga activa o Tipo de test", Text.Trim, type text}, {"Marca", Text.Trim, type text}, {"Dosis", Text.Trim, type text}, {"Forma dosificación", Text.Trim, type text}, {"Lugar de fabricación", Text.Trim, type text}}),
    //Reemplazo las referencias "See ..." de la columna Costo flete por envío (USD) por sus valores:
    // PASO PREVIO TÉCNICO: Cargo en memoria la columna de costos para que la búsqueda sea rápida
    CostoBuffer = List.Buffer(Origen[#"Costo flete por envío (USD)"]),
    IDBuffer = List.Buffer(#"Texto recortado1"[ID]),
    // PASO 1: Extaigo ID
    #"Paso1_ExtraerID" = Table.AddColumn(#"Texto recortado1", "ID_Referencia", each 
        if Text.Contains(Text.From([#"Costo flete por envío (USD)"]), "ID#:") then 
            Number.From(Text.BetweenDelimiters(Text.From([#"Costo flete por envío (USD)"]), "ID#:", ")")) 
        else null, Int64.Type),
    #"Consultas combinadas" = Table.NestedJoin(Paso1_ExtraerID, {"ID_Referencia"}, Paso1_ExtraerID, {"ID"}, "Paso1_ExtraerID", JoinKind.LeftOuter),
    #"Se expandió Paso1_ExtraerID" = Table.ExpandTableColumn(#"Consultas combinadas", "Paso1_ExtraerID", {"Costo flete por envío (USD)"}, {"Paso1_ExtraerID.Costo flete por envío (USD)"}),
    #"Columna condicional agregada" = Table.AddColumn(#"Se expandió Paso1_ExtraerID", "Costo Final", each if [ID_Referencia] = null then [#"Costo flete por envío (USD)"] else [#"Paso1_ExtraerID.Costo flete por envío (USD)"]),
    #"Tipo cambiado3" = Table.TransformColumnTypes(#"Columna condicional agregada",{{"Costo Final", type number}}),
    #"Columnas quitadas3" = Table.RemoveColumns(#"Tipo cambiado3",{"ID_Referencia", "Paso1_ExtraerID.Costo flete por envío (USD)", "Costo flete por envío (USD)"}),
    #"Columnas reordenadas2" = Table.ReorderColumns(#"Columnas quitadas3",{"ID", "CodProyecto", "Num Cotización", "Num OP/PE", "Num Envío", "País Destino", "Administrado por", "INCOterm", "Modo de envío", "Fecha primer presupuesto", "Fecha envío OP a proveedor", "Fecha entrega programada", "Fecha entega", "Fecha entrega registrada", "Grupo producto", "Subclasificación", "Proveedor", "Descripción producto", "Droga activa o Tipo de test", "Marca", "Dosis", "Forma dosificación", "Unidades por pack", "Cant de packs por línea producto", "Valor línea producto", "Valor del pack", "Precio unitario", "Lugar de fabricación", "Costo Final", "Tasa anual aseguramiento línea producto (USD)"}),
    #"Columnas con nombre cambiado3" = Table.RenameColumns(#"Columnas reordenadas2",{{"Costo Final", "Costo flete por envío (USD)"}}),
    #"Filas ordenadas" = Table.Sort(#"Columnas con nombre cambiado3",{{"ID", Order.Ascending}}),
    #"Tipo cambiado4" = Table.TransformColumnTypes(#"Filas ordenadas",{{"Valor línea producto", Currency.Type}, {"Valor del pack", Currency.Type}, {"Precio unitario", Currency.Type}, {"Costo flete por envío (USD)", Currency.Type}, {"Tasa anual aseguramiento línea producto (USD)", Currency.Type}})
in
    #"Tipo cambiado4"

```  
---

## 🛠️ Herramientas Utilizadas
- Microsoft Excel  
- Power Query (M language)
- SQL (modeling)
- Microsoft Power BI (M, DAX, dashboard)

---

## 📁 Estructura del Repositorio
