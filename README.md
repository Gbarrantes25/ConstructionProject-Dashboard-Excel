# Construction Project Dashboard

## 📃 Descripción General
Este proyecto fue elaborado para una empresa del rubro de construcción con 10 proyectos inmobiliarios. Los datos empleados son ficticios.
La finalidad de este proyecto es revisar el avance de cada proyecto por fase e items.


## 📊 Contenido del proyecto
- Hoja "DimProject": Contiene la tabla dimensional de Proyectos.
- Hoja "DimItems": Contiene la tabla dimensional de items.
- Hoja "FactProgress": Contiene la tabla de hechos de Progreso de proyectos.
- Hoja "DimPM": Contiene la tabla dimensional de Project Management.
- Hoja "DimOwner": Contiene la tabla dimensional de los responsables por items.
- Hoja "Overview": Contiene la primera hoja del Dashboard.
- Hoja "Phase & Line": Contiene la segunda hoja del Dashboard.
- Hoja "Gantt View": Contiene la última hoja del Dashboard.
- Hoja "Settings": Contiene los datos de la interactividad del Dashboard.


## 🛠️ Herramientas y Tecnologías Utilizadas
- Visualización: Microsoft Excel.
- Fuente de Datos: Está incluido dentro del archivo .xlsx
- Lenguajes: DAX para las medidas calculadas y Power Query (Lenguaje M) para la transformación de datos.


