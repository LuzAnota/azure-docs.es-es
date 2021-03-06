---
title: En este tutorial, va a crear una máquina virtual de Azure Stack mediante una plantilla | Microsoft Docs
description: Describe cómo usar el Kit de desarrollo de Azure Stack para crear una máquina virtual mediante una plantilla predefinida y una plantilla personalizada de GitHub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 9ccdea6ca0bf46bbc19e5a7e19ac2d3527138345
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241787"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Tutorial: creación de una máquina virtual mediante una plantilla de comunidad

Como operador o usuario de Azure Stack, puede crear una máquina virtual mediante [plantillas de inicio rápido personalizadas de GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates) en lugar de implementar una manualmente desde Marketplace de Azure Stack.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Uso de plantillas de inicio rápido de Azure Stack 
> * Crear una máquina virtual con una plantilla personalizada de GitHub
> * Iniciar minikube e instalar una aplicación

## <a name="azure-stack-quickstart-templates"></a>Plantillas de inicio rápido de Azure Stack

Las plantillas de inicio rápido de Azure Stack se almacenan en el [repositorio público de plantillas de inicio rápido de AzureStack](https://github.com/Azure/AzureStack-QuickStart-Templates) en GitHub. Este repositorio contiene las plantillas de implementación de Azure Resource Manager que se han probado con el Kit de desarrollo de Microsoft Azure Stack. Puede utilizarlas para que sea más fácil evaluar Azure Stack y utilizar el entorno del Kit de desarrollo de Azure Stack. 

Con el tiempo, muchos usuarios de GitHub han contribuido al repositorio, dando como resultado una colección de más de 400 plantillas de implementación. Este repositorio es un excelente punto de partida para obtener una mejor comprensión de cómo puede implementar varios tipos de entornos en Azure Stack. 

>[!IMPORTANT]
> Algunas de estas plantillas las crean miembros de la comunidad y no Microsoft. Su propietario (no Microsoft) le permite usar las plantillas con arreglo a un acuerdo de licencia. Microsoft no se responsabiliza de estas plantillas en cuanto a su seguridad, compatibilidad ni rendimiento. Las plantillas de la comunidad no reciben ningún tipo de soporte en ningún servicio ni programa de soporte de Microsoft. Dichas plantillas se proporcionan TAL CUAL, sin garantía de ningún tipo.

Si desea contribuir con sus plantillas de Azure Resource Manager a GitHub, haga su contribución en el repositorio [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates). Para más información sobre este repositorio y cómo realizar contribuciones, consulte el [archivo Léame](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 

## <a name="create-a-vm-using-a-custom-github-template"></a>Crear una máquina virtual con una plantilla personalizada de GitHub

En este tutorial de ejemplo, la plantilla de inicio rápido [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) de Azure Stack se utiliza para implementar una máquina virtual Ubuntu 16.04 en Azure Stack mediante la ejecución de Minikube para administrar un clúster de Kubernetes.

Minikube es una herramienta que facilita la ejecución local de Kubernetes. Minikube ejecuta un clúster de Kubernetes de un solo nodo dentro de una máquina virtual para que los usuarios puedan probar Kubernetes o desarrollar con él día a día. Admite un clúster de Kubernetes simple, de un nodo, que se ejecuta en una máquina virtual Linux. Minikube es la forma más rápida y directa de poner en marcha un clúster de Kubernetes totalmente funcional. Permite a los desarrolladores desarrollar y probar sus implementaciones de aplicaciones basadas en Kubernetes en sus máquinas locales. Arquitectónicamente, la máquina virtual de Minikube ejecuta los componentes del nodo principal y agente de manera local:

- Los componentes del nodo principal como el servidor de API, Scheduler y el [servidor etcd](https://coreos.com/etcd/) se ejecutan en un único proceso de Linux denominado LocalKube.
- Los componentes del nodo de agente se ejecutan dentro de contenedores de docker exactamente como llevarían a cabo en un nodo de agente normal. Desde el punto de vista de la implementación de aplicaciones, no hay diferencia cuando la aplicación se implementa en un clúster de Minikube o Kubernetes normal.

Esta plantilla instala los componentes siguientes:

- Máquina virtual Ubuntu 16.04 LTS
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> La imagen de máquina virtual Ubuntu (Ubuntu Server 16.04 LTS en este ejemplo) ya se debe haber agregado en Marketplace de Azure Stack antes de realizar estos pasos.

1.  Seleccione **+ Crear un recurso**, después, **Personalizar** y, a continuación, **Implementación de plantilla**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Seleccione **Editar plantilla**.

    ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Seleccione **Plantilla de inicio rápido**.

    ![](media/azure-stack-create-vm-template/3.PNG)

4. Seleccione **101-vm-linux-minikube** en las plantillas disponibles mediante la lista desplegable **Seleccionar una plantilla** y, después, haga clic en **Aceptar**.  

    ![](media/azure-stack-create-vm-template/4.PNG)

5. Si desea realizar modificaciones en el archivo JSON de la plantilla, puede hacerlo. En caso contrario, o cuando haya terminado, seleccione **Guardar** para cerrar el cuadro de diálogo **Editar plantilla**.

    ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Seleccione **Parámetros**, complete o modifique los campos disponibles según sea necesario y, después, haga clic en **Aceptar**. Seleccione la suscripción que desea utilizar, cree o elija un nombre de grupo de recursos existente y, después, seleccione **Crear** para iniciar la implementación de la plantilla.

    ![](media/azure-stack-create-vm-template/6.PNG)

7. Seleccione la suscripción que desea utilizar, cree o elija un nombre de grupo de recursos existente y, después, seleccione **Crear** para iniciar la implementación de la plantilla.

    ![](media/azure-stack-create-vm-template/7.PNG)

8. La implementación del grupo de recursos tarda varios minutos en crear una máquina virtual basada en la plantilla personalizada. Puede supervisar el estado de la instalación a través de notificaciones y de las propiedades del grupo de recursos. 

    ![](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > La máquina virtual se ejecutará cuando se complete la implementación. 

## <a name="start-minikube-and-install-an-application"></a>Iniciar Minikube e instalar una aplicación

Ahora que se ha creado correctamente la máquina virtual Linux, puede iniciar sesión para iniciar Minikube e instalar una aplicación. 

1. Una vez finalizada la implementación, seleccione **Conectar** para ver la dirección IP pública que se usará para conectarse a la máquina virtual Linux. 

    ![](media/azure-stack-create-vm-template/9.PNG)

2. Desde un símbolo del sistema con privilegios elevados, ejecute **mstsc.exe** para abrir Conexión a Escritorio remoto y conéctese a la dirección IP pública de la máquina virtual Linux detectada en el paso anterior. Cuando se le solicite iniciar sesión en xRDP, utilice las credenciales que especificó al crear la máquina virtual.

    ![](media/azure-stack-create-vm-template/10.PNG)

3. Abra el emulador del terminal y escriba los siguientes comandos para iniciar Minikube:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![](media/azure-stack-create-vm-template/11.PNG)

4. Abra el explorador web y visite la dirección del panel de Kubernetes. Enhorabuena, ahora tiene una instalación de Kubernetes completamente funcional con Minikube.

    ![](media/azure-stack-create-vm-template/12.PNG)

5. Para implementar una aplicación de ejemplo, visite la página oficial de documentación de Kubernetes, omita la sección "Create Minikube Cluster" (Creación de un clúster de Minikube) ya que ya ha creado uno anteriormente. Vaya a la sección "Create your Node.js application" (Creación de la aplicación Node.js) en https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Conocer las plantillas de inicio rápido de Azure Stack 
> * Crear una máquina virtual con una plantilla personalizada de GitHub
> * Iniciar minikube e instalar una aplicación

