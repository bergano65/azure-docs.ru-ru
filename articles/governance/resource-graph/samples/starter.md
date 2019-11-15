---
title: Примеры запросов Starter
description: Составление простых запросов с помощью Azure Resource Graph, в том числе запросов о подсчете ресурсов, упорядочивании ресурсов или их фильтрации по определенному тегу.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/21/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 7eef353f45d0387a45fbf8180bc49cae7975dfa9
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622504"
---
# <a name="starter-resource-graph-queries"></a>Запросы к Resource Graph для начинающих

Первым шагом на пути к пониманию запросов к Azure Resource Graph является общее понимание [языка запросов](../concepts/query-language.md). Если вы еще не знакомы с [языком запросов Kusto (KQL)](/azure/kusto/query/index), советуем просмотреть [учебник по KQL](/azure/kusto/query/tutorial), чтобы понять, как составлять запросы к ресурсам, которые вы ищете.

Мы рассмотрим следующие запросы для начинающих:

> [!div class="checklist"]
> - [Подсчет ресурсов Azure](#count-resources)
> - [Подсчет ресурсов хранилища ключей](#count-keyvaults)
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
> - [Отображение несвязанных групп безопасности сети](#unassociated-nsgs)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

## <a name="language-support"></a>Поддержка языков

Azure CLI (с помощью расширения) и Azure PowerShell (с помощью модуля) поддерживают Azure Resource Graph. Перед выполнением любого из следующих запросов убедитесь, что среда готова. В разделах об [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) и [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) приведены инструкции по установке и проверке выбранной вами среды оболочки.

## <a name="a-namecount-resources-count-azure-resources"></a><a name="count-resources" />Подсчет ресурсов Azure

Этот запрос возвращает количество ресурсов Azure в доступных вам подписках. Этот запрос также удобно использовать, чтобы проверить, установлены ли в выбранной оболочке соответствующие компоненты Azure Resource Graph и работают ли они.

```kusto
Resources
| summarize count()
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-namecount-keyvaults-count-key-vault-resources"></a><a name="count-keyvaults" />Подсчет ресурсов хранилища ключей

Для подсчета количества возвращаемых записей в этом запросе используется `count` вместо `summarize`. В подсчет включаются только хранилища ключей.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-namelist-resources-list-resources-sorted-by-name"></a><a name="list-resources" />Вывод списка ресурсов, отсортированных по имени

Этот запрос возвращает любой тип ресурса, но только свойства **name**, **type** и **location**. Он использует выражение `order by`, чтобы сортировать свойства по свойству **name** в порядке возрастания (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-nameshow-vms-show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />Отображение всех виртуальных машин, упорядоченных по имени в порядке убывания

Чтобы вывести только список виртуальных машин (относящихся к типу `Microsoft.Compute/virtualMachines`), в результатах можно применить сопоставление по свойству **type**. Аналогично предыдущему запросу, `desc` изменяет `order by` для сортировки по убыванию. `=~` в сопоставлении типа указывает Resource Graph не учитывать регистр.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-nameshow-sorted-show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />Отображение первых пяти виртуальных машин по имени и типу ОС

Этот запрос будет использовать `top` для извлечения только пяти соответствующих записей, упорядоченных по имени. Типом ресурса Azure является `Microsoft.Compute/virtualMachines`. `project` указывает Azure Resource Graph, какие свойства следует включить.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-namecount-os-count-virtual-machines-by-os-type"></a><a name="count-os" />Подсчет виртуальных машин по типу ОС

Основываясь на предыдущем запросе, мы по-прежнему ограничиваем результаты типом ресурса Azure `Microsoft.Compute/virtualMachines`, но больше не ограничиваем количество возвращаемых записей.
Вместо этого мы использовали `summarize` и `count()`, чтобы определить способ группирования и объединения значений по свойству (`properties.storageProfile.osDisk.osType` в этом примере). Чтобы получить представление о том, как эта строка выглядит в полном объекте, ознакомьтесь с [просмотром ресурсов и обнаружением виртуальных машин](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

Этот запрос можно составить по-другому. Можно расширить (`extend`) свойство и присвоить ему временное имя для использования в запросе, в этом случае — **os**. Затем **os** используется для `summarize` и `count()`, как в предыдущем примере.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

> [!NOTE]
> Имейте в виду, что, хотя `=~` позволяет сопоставление без учета регистра, использование свойств (таких как **properties.storageProfile.osDisk.osType**) в запросе требует соблюдения регистра. Если свойство указано в неправильном регистре, значение по-прежнему может возвращаться, но группирование или сведение данных будет выполняться неправильно.

## <a name="a-nameshow-storage-show-resources-that-contain-storage"></a><a name="show-storage" />Отображение ресурсов, которые содержит хранилище

Вместо явного сопоставления определенного типа этот пример запроса будет искать все ресурсы Azure, содержащие (`contains`) слово **storage**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-namelist-publicip-list-all-public-ip-addresses"></a><a name="list-publicip" />Вывод списка общедоступных IP-адресов

Аналогично предыдущему примеру этот запрос ищет все ресурсы, тип которых содержит слово **publicIPAddresses**.
Этот запрос расширяет данный шаблон, чтобы включить в результаты только ресурсы, у которых **properties.ipAddress**
 имеет значение `isnotempty`, и возвращать только ресурсы с действительным значением **properties.ipAddress**. Кроме того, число результатов ограничено (`limit`) первыми
100. В зависимости от выбранной оболочки может потребоваться экранировать кавычки.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-namecount-resources-by-ip-count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />Подсчет ресурсов с IP-адресами, настроенными посредством подписки

Используя предыдущий пример запроса и добавив `summarize` и `count()`, мы можем получить список ресурсов с настроенными IP-адресами для подписки.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-namelist-tag-list-resources-with-a-specific-tag-value"></a><a name="list-tag" />Вывод списка ресурсов с определенным значением тега

Мы можем ограничить результаты не только по типу ресурса Azure, а, например, по тегу. В этом примере мы применяем фильтр для получения ресурсов Azure с тегом **Environment** со значением **Internal**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

Чтобы указать теги ресурсов и их значения, добавьте свойство **tags** для ключевого слова `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-namelist-specific-tag-list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />Вывод списка всех учетных записей хранения с определенным значением тега

Объедините функции фильтра в предыдущем примере и фильтрацию по типу ресурса Azure с помощью свойства **type**. Этот запрос также ограничивает поиск определенных типов ресурсов Azure за счет использования конкретного имени и значения тега.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

> [!NOTE]
> В этом примере для сопоставления используется условный оператор `==` вместо `=~`. При использовании `==` учитывается регистр.

## <a name="a-nameshow-aliases-show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />Отображение псевдонимов для ресурса виртуальной машины

[Псевдонимы Политики Azure](../../policy/concepts/definition-structure.md#aliases) используются Политикой Azure для управления соответствием ресурсов. Azure Resource Graph может возвращать _псевдонимы_ типа ресурса. Эти значения можно использовать для сравнения с текущим значением псевдонимов при создании пользовательского определения политики. Массив _псевдонимов_ не предоставляется по умолчанию в результатах запроса. Используйте `project aliases`, чтобы явным способом добавить его к результатам.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-namedistinct-alias-values-show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />Отображение уникальных значений для конкретного псевдонима

Отображение значения псевдонимов на один ресурс является полезным, но оно не показывает значение true при использовании Azure Resource Graph для запроса между подписками. В этом примере рассматриваются все значения конкретного псевдонима и возвращаются уникальные значения.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="a-nameunassociated-nsgs-show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />Отображение несвязанных групп безопасности сети

Этот запрос возвращает группы безопасности сети (NSG), которые не связаны с сетевым интерфейсом или подсетью.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о [языке запросов](../concepts/query-language.md).
- Узнайте больше о том, как [изучать ресурсы](../concepts/explore-resources.md).
- Изучите примеры [усложненных запросов](advanced.md).