## ⚙️ Configuración del Entorno
- Software Necesario: Microsoft Excel.
- Instalación:
  - Descargar [Construction Project Dashboard.xlsx](https://github.com/Gbarrantes25/ConstructionProject-Dashboard-Excel/blob/main/Construction%20Project%20Dashboard.xlsx) con Microsoft Excel.
  - Entrar a Inicio y darle click a "Actualizar".


## 📂 Estructura del Repositorio
<code>.
  ├── Construction Project Dashboard.xlsx  # Contiene el archivo del proyecto en formato .xlsx            
  └── README.md                            # Este archivo.
</code>


## ✅ Características Principales
- Transformaciones en Power Query: Se realizaron procesos de limpieza y modelado de datos para optimizar el rendimiento.
- Creación de tabla calendario.
- Medidas DAX:
  <details>
  <summary>Click para expandir medidas</summary>

    
  - **#Total Projects:** `DISTINCTCOUNT(FactProgress[ID_Project])`
  - **#Budget:** `SUM(DimProject[Budget])`
  - **#Actual Cost:** `SUM(FactProgress[Real_Cost])`
  - **#Planned Cost:** `SUM(FactProgress[Expected_Cost])`
  - **#At-Risk Projects:**
    ```dax
    VAR _count = COUNTROWS(FILTER(DimProject,[_Alert Progress]="Critical" || [_Alert Progress]="Behind Schedule")) 
    RETURN IF(OR(ISBLANK(_count),_count=0),0,_count)
    ```
  - **#Cost Progress(%):** `DIVIDE([#Actual Cost],[#Planned Cost],0)`
  - **#Planned Progress(%):** `MIN(1,DIVIDE([#PlanToday],[#Planned Days],0))`
  - **#PlanToday:** `SUM(FactProgress[Plan Today])`
  - **#Planned Days:** `DATEDIFF(MIN([Planned_StartDate]),MAX([Planned_EndDate]),DAY)+1`
  - **#Real Progress(%):** 
    ```dax
    VAR _progress = DIVIDE([#Real Today],[#Real Days],0) 
    VAR result = IF(_progress>1,1,_progress) 
    RETURN result
    ```
  - **#Real Today:** `SUMX(FactProgress,[Real Today])`
  - **#Real Days:** `SUM([Real Days])`
  - **#BudgetvsActual:** `DIVIDE([#Actual Cost],[#Budget],0)`
  - **Alert Progress:**
    ```dax
    VAR dateendplanned = MAX(FactProgress[Planned_EndDate])
    VAR dateendreal = COUNTBLANK(FactProgress[Real_EndDate])
    VAR realprogress = [#Real Progress(%)]
    VAR plannedprogress = [#Planned Progress(%)]
    VAR result = SWITCH(TRUE(),realprogress=1,"Complete",AND(dateendreal>0,dateendplanned<TODAY()),"Critical",realprogress<plannedprogress-0.1,"Behind schedule","On schedule")
    RETURN result
    ```
  - **Alert Cost:**
    ```dax
    SWITCH(TRUE(),[#Cost Progress(%)]>1.1,"Critical Cost Overrun",[#Cost Progress(%)]>1,"Moderate cost overrun", [#Cost Progress(%)]>0.94,"On budget",[#Cost Progress(%)]>0,"Cost Efficiency","Unexpended budget")
    ```
  - **_CPI:**
    ```dax
    VAR _progress = SWITCH(TRUE(),([#Actual Cost]>0) && ([#Real Progress(%)]>0),(([#Real Progress(%)]*[#Planned Cost])/[#Actual Cost]),0)
    VAR result = SWITCH(TRUE(),_progress=0,"Unexpended budget",_progress>=1.05,"Excellent",_progress>1,"Good",_progress>0.9,"Warning",_progress>0,"Critical")
    RETURN result
    ```
  - **PM:**
    ```dax
    IF(HASONEVALUE(DimProject[Name_Project]),MAXX(FactProgress,RELATED(DimProjectManagement[PM_Name])),"PM")
    ```
  - **KPI Alert Cost:**
    ```dax
    SWITCH(TRUE(),[_Alert Cost]="Critical Cost Overrun" || [_Alert Cost]="Moderate cost overrun",1, [_Alert Cost]="On Budget",0.7,[_Alert Cost] = "Cost Efficiency",0.5,0)
    ```
  - **KPI CPI:**
    ```dax
    SWITCH(TRUE(),[_CPI]="Unexpended Budget",1,[_CPI]="Excellent",0.9,[_CPI]="Good",0.75,[_CPI]="Warning",0.5,[_CPI]="Critical",0.1)
    ```
  - **KPI Progress:**
    ```dax
    SWITCH(TRUE(),[_Alert Progress]="Complete",1,[_Alert Progress]="Behind Schedule",0.9,[_Alert Progress]="On Schedule",0.5,[_Alert Progress]="Critical",0.1)
    ```
  - **#Total Items:**
    ```dax
    DISTINCTCOUNT(FactProgress[ID_Item])
    ```
  - **#Temporary Works Progress:**
    ```dax
    CALCULATE([#Real Progress(%)],DimItems[Phase]="1. Obras Provisionales")
    ```
  - **#Structural Works Progress:**
    ```dax
    CALCULATE([#Real Progress(%)],DimItems[Phase]="2. Estructuras")
    ```
  - **#MEP Progress:**
    ```dax
    CALCULATE([#Real Progress(%)],DimItems[Phase]="3. Instalaciones")
    ```
  - **#Finishes Progress:**
    ```dax
    CALCULATE([#Real Progress(%)],DimItems[Phase]="4. Acabados")
    ```
  - **#Critical Items:**
    ```dax
    VAR CriticalItems = COUNTROWS(FILTER(FactProgress,[Alert Progress]="Crítico"))
    RETURN IF(OR(ISBLANK(CriticalItems),CriticalItems=0),0,CriticalItems)
    ```
  - **#CPI:**
    ```dax
    IF(AND([#Planned Cost]>0,[#Actual Cost]>0),DIVIDE(([#Planned Cost]*[#Real Progress(%)]),[#Actual Cost],0),BLANK())
    ```
  - **#CV:**
    ```dax
    VAR cv = DIVIDE([#Actual Cost],[#Planned Cost],0)-1
    RETURN IF(cv=-1,0,cv)
    ```
  - **#Planned_DateMin:**
    ```dax
    VAR _date = MIN(FactProgress[Planned_StartDate])
    RETURN IF(HASONEVALUE(DimProject[Name_Project]),_date,"Select a project")
    ```
  - **#Planned_DateMax:**
    ```dax
    VAR _date= MAX(FactProgress[Planned_EndDate])
    RETURN IF(HASONEVALUE(DimProject[Name_Project]),_date,"Select a project")
    ```
  - **#Total Days Overdue:**
    ```dax
    VAR days = [#Real Days]-[#Planned Days]
    VAR validate = IF(days<0,0,days)
    RETURN IF(HASONEVALUE(DimProject[Name_Project]),validate,"Select a project")
    ```
  - **#Actual_DateMin:**
    ```dax
    VAR _date= MIN(FactProgress[Real_StartDate])
    RETURN IF(HASONEVALUE(DimProject[Name_Project]),_date,"Select a project")
    ```
  - **#Actual_DateMax:**
    ```dax
    VAR _date= MAX(FactProgress[Real_EndDate])
    RETURN IF(HASONEVALUE(DimProject[Name_Project]),_date,"Select a project")
    ```
  - **_Owner:**
    ```dax
    =IF(HASONEVALUE(DimProject[Name_Project]),MAXX(FactProgress,RELATED(DimOwner[Owner_Name])),"Owner")
    ```
  </details>
- Diseño Interactivo: Uso de paginado, controles de formulario y segmentación de datos.

## 🖼️ Vistas Previas del proyecto
<details>
  <summary>Capturas</summary>
    <img width="1702" height="808" alt="image" src="https://github.com/user-attachments/assets/eabacd09-8f1b-4dd4-a200-187b21fe0135" />
    <img width="1612" height="882" alt="image" src="https://github.com/user-attachments/assets/a6c5243e-b246-4340-a703-7bb0769cf6fa" />
    <img width="2159" height="874" alt="image" src="https://github.com/user-attachments/assets/3c2b3ddf-2796-4844-8dc2-9837eed6e9fd" />
</details>

<details>
  <summary>Video</summary>
  https://www.youtube.com/watch?v=ISpue331_Y8
</details>



## 👤 Autor
- Giancarlo Barrantes
- Lima, Perú
- [Linkedin](https://www.linkedin.com/in/gb25/)
