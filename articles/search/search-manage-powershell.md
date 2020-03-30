---
title: Скрипты PowerShell с помощью модуля Az.Search
titleSuffix: Azure Cognitive Search
description: Создайте и настройте службу когнитивного поиска Azure с помощью PowerShell. Вы можете масштабировать службу вверх или вниз, управлять админ-и запросом api-ключей, а также запросом для системной информации.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209302"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Управление службой когнитивного поиска Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Для создания и настройки Azure Cognitive Search можно запустить cmdlets и скрипты PowerShell на Windows, Linux или в [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview) Модуль **Az.Search** расширяет [Azure PowerShell](https://docs.microsoft.com/powershell/) с полным паритетом до [AAИ управления поиском REST](https://docs.microsoft.com/rest/api/searchmanagement) и возможностью выполнять следующие задачи:

> [!div class="checklist"]
> * [Список поисковых услуг в подписке](#list-search-services)
> * [Информация об услуге возврата](#get-search-service-information)
> * [Создание или удаление службы](#create-or-delete-a-service)
> * [Регенерировать API-ключи admin](#regenerate-admin-keys)
> * [Создание или удаление api-ключей запроса](#create-or-delete-query-keys)
> * [Масштабирование вверх или вниз с помощью реплик и перегородок](#scale-replicas-and-partitions)

Иногда задаются вопросы о *задачах, не* всданных вышеуказанного списка. В настоящее время нельзя использовать модуль **Az.Search** или aPI управления REST для изменения имени сервера, региона или уровня. Выделенные ресурсы выделяются при создании службы. Таким образом, изменение базового оборудования (типа местоположения или узла) требует новой службы. Аналогичным образом, нет инструментов или AA для передачи содержимого, например индекса, из одной службы в другую.

В рамках службы создание контента и управление контентом происходит через [поисковую службу REST API](https://docs.microsoft.com/rest/api/searchservice/) или [.NET SDK.](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) Хотя для содержимого нет специальных команд PowerShell, можно написать скрипт PowerShell, который вызывает REST или .NET AIS для создания и загрузки индексов.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Проверка версий и модулей загрузки

Примеры в этой статье являются интерактивными и требуют повышенных разрешений. Azure PowerShell (модуль **Az)** должен быть установлен. Для получения дополнительной информации [см.](/powershell/azure/overview)

### <a name="powershell-version-check-51-or-later"></a>Проверка версии PowerShell (5.1 или более поздней версии)

Местная PowerShell должна быть 5.1 или более поздней, на любой поддерживаемой операционной системе.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Нагрузка Azure PowerShell

Если вы не уверены, установлен ли **Az,** запустите следующую команду в качестве шага проверки. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Некоторые системы не загружают модули автоматической загрузки. Если вы получили ошибку в предыдущей команде, попробуйте загрузить модуль, и если это не удается, вернитесь к инструкциям по установке, чтобы увидеть, если вы пропустили шаг.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Подключение к Azure с помощью маркера ввески браузера

Для подключения к подписке в PowerShell можно использовать учетные данные портала. Кроме того, вы можете [проверить подлинность неинтерактивно с помощью основного сервиса.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

```azurepowershell-interactive
Connect-AzAccount
```

Если у вас есть несколько подписок Azure, установите подписку Azure. Чтобы просмотреть список текущих подписок, выполните следующую команду.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Чтобы указать подписку, выполните указанную ниже команду. В приведенном ниже примере имя подписки — `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Список услуг по подписке

Следующие команды от [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), возвращая информацию о существующих ресурсов и услуг, уже подготовленных в вашей подписке. Если вы не знаете, сколько поисковых служб уже создано, эти команды возвращают эту информацию, сохраняя вам поездку на портал.

Первая команда возвращает все поисковые службы.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Из списка услуг верните информацию о конкретном ресурсе.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Результаты должны выглядеть следующим образом.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Импорт Az.Search

Команды [**от Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) недоступны до загрузки модуля.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Перечислите все команды Az.Search

В качестве шага проверки верните список команд, представленных в модуле.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Результаты должны выглядеть следующим образом.

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

## <a name="get-search-service-information"></a>Получить информацию об поисковых службах

После импорта **Az.Search** и получения данных о группе ресурсов, содержащей службу поиска, запустите [Get-AzSearchService,](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) чтобы вернуть определение службы, включая количество имен, регионов, уровней и реплик и разделов.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Результаты должны выглядеть следующим образом.

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

[**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) используется для [создания нового поискового сервиса.](search-create-service-portal.md)

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Результаты должны выглядеть следующим образом.

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

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) используется для пролонгивания [ключей API-aPI-](search-security-api-keys.md)admin. Для получения подлинного доступа создаются два ключа от админ- с каждой службой. Ключи требуются по каждому запросу. Оба клавиши админ функционально эквивалентны, предоставляя полный доступ к поисковой службе с возможностью получения любой информации или создания и удаления любого объекта. Существуют два ключа, так что вы можете использовать один при замене другого. 

Вы можете регенерировать только один за один `primary` `secondary` раз, указанный как ключ или ключ. Для непрерывного обслуживания не забудьте обновить весь клиентский код, чтобы использовать вторичный ключ при переворачивании основного ключа. Избегайте смены ключей во время операции в полете.

Как и следовало ожидать, если вы регенерируете ключи без обновления клиентского кода, запросы с использованием старого ключа не сбудутся. Восстановление всех новых ключей не блокирует вас навсегда, и вы все равно можете получить доступ к сервису через портал. После регенерации первичных и вторичных ключей можно обновить клиентский код, чтобы использовать новые клавиши, и операции возобновятся соответствующим образом.

Значения для ключей API генерируются службой. Вы не можете предоставить пользовательский ключ для использования Azure Cognitive Search. Аналогичным образом, не существует определенного пользователем имени для Admin API-ключей. Ссылки на ключ являются фиксированными `primary` `secondary`строками, либо . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Результаты должны выглядеть следующим образом. Оба ключа возвращаются, даже если вы меняете только по одному.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Создание или удаление ключей запроса

[**New-AzSearch'eryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) используется для создания [ключей API](search-security-api-keys.md) запроса для доступа только для чтения из клиентских приложений в индекс когнитивного поиска Azure. Ключи запроса используются для проверки подлинности определенного индекса с целью получения результатов поиска. Ключи запроса не предоставляют доступ только для чтения к другим элементам службы, таким как индекс, источник данных или индексер.

Вы не можете предоставить ключ для использования Azure Cognitive Search. Ключи API генерируются службой.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Масштаб реплики и разделы

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) используется для [увеличения или уменьшения реплик и разделов](search-capacity-planning.md) для считывания платежных ресурсов в вашей службе. Увеличение реплик или разделов добавляет к вашему счету, который имеет как фиксированные, так и переменные заряды. Если у вас есть временная потребность в дополнительной вычислительной мощности, можно увеличить реплики и разделы для обработки рабочей нагрузки. Область мониторинга на странице портала «Обзор» содержит плитки по задержке запроса, запросам в секунду и дросселю, указывающие на то, является ли текущая емкость адекватной.

Это может занять некоторое время, чтобы добавить или удалить ресурсы. Корректировки емкости происходят в фоновом режиме, что позволяет продолжать работу существующих рабочих нагрузок. Дополнительная емкость используется для входящих запросов, как только она будет готова, без дополнительной конфигурации. 

Удаление емкости может быть разрушительным. Для того чтобы избежать отброшенных запросов, рекомендуется прекратить все задания индексирования и индексирования до сокращения емкости. Если это невозможно, можно рассмотреть возможность постепенного сокращения емкости, одной реплики и раздела за раз, пока не будут достигнуты новые целевые уровни.

Как только вы отправите команду, нет никакого способа, чтобы завершить его в середине. Вам придется подождать, пока команда будет закончена, прежде чем пересмотреть счеты.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Результаты должны выглядеть следующим образом.

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

Создайте [индекс,](search-what-is-an-index.md) [задавайте запрос на индекс](search-query-overview.md) с помощью портала, АПО REST или .NET SDK.

* [Создание индекса службы "Когнитивный поиск Azure" на портале Azure](search-create-index-portal.md)
* [Настройка индекса для загрузки данных из других служб](search-indexer-overview.md)
* [Запрос индекса когнитивного поиска Azure с помощью поиска на портале Azure](search-explorer.md)
* [Как использовать Azure Cognitive Search в .NET](search-howto-dotnet-sdk.md)