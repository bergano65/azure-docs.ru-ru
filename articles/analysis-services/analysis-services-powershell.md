---
title: Управление службами Azure Analysis Services с помощью PowerShell | Документация Майкрософт
description: Описывается управление службами Azure Analysis Services с помощью PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 07/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a958c33e173c881a3ad09a49fe9f71ddb0c9df56
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508948"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Управление службами Azure Analysis Services с помощью PowerShell

В этой статье описаны командлеты PowerShell, используемые для выполнения задач управления базами данных и сервером служб Azure Analysis Services. 

Задачи управления ресурсов сервера, такие как создание или удаление сервера, приостановка или возобновление работы сервера или изменение уровня обслуживания (уровня) с помощью командлетов Azure Analysis Services. Для выполнения других задач управления базами данных, таких как добавление или удаление участников роли, обработка или секционирование, используются командлеты, включенные в том же модуле SqlServer, что и в службах SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Разрешения

Для большинства задач PowerShell требуется, чтобы у пользователя были привилегии администратора на сервере служб Analysis Services, которым он управляет. Запланированные задачи PowerShell являются автоматическими операциями. У учетной записи или субъекта-службы, запускающих планировщик, должны быть права администратора на сервере служб Analysis Services. 

Для работы сервера с помощью командлетов Azure PowerShell, учетную запись или планировщик учетной записи необходимо также принадлежать к роли владельца ресурса в [управления доступом на основе ролей (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Ресурс и сервер управления 

Установите модуль - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Документация: [Az.AnalysisServices ссылки](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Операции с базой данных

Azure операций базы данных Analysis Services используется один и тот же модуль SqlServer как SQL Server Analysis Services. Однако для служб Azure Analysis Services поддерживаются не все командлеты. 

Модуль SqlServer предоставляет командлеты для конкретных задач управления базой данных, а также командлет общего назначения Invoke-ASCmd, который принимает запрос TMSL или сценарий. Для служб Azure Analysis Services поддерживаются следующие командлеты из модуля SqlServer.

Установите модуль - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Документация: [ссылку SqlServer](/powershell/module/sqlserver)

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
* [Tabular Model Programming for Compatibility Level 1200 and higher](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200) (Программирование табличной модели для уровня совместимости 1200 и более высокого)
