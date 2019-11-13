---
title: Основные сведения о языке запросов
description: Описывает таблицы графа ресурсов и доступные типы данных, операторы и функции Kusto, которые можно использовать с графом ресурсов Azure.
ms.date: 10/21/2019
ms.topic: conceptual
ms.openlocfilehash: baef46f4ba6f899c2c0a1392f87006223d75a4e1
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959053"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Общие сведения о языке запросов графика ресурсов Azure

Язык запросов для графика ресурсов Azure поддерживает ряд операторов и функций. Каждая работа и работает на основе [языка запросов Kusto (ККЛ)](/azure/kusto/query/index). Чтобы узнать о языке запросов, используемом графиком ресурсов, начните с [учебника по ККЛ](/azure/kusto/query/tutorial).

В этой статье рассматриваются языковые компоненты, поддерживаемые графиком ресурсов:

- [Таблицы графов ресурсов](#resource-graph-tables)
- [Поддерживаемые элементы языка ККЛ](#supported-kql-language-elements)
- [Escape-символы](#escape-characters)

## <a name="resource-graph-tables"></a>Таблицы графов ресурсов

Граф ресурсов предоставляет несколько таблиц для хранения данных о диспетчер ресурсов типах ресурсов и их свойствах. Эти таблицы можно использовать с операторами `join` или `union` для получения свойств из связанных типов ресурсов. Ниже приведен список таблиц, доступных в диаграмме ресурсов.

|Таблицы графов ресурсов |ОПИСАНИЕ |
|---|---|
|Ресурсы |Таблица по умолчанию, если в запросе не определено значение None. Большинство диспетчер ресурсов типов и свойств ресурсов. |
|ресаурцеконтаинерс |Включает подписку (в предварительной версии — `Microsoft.Resources/subscriptions`) и типы ресурсов и данные группы ресурсов (`Microsoft.Resources/subscriptions/resourcegroups`). |
|алертсманажементресаурцес |Содержит ресурсы, _связанные_ с `Microsoft.AlertsManagement`. |
|секуритиресаурцес |Содержит ресурсы, _связанные_ с `Microsoft.Security`. |

> [!NOTE]
> _Resources_ является таблицей по умолчанию. При запросе к таблице _Resources_ не требуется указывать имя таблицы, если не используются `join` и `union`. Однако рекомендуется всегда включать в запрос начальную таблицу.

Используйте обозреватель графов ресурсов на портале, чтобы узнать, какие типы ресурсов доступны в каждой таблице. В качестве альтернативы можно использовать запрос, например `<tableName> | distinct type`, чтобы получить список типов ресурсов, поддерживаемых данной таблицей графов ресурсов, которые существуют в вашей среде.

В следующем запросе показан простой `join`. Результат запроса объединяет столбцы вместе и все дублирующиеся имена столбцов из соединяемой таблицы, _ресаурцеконтаинерс_ в этом примере, добавляются к **1**. Так как таблица _ресаурцеконтаинерс_ имеет типы для подписок и групп ресурсов, для подключения к ресурсу из таблицы _Resources_ может использоваться любой тип.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Следующий запрос демонстрирует более сложное использование `join`. Запрос ограничивает объединенную таблицу до ресурсов подписки, а `project` включает только исходное поле _subscriptionId_ и поле _name_, переименованное в _SubName_. Переименование поля позволяет избежать `join` добавления его в качестве _name1_ , так как поле уже существует в _ресурсах_. Исходная таблица фильтруется с `where`, а следующая `project` включает столбцы из обеих таблиц. Результатом запроса является одно хранилище ключей, отображающее тип, имя хранилища ключей и имя подписки, в которой оно находится.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> При ограничении `join`ных результатов `project`, свойство, используемое `join` для связи двух таблиц, _SubscriptionId_ в приведенном выше примере, должно быть включена в `project`.

## <a name="supported-kql-language-elements"></a>Поддерживаемые элементы языка ККЛ

Граф ресурсов поддерживает все [типы данных](/azure/kusto/query/scalar-data-types/)ККЛ, [скалярные функции](/azure/kusto/query/scalarfunctions), [скалярные операторы](/azure/kusto/query/binoperators)и [агрегатные функции](/azure/kusto/query/any-aggfunction). Определенные [табличные операторы](/azure/kusto/query/queries) поддерживаются графиком ресурсов, некоторые из которых имеют разные поведения.

### <a name="supported-tabulartop-level-operators"></a>Поддерживаемые операторы табличного или верхнего уровня

Ниже приведен список табличных операторов ККЛ, поддерживаемых графиком ресурсов с конкретными примерами.

|ккл |Пример запроса к диаграмме ресурсов |Примечания |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Подсчет ключевых хранилищ](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Отображение уникальных значений для конкретного псевдонима](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[Подсчет виртуальных машин по типу ОС](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Хранилище ключей с именем подписки](../samples/advanced.md#join) |Поддерживаемые флаги соединений: [иннеруникуе](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Ограничение в 3 `join` в одном запросе. Пользовательские стратегии подключения, такие как широковещательное соединение, не допускаются. Может использоваться в одной таблице или между таблицами _Resources_ и _ресаурцеконтаинерс_ . |
|[limit](/azure/kusto/query/limitoperator) |[Вывод списка общедоступных IP-адресов](../samples/starter.md#list-publicip) |Синоним `take` |
|[MV — развернуть](/azure/kusto/query/mvexpandoperator) |[Список Cosmos DB с конкретным указанием расположений записи](../samples/advanced.md#mvexpand-cosmosdb) |_Ровлимит_ максимум 400 |
|[порядок](/azure/kusto/query/orderoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) |Синоним `sort` |
|[project](/azure/kusto/query/projectoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Удаление столбцов из результатов](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) |Синоним `order` |
|[summarize](/azure/kusto/query/summarizeoperator) |[Подсчет ресурсов Azure](../samples/starter.md#count-resources) |Только первая страница |
|[take](/azure/kusto/query/takeoperator) |[Вывод списка общедоступных IP-адресов](../samples/starter.md#list-publicip) |Синоним `limit` |
|[В начало](/azure/kusto/query/topoperator) |[Отображение первых пяти виртуальных машин по имени и типу ОС](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Объединение результатов из двух запросов в один результат](../samples/advanced.md#unionresults) |Допускается использование одной таблицы: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. Ограничение в 3 `union` в одном запросе. Нечеткое разрешение для `union`ных таблиц не разрешено. Может использоваться в одной таблице или между таблицами _Resources_ и _ресаурцеконтаинерс_ . |
|[where](/azure/kusto/query/whereoperator) |[Отображение ресурсов, которые содержат хранилище](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-символы

Некоторые имена свойств, например те, которые включают `.` или `$`, должны быть заключены в запрос или экранированы в запросе, либо имя свойства интерпретируется неправильно и не предоставляет ожидаемых результатов.

- `.` заключайте имя свойства следующим образом: `['propertyname.withaperiod']`
  
  Пример запроса, который заключает в оболочку свойство _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` — escape-последовательность символа в имени свойства. Используемый escape-символ зависит от графа ресурсов оболочки, из которого выполняется.

  - **bash** - `\`

    Пример запроса, который обходит свойство _\$Type_ в Bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** — не экранирование `$` символа.

  - **PowerShell** - ``` ` ```

    Пример запроса, который переключает свойство _\$тип_ в PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Дополнительная информация

- См. язык, используемый в [начальных запросах](../samples/starter.md).
- См. Дополнительные сведения о расширенном использовании в [расширенных запросах](../samples/advanced.md).
- Узнайте больше о том, как [исследовать ресурсы](explore-resources.md).