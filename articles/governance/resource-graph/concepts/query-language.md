---
title: Основные сведения о языке запросов
description: Описание таблиц Resource Graph и доступных типов данных, операторов и функций Kusto, которые можно использовать с Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 944d0f2676f1a82c80be33a6c1a91d34bc8a32f7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654450"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Общие сведения о языке запросов графика ресурсов Azure

Язык запросов для графика ресурсов Azure поддерживает ряд операторов и функций. Каждый из них работает так же, как элементы [языка запросов Kusto (KQL)](/azure/kusto/query/index). Чтобы ознакомиться с языком запросов, используемом в Resource Graph, начните с [учебника по KQL](/azure/kusto/query/tutorial).

В этой статье рассматриваются компоненты языка, поддерживаемые Resource Graph:

- [таблицы Resource Graph;](#resource-graph-tables)
- [поддерживаемые элементы языка KQL;](#supported-kql-language-elements)
- [escape-символы.](#escape-characters)

## <a name="resource-graph-tables"></a>Таблицы Resource Graph

Resource Graph предоставляет несколько таблиц для хранения данных по типам ресурсов Resource Manager и их свойствам. Эти таблицы можно использовать с оператором `join` или `union` для получения свойств из связанных типов ресурсов. Ниже приведен список таблиц, доступных в Resource Graph.

|Таблицы Resource Graph |Описание |
|---|---|
|Ресурсы |Таблица по умолчанию, если таблица в запросе не определена. В ней содержится большинство типов ресурсов Resource Manager и их свойств. |
|ResourceContainers |Содержит подписку (в предварительной версии — `Microsoft.Resources/subscriptions`) и типы ресурсов и данные группы ресурсов (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AdvisorResources |Содержит ресурсы, _связанные_ с `Microsoft.Advisor`. |
|AlertsManagementResources |Содержит ресурсы, _связанные_ с `Microsoft.AlertsManagement`. |
|HealthResources |Содержит ресурсы, _связанные_ с `Microsoft.ResourceHealth`. |
|MaintenanceResources |Содержит ресурсы, _связанные_ с `Microsoft.Maintenance`. |
|SecurityResources |Содержит ресурсы, _связанные_ с `Microsoft.Security`. |

Полный список, включая типы ресурсов, см. в [справочнике по поддерживаемым таблицам и типам ресурсов](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resources_ — это таблица по умолчанию. При запросе таблицы _Resources_ не требуется указывать имя таблицы, если не используется `join` или `union`. Однако рекомендуется всегда включать в запрос начальную таблицу.

Используйте обозреватель Resource Graph на портале, чтобы узнать, какие типы ресурсов доступны в каждой таблице. В качестве альтернативы можно использовать такой запрос, как `<tableName> | distinct type`, чтобы получить список имеющихся в среде типов ресурсов, которые поддерживаются данной таблицей Resource Graph.

В приведенном ниже запросе показана простая операция `join`. В результате запроса столбцы объединяются, а ко всем дублирующимся именам столбцов из соединяемой таблицы (в этом примере _ResourceContainers_) добавляется **1**. Так как таблица _ResourceContainers_ содержит типы как для подписок, так и для групп ресурсов, любой из этих типов может использоваться для соединения с ресурсом из таблицы _ресурсов_.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

В приведенном ниже запросе показано более сложное использование `join`. Запрос ограничивает объединенную таблицу до ресурсов подписки, а `project` включает только исходное поле _subscriptionId_ и поле _name_, переименованное в _SubName_. Переименование полей позволяет избежать `join`, добавляя его как _name1_, так как поле уже существует в _Resources_. Исходная таблица фильтруется с `where`, а следующая `project` включает столбцы из обеих таблиц. Результатом запроса является одно хранилище ключей, отображающее тип, имя хранилища ключей и имя подписки, в которой оно находится.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> При ограничении результатов `join` с помощью `project` свойство, используемое `join` для связывания двух таблиц (_subscriptionId_ в приведенном выше примере), должно включаться в `project`.

## <a name="supported-kql-language-elements"></a>Поддерживаемые элементы языка KQL

Resource Graph поддерживает все [типы данных](/azure/kusto/query/scalar-data-types/), [скалярные функции](/azure/kusto/query/scalarfunctions), [скалярные операторы](/azure/kusto/query/binoperators) и [агрегатные функции](/azure/kusto/query/any-aggfunction) KQL. Resource Graph поддерживает определенные [табличные операторы](/azure/kusto/query/queries), некоторые из которых имеют разное поведение.

### <a name="supported-tabulartop-level-operators"></a>Поддерживаемые табличные операторы и операторы верхнего уровня

Ниже приведен список табличных операторов KQL, поддерживаемых Resource Graph, с примерами.

|KQL |Пример запроса Resource Graph |Примечания |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Число хранилищ ключей](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Отображение уникальных значений для конкретного псевдонима](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[Подсчет виртуальных машин по типу ОС](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Хранилище ключей с именем подписки](../samples/advanced.md#join) |Поддерживаемые разновидности оператора соединения: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Не более 3 `join` в одном запросе. Пользовательские стратегии соединения, такие как широковещательное соединение, не допускаются. Может использоваться в одной таблице или между таблицами _Resources_ и _ResourceContainers_. |
|[limit](/azure/kusto/query/limitoperator) |[Вывод списка общедоступных IP-адресов](../samples/starter.md#list-publicip) |Синоним `take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Устаревший оператор, используйте вместо него `mv-expand`. Максимальное значение _RowLimit_ равно 400. Значение по умолчанию — 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Список Cosmos DB с конкретным указанием расположений записи](../samples/advanced.md#mvexpand-cosmosdb) |Максимальное значение _RowLimit_ равно 400. Значение по умолчанию — 128. |
|[order](/azure/kusto/query/orderoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) |Синоним `sort` |
|[project](/azure/kusto/query/projectoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Удаление столбцов из результатов](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) |Синоним `order` |
|[summarize](/azure/kusto/query/summarizeoperator) |[Подсчет ресурсов Azure](../samples/starter.md#count-resources) |Только первая упрощенная страница |
|[take](/azure/kusto/query/takeoperator) |[Вывод списка общедоступных IP-адресов](../samples/starter.md#list-publicip) |Синоним `limit` |
|[В начало](/azure/kusto/query/topoperator) |[Отображение первых пяти виртуальных машин по имени и типу ОС](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Объединение результатов из двух запросов в один результат](../samples/advanced.md#unionresults) |Допускается одна таблица: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. Не более 3 разветвлений `union` в одном запросе. Нечеткое разрешение таблиц разветвлений `union` не допускается. Может использоваться в одной таблице или между таблицами _Resources_ и _ResourceContainers_. |
|[where](/azure/kusto/query/whereoperator) |[Отображение ресурсов, которые содержат хранилище](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-символы

Некоторые имена свойств, например включающие в себя `.` или `$`, должны заключаться в оболочку или экранироваться в запросе, иначе они будут интерпретироваться неправильно и давать непредвиденные результаты.

- `.` — заключите имя свойства следующим образом: `['propertyname.withaperiod']`
  
  Пример запроса, в котором заключается свойство _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` — экранируйте символ в имени свойства. Используемый escape-символ зависит от оболочки, из которой выполняется Resource Graph.

  - **bash** - `\`

    Пример запроса, в котором экранируется свойство _\$type_ в bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** — не экранируйте символ `$`.

  - **PowerShell** - ``` ` ```

    Пример запроса, в котором экранируется свойство _\$type_ в PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Дальнейшие действия

- См. описание используемого языка в разделе [Запросы для начинающих](../samples/starter.md).
- См. описание расширенных вариантов использования в разделе [Расширенные запросы](../samples/advanced.md).
- Узнайте больше о том, как [изучать ресурсы](explore-resources.md).