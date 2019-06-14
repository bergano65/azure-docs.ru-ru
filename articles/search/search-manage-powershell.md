---
title: Скрипты PowerShell, с помощью модуля Az.Search - поиска Azure
description: Создание и настройка службы поиска Azure с помощью PowerShell. Можно масштабировать службы, управлять администратора и ключи api запроса и запроса системной информации.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194280"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Управление службой поиска Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [ПАКЕТ SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Можно запустить командлетах и сценариях PowerShell, в Windows, Linux, или в [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) для создания и настройки службы поиска Azure. **Az.Search** модуль расширяет функциональность Azure PowerShell] с помощью полного соответствия для [API REST управления службы поиска Azure](https://docs.microsoft.com/rest/api/searchmanagement). С помощью Azure PowerShell и **Az.Search**, можно выполнять следующие задачи:

> [!div class="checklist"]
> * [Список всех служб поиска в подписке](#list-search-services)
> * [Получить сведения о службе поиска](#get-search-service-information)
> * [Создание или удаление службы](#create-or-delete-a-service)
> * [Повторное создание ключей API администратора](#regenerate-admin-keys)
> * [Создание и удаление ключей api запроса](#create-or-delete-query-keys)
> * [Масштабирование службы путем увеличения или уменьшения реплик и секций](#scale-replicas-and-partitions)

PowerShell не позволяет изменить имя, регион или уровня службы. Выделенные ресурсы выделяются при создании службы. Изменение базового оборудования (типа узла или расположение) требуется новая служба. Отсутствуют средства или API-интерфейсы для переноса содержимого из одной службы в другую. Все управление содержимым выполняется средствами [REST](https://docs.microsoft.com/rest/api/searchservice/) или [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) API-интерфейсы, и если вы хотите перемещать индексы, необходимо будет повторно создать и загружает их повторно в новой службе. 

Пока нет выделенных команд PowerShell для управления содержимым, можно написать сценарий PowerShell, который вызывает REST или .NET для создания и загрузки индексов. **Az.Search** модуль сам по себе не поддерживает эти операции.

Другие задачи, не поддерживается с помощью PowerShell или другой API (только для портала):
+ [Подключение ресурса cognitive services](cognitive-search-attach-cognitive-services.md) для [насыщенные AI индексирования](cognitive-search-concept-intro.md). Cognitive service присоединяется к знаний и навыков, а не подписки или службы.
+ [Решения для мониторинга надстройки](search-monitor-usage.md#add-on-monitoring-solutions) или [аналитика поискового трафика](search-traffic-analytics.md) используется для наблюдения за службы поиска Azure.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Проверка версии и загрузка модулей

В примерах в этой статье, являются интерактивными и требуются повышенные разрешения. Azure PowerShell ( **Az** модуля) должен быть установлен. Дополнительные сведения см. в статье [Установка Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Проверка версии PowerShell (версии 5.1 или более поздней версии)

Локальной версии PowerShell должен быть версии 5.1 или более поздней версии, в любой поддерживаемой операционной системе.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Загрузить Azure PowerShell

Если вы не уверены ли **Az** установлен, выполните следующую команду в качестве шага проверки. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

В некоторых системах сделать не автоматически загружать модули. Если отобразится сообщение об ошибке предыдущей командой, попробуйте загрузить модуль и если это не помогло, вернитесь на инструкции по установке, чтобы увидеть, если вы пропустили шаг.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Подключение к Azure с помощью браузера маркер входа

Учетные данные портала входа в систему можно использовать для подключения к подписке в PowerShell. Вы также можете [проверки подлинности не в интерактивном режиме с помощью субъекта-службы](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Если удерживать несколько подписок Azure, настройте свою подписку Azure. Чтобы просмотреть список текущих подписок, выполните следующую команду.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Чтобы указать подписку, выполните указанную ниже команду. В приведенном ниже примере имя подписки — `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Список всех служб поиска Azure в подписке

Следующие команды являются из [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), возврат сведений о существующих ресурсов и служб, уже подготовленные в подписке. Если вы не знаете, какое количество служб поиска уже созданы, эти команды возвращают эти сведения, исключив время на портале.

Первая команда возвращает все службы поиска.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Из списка служб возвращают сведения о конкретном ресурсе.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Результаты должны выглядеть аналогично приведенному ниже.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Импортировать Az.Search

Команды из [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) недоступны до загрузки модуля.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Перечислить все команды Az.Search

В качестве шага проверки получения списка команд в модуле.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Результаты должны выглядеть аналогично приведенному ниже.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Получение сведений о службе поиска

После **Az.Search** импортируется и известно, группу ресурсов, содержащую службу поиска, запустите [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) для получения определения службы, включая имя, регион, уровня и реплики и данные о числе секций.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Результаты должны выглядеть аналогично приведенному ниже.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Создание или удаление службы

[**Новый AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) используется [создать новую службу поиска](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Результаты должны выглядеть аналогично приведенному ниже.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Повторное создание ключей администратора

[**Новый AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) используется для смены администратора [ключи API](search-security-api-keys.md). Два ключа администратора создаются с каждой из служб для доступа с проверкой подлинности. Ключи требуются при каждом запросе. Оба ключа администратора функционально эквивалентны, предоставляя полный доступ на запись к службе поиска возможность извлекать любые данные или создавать и удалять любой объект. Таким образом, можно использовать один при замене другой, существует два ключа. 

Можно только создать повторно, по одному как `primary` или `secondary` ключ. Непрерывное обслуживание не забудьте обновить весь код клиента для использования вторичного ключа при смене первичного ключа. Избегайте изменяется ключи, пока операции в процессе передачи.

Как можно догадаться, при повторном создании ключей без обновления кода клиента, запросы, с помощью старого ключа, будут завершаться ошибкой. Повторное создание всех новых ключей не блокирует без возможности восстановления можно эффективно использовать службы и могут по-прежнему обращаться к ней через портал. После повторного создания первичного и вторичного ключей, можно обновить код клиента для использования новых ключей и операции будут возобновлены соответствующим образом.

Значения для ключей API, сформированные службой. Не может предоставить пользовательский ключ для поиска Azure для использования. Аналогичным образом отсутствует имя определяемого пользователем для ключей API администратора. Ссылки на ключ исправленные строки, либо `primary` или `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Результаты должны выглядеть аналогично приведенному ниже. Оба ключа возвращаются, несмотря на то, что только изменения одного за раз.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Создание и удаление ключей запросов

[**Новый AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) используется для создания запроса [ключи API](search-security-api-keys.md) для доступа только для чтения из клиентских приложений в индекс поиска Azure. Ключи запросов используются для проверки подлинности для конкретного индекса для получения результатов поиска. Ключи запросов не предоставляйте доступ только для чтения к другим элементам в службе, например индекс, источник данных или индексатора.

Не удается задать ключ для поиска Azure для использования. Служба создаются ключи API.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Масштабирования реплик и секций

[**SET-AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) используется [увеличить или уменьшить реплик и секций](search-capacity-planning.md) для топологий оплачиваемых ресурсов в службе. Увеличение реплик или секций добавляет на ваш счет, в котором содержатся оба расходов фиксированных и переменных. Если у вас есть временный потребность в дополнительных вычислительных мощностей, можно увеличить реплик и разделов для обработки рабочей нагрузки. Область мониторинга на странице обзора портала имеет плиток на задержки при обработке запросов, запросов в секунду и регулирования, указывающее, является ли текущий объем будет достаточно.

Может занять некоторое время, чтобы добавить или удалить ресурсы. Настройки емкости происходят в фоновом режиме, позволяя существующие рабочие нагрузки продолжить. Дополнительную емкость используется для входящих запросов, как только она готова, с каких-либо дополнительных настроек. 

Удаление емкости может быть может нарушить работу системы. Чтобы избежать потерянных запросов рекомендуется остановки всех заданий индексации и индексатор до снижает емкость. Если это невозможно, можно попробовать постепенно, снижает емкость одного реплик и секций одновременно, достижения нового целевого уровня.

После отправки команды, никак не завершить работу через. Необходимо дождаться завершения команды перед пересмотра счетчики.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Результаты должны выглядеть аналогично приведенному ниже.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```


## <a name="next-steps"></a>Дальнейшие действия

Построение [индекс](search-what-is-an-index.md), [запросы к индексу](search-query-overview.md) с помощью портала, REST API или пакет SDK для .NET.

* [Создание индекса службы "Поиск Azure" на портале Azure](search-create-index-portal.md)
* [Настройка индексатора для загрузки данных из других служб](search-indexer-overview.md)
* [Отправка запросов в индекс службы "Поиск Azure" с использованием обозревателя поиска на портале Azure](search-explorer.md)
* [Как использовать Поиск Azure в приложении .NET](search-howto-dotnet-sdk.md)