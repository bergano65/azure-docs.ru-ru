---
title: Сценарии PowerShell с помощью команды AZ. Search
titleSuffix: Azure Cognitive Search
description: Создание и Настройка службы Когнитивный поиск Azure с помощью PowerShell. Вы можете масштабировать службу по мере увеличения или уменьшения масштаба, управлять ключами API администрирования и запросов, а также запрашивать сведения о системе.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 9f189d1889f3ca3a3aa3234432452b1b3d696c04
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935098"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Управление службой Когнитивный поиск Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [Пакет SDK для .NET](/dotnet/api/microsoft.azure.management.search)
> * [Языке](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Вы можете запускать командлеты и скрипты PowerShell в Windows, Linux или в [Azure Cloud Shell](../cloud-shell/overview.md) для создания и настройки когнитивный Поиск Azure. Модуль **AZ. Search** расширяет [Azure PowerShell](/powershell/) с полным контролем четности к [API-интерфейсам управления поиском](/rest/api/searchmanagement) и позволяет выполнять следующие задачи:

> [!div class="checklist"]
> * [Перечисление служб поиска в подписке](#list-search-services)
> * [Возврат сведений о службе](#get-search-service-information)
> * [Создание или удаление службы](#create-or-delete-a-service)
> * [Повторное создание ключей API администратора](#regenerate-admin-keys)
> * [Создание или удаление ключей API запроса](#create-or-delete-query-keys)
> * [Увеличение или уменьшение масштаба с помощью реплик и секций](#scale-replicas-and-partitions)

Иногда вопросы задаются *не* в приведенном выше списке. В настоящее время нельзя использовать модуль **AZ. Search** или REST API управления для изменения имени сервера, региона или уровня. Выделенные ресурсы выделяются при создании службы. Таким образом, для изменения базового оборудования (расположения или типа узла) требуется новая служба. Аналогично, отсутствуют средства или интерфейсы API для передачи содержимого, например индекса, из одной службы в другую.

В рамках службы создание и управление содержимым осуществляется с помощью [Служба поиска REST API](/rest/api/searchservice/) или [пакета SDK для .NET](/dotnet/api/?term=microsoft.azure.search). Хотя для содержимого нет выделенных команд PowerShell, можно написать сценарий PowerShell, который вызывает API-интерфейсы RESTFUL или .NET для создания и загрузки индексов.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Проверка версий и модулей загрузки

Примеры в этой статье являются интерактивными и нуждаются в повышенных правах. Необходимо установить Azure PowerShell (модуль **AZ** ). Дополнительные сведения см. в статье [Установка Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>Проверка версии PowerShell (5,1 или более поздней версии)

Локальное PowerShell должно быть 5,1 или более поздней версии в любой поддерживаемой операционной системе.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Azure PowerShell загрузки

Если вы не уверены, установлено ли **AZ** , выполните следующую команду в качестве шага проверки. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Некоторые системы не выполняют автоматическую загрузку модулей. Если при выполнении предыдущей команды возникнет ошибка, попробуйте загрузить модуль. Если это не удается, вернитесь к инструкциям по установке, чтобы узнать, пропущен ли шаг.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Подключение к Azure с помощью токена входа в браузере

Для подключения к подписке в PowerShell можно использовать учетные данные для входа на портал. Кроме того, можно [выполнять проверку подлинности в неинтерактивном режиме с помощью субъекта-службы](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Если вы удерживаете несколько подписок Azure, задайте свою подписку Azure. Чтобы просмотреть список текущих подписок, выполните следующую команду.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Чтобы указать подписку, выполните указанную ниже команду. В приведенном ниже примере имя подписки — `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Перечисление служб в подписке

Следующие команды относятся к [**AZ. Resources**](/powershell/module/az.resources/?view=azps-1.4.0#resources)и возвращают сведения о существующих ресурсах и службах, уже подготовленных в вашей подписке. Если вы не уверены, сколько служб поиска уже создано, эти команды возвращают эту информацию, экономя на портале.

Первая команда возвращает все службы поиска.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

В списке служб верните сведения о конкретном ресурсе.

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

## <a name="import-azsearch"></a>Импорт AZ. Search

Команды из [**AZ. Search**](/powershell/module/az.search/?view=azps-1.4.0#search) недоступны, пока не будет загружен модуль.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Вывод списка всех команд AZ. Search

На этапе проверки возвращается список команд, предоставленных в модуле.

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

## <a name="get-search-service-information"></a>Получение сведений о службе поиска

После команды **AZ. Search** импортируется, и вы узнаете о группе ресурсов, содержащей службу поиска, выполните командлет [Get-азсеарчсервице](/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) , чтобы получить определение службы, включая имя, регион, уровень, реплику и число разделов.

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

[**New-азсеарчсервице**](/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) используется для [создания новой службы поиска](search-create-service-portal.md).

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

[**New-азсеарчадминкэй**](/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) используется для отката [ключей API](search-security-api-keys.md)администратора. Для каждой службы создаются два ключа администратора для доступа с проверкой подлинности. Для каждого запроса требуются ключи. Оба ключа администратора функционально эквивалентны, они предоставляют полный доступ для записи службе поиска с возможностью получения любых сведений, а также создания и удаления любого объекта. Существует два ключа, которые можно использовать при замене другого. 

Его можно создать только по одному, указанному в качестве `primary` `secondary` ключа или. При использовании непрерывной службы не забудьте обновить весь код клиента для использования вторичного ключа при последовательном переходе по первичному ключу. Старайтесь не менять ключи, пока операции находятся в полете.

Как вы можете ожидать, при повторном создании ключей без обновления клиентского кода запросы, использующие старый ключ, завершатся ошибкой. Повторное создание всех новых ключей не приводит к окончательной блокировке вашей службы, и вы по-прежнему можете обращаться к службе через портал. После повторного создания первичного и вторичного ключей можно обновить код клиента, чтобы использовать новые ключи, и операции будут возобновлены соответствующим образом.

Значения ключей API создаются службой. Вы не можете предоставить настраиваемый ключ для Azure Когнитивный поиск. Точно так же не существует определяемого пользователем имени для ключей API администратора. Ссылки на ключ являются фиксированными строками: `primary` или `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Результаты должны выглядеть следующим образом. Возвращаются оба ключа, несмотря на то, что вы изменяете их только один раз.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Создание или удаление ключей запроса

[**New-азсеарчкуерикэй**](/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) используется для создания [ключей API](search-security-api-keys.md) запросов для доступа только для чтения из клиентских приложений к индексу Azure когнитивный Поиск. Ключи запроса используются для проверки подлинности определенного индекса с целью получения результатов поиска. Ключи запроса не предоставляют доступ только для чтения к другим элементам службы, таким как индекс, источник данных или индексатор.

Вы не можете предоставить ключ для использования Когнитивный поиск Azure. Ключи API создаются службой.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Масштабирование реплик и секций

[**Set-азсеарчсервице**](/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) используется для [увеличения или уменьшения количества реплик и секций](search-capacity-planning.md) , чтобы перенастроить оплачиваемые ресурсы в службе. При увеличении количества реплик или секций добавляется к счету, у которого есть фиксированная и переменная плата. При наличии временной потребности в дополнительной вычислительной мощности можно увеличить реплики и секции для обработки рабочей нагрузки. В области мониторинг на странице Обзор портала находятся плитки о задержке запросов, запросах в секунду и регулированиях, указывающих, достаточно ли текущих мощностей.

Добавление или удаление перебора может занять некоторое время. Корректировки производительности выполняются в фоновом режиме, что позволяет продолжать работу существующих рабочих нагрузок. Дополнительная емкость используется для входящих запросов, как только она будет готова, без необходимости дополнительной настройки. 

Удаление емкости может нарушить работу. Остановка всех заданий индексирования и индексирования перед уменьшением емкости рекомендуется, чтобы избежать отброшенных запросов. Если это нецелесообразно, можно потребовать более постепенного снижения емкости по одной реплике и секции, пока не будут достигнуты новые целевые уровни.

После отправки команды нет возможности завершить ее с самого посредине. Перед изменением счетчиков необходимо будет дождаться завершения команды.

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

Создайте [индекс](search-what-is-an-index.md), [запросите индекс](search-query-overview.md) с помощью портала, API-интерфейсов и пакета SDK для .NET.

* [Создание индекса службы "Когнитивный поиск Azure" на портале Azure](search-get-started-portal.md)
* [Настройка индексатора для загрузки данных из других служб](search-indexer-overview.md)
* [Запрос индекса Когнитивный поиск Azure с помощью обозревателя поиска в портал Azure](search-explorer.md)
* [Использование Когнитивный поиск Azure в .NET](search-howto-dotnet-sdk.md)