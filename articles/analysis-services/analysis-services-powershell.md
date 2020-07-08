---
title: Управление службами Azure Analysis Services с помощью PowerShell | Документация Майкрософт
description: Описание командлетов PowerShell для Azure Analysis Services, используемых для распространенных административных задач, таких как создание серверов, приостановка операций или изменение уровня обслуживания.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dfa9de94665c0ee98d0252be6de807f18e39b087
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697972"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Управление службами Azure Analysis Services с помощью PowerShell

В этой статье описаны командлеты PowerShell, используемые для выполнения задач управления базами данных и сервером служб Azure Analysis Services. 

Для выполнения таких задач управления ресурсами сервера, как создание или удаление сервера, приостановка или возобновление работы сервера или изменение уровня обслуживания (уровня служб), используются командлеты Azure Analysis Services. Для выполнения других задач управления базами данных, таких как добавление или удаление участников роли, обработка или секционирование, используются командлеты, включенные в том же модуле SqlServer, что и в службах SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Разрешения

Для большинства задач PowerShell требуется, чтобы у пользователя были привилегии администратора на сервере служб Analysis Services, которым он управляет. Запланированные задачи PowerShell являются автоматическими операциями. У учетной записи или субъекта-службы, запускающих планировщик, должны быть права администратора на сервере служб Analysis Services. 

Для выполнения операций с сервером с использованием командлетов Azure PowerShell учетная запись или планировщик учетной записи должны также принадлежать к роли владельца для данного ресурса (указывается в настройках [управления доступом на основе ролей (RBAC) в Azure](../role-based-access-control/overview.md)). 

## <a name="resource-and-server-operations"></a>Операции с ресурсами и серверами 

Установка модуля: [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Документация: [справочные материалы по Az.AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Операции с базой данных

Для операций с базами данных служб Azure Analysis Services используется тот же модуль SqlServer, что и для служб SQL Server Analysis Services. Однако для служб Azure Analysis Services поддерживаются не все командлеты. 

Модуль SqlServer предоставляет командлеты для конкретных задач управления базой данных, а также командлет общего назначения Invoke-ASCmd, который принимает запрос TMSL или сценарий. Для служб Azure Analysis Services поддерживаются следующие командлеты из модуля SqlServer.

Установка модуля: [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Документация: [справочные материалы по SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Поддерживаемые командлеты

|Командлет|Описание|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Добавление участника в роль базы данных.| 
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
* [Скачивание модуля PowerShell SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачивание SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Модуль SqlServer в коллекции PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Программирование табличных моделей с уровнем совместимости 1200 и выше](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
