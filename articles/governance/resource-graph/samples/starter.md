---
title: Примеры запросов Starter
description: Составление простых запросов с помощью Azure Resource Graph, в том числе запросов о подсчете ресурсов, упорядочивании ресурсов или их фильтрации по определенному тегу.
ms.date: 06/01/2020
ms.topic: sample
ms.openlocfilehash: db3c866a52351b9cb18f91970780d9ae72046e0a
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84231055"
---
# <a name="starter-resource-graph-query-samples"></a>Примеры запросов к Resource Graph для начинающих

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
> - [Получение сводки по сокращению затрат из Помощника по Azure](#advisor-savings)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free), прежде чем начинать работу.

## <a name="language-support"></a>Поддержка языков

Azure CLI (с помощью расширения) и Azure PowerShell (с помощью модуля) поддерживают Azure Resource Graph. Перед выполнением любого из следующих запросов убедитесь, что среда готова. В разделах об [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) и [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) приведены инструкции по установке и проверке выбранной вами среды оболочки.

## <a name="count-azure-resources"></a><a name="count-resources"></a>Подсчет ресурсов Azure

Этот запрос возвращает количество ресурсов Azure в доступных вам подписках. Этот запрос также удобно использовать, чтобы проверить, установлены ли в выбранной оболочке соответствующие компоненты Azure Resource Graph и работают ли они.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults"></a>Подсчет ресурсов хранилища ключей

Для подсчета количества возвращаемых записей в этом запросе используется `count` вместо `summarize`. В подсчет включаются только хранилища ключей.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources"></a>Вывод списка ресурсов, отсортированных по имени

Этот запрос возвращает любой тип ресурса, но только свойства **name**, **type** и **location**. Он использует выражение `order by`, чтобы сортировать свойства по свойству **name** в порядке возрастания (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"></a>Отображение всех виртуальных машин, упорядоченных по имени в порядке убывания

Чтобы вывести только список виртуальных машин (относящихся к типу `Microsoft.Compute/virtualMachines`), в результатах можно применить сопоставление по свойству **type**. Аналогично предыдущему запросу, `desc` изменяет `order by` для сортировки по убыванию. `=~` в сопоставлении типа указывает Resource Graph не учитывать регистр.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"></a>Отображение первых пяти виртуальных машин по имени и типу ОС

Этот запрос будет использовать `top` для извлечения только пяти соответствующих записей, упорядоченных по имени. Типом ресурса Azure является `Microsoft.Compute/virtualMachines`. `project` указывает Azure Resource Graph, какие свойства следует включить.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os"></a>Подсчет виртуальных машин по типу ОС

Основываясь на предыдущем запросе, мы по-прежнему ограничиваем результаты типом ресурса Azure `Microsoft.Compute/virtualMachines`, но больше не ограничиваем количество возвращаемых записей.
Вместо этого мы использовали `summarize` и `count()`, чтобы определить способ группирования и объединения значений по свойству (`properties.storageProfile.osDisk.osType` в этом примере). Чтобы получить представление о том, как эта строка выглядит в полном объекте, ознакомьтесь с [просмотром ресурсов и обнаружением виртуальных машин](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

Этот запрос можно составить по-другому. Можно расширить (`extend`) свойство и присвоить ему временное имя для использования в запросе, в этом случае — **os**. Затем **os** используется для `summarize` и `count()`, как в предыдущем примере.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

> [!NOTE]
> Имейте в виду, что, хотя `=~` позволяет сопоставление без учета регистра, использование свойств (таких как **properties.storageProfile.osDisk.osType**) в запросе требует соблюдения регистра. Если свойство указано в неправильном регистре, возвращается значение NULL или неверное значение. Группирование или сведение данных будет выполняться неправильно.

## <a name="show-resources-that-contain-storage"></a><a name="show-storage"></a>Отображение ресурсов, которые содержат хранилище

Вместо явного сопоставления определенного типа этот пример запроса будет искать все ресурсы Azure, содержащие (`contains`) слово **storage**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip"></a>Вывод списка общедоступных IP-адресов

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"></a>Подсчет ресурсов с IP-адресами, настроенными посредством подписки

Используя предыдущий пример запроса и добавив `summarize` и `count()`, мы можем получить список ресурсов с настроенными IP-адресами для подписки.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag"></a>Вывод списка ресурсов с определенным значением тега

Мы можем ограничить результаты не только по типу ресурса Azure, а, например, по тегу. В этом примере мы применяем фильтр для получения ресурсов Azure с тегом **Environment** со значением **Internal**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

Чтобы указать теги ресурсов и их значения, добавьте свойство **tags** для ключевого слова `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"></a>Вывод списка всех учетных записей хранения с определенным значением тега

Объедините функции фильтра в предыдущем примере и фильтрацию по типу ресурса Azure с помощью свойства **type**. Этот запрос также ограничивает поиск определенных типов ресурсов Azure за счет использования конкретного имени и значения тега.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

> [!NOTE]
> В этом примере для сопоставления используется условный оператор `==` вместо `=~`. При использовании `==` учитывается регистр.

## <a name="show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"></a>Отображение псевдонимов для ресурса виртуальной машины

[Псевдонимы Политики Azure](../../policy/concepts/definition-structure.md#aliases) используются Политикой Azure для управления соответствием ресурсов. Azure Resource Graph может возвращать _псевдонимы_ типа ресурса. Эти значения можно использовать для сравнения с текущим значением псевдонимов при создании пользовательского определения политики. Массив _псевдонимов_ не предоставляется по умолчанию в результатах запроса. Используйте `project aliases`, чтобы явным способом добавить его к результатам.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20limit%201%0D%0A%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20limit%201%0D%0A%7C%20project%20aliases" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20limit%201%0D%0A%7C%20project%20aliases" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"></a>Отображение уникальных значений для конкретного псевдонима

Отображение значения псевдонимов на один ресурс является полезным, но оно не показывает значение true при использовании Azure Resource Graph для запроса между подписками. В этом примере рассматриваются все значения конкретного псевдонима и возвращаются уникальные значения.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20alias%20%3D%20aliases%5B%27Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType%27%5D%0D%0A%7C%20distinct%20tostring%28alias%29" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20alias%20%3D%20aliases%5B%27Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType%27%5D%0D%0A%7C%20distinct%20tostring%28alias%29" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20alias%20%3D%20aliases%5B%27Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType%27%5D%0D%0A%7C%20distinct%20tostring%28alias%29" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs"></a>Отображение несвязанных групп безопасности сети

Этот запрос возвращает группы безопасности сети (NSG), которые не связаны с сетевым интерфейсом или подсетью.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="get-cost-savings-summary-from-azure-advisor"></a><a name="advisor-savings"></a>Получение сводки по сокращению затрат из Помощника по Azure

Этот запрос позволяет получить сводку по сокращению затрат для каждой рекомендации [Помощника по Azure](../../../advisor/advisor-overview.md).

```kusto
advisorresources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'Cost'
| extend
    resources = tostring(properties.resourceMetadata.resourceId),
    savings = todouble(properties.extendedProperties.savingsAmount),
    solution = tostring(properties.shortDescription.solution),
    currency = tostring(properties.extendedProperties.savingsCurrency)
| summarize
    dcount(resources), 
    bin(sum(savings), 0.01)
    by solution, currency
| project solution, dcount_resources, sum_savings, currency
| order by sum_savings desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

![Значок обозревателя Resource Graph](../media/resource-graph-small.png) Попробуйте выполнить этот запрос в обозревателе Azure Resource Graph:

- Портал Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.com</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал Azure для государственных организаций: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.us</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)
- Портал 21Vianet Azure для Китая: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.cn</a> ![Значок "Открыть ссылку в новом окне"](../../media/new-window.png)

---

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [языке запросов](../concepts/query-language.md).
- Узнайте больше о том, как [изучать ресурсы](../concepts/explore-resources.md).
- Ознакомьтесь с примерами [усложненных запросов](advanced.md).