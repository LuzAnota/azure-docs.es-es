---
title: Notas de la versión de la actualización 4 de App Service en Azure Stack | Microsoft Docs
description: Obtenga más información sobre el contenido de la actualización 4 para App Service en Azure Stack, los problemas conocidos y la ubicación de descarga.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 2555ce4b9485ba6464bde3422df114343b579641
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243426"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>Notas de la versión de la actualización 4 de App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Estas notas de la versión describen las mejoras y correcciones de la actualización 4 de Azure App Service en Azure Stack y los problemas conocidos. Los problemas conocidos se dividen en aquellos que están relacionados directamente con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1809 al sistema integrado de Azure Stack o implemente el kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.4.
>
>

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 4 de App Service en Azure Stack es **78.0.13698.5**

### <a name="prerequisites"></a>Requisitos previos

Remítase a la [documentación de introducción](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack a la versión 1.4:

- Asegúrese de que todos los roles están listos en la administración de Azure App Service desde el portal de administración de Azure Stack.

- Realice una copia de seguridad de App Service y de las bases de datos maestras:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Realice una copia de seguridad del recurso compartido de archivos del contenido de la aplicación inquilina.

- Distribuya la extensión de script personalizado versión 1.9 del Marketplace.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 4 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- Resolución de la vulnerabilidad de scripts entre sitios [CVE 2018 8600](https://aka.ms/CVE20188600).

- Se agregó compatibilidad para la versión de API 2018-02-01 de App Service.

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK de Azure Stack Portal.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
  - Se agregó NodeJS 10.6.0.
  - Se agregó NPM 6.1.0.
  - Se agregó Zulu OpenJDK 8.31.0.2.
  - Se agregó Tomcat 8.5.34 y 9.0.12.
  - Se han agregado las versiones PHP:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Se ha actualizado a las versiones de Python:
    - 2.7.15
    - 3.6.6
  - Se ha actualizado Git para Windows a la versión 2.17.1.2
  - Se actualizó Kudu a la versión 78.11022.3613.
  
- **Actualizaciones al sistema operativo subyacente de todos los roles**:
  - [Actualización acumulativa 2018-10 para Windows Server 2016 en sistemas basados en x64 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Se resolvió el problema de validación de plantilla durante la implementación de elementos de la galería en los CMS de Wordpress, DNN y Orchard.

- Se resolvió el problema de configuración cuando Azure Stack rota el certificado de cliente de Azure Resource Manager.

- Se restauró la funcionalidad en la configuración de uso compartido de recursos entre orígenes del portal de inquilino de App Service.

- Se muestra un mensaje de error en el portal de administración de App Service cuando el panel de control del proveedor de recursos no puede conectarse a la instancia de SQL Server configurada.

- Se garantiza que el punto de conexión se agregue a la cadena de conexión de almacenamiento personalizada cuando se especifica en la nueva aplicación de función.

### <a name="post-update-steps-optional"></a>Pasos posteriores a la actualización (opcional)

Para los clientes que quieran migrar a una base de datos independiente para las implementaciones de Azure App Service en Azure Stack existentes, ejecute estos pasos una vez completada la actualización 1.4 de Azure App Service en Azure Stack:

> [!IMPORTANT]
> Este procedimiento de migración tarda aproximadamente entre 5 y 10 minutos.  El procedimiento implica la eliminación de las sesiones de inicio de sesión de base de datos existentes.  Planee el tiempo de inactividad para migrar y validar Azure App Service en Azure Stack después de la migración.  Si ha completado estos pasos después de actualizar a Azure App Service en Azure Stack 1.3, no es necesario que los realice.
>
>

1. Agregue las [bases de datos de AppService (appservice_hosting y appservice_metering) al grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Habilite la base de datos independiente.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Durante la conversión de una base de datos a parcialmente independiente, la conversión provocará tiempo de inactividad, ya que todas las sesiones activas deben terminarse.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

Validación

1. Compruebe si SQL Server tiene la independencia habilitada.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Compruebe el comportamiento de independencia existente.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada, tal y como se describe en la documentación de implementación de Azure App Service en Azure Stack.

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Vaya a WorkersNsg en el Portal de administración y agregue una regla de seguridad de salida con las siguientes propiedades:
 * Origen: Cualquiera
 * Intervalo de puertos de origen: *
 * Destino: Direcciones IP
 * Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
 * Intervalo de puertos de destino: 445
 * Protocolo: TCP
 * Acción: PERMITIR
 * Prioridad: 700
 * Nombre: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

Consulte la documentación de las [notas de la versión de Azure Stack 1809](azure-stack-update-1809.md)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para obtener más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
