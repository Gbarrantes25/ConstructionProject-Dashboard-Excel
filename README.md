# 🏗️ Construction Project Dashboard

[![Microsoft Excel](https://img.shields.io/badge/Microsoft%20Excel-217346?style=flat&logo=microsoftexcel&logoColor=white)](https://www.microsoft.com/en-us/microsoft-365/excel)
[![Power Query](https://img.shields.io/badge/Power%20Query-0078D4?style=flat&logo=microsoft&logoColor=white)](https://learn.microsoft.com/en-us/power-query/)
[![DAX](https://img.shields.io/badge/DAX-F2C811?style=flat&logo=powerbi&logoColor=black)](https://learn.microsoft.com/en-us/dax/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/Version-1.0-blue.svg)]()

---

## 📋 Tabla de Contenidos

- [Descripción General](#-descripción-general)
- [Características Principales](#-características-principales)
- [Contenido del Proyecto](#-contenido-del-proyecto)
- [Herramientas y Tecnologías](#️-herramientas-y-tecnologías-utilizadas)
- [Configuración del Entorno](#️-configuración-del-entorno)
- [Estructura del Repositorio](#-estructura-del-repositorio)
- [Medidas DAX Implementadas](#-medidas-dax-implementadas)
- [Vistas Previas](#️-vistas-previas-del-proyecto)
- [Autor](#-autor)
- [Licencia](#-licencia)

---

## 📃 Descripción General

Dashboard profesional para el **seguimiento y control de proyectos de construcción**, desarrollado en **Microsoft Excel** con **Power Query** y **DAX (Power Pivot)**. Este proyecto permite a gerentes de obra y stakeholders transformar datos crudos en información accionable mediante:

- 📊 **Visualización en Tiempo Real:** Progreso real vs. planificado de manera dinámica.
- 💰 **Control Presupuestario:** Monitoreo de desviaciones de presupuesto y cronograma.
- 🚨 **Sistema de Alertas:** Identificación automática de proyectos en riesgo (Critical, Behind Schedule).
- 📈 **KPIs de Construcción:** Métricas clave como CPI, avance por fase (Obras Provisionales, Estructuras, Instalaciones, Acabados) y análisis de costos.

---

## ✨ Características Principales

### 🔄 Transformaciones en Power Query
- Procesos de limpieza y modelado de datos para optimizar el rendimiento.
- Creación de tabla calendario personalizada para análisis temporal.
- Unificación de fuentes de datos en un modelo relacional coherente.

### 📐 Modelo de Datos
- **Tablas de Dimensión:** `DimProject`, `DimItems`, `DimPM` (Project Management), `DimOwner`.
- **Tabla de Hechos:** `FactProgress` con información detallada de progreso, costos y fechas.
- Relaciones optimizadas para análisis multidimensional dentro de Excel.

### 🎨 Diseño Interactivo
- Navegación por hojas con paginado intuitivo.
- Controles de formulario y segmentación de datos (Slicers) para filtrado dinámico.
- Visualizaciones condicionales con formatos y alertas de color automáticas.

---

## 📊 Contenido del Proyecto

| Hoja | Descripción | Contenido Principal |
|--------|-------------|---------------------|
| **Overview** | Vista general ejecutiva | KPIs globales, estado de proyectos, alertas de costo y progreso. |
| **Phase & Line** | Análisis por fase y partida | Desglose del avance por fases (Obras Provisionales, Estructuras, Instalaciones, Acabados). |
| **Gantt View** | Análisis temporal | Comparación de fechas planificadas vs. reales y días de retraso. |
| **Settings** | Configuración | Datos y parámetros que controlan la interactividad del Dashboard. |

---

## 🛠️ Herramientas y Tecnologías Utilizadas

### Software Requerido
- **Microsoft Excel** (versión 2016 o superior, recomendado Microsoft 365 para mejores funciones de Power Pivot).

### Fuente de Datos
- 📄 Los datos fuente están integrados y estructurados directamente dentro del archivo `.xlsx` en las hojas de dimensiones y hechos.

### Lenguajes y Tecnologías
- **DAX** - Cálculo de medidas y KPIs avanzados (+25 fórmulas).
- **Power Query (Lenguaje M)** - Transformación, limpieza y carga de datos.
- **Power Pivot** - Modelado de datos y creación de relaciones dentro de Excel.

---

## ⚙️ Configuración del Entorno

### Prerequisitos
1. Tener instalado **Microsoft Excel** (versión de escritorio).
2. Asegurarse de que los complementos **Power Query** y **Power Pivot** estén habilitados en Excel.

### Paso 1: Obtener el Proyecto
1. Clonar o descargar el repositorio completo.
2. Ubicar el archivo `Construction Project Dashboard.xlsx`.

### Paso 2: Cargar y Actualizar Datos
1. Abrir el archivo en Microsoft Excel.
2. Navegar a la pestaña **Datos** en la cinta de opciones.
3. Hacer clic en el botón **Actualizar todo** para refrescar las consultas de Power Query y el modelo de datos Power Pivot.
4. Si se desea modificar los datos, se pueden editar las tablas directamente o ajustar las consultas en el Editor de Power Query.

---

## 📂 Estructura del Repositorio

```text
.
├── Construction Project Dashboard.xlsx  # Archivo principal del proyecto con el dashboard, modelo de datos y macros
├── .gitignore                           # Archivo de configuración Git
└── README.md                            # Este archivo de documentación
```

## 🧮 Medidas DAX Implementadas
El proyecto cuenta con más de 25 medidas DAX organizadas para el control de proyectos de construcción. A continuación, se detallan las principales:

#### 📊 Métricas Básicas y de Progreso
<details>
<summary>Ver medidas básicas</summary>

#Total Projects 

```
= DISTINCTCOUNT(FactProgress[ID_Project])
```

#Total Items 

```
= DISTINCTCOUNT(FactProgress[ID_Item])
```

#Budget 

```
= SUM(DimProject[Budget])
```

#Actual Cost 

```
= SUM(FactProgress[Real_Cost])
```

#Planned Cost 

```
= SUM(FactProgress[Expected_Cost])
```

#PlanToday 

```
= SUM(FactProgress[Plan Today])
```

#Real Today 

```
= SUMX(FactProgress, [Real Today])
```

#Planned Days 

```
= DATEDIFF(MIN([Planned_StartDate]), MAX([Planned_EndDate]), DAY) + 1
```

#Real Days 

```
= SUM([Real Days])
```

</details>

#### 📈 Indicadores de Rendimiento (KPIs)
<details>
<summary>Ver cálculos de rendimiento</summary>

#Cost Progress(%) 

```
= DIVIDE([#Actual Cost], [#Planned Cost], 0)
```

#Planned Progress(%) 

```
= MIN(1, DIVIDE([#PlanToday], [#Planned Days], 0))
```

#Real Progress(%) 

```
= 
VAR _progress = DIVIDE([#Real Today], [#Real Days], 0)
VAR result = IF(_progress > 1, 1, _progress)
RETURN result
```

#BudgetvsActual 

```
= DIVIDE([#Actual Cost], [#Budget], 0)
```

#CPI 

```
= 
IF(AND([#Planned Cost] > 0, [#Actual Cost] > 0), 
   DIVIDE(([#Planned Cost] * [#Real Progress(%)]), [#Actual Cost], 0), 
   BLANK())
```

#CV 

```
= 
VAR cv = DIVIDE([#Actual Cost], [#Planned Cost], 0) - 1
RETURN IF(cv = -1, 0, cv)
```

</details>

#### 🚦 Sistema de Alertas
<details>
<summary>Ver medidas de alertas</summary>

Alert Progress 

```
= 
VAR dateendplanned = MAX(FactProgress[Planned_EndDate])
VAR dateendreal = COUNTBLANK(FactProgress[Real_EndDate])
VAR realprogress = [#Real Progress(%)]
VAR plannedprogress = [#Planned Progress(%)]
VAR result = SWITCH(TRUE(),
    realprogress = 1, "Complete",
    AND(dateendreal > 0, dateendplanned < TODAY()), "Critical",
    realprogress < plannedprogress - 0.1, "Behind schedule",
    "On schedule"
)
RETURN result
```

Alert Cost 

```
= 
SWITCH(TRUE(),
    [#Cost Progress(%)] > 1.1, "Critical Cost Overrun",
    [#Cost Progress(%)] > 1, "Moderate cost overrun",
    [#Cost Progress(%)] > 0.94, "On budget",
    [#Cost Progress(%)] > 0, "Cost Efficiency",
    "Unexpended budget"
)
```

_CPI 

```
= 
VAR _progress = SWITCH(TRUE(),
    ([#Actual Cost] > 0) && ([#Real Progress(%)] > 0), (([#Real Progress(%)] * [#Planned Cost]) / [#Actual Cost]),
    0
)
VAR result = SWITCH(TRUE(),
    _progress = 0, "Unexpended budget",
    _progress >= 1.05, "Excellent",
    _progress > 1, "Good",
    _progress > 0.9, "Warning",
    _progress > 0, "Critical"
)
RETURN result
```

#At-Risk Projects 

```
= 
VAR _count = COUNTROWS(FILTER(DimProject, [_Alert Progress] = "Critical" || [_Alert Progress] = "Behind Schedule"))
RETURN IF(OR(ISBLANK(_count), _count = 0), 0, _count)
```

</details>

#### 🏗️ Avance por Fase de Construcción
<details>
<summary>Ver medidas por fase</summary>

#Temporary Works Progress 

```
= CALCULATE([#Real Progress(%)], DimItems[Phase] = "1. Obras Provisionales")
```

#Structural Works Progress 

```
= CALCULATE([#Real Progress(%)], DimItems[Phase] = "2. Estructuras")
```

#MEP Progress 

```
= CALCULATE([#Real Progress(%)], DimItems[Phase] = "3. Instalaciones")
```

#Finishes Progress 

```
= CALCULATE([#Real Progress(%)], DimItems[Phase] = "4. Acabados")
```

#Critical Items 

```
= 
VAR CriticalItems = COUNTROWS(FILTER(FactProgress, [Alert Progress] = "Crítico"))
RETURN IF(OR(ISBLANK(CriticalItems), CriticalItems = 0), 0, CriticalItems)
```

</details>

#### 📅 Fechas y Contexto
<details>
<summary>Ver medidas de fechas y contexto</summary>

#Planned_DateMin 

```
= 
VAR _date = MIN(FactProgress[Planned_StartDate])
RETURN IF(HASONEVALUE(DimProject[Name_Project]), _date, "Select a project")
```

#Planned_DateMax 

```
= 
VAR _date = MAX(FactProgress[Planned_EndDate])
RETURN IF(HASONEVALUE(DimProject[Name_Project]), _date, "Select a project")
```

#Actual_DateMin 

```
= 
VAR _date = MIN(FactProgress[Real_StartDate])
RETURN IF(HASONEVALUE(DimProject[Name_Project]), _date, "Select a project")
```

#Actual_DateMax 

```
= 
VAR _date = MAX(FactProgress[Real_EndDate])
RETURN IF(HASONEVALUE(DimProject[Name_Project]), _date, "Select a project")
```

#Total Days Overdue 

```
= 
VAR days = [#Real Days] - [#Planned Days]
VAR validate = IF(days < 0, 0, days)
RETURN IF(HASONEVALUE(DimProject[Name_Project]), validate, "Select a project")
```

PM 

```
= IF(HASONEVALUE(DimProject[Name_Project]), MAXX(FactProgress, RELATED(DimProjectManagement[PM_Name])), "PM")
```

_Owner 

```
= IF(HASONEVALUE(DimProject[Name_Project]), MAXX(FactProgress, RELATED(DimOwner[Owner_Name])), "Owner")
```

</details>

## 🖼️ Vistas Previas del Proyecto
<details>
<summary>📸 Ver Capturas de Pantalla</summary>
    <img width="1702" height="808" alt="image" src="https://github.com/user-attachments/assets/eabacd09-8f1b-4dd4-a200-187b21fe0135" />
    <img width="1612" height="882" alt="image" src="https://github.com/user-attachments/assets/a6c5243e-b246-4340-a703-7bb0769cf6fa" />
    <img width="2159" height="874" alt="image" src="https://github.com/user-attachments/assets/3c2b3ddf-2796-4844-8dc2-9837eed6e9fd" />

</details>

<details>
<summary>🎥 Ver Video Demostrativo</summary>
  
   Ver en YouTube: https://youtu.be/UGYFBVz-8AA?si=TrvGqWUrrEhUjzlv
</details>

## 👤 Autor
Giancarlo Barrantes

Business Intelligence Developer | Power BI Specialist


<a href="https://www.linkedin.com/in/gb25"><img width="70" height="20" alt="image" src="https://github.com/user-attachments/assets/e0d69586-c6f9-4f66-9a3d-bc8dfa146463" /></a>
<a href="https://www.instagram.com/gbarrantes25"><img width="70" height="20" alt="image" src="https://github.com/user-attachments/assets/68773029-5e0f-49da-92e9-fb93a223dd09" />
</a>
<a href="https://www.tiktok.com/@gbarrantes25"><img width="65" height="20" alt="image" src="https://github.com/user-attachments/assets/349c87c0-1f3a-4368-8517-64a6012565c0" /></a>
<a href="https://www.youtube.com/@gbarrantes25"><img width="70" height="20" alt="image" src="https://github.com/user-attachments/assets/d9e2cb69-29ec-47fc-93ae-30896a7b533d" />
</a>

## 📝 Licencia
Este proyecto está bajo la Licencia MIT - ver el archivo <a href="https://mit-license.org/">LICENSE</a> para más detalles.

## ⭐ Agradecimientos
Si este proyecto te resultó útil, considera darle una ⭐ en GitHub y compartirlo con otros profesionales de Business Intelligence.

<div align="center">
   Hecho por <a href="https://gbarrantes25.github.io/">Giancarlo Barrantes</a>
</div>
