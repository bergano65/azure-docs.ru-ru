---
title: Начало работы с Azure Data Lake Analytics с помощью Azure PowerShell
description: Создание учетной записи Azure Data Lake Analytics и отправка задания U-SQL с помощью Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.topic: conceptual
ms.date: 05/04/2017
ms.openlocfilehash: ad0a8ea4d06b5085179d4fd3c162114c00518ce1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156473"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Начало работы с Azure Data Lake Analytics с помощью Azure PowerShell

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как использовать Azure PowerShell для создания учетных записей Azure Data Lake Analytics, а затем отправлять и выполнять задания U-SQL. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Перед началом работы с этим руководством необходимо иметь следующую информацию:

* **Учетная запись Azure Data Lake Analytics**. См. раздел [Начало работы с Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Рабочая станция с Azure PowerShell**. Ознакомьтесь со статьей [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Вход в Azure

Для работы с руководством необходим опыт работы с Azure PowerShell. В частности вам нужно знать, как выполнить вход в Azure. Если вам нужна помощь, см. раздел [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Вход в систему с использованием имени подписки:

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

Вместо имени подписки для входа можно использовать ее идентификатор:

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

При успешном выполнении этой команды выходные данные будут выглядеть так:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Подготовка к работе с руководством

В этом руководстве во фрагментах кода PowerShell для хранения информации используются следующие переменные:

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Получение сведений об учетной записи Data Lake Analytics

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Отправка задания U-SQL

Создайте переменную для хранения скрипта U-SQL PowerShell.

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Отправьте текст сценария с использованием командлета `Submit-AdlJob` и параметра `-Script`.

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

Кроме того, вы можете отправить файл сценария с помощью параметра `-ScriptPath`:

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

Получите сведения о состоянии задания с помощью `Get-AdlJob`. 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Вместо того чтобы снова и снова вызывать командлет Get-AdlJob, пока задание не будет завершено, используйте командлет `Wait-AdlJob`.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Скачайте выходной файл с помощью `Export-AdlStoreItem`.

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>См. также

* Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).