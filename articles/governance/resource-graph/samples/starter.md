---
title: Примеры запросов Starter
description: Составление простых запросов с помощью Azure Resource Graph, в том числе запросов о подсчете ресурсов, упорядочивании ресурсов или их фильтрации по определенному тегу.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 98b05f74f0d6f7d20b5aa7ed77047818f217f147
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691176"
---
# <a name="starter-resource-graph-queries"></a>Запросы к Resource Graph для начинающих

Первым шагом на пути к пониманию запросов к Azure Resource Graph является общее понимание [языка запросов](../concepts/query-language.md). Если вы еще не знакомы с [обозревателем данных Azure](../../../data-explorer/data-explorer-overview.md), рекомендуется просмотреть основные сведения, чтобы понять, как составлять запросы к ресурсам, которые вы ищете.

Мы рассмотрим следующие запросы для начинающих:

> [!div class="checklist"]
> - [Подсчет ресурсов Azure](#count-resources)
> - [Вывод списка ресурсов, отсортированных по имени](#list-resources)
> - [Отображение всех виртуальных машин, упорядоченных по имени в порядке убывания](#show-vms)
> - [Отображение первых пяти виртуальных машин по имени и типу ОС](#show-sorted)
> - [Подсчет виртуальных машин по типу ОС](#count-os)
> - [Отображение ресурсов, которые содержат хранилище](#show-storage)
> - [Вывод списка общедоступных IP-адресов](#list-publicip)
> - [Подсчет ресурсов с IP-адресами, настроенными посредством подписки](#count-resources-by-ip)
> - [Вывод списка ресурсов с определенным значением тега](#list-tag)
> - [Вывод списка всех учетных записей хранения с определенным значением тега](#list-specific-tag)
> - [Отображение псевдонимов для ресурса виртуальной машины](#show-aliases)
> - [Отображение уникальных значений для конкретного псевдонима](#distinct-alias-values)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Поддержка языков

Azure CLI (с помощью расширения) и Azure PowerShell (с помощью модуля) поддерживают Azure Resource Graph. Перед выполнением любого из следующих запросов убедитесь, что среда готова. В разделах об [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) и [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) приведены инструкции по установке и проверке выбранной вами среды оболочки.

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Подсчет ресурсов Azure

Этот запрос возвращает количество ресурсов Azure в доступных вам подписках. Этот запрос также удобно использовать, чтобы проверить, установлены ли в выбранной оболочке соответствующие компоненты Azure Resource Graph и работают ли они.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>Вывод списка ресурсов, отсортированных по имени

Этот запрос возвращает любой тип ресурса, но только свойства **name**, **type** и **location**. Он использует выражение `order by`, чтобы сортировать свойства по свойству **name** в порядке возрастания (`asc`).

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Отображение всех виртуальных машин, упорядоченных по имени в порядке убывания

Чтобы вывести только список виртуальных машин (относящихся к типу `Microsoft.Compute/virtualMachines`), в результатах можно применить сопоставление по свойству **type**. Аналогично предыдущему запросу, `desc` изменяет `order by` для сортировки по убыванию. `=~` в сопоставлении типа указывает Resource Graph не учитывать регистр.

```kusto
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>Отображение первых пяти виртуальных машин по имени и типу ОС

Этот запрос будет использовать `limit` для извлечения только пяти соответствующих записей, упорядоченных по имени. Типом ресурса Azure является `Microsoft.Compute/virtualMachines`. `project` указывает Azure Resource Graph, какие свойства следует включить.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Подсчет виртуальных машин по типу ОС

Основываясь на предыдущем запросе, мы по-прежнему ограничиваем результаты типом ресурса Azure `Microsoft.Compute/virtualMachines`, но больше не ограничиваем количество возвращаемых записей.
Вместо этого мы использовали `summarize` и `count()`, чтобы определить способ группирования и объединения значений по свойству (`properties.storageProfile.osDisk.osType` в этом примере). Чтобы получить представление о том, как эта строка выглядит в полном объекте, ознакомьтесь с [просмотром ресурсов и обнаружением виртуальных машин](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Этот запрос можно составить по-другому. Можно расширить (`extend`) свойство и присвоить ему временное имя для использования в запросе, в этом случае — **os**. Затем **os** используется для `summarize` и `count()`, как в предыдущем примере.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Имейте в виду, что, хотя `=~` позволяет сопоставление без учета регистра, использование свойств (таких как **properties.storageProfile.osDisk.osType**) в запросе требует соблюдения регистра. Если свойство указано в неправильном регистре, значение по-прежнему может возвращаться, но группирование или сведение данных будет выполняться неправильно.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>Отображение ресурсов, которые содержит хранилище

Вместо явного сопоставления определенного типа этот пример запроса будет искать все ресурсы Azure, содержащие (`contains`) слово **storage**.

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>Вывод списка общедоступных IP-адресов

Аналогично предыдущему примеру этот запрос ищет все ресурсы, тип которых содержит слово **publicIPAddresses**.
Этот запрос расширяет данный шаблон, чтобы включить в результаты только ресурсы, у которых **properties.ipAddress**
 имеет значение `isnotempty`, и возвращать только ресурсы с действительным значением **properties.ipAddress**. Кроме того, число результатов ограничено (`limit`) первыми
100. В зависимости от выбранной оболочки может потребоваться экранировать кавычки.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>Подсчет ресурсов с IP-адресами, настроенными посредством подписки

Используя предыдущий пример запроса и добавив `summarize` и `count()`, мы можем получить список ресурсов с настроенными IP-адресами для подписки.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>Вывод списка ресурсов с определенным значением тега

Мы можем ограничить результаты не только по типу ресурса Azure, а, например, по тегу. В этом примере мы применяем фильтр для получения ресурсов Azure с тегом **Environment** со значением **Internal**.

```kusto
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Чтобы указать теги ресурсов и их значения, добавьте свойство **tags** для ключевого слова `project`.

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>Вывод списка всех учетных записей хранения с определенным значением тега

Объедините функции фильтра в предыдущем примере и фильтрацию по типу ресурса Azure с помощью свойства **type**. Этот запрос также ограничивает поиск определенных типов ресурсов Azure за счет использования конкретного имени и значения тега.

```kusto
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> В этом примере для сопоставления используется условный оператор `==` вместо `=~`. При использовании `==` учитывается регистр.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>Отображение псевдонимов для ресурса виртуальной машины

[Псевдонимы Политики Azure](../../policy/concepts/definition-structure.md#aliases) используются Политикой Azure для управления соответствием ресурсов. Azure Resource Graph может возвращать _псевдонимы_ типа ресурса. Эти значения можно использовать для сравнения с текущим значением псевдонимов при создании пользовательского определения политики. Массив _псевдонимов_ не предоставляется по умолчанию в результатах запроса. Используйте `project aliases`, чтобы явным способом добавить его к результатам.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>Отображение уникальных значений для конкретного псевдонима

Отображение значения псевдонимов на один ресурс является полезным, но оно не показывает значение true при использовании Azure Resource Graph для запроса между подписками. В этом примере рассматриваются все значения конкретного псевдонима и возвращаются уникальные значения.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о [языке запросов](../concepts/query-language.md)
- Узнайте больше о [просмотре ресурсов](../concepts/explore-resources.md).
- Изучите примеры в разделе [Усложненные запросы](advanced.md).