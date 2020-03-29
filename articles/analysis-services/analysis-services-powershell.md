---
title: Управление службами Azure Analysis Services с помощью PowerShell | Документация Майкрософт
description: Описывает службы анализа Azure PowerShell для общих административных задач, таких как создание серверов, приостановка операций или изменение уровня обслуживания.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572694"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Управление службами Azure Analysis Services с помощью PowerShell

В этой статье описаны командлеты PowerShell, используемые для выполнения задач управления базами данных и сервером служб Azure Analysis Services. 

Задачи управления серверными ресурсами, такие как создание или удаление сервера, приостановка или возобновление операций сервера или изменение уровня обслуживания (уровня) используют cmdlets Azure Analysis Services. Для выполнения других задач управления базами данных, таких как добавление или удаление участников роли, обработка или секционирование, используются командлеты, включенные в том же модуле SqlServer, что и в службах SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Разрешения

Для большинства задач PowerShell требуется, чтобы у пользователя были привилегии администратора на сервере служб Analysis Services, которым он управляет. Запланированные задачи PowerShell являются автоматическими операциями. У учетной записи или субъекта-службы, запускающих планировщик, должны быть права администратора на сервере служб Analysis Services. 

Для операций сервера с использованием cmdlets Azure PowerShell ваша учетная запись или планировщик выполнения учетной записи также должны принадлежать к роли владельца ресурса в [Azure Role-Based Control (RBAC).](../role-based-access-control/overview.md) 

## <a name="resource-and-server-operations"></a>Операции с ресурсами и серверами 

Установка модуля - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Документация - [Ссылка Az.AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Операции с базой данных

Для операций с базами данных служб Azure Analysis Services используется тот же модуль SqlServer, что и для служб SQL Server Analysis Services. Однако для служб Azure Analysis Services поддерживаются не все командлеты. 

Модуль SqlServer предоставляет командлеты для конкретных задач управления базой данных, а также командлет общего назначения Invoke-ASCmd, который принимает запрос TMSL или сценарий. Для служб Azure Analysis Services поддерживаются следующие командлеты из модуля SqlServer.

Установка модуля - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Документация - [Ссылка sqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Поддерживаемые cmdlets

|Командлет|Описание|
|------------|-----------------| 
|[Адди-РольМбер](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Добавление участника в роль базы данных.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Архивация базы данных Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Удаление участника из роли базы данных.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Выполнение сценария TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Обработка базы данных.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Обработка секции.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Обработка таблицы.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Объединение секции.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Восстановление базы данных Analysis Services.| 
  

## <a name="related-information"></a>Дополнительные сведения

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Скачайте модуль PowerShell для SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачивание SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Модуль SqlServer в коллекции PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Программирование табличных моделей с уровнем совместимости 1200 и выше](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
