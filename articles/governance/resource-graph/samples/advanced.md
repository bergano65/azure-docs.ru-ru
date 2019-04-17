---
title: Примеры расширенных запросов
description: С помощью Azure Resource Graph можно выполнять расширенные запросы, такие как получение емкости масштабируемого набора виртуальных МАШИН, списка всех используемых тегов и виртуальных машин, соответствующих регулярному выражению.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 9a243dd236a8c499602a9070a7dd61e69541d58d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256827"
---
# <a name="advanced-resource-graph-queries"></a>Продвинутые запросы графика ресурсов

Первым шагом на пути к пониманию запросов к Azure Resource Graph является общее понимание [языка запросов](../concepts/query-language.md). Если вы еще не знакомы с [обозревателем данных Azure](../../../data-explorer/data-explorer-overview.md), рекомендуется просмотреть основные сведения, чтобы понять, как составлять запросы к ресурсам, которые вы ищете.

Мы рассмотрим следующие продвинутые запросы:

> [!div class="checklist"]
> - [получение емкости и размера VMSS;](#vmss-capacity)
> - [получение списка всех названий тегов;](#list-all-tags)
> - [виртуальные машины, сопоставленные по регулярному выражению.](#vm-regex)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

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
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
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
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Виртуальные машины, сопоставленные по регулярному выражению

Этот запрос выполняет поиск виртуальных машин, которые соответствуют [регулярному выражению](/dotnet/standard/base-types/regular-expression-language-quick-reference) (известному как _регулярное выражение_).
**matches regex\@** позволяет определить регулярное выражение для сопоставления, которое равно `^Contoso(.*)[0-9]+$`. Дополнительные сведения об определении регулярного выражения см. в разделе:

- `^` — сопоставление должно начаться в начале строки.
- `Contoso` — строка с учетом регистра.
- `(.*)` — сопоставление части выражения:
  - `.` — сопоставляется с любым единичным символом (кроме новой строки).
  - `*` — сопоставляется с предыдущим элементом ноль или более раз.
- `[0-9]` — сопоставление группы символ для цифр от 0 до 9.
- `+` — сопоставляется с предыдущим элементом один или более раз.
- `$` — сопоставление предыдущего элемента должно находиться в конце строки.

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
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Дополнительная информация

- См. примеры в разделе [начальных запросов](starter.md)
- Дополнительные сведения о [языке запросов](../concepts/query-language.md)
- Подробнее о [просмотре ресурсов](../concepts/explore-resources.md)