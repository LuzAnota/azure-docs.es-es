---
title: 'Lección 9 del tutorial de Azure Analysis Services: Creación de jerarquías | Microsoft Docs'
description: Describe cómo crear jerarquías en un modelo tabular.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f34b2fd2ba5f6e3e1433fb8dac04d931a36920d0
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188970"
---
# <a name="create-hierarchies"></a>Creación de jerarquías

En esta lección, creará jerarquías. Las jerarquías son grupos de columnas dispuestas en niveles. Por ejemplo, una jerarquía Geografía puede tener los subniveles País, Estado, Provincia y Ciudad. Las jerarquías pueden aparecer separadas de otras columnas en una lista de campos de aplicación cliente de generación de informes, lo que facilita que los usuarios puedan navegar por ellas e incluirlas en un informe. Para más información, consulte [Jerarquías](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)
  
Para crear jerarquías, use el diseñador de modelos de *Vista de diagrama*. La creación y la administración de jerarquías no se admiten en vista de datos.  
  
Tiempo estimado para completar esta lección: **20 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Para poder realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 8: Creación de perspectivas](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Creación de jerarquías  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Para crear una jerarquía de categorías en la tabla DimProduct, siga estos pasos:  
  
1.  En el diseñador de modelos (vista de diagrama), haga clic en la tabla **DimProduct** > **Crear jerarquía**. Aparece una nueva jerarquía en la parte inferior de la ventana de tabla. Cambie el nombre de la jerarquía **Categoría**.  
  
2.  Haga clic y arrastre la columna **ProductCategoryName** a la nueva jerarquía **Categoría**.  
  
3.  En la jerarquía **Categoría**, haga clic con el botón derecho en la columna **ProductCategoryName** > **Cambiar nombre** y luego escriba **Categoría**.  
  
    > [!NOTE]  
    > Al cambiar el nombre de una columna de una jerarquía no cambia el nombre de esa columna en la tabla. Una columna de una jerarquía es simplemente una representación de la columna en la tabla.  
  
4.  Haga clic y arrastre la columna **ProductSubcategoryName** a la jerarquía **Categoría**. Cambie su nombre por **Subcategoría**. 
  
5.  Haga clic con el botón derecho en la columna **ModelName** > **Agregar a la jerarquía** y luego seleccione **Categoría**. Cambie su nombre por **Modelo**.

6.  Por último, agregue **EnglishProductName** a la jerarquía Categoría. Cambie su nombre por **Producto**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Para crear jerarquías en la tabla DimDate, siga estos pasos:  
  
1.  En la tabla **DimDate**, cree una jerarquía llamada **Calendario**.  
  
3.  Agregue las siguientes columnas en orden:

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  En la tabla **DimDate**, cree una jerarquía **Fiscal**. Incluya las siguientes columnas en orden:  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  Por último, en la tabla **DimDate**, cree una jerarquía **ProductionCalendar**. Incluya las siguientes columnas en orden:  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>Pasos siguientes
[Lección 10: Creación de particiones](../tutorials/aas-lesson-10-create-partitions.md). 
  
  
