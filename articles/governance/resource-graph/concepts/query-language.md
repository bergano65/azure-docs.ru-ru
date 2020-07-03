---
title: Основные сведения о языке запросов
description: Описывает таблицы графа ресурсов и доступные типы данных, операторы и функции Kusto, которые можно использовать с графом ресурсов Azure.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78927496"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Общие сведения о языке запросов графика ресурсов Azure

Язык запросов для графика ресурсов Azure поддерживает ряд операторов и функций. Каждая работа и работает на основе [языка запросов Kusto (ККЛ)](/azure/kusto/query/index). Чтобы узнать о языке запросов, используемом графиком ресурсов, начните с [учебника по ККЛ](/azure/kusto/query/tutorial).

В этой статье рассматриваются языковые компоненты, поддерживаемые графиком ресурсов:

- [Таблицы графов ресурсов](#resource-graph-tables)
- [Поддерживаемые элементы языка ККЛ](#supported-kql-language-elements)
- [Escape-символы](#escape-characters)

## <a name="resource-graph-tables"></a>Таблицы графов ресурсов

Граф ресурсов предоставляет несколько таблиц для хранения данных о диспетчер ресурсов типах ресурсов и их свойствах. Эти таблицы можно использовать с `join` операторами `union` или для получения свойств из связанных типов ресурсов. Ниже приведен список таблиц, доступных в диаграмме ресурсов.

|Таблицы графов ресурсов |Описание |
|---|---|
|Ресурсы |Таблица по умолчанию, если в запросе не определено значение None. Большинство диспетчер ресурсов типов и свойств ресурсов. |
|ресаурцеконтаинерс |Включает подписки (в предварительной версии `Microsoft.Resources/subscriptions`) и типы ресурсов и`Microsoft.Resources/subscriptions/resourcegroups`данные группы ресурсов (). |
|адвисорресаурцес |Включает ресурсы _related_ , связанные `Microsoft.Advisor`с. |
|алертсманажементресаурцес |Включает ресурсы _related_ , связанные `Microsoft.AlertsManagement`с. |
|маинтенанцересаурцес |Включает ресурсы _related_ , связанные `Microsoft.Maintenance`с. |
|секуритиресаурцес |Включает ресурсы _related_ , связанные `Microsoft.Security`с. |

Полный список, включая типы ресурсов, см. в разделе [reference: Supported Tables and Resource Types](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resources_ является таблицей по умолчанию. При запросе к таблице _Resources_ не требуется указывать имя таблицы, если не `join` используется или `union` . Однако рекомендуется всегда включать в запрос начальную таблицу.

Используйте обозреватель графов ресурсов на портале, чтобы узнать, какие типы ресурсов доступны в каждой таблице. В качестве альтернативы можно использовать запрос, например `<tableName> | distinct type` , для получения списка типов ресурсов, поддерживаемых данной таблицей графа ресурсов, которые существуют в вашей среде.

В следующем запросе показан простой `join`. Результат запроса объединяет столбцы вместе и все дублирующиеся имена столбцов из соединяемой таблицы, _ресаурцеконтаинерс_ в этом примере, добавляются к **1**. Так как таблица _ресаурцеконтаинерс_ имеет типы для подписок и групп ресурсов, для подключения к ресурсу из таблицы _Resources_ может использоваться любой тип.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Следующий запрос демонстрирует более сложное использование `join`. Запрос ограничивает объединенную таблицу до ресурсов подписки, а `project` включает только исходное поле _subscriptionId_ и поле _name_, переименованное в _SubName_. Переименование поля позволяет `join` избежать добавления в качестве _name1_ , так как поле уже существует в _ресурсах_. Исходная таблица фильтруется с `where`, а следующая `project` включает столбцы из обеих таблиц. Результатом запроса является одно хранилище ключей, отображающее тип, имя хранилища ключей и имя подписки, в которой оно находится.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> При ограничении `join` результатов свойством `project`, используемым `join` для связи двух таблиц, _SubscriptionId_ в приведенном выше примере должен быть добавлен в. `project`

## <a name="supported-kql-language-elements"></a>Поддерживаемые элементы языка ККЛ

Граф ресурсов поддерживает все [типы данных](/azure/kusto/query/scalar-data-types/)ККЛ, [скалярные функции](/azure/kusto/query/scalarfunctions), [скалярные операторы](/azure/kusto/query/binoperators)и [агрегатные функции](/azure/kusto/query/any-aggfunction). Определенные [табличные операторы](/azure/kusto/query/queries) поддерживаются графиком ресурсов, некоторые из которых имеют разные поведения.

### <a name="supported-tabulartop-level-operators"></a>Поддерживаемые операторы табличного или верхнего уровня

Ниже приведен список табличных операторов ККЛ, поддерживаемых графиком ресурсов с конкретными примерами.

|ккл |Пример запроса к диаграмме ресурсов |Примечания |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Подсчет ключевых хранилищ](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Отображение уникальных значений для определенного псевдонима](../samples/starter.md#distinct-alias-values) | |
|[расширений](/azure/kusto/query/extendoperator) |[Подсчет количества виртуальных машин по типу ОС](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Хранилище ключей с именем подписки](../samples/advanced.md#join) |Поддерживаемые флаги соединений: [иннеруникуе](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Ограничение в 3 `join` в одном запросе. Пользовательские стратегии подключения, такие как широковещательное соединение, не допускаются. Может использоваться в одной таблице или между таблицами _Resources_ и _ресаурцеконтаинерс_ . |
|[Размер](/azure/kusto/query/limitoperator) |[Список всех общедоступных IP-адресов](../samples/starter.md#list-publicip) |Синоним`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Устаревший оператор. `mv-expand` используйте вместо него. _Ровлимит_ максимум 400. Значение по умолчанию — 128. |
|[MV — развернуть](/azure/kusto/query/mvexpandoperator) |[Список Cosmos DB с конкретным указанием расположений записи](../samples/advanced.md#mvexpand-cosmosdb) |_Ровлимит_ максимум 400. Значение по умолчанию — 128. |
|[порядок](/azure/kusto/query/orderoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) |Синоним`sort` |
|[проект](/azure/kusto/query/projectoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Удаление столбцов из результатов](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) |Синоним`order` |
|[SUMMARIZE](/azure/kusto/query/summarizeoperator) |[Подсчет ресурсов Azure](../samples/starter.md#count-resources) |Только первая страница |
|[нимают](/azure/kusto/query/takeoperator) |[Список всех общедоступных IP-адресов](../samples/starter.md#list-publicip) |Синоним`limit` |
|[Вверх](/azure/kusto/query/topoperator) |[Показывать первые пять виртуальных машин по имени и типу их ОС](../samples/starter.md#show-sorted) | |
|[наборов](/azure/kusto/query/unionoperator) |[Объединение результатов из двух запросов в один результат](../samples/advanced.md#unionresults) |Допустимая одна таблица _T_ `| union` \[ `kind=` `inner` \| `outer` \] :\] _Таблица_T `withsource=` _ColumnName_ ColumnName. \[ Ограничение в 3 `union` отрезков в одном запросе. Нечеткое разрешение `union` для разрешающих таблиц не допускается. Может использоваться в одной таблице или между таблицами _Resources_ и _ресаурцеконтаинерс_ . |
|[where](/azure/kusto/query/whereoperator) |[Отображение ресурсов, содержащих хранилище](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-символы

Некоторые имена свойств, например те, которые включают `.` или `$`, должны быть заключены в запрос или экранированы в запросе, либо имя свойства интерпретируется неправильно и не предоставляет ожидаемые результаты.

- `.`— Заключите имя свойства в следующее:`['propertyname.withaperiod']`
  
  Пример запроса, который заключает в оболочку свойство _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`— Escape-последовательность символа в имени свойства. Используемый escape-символ зависит от графа ресурсов оболочки, из которого выполняется.

  - **Bug** - `\`

    Пример запроса, который экранирование _ \$типа_ свойства в Bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** — не Escape- `$` символ.

  - **Оболочк** - ``` ` ```

    Пример запроса, который обходит _ \$тип_ свойства в PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Дальнейшие шаги

- См. язык, используемый в [начальных запросах](../samples/starter.md).
- См. Дополнительные сведения о расширенном использовании в [расширенных запросах](../samples/advanced.md).
- Узнайте больше о том, как [изучать ресурсы](explore-resources.md).