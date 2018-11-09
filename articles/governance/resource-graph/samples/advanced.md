---
title: Продвинутые запросы к графику ресурсов Azure
description: Используйте график ресурсов Azure для выполнения некоторых сложных запросов.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: fbbdc4a67cd6f2e7d74031f7acc584bf0004bea4
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085382"
---
# <a name="advanced-resource-graph-queries"></a>Продвинутые запросы графика ресурсов

Первым шагом на пути к пониманию запросов к Azure Resource Graph является общее понимание [языка запросов](../concepts/query-language.md). Если вы еще не знакомы с [обозревателем данных Azure](../../../data-explorer/data-explorer-overview.md), рекомендуется просмотреть основные сведения, чтобы понять, как составлять запросы к ресурсам, которые вы ищете.

Мы рассмотрим следующие продвинутые запросы:

> [!div class="checklist"]
> - [Получение емкости и размера VMSS](#vmss-capacity)
> - [Вывести список всех названий тегов](#list-all-tags)
> - [Виртуальные машины, сопоставленные по регулярному выражению](#vm-regex)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

## <a name="language-support"></a>Поддержка языков

Azure CLI (с помощью расширения) и Azure PowerShell (с помощью модуля) поддерживают Azure Resource Graph. Перед выполнением любого из следующих запросов убедитесь, что среда готова. В разделах об [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) и [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) приведены инструкции по установке и проверке выбранной вами среды оболочки.

## <a name="vmss-capacity"></a>Получение сведений о емкости и производительности масштабируемого набора виртуальных машин

Этот запрос выполняет поиск ресурсов масштабируемого набора виртуальных машин и возвращает разные сведения, включая размер виртуальной машины и емкость масштабируемого набора. В запросе используется функция `toint()` для приведения значения емкости в число для сортировки. Наконец, столбцы переименовываются в пользовательские именованные свойства.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags">Вывести список всех названий тегов</a>

Этот запрос начинается с тега и строит объект JSON, перечисляющий все уникальные имена тегов и их соответствующие типы.

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Виртуальные машины, сопоставленные по регулярному выражению

Этот запрос выполняет поиск виртуальных машин, которые соответствуют [регулярному выражению](/dotnet/standard/base-types/regular-expression-language-quick-reference) (известному как _регулярное выражение_).
**Соответствие регулярному выражению @** позволяет определить регулярное выражение для сопоставления, которое является **^Contoso(.*) [0-9]+$**. Дополнительные сведения об определении регулярного выражения см. в разделе:

- `^` — Сопоставление должно начаться в начале строки.
- `Contoso` — Строка с учетом регистра.
- `(.*)` — Сопоставление части выражения:
  - `.` — Сопоставляется с любым единичным символом (кроме новой строки).
  - `*` — Сопоставляется с предыдущим элементом ноль или более раз.
- `[0-9]` — Сопоставление группы символ для чисел от 0 до 9.
- `+` — Сопоставляется с предыдущим элементом один или более раз.
- `$` — Сопоставление предыдущего элемента должно находиться в конце строки.

После сопоставления по имени запрос проектирует имя и порядок по возрастанию имени.

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Дополнительная информация

- См. примеры в разделе [начальных запросов](starter.md)
- Дополнительные сведения о [языке запросов](../concepts/query-language.md)
- Подробнее о [просмотре ресурсов](../concepts/explore-resources.md)