---
title: Основные сведения о языке запросов
description: Описывает таблицы графики ресурсов и доступные типы данных Kusto, операторы и функции, пригодные для использования с помощью графика ресурсов Azure.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927496"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Общие сведения о языке запросов графика ресурсов Azure

Язык запросов для графика ресурсов Azure поддерживает ряд операторов и функций. Каждая работа и работа на основе [языка книжки Кусто (КЗЛ)](/azure/kusto/query/index). Чтобы узнать о языке запроса, используемом Resource Graph, начните с [учебника для K'L](/azure/kusto/query/tutorial).

В этой статье рассматриваются языковые компоненты, поддерживаемые Resource Graph:

- [Таблицы графиков ресурсов](#resource-graph-tables)
- [Поддерживаемые элементы языка КЗЛ](#supported-kql-language-elements)
- [Escape-символы](#escape-characters)

## <a name="resource-graph-tables"></a>Таблицы графиков ресурсов

Resource Graph предоставляет несколько таблиц для данных, которые он хранит о типах ресурсов Resource Manager и их свойствах. Эти таблицы могут `join` `union` использоваться с операторами или операторами для получения свойств из связанных типов ресурсов. Вот список таблиц, доступных в графике ресурсов:

|Таблицы графиков ресурсов |Описание |
|---|---|
|Ресурсы |Таблица по умолчанию, если она не определена в запросе. Большинство типов и свойств ресурсов менеджера ресурсов ресурса находятся здесь. |
|Ресурсконтейнеры |Включает подписку (в `Microsoft.Resources/subscriptions`предварительном просмотре -- ) и группу ресурсов ( )`Microsoft.Resources/subscriptions/resourcegroups`типы ресурсов и данные. |
|КонсультантРесурсы |Включает ресурсы, _связанные_ с `Microsoft.Advisor`. |
|ОповещенияМенеджментРесурсы |Включает ресурсы, _связанные_ с `Microsoft.AlertsManagement`. |
|Техническое обслуживаниеРесурсы |Включает ресурсы, _связанные_ с `Microsoft.Maintenance`. |
|Ресурсы безопасностиРесурсы |Включает ресурсы, _связанные_ с `Microsoft.Security`. |

Полный список, включав в который будут включены типы ресурсов, [см.](../reference/supported-tables-resources.md)

> [!NOTE]
> _Ресурсы_ — это таблица по умолчанию. При запросе таблицы _ресурсов_ не требуется указано имя `join` `union` таблицы, если они не используются или не используются. Однако рекомендуемая практика заключается в том, чтобы всегда включать начальную таблицу в запрос.

Используйте ресурс Graph Explorer на портале, чтобы узнать, какие типы ресурсов доступны в каждой таблице. В качестве альтернативы используйте такой `<tableName> | distinct type` запрос, чтобы получить список типов ресурсов, которые поддерживаются в данной таблице данных диаграмм ресурсов, которые существуют в вашей среде.

Следующий запрос показывает `join`простой . Результат запроса сочетает в себе столбцы, и любые дубликаты названий столбцов из объединенной _таблицы, ResourceContainers_ в этом примере, прикладываются с **1**. Поскольку _таблица ResourceContainers_ имеет типы как для подписок, так и для групп ресурсов, любой тип может быть использован для соединения с ресурсом из _таблицы ресурсов._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Следующий запрос показывает более сложное использование `join`. Запрос ограничивает объединенную таблицу до ресурсов подписки, а `project` включает только исходное поле _subscriptionId_ и поле _name_, переименованное в _SubName_. Переименование поля `join` позволяет избежать добавления его в качестве _name1,_ так как поле уже существует в _Ресурсах._ Исходная таблица фильтруется с `where`, а следующая `project` включает столбцы из обеих таблиц. Результатом запроса является одно хранилище ключей, отображающее тип, имя хранилища ключей и имя подписки, в которой оно находится.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> При `join` ограничении `project`результатов с `join` , свойство, используемое для соотнесения `project`двух таблиц, _subscriptionId_ в приведенном выше примере, должны быть включены в .

## <a name="supported-kql-language-elements"></a>Поддерживаемые элементы языка КЗЛ

Resource Graph поддерживает все [типы данных](/azure/kusto/query/scalar-data-types/)КЗЛ, [функции масштабирования,](/azure/kusto/query/scalarfunctions) [операторов масштабирования](/azure/kusto/query/binoperators)и [функции агрегирования.](/azure/kusto/query/any-aggfunction) Конкретные [таблетчатые операторы](/azure/kusto/query/queries) поддерживаются Resource Graph, некоторые из которых имеют различное поведение.

### <a name="supported-tabulartop-level-operators"></a>Поддерживаемые таблики/операторы верхнего уровня

Вот список таблоярных операторов КЗЛ, поддерживаемых Resource Graph с конкретными образцами:

|Kql |Запрос образца ресурса График |Примечания |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Подсчитайте хранилища ключей](../samples/starter.md#count-keyvaults) | |
|[Различных](/azure/kusto/query/distinctoperator) |[Показать различные значения для конкретного псевдонима](../samples/starter.md#distinct-alias-values) | |
|[Расширить](/azure/kusto/query/extendoperator) |[Подсчитайте виртуальные машины по типу ОС](../samples/starter.md#count-os) | |
|[Присоединиться к](/azure/kusto/query/joinoperator) |[Хранилище ключей с именем подписки](../samples/advanced.md#join) |Присоединяйтесь к поддерживаемым вкусам: [иннерум,](/azure/kusto/query/joinoperator#default-join-flavor) [внутренний,](/azure/kusto/query/joinoperator#inner-join) [левый.](/azure/kusto/query/joinoperator#left-outer-join) Ограничение 3 `join` в одном запросе. Пользовательские стратегии соединения, такие как соединение вещания, не допускаются. Может использоваться в одной таблице или между таблицами _ресурсов_ и _ресурсов._ |
|[Предел](/azure/kusto/query/limitoperator) |[Перечислите все общедоступные IP-адреса](../samples/starter.md#list-publicip) |Синоним`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Устаревший оператор, вместо этого используйте. `mv-expand` _RowLimit_ максимум 400. Значение по умолчанию — 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Список Космос DB с конкретными местами записи](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ максимум 400. Значение по умолчанию — 128. |
|[Заказ](/azure/kusto/query/orderoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) |Синоним`sort` |
|[Проекта](/azure/kusto/query/projectoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Удаление столбцов из результатов](../samples/advanced.md#remove-column) | |
|[Сортировки](/azure/kusto/query/sortoperator) |[Вывод списка ресурсов, отсортированных по имени](../samples/starter.md#list-resources) |Синоним`order` |
|[Суммировать](/azure/kusto/query/summarizeoperator) |[Подсчет ресурсов Azure](../samples/starter.md#count-resources) |Упрощенная первая страница только |
|[Принять](/azure/kusto/query/takeoperator) |[Перечислите все общедоступные IP-адреса](../samples/starter.md#list-publicip) |Синоним`limit` |
|[Вверх](/azure/kusto/query/topoperator) |[Показать первые пять виртуальных машин по имени и типу ОС](../samples/starter.md#show-sorted) | |
|[Союза](/azure/kusto/query/unionoperator) |[Объединить результаты двух запросов в один результат](../samples/advanced.md#unionresults) |Одностоловая таблица разрешена: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _Таблица_ _столбцов_ \] . Ограничение 3 `union` ноги в одном запросе. Нечеткое разрешение `union` таблиц для ног не допускается. Может использоваться в одной таблице или между таблицами _ресурсов_ и _ресурсов._ |
|[where](/azure/kusto/query/whereoperator) |[Отображить ресурсы, содержащие хранилище](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-символы

Некоторые имена свойств, такие как `.` `$`те, которые включают или , должны быть завернуты или сбежали в запросе или имя свойства интерпретируется неправильно и не дает ожидаемых результатов.

- `.`- Оберните имя свойства как таковое:`['propertyname.withaperiod']`
  
  Пример запроса, который обертывает свойство _odata.type:_

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- Побег символ в названии собственности. Используемый символ побега зависит от оболочки РесурсНая Диаграмма запущена из.

  - **Bash** - `\`

    Пример запроса, который избегает _ \$типа_ свойства в Bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Не убегайте `$` от персонажа.

  - **Powershell** - ``` ` ```

    Пример запроса, который избегает _ \$типа_ свойств в PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с языком, который используется в [запросах Starter.](../samples/starter.md)
- Просмотрите расширенные запросы в [Расширенных запросах.](../samples/advanced.md)
- Узнайте больше о том, как [изучать ресурсы](explore-resources.md).