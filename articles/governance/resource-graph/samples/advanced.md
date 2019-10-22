---
title: Примеры расширенных запросов
description: С помощью Azure Resource Graph можно выполнять расширенные запросы, например для получения емкости масштабируемого набора виртуальных машин, перечисления всех используемых тегов и сопоставления виртуальных машин с использованием регулярных выражений.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 8b848eac338260340cb9160a72f33294e51101f2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387579"
---
# <a name="advanced-resource-graph-queries"></a>Продвинутые запросы графика ресурсов

Первым шагом на пути к пониманию запросов к Azure Resource Graph является общее понимание [языка запросов](../concepts/query-language.md). Если вы еще не знакомы с [обозревателем данных Azure](../../../data-explorer/data-explorer-overview.md), рекомендуется просмотреть основные сведения, чтобы понять, как составлять запросы к ресурсам, которые вы ищете.

Мы рассмотрим следующие продвинутые запросы:

> [!div class="checklist"]
> - [Получение сведений о емкости и производительности масштабируемого набора виртуальных машин](#vmss-capacity)
> - [Удаление столбцов из результатов](#remove-column)
> - [Вывести список всех названий тегов](#list-all-tags)
> - [Виртуальные машины, сопоставленные по регулярному выражению](#vm-regex)
> - [Список Cosmos DB с конкретным указанием расположений записи](#mvexpand-cosmosdb)
> - [Хранилище ключей с именем подписки](#join)
> - [Список баз данных SQL и их эластичных пулов](#join-sql)
> - [Список виртуальных машин с их сетевым интерфейсом и общедоступным IP-адресом](#join-vmpip)
> - [Поиск учетных записей хранения с указанным тегом в группе ресурсов](#join-findstoragetag)
> - [Объединение результатов из двух запросов в один результат](#unionresults)
> - [Включение имен клиентов и подписок с DisplayNames](#displaynames)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

## <a name="language-support"></a>Поддержка языков

Azure CLI (с помощью расширения) и Azure PowerShell (с помощью модуля) поддерживают Azure Resource Graph. Перед выполнением любого из следующих запросов убедитесь, что среда готова. В разделах об [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) и [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) приведены инструкции по установке и проверке выбранной вами среды оболочки.

## <a name="vmss-capacity"></a>Получение сведений о емкости и производительности масштабируемого набора виртуальных машин

Этот запрос выполняет поиск ресурсов масштабируемого набора виртуальных машин и возвращает разные сведения, включая размер виртуальной машины и емкость масштабируемого набора. В запросе используется функция `toint()` для приведения значения емкости в число для сортировки. Наконец, столбцы переименовываются в пользовательские именованные свойства.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="remove-column"></a>Удаление столбцов из результатов

Следующий запрос использует `summarize` для подсчета ресурсов по подписке, `join` — для их объединения с данными о подписке из таблицы _ResourceContainers_, а затем `project-away` — для удаления некоторых столбцов.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

## <a name="list-all-tags"></a>Вывести список всех названий тегов

Этот запрос начинается с тега и строит объект JSON, перечисляющий все уникальные имена тегов и их соответствующие типы.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Виртуальные машины, сопоставленные по регулярному выражению

Этот запрос выполняет поиск виртуальных машин, которые соответствуют [регулярному выражению](/dotnet/standard/base-types/regular-expression-language-quick-reference) (известному как _регулярное выражение_). **matches regex\@** позволяет определить регулярное выражение для сопоставления, которое равно `^Contoso(.*)[0-9]+$`.
Дополнительные сведения об определении регулярного выражения см. в разделе:

- `^` — Сопоставление должно начаться в начале строки.
- `Contoso` — Строка с учетом регистра.
- `(.*)` — Сопоставление части выражения:
  - `.` — Сопоставляется с любым единичным символом (кроме новой строки).
  - `*` — Сопоставляется с предыдущим элементом ноль или более раз.
- `[0-9]` — Сопоставление группы символ для чисел от 0 до 9.
- `+` — Сопоставляется с предыдущим элементом один или более раз.
- `$` — Сопоставление предыдущего элемента должно находиться в конце строки.

После сопоставления по имени запрос проектирует имя и порядок по возрастанию имени.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="mvexpand-cosmosdb"></a>Список Cosmos DB с конкретным указанием расположений записи

Следующий запрос ограничивает ресурсы Cosmos DB, использует `mv-expand` для расширения пакета свойств для **properties.writeLocations**, а затем проецирует конкретные поля и ограничивает результаты до значения **properties.writeLocations.locationName**, совпадающего с "Восточная часть США" или "Западная часть США".

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

## <a name="join"></a>Хранилище ключей с именем подписки

В следующем запросе показано сложное использование `join`. Запрос ограничивает объединенную таблицу до ресурсов подписки, а `project` включает только исходное поле _subscriptionId_ и поле _name_, переименованное в _SubName_. Переименование полей позволяет избежать `join`, добавляя его как _name1_, так как поле уже существует в _resources_. Исходная таблица фильтруется с `where`, а следующая `project` включает столбцы из обеих таблиц. Результатом запроса является одно хранилище ключей, отображающее тип, имя хранилища ключей и имя подписки, в которой оно находится.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

## <a name="join-sql"></a>Список баз данных SQL и их эластичных пулов

В следующем запросе для объединения ресурсов базы данных SQL и связанных с ними эластичных пулов используется **leftouter** `join` (если такие имеются).

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

## <a name="join-vmpip"></a>Список виртуальных машин с их сетевым интерфейсом и общедоступным IP-адресом

В этом запросе для объединения виртуальных машин, связанных с ними сетевых интерфейсов и любых общедоступных IP-адресов, связанных с этими сетевыми интерфейсами используются две команды **leftouter** `join`.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mvexpand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mvexpand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

```azurecli-interactive
azure graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

## <a name="join-findstoragetag"></a>Поиск учетных записей хранения с указанным тегом в группе ресурсов

Следующий запрос использует **innerunique** `join` для подключения учетных записей хранилища к группам ресурсов с указанными именем и значением тега.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=innerunique (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['key1'] == 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

## <a name="unionresults"></a>Объединение результатов из двух запросов в один результат

Следующий запрос использует `union`, чтобы получить результаты из таблицы _ResourceContainers_ и добавить их в результаты из таблицы _Resources_.

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

## <a name="displaynames"></a>Включение имен клиентов и подписок с DisplayNames

В этом запросе используется новый параметр **Include** со значением _DisplayNames_ для включения **subscriptionDisplayName** и **tenantDisplayName** в результаты. Этот параметр доступен только в Azure CLI и Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Если в запросе не используется **project** для определения возвращаемых свойств, **subscriptionDisplayName** и **tenantDisplayName** автоматически включаются в результаты.
> Если в запросе используется **project**, каждое из полей _DisplayName_ должно быть явно включено в **project**, иначе они не будут включены в результаты, даже при использовании параметра **Include**.

## <a name="next-steps"></a>Дополнительная информация

- См. примеры в разделе [начальных запросов](starter.md)
- Дополнительные сведения о [языке запросов](../concepts/query-language.md)
- Подробнее о [просмотре ресурсов](../concepts/explore-resources.md)