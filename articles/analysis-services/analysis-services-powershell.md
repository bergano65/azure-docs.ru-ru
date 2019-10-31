---
title: Управление службами Azure Analysis Services с помощью PowerShell | Документация Майкрософт
description: Описывается управление службами Azure Analysis Services с помощью PowerShell.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 329ea51895c45c8cdf50a323328fac3c580ffc16
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146220"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Управление службами Azure Analysis Services с помощью PowerShell

В этой статье описаны командлеты PowerShell, используемые для выполнения задач управления базами данных и сервером служб Azure Analysis Services. 

Задачи управления ресурсами сервера, такие как создание или удаление сервера, приостановка или возобновление операций сервера или изменение уровня обслуживания (уровня), используют Azure Analysis Services командлетов. Для выполнения других задач управления базами данных, таких как добавление или удаление участников роли, обработка или секционирование, используются командлеты, включенные в том же модуле SqlServer, что и в службах SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Разрешения

Для большинства задач PowerShell требуется, чтобы у пользователя были привилегии администратора на сервере служб Analysis Services, которым он управляет. Запланированные задачи PowerShell являются автоматическими операциями. У учетной записи или субъекта-службы, запускающих планировщик, должны быть права администратора на сервере служб Analysis Services. 

Для операций сервера, использующих командлеты Azure PowerShell, ваша учетная запись или планировщик учетной записи также должен принадлежать роли владельца для ресурса в [контроле доступа на основе ролей Azure (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Операции с ресурсами и серверами 

Установка модуля — [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Документация — [AZ. AnalysisServices Reference](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Операции с базой данных

Azure Analysis Services операции с базой данных используют тот же модуль SqlServer, что и SQL Server Analysis Services. Однако для служб Azure Analysis Services поддерживаются не все командлеты. 

Модуль SqlServer предоставляет командлеты для конкретных задач управления базой данных, а также командлет общего назначения Invoke-ASCmd, который принимает запрос TMSL или сценарий. Для служб Azure Analysis Services поддерживаются следующие командлеты из модуля SqlServer.

Установка модуля —  [SQLServer](https://www.powershellgallery.com/packages/SqlServer)  
Документация — [Справочник по SQLServer](/powershell/module/sqlserver)

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
  

## <a name="related-information"></a>Связанные сведения

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Скачивание модуля PowerShell SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачивание SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Модуль SqlServer в коллекции PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200 and higher](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200) (Программирование табличной модели для уровня совместимости 1200 и более высокого)
