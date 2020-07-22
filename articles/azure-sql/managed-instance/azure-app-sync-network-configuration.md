---
title: Синхронизация конфигурации сети для службы приложений Azure
titleSuffix: Azure SQL Managed Instance
description: В этой статье описывается, как синхронизировать конфигурацию сети для плана размещения службы приложений Azure с Управляемый экземпляр Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695459"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Синхронизация конфигурации сети для плана размещения службы приложений Azure с помощью Управляемый экземпляр Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Возможно, это связано с тем, что несмотря на то, что [приложение интегрировано с виртуальной сетью Azure](../../app-service/web-sites-integrate-with-vnet.md), невозможно установить соединение с SQL управляемый экземпляр. При обновлении или синхронизации конфигурация сети для плана обслуживания может решить эту проблему. 

## <a name="sync-network-configuration"></a>Синхронизация конфигурации сети 

Для этого выполните следующие действия:  

1. Перейдите к плану службы веб-приложений.

   ![Снимок экрана плана службы приложений](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Выберите **сети** , а затем **щелкните здесь для управления**.

   ![Снимок экрана: Управление планом службы](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Выберите **виртуальную сеть** и щелкните **Синхронизировать сеть**.

   ![Снимок экрана: сеть синхронизации](./media/azure-app-sync-network-configuration/sync.png)

4. Дождитесь завершения синхронизации.
  
   ![Снимок экрана: Синхронизация выполнена](./media/azure-app-sync-network-configuration/sync-done.png)

Теперь вы можете попытаться восстановить подключение к Управляемый экземпляр SQL.

## <a name="next-steps"></a>Дальнейшие шаги

- Сведения о настройке виртуальной сети для Управляемый экземпляр SQL см. в статье [Архитектура виртуальной сети sql управляемый экземпляр](connectivity-architecture-overview.md) и [Настройка существующей виртуальной сети](vnet-existing-add-subnet.md).
