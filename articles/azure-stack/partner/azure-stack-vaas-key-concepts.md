---
title: Conceptos clave de la validación como servicio de Azure Stack | Microsoft Docs
description: Describe los conceptos clave de la validación como servicio de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1f5c47dd3453c0c8f02f1b0a87e5f2fff123f8be
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242814"
---
# <a name="validation-as-a-service-key-concepts"></a>Conceptos clave de la validación como servicio

En este artículo se describen los conceptos clave de la validación como servicio (VaaS).

## <a name="solutions"></a>Soluciones

Una solución VaaS representa una solución de Azure Stack con una lista de materiales de hardware en particular. La solución VaaS actúa como contenedor para los flujos de trabajo que se ejecutan frente a la solución de Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Creación de una solución en el portal de VaaS

1. Inicie sesión en el [portal de VaaS](https://azurestackvalidation.com).
2. En el panel de soluciones, seleccione **Nueva solución**.
3. Escriba un nombre para la solución. Para obtener sugerencias de nombres, consulte [Convenciones de nomenclatura para soluciones VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Seleccione **Guardar** para crear la solución.

## <a name="workflows"></a>Flujos de trabajo

Un flujo de trabajo VaaS funciona dentro del contexto de una solución VaaS. Representa una serie de conjuntos de pruebas que actúan sobre la funcionalidad de implementación de Azure Stack. Para cada actualización de software o implementación de una solución de Azure Stack, se debe crear un flujo de trabajo.

Los flujos de trabajo se clasifican por tipo de escenario de prueba. En las pruebas no oficiales, el flujo de trabajo **Pruebas superadas** le permite seleccionar las pruebas de toda la documentación y el material adjunto de VaaS disponible. En las pruebas oficiales, el flujo de trabajo de **validación** tiene como destino escenarios de prueba específicos seleccionados por Microsoft.

![Iconos de flujo de trabajo de VaaS](media/tile_all-workflows.png)

> [!NOTE]
> El flujo de trabajo de **validación de la solución**actualmente admite dos escenarios: [Validación de paquetes OEM](azure-stack-vaas-validate-oem-package.md) y [Validación de las actualizaciones de software de Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Para más información sobre los tipos de flujo de trabajo, consulte [¿Qué es la validación como servicio de Azure Stack?](azure-stack-vaas-overview.md)

### <a name="getting-started-with-vaas-workflows"></a>Introducción a los flujos de trabajo de VaaS

1. En el panel de soluciones, cree una solución o seleccione una existente. Esto actualiza y permite los iconos de flujos de trabajo.
2. Para crear un flujo de trabajo, seleccione **Iniciar** en cualquiera de los iconos. Para obtener información específica para cada flujo de trabajo, consulte los artículos siguientes:
    - Prueba superada: [Inicio rápido: Uso del portal de validación como servicio para programar la primera prueba](azure-stack-vaas-schedule-test-pass.md)
    - Validación de la solución: [Validación de una nueva solución de Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Validación de la solución: [Validación de las actualizaciones de software de Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Validación de la solución: [Validación de paquetes de OEM](azure-stack-vaas-validate-oem-package.md)

3. Para administrar o supervisar un flujo de trabajo existente, seleccione **Administrar** en el icono del flujo de trabajo. Seleccione el nombre del flujo de trabajo y use el botón **Editar** para ver las propiedades o cambiar los parámetros comunes de las pruebas.

Para más información sobre las propiedades y los parámetro de los flujos de trabajo, consulte [Parámetros comunes del flujo de trabajo en la validación como servicio de Azure Stack](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Pruebas

Una prueba en VaaS consta de un conjunto de acciones que se ejecutan en una solución de Azure Stack. Las pruebas tienen distintos propósitos identificados por categoría, como funcional o confiabilidad, y tienen como destino uno o varios servicios de Azure Stack. Cada prueba define su propio conjunto de parámetros, algunos de los cuales se especifican mediante los parámetros comunes del flujo de trabajo que los contiene.

Para más información sobre cómo administrar y supervisar las pruebas, consulte [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md).

Para más información sobre los parámetros de prueba, consulte [Parámetros comunes del flujo de trabajo en la validación como servicio de Azure Stack](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agentes

Un agente de VaaS dirige la ejecución de las pruebas. Dos tipos de agentes ejecutan las pruebas de VaaS:

- El **agente en la nube**. Es el agente predeterminado disponible en VaaS. No se necesita ninguna configuración, pero requiere conectividad de entrada a su entorno, y los puntos de conexión de Azure Stack deben poder resolverse desde internet. Algunas pruebas no son compatibles con el agente en la nube.
- Un **agente local**. Esto le permite ejecutar la validación en escenarios donde no es posible la conectividad de entrada a su entorno. Algunas pruebas requieren la ejecución a través del agente local.

Los agentes locales no están asociados a ninguna solución de Azure Stack o VaaS en particular. Como práctica recomendada, deben ejecutarse fuera de un entorno de Azure Stack.

Para obtener instrucciones sobre cómo agregar un agente local, consulte [Implementación del agente local](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos recomendados de la validación como servicio](azure-stack-vaas-best-practice.md)