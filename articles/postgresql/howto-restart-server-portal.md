---
title: Reinicio de un servidor de Azure Database for PostgreSQL mediante Azure Portal
description: En este artículo se describe cómo reiniciar un servidor de Azure Database for PostgreSQL mediante Azure Portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2018
ms.openlocfilehash: 7d409db839f94e27ac036550c22302188f37cc90
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545882"
---
# <a name="restart-azure-database-for-postgresql-server-using-azure-portal"></a>Reinicio de un servidor de Azure Database for PostgreSQL mediante Azure Portal
En este tema se describe cómo reiniciar un servidor de Azure Database for PostgreSQL. Es posible que deba reiniciar el servidor por motivos de mantenimiento, lo que causa una breve interrupción del servicio mientras el servidor realiza la operación.

Si el servicio está ocupado, se bloqueará el reinicio del servidor. Por ejemplo, el servicio puede estar procesando una operación solicitada anteriormente, como el escalado de núcleos virtuales.
 
El tiempo necesario para completar un reinicio depende del proceso de recuperación de PostgreSQL. Para reducir el tiempo de reinicio, se recomienda que minimice la cantidad de actividades que se ejecutan en el servidor antes del reinicio.

## <a name="prerequisites"></a>Requisitos previos
Para completar esta guía, necesita:
- Un [servidor y una base de datos de Azure Database for PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="perform-server-restart"></a>Realización del reinicio del servidor

Los pasos siguientes reinician el servidor de PostgreSQL:

1. En Azure Portal, seleccione el servidor de Azure Database for PostgreSQL.

2. En la barra de herramientas de la página de **información general** del servidor, haga clic en **Restart** (reiniciar).

   ![Azure Database for PostgreSQL - Información general - Botón Reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Haga clic en **Sí** para confirmar el reinicio del servidor.

   ![Azure Database for PostgreSQL - Confirmación del reinicio ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que el estado del servidor cambia a "Reiniciando".

   ![Azure Database for PostgreSQL - Estado del reinicio ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirme que el reinicio del servidor es correcto.

   ![Azure Database for PostgreSQL - Reinicio correcto ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Pasos siguientes

[Inicio rápido: Creación de un servidor de Azure Database for PostgreSQL mediante Azure Portal](./quickstart-create-server-database-portal.md)