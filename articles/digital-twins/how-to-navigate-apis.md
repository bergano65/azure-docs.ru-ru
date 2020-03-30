---
title: Навигация по AI - Azure Digital Twins Документы Майкрософт
description: Сведения об общих шаблонах запросов API управления Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265172"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Как использовать API управления Azure Digital Twins

API управления Azure Digital Twins предоставляют эффективные функциональные возможности для приложений Интернета вещей. В этой статье показано, как перемещаться по структуре API.  

## <a name="api-summary"></a>Сводные данные API

В следующем списке показаны компоненты API-интерфейсов Digital Twins.

* [/пробелы](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Эти AAP взаимодействуют с физическими местоположениями в настройке. Они помогают создавать и удалять цифровые сопоставления физических расположений, а также управлять ими в виде [пространственного графа](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/устройства](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Эти AAP взаимодействуют с устройствами в настройке. Эти устройства могут управлять одними или несколькими датчиками. Например, устройством может быть телефон, датчик Raspberry Pi, шлюз Lora и т. д.

* [/сенсоры](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Эти AAP помогут вам общаться с датчиками, связанными с вашими устройствами и вашимфизическим местоположением. Датчики записывают и отправляют значения окружающей среды, которые затем можно использовать, чтобы управлять условиями окружающей среды.  

* [/ресурсы](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Эти AAP помогут настроить ресурсы, такие как концентратор IoT, для экземпляра Digital Twins.

* [/Типы](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Эти AAP позволяют связывать расширенные типы с объектами Digital Twins, добавляя к этим объектам определенные характеристики. Эти типы позволяют легко фильтровать и группировать объекты в пользовательском интерфейсе и настраиваемых функциях, обрабатывающих данные телеметрии. Примерами расширенных типов являются *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType* и другие.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Эти AAP помогут вам управлять онтологии, которые являются коллекциями расширенных типов. Онтологии предоставляют имена для типов объектов в соответствии с физической средой, которую они представляют. Например, онтология *BACnet* содержит конкретные имена *типов датчиков*, *типов данных*, *подтипов данных* и *типов блоков данных*. Создание онтологий и управление ими осуществляется с помощью службы. Пользователи могут загружать и выгружать онтологии. При загрузке онтологии все связанные с ней имена типов включаются и могут быть подготовлены в пространственном графе. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Вы можете использовать эти AIS для создания пользовательских свойств для ваших *пространств,* *устройств,* *пользователей*и *датчиков.* Эти свойства создаются в виде пар "ключ — значение". Тип данных для этих свойств можно определить с помощью параметра *PrimitiveDataType*. Например, можно определить свойство с именем *BasicTemperatureDeltaProcessingRefreshtime* типа *uint* для датчиков, а затем для каждого из датчиков присвоить значение этого свойства. При создании свойства можно также добавить ограничения для этих значений, например, диапазоны *Min* и *Max*, а также допустимые значения, такие как *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Эти AI позволяют указать условия, которые вы хотите оценить по входящим данным устройства. Дополнительные сведения см. в [этой статье](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Эти AI позволяют создавать, удалять или обновлять пользовательскую функцию, которая будет выполняться при возникновении условий, определенных *сопоставителями,* для обработки данных, поступающих из вашей установки. Дополнительные сведения об этих настраиваемых функциях, которые также называются *определяемыми пользователем*, см. [в этой статье](concepts-user-defined-functions.md#user-defined-functions). 

* [/конечные точки](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Эти AAP позволяют создавать конечные точки, чтобы ваше решение Digital Twins можно было общаться с другими службами Azure для хранения и аналитики данных. Дополнительные сведения см. в [этой статье](concepts-events-routing.md). 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Эти AA позволяют управлять магазинами ключей безопасности для ваших пространств. Эти хранилища могут содержать коллекцию ключей безопасности и позволяют легко извлекать последние действительные ключи.

* [/пользователи](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Эти AA позволяет связывать пользователей с пробелами, чтобы найти этих людей, когда это необходимо. 

* [/система](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Эти AA позволяют управлять общесистемных настройками, такими как типы пространств и датчиков по умолчанию. 

* [/RoleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Эти AI позволяют связывать роли с такими объектами, как идентификатор пользователя, идентификатор функции, определяемый пользователем, ит и т.д. Каждое назначение ролей включает идентификатор ассоциированного объекта, тип сущности, идентификатор ассоциированного роли, идентификатор клиента и путь, определяющий верхний предел ресурса, к которому объект может получить доступ с этой ассоциацией. Дополнительные сведения см. в [этой статье](security-role-based-access-control.md).


## <a name="api-navigation"></a>Переход между API

Программные интерфейсы Digital Twins поддерживают фильтрацию и навигацию по пространственному графу с использованием следующих параметров:

- **spaceId**: API будет фильтровать результаты по данному идентификатору пространства. Кроме того, к API [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) можно применить логический флаг **useParentSpace**, который указывает, что данный идентификатор пространства ссылается на родительское пространство вместо текущего. 

- **minLevel** и **maxLevel**: Корневые пространства считаются на уровне 1. Пространства в родительском пространстве на уровне *n* располагаются на уровне *n+1*. С помощью этих значений можно фильтровать результаты по определенных уровнях. При установке эти значения становятся инклюзивными. Устройства, датчики и другие объекты находятся на одном уровне с ближайшим пространством. Чтобы получить все объекты на определенном уровне, задайте для параметров **minLevel** и **maxLevel** одинаковое значение.

- **minRelative** и **maxRelative**: Когда эти фильтры даны, соответствующий уровень относительно уровня данного идентификатора пространства:
   - Относительный уровень *0* соответствует тому же уровню, что и заданный идентификатор пространства.
   - Относительный уровень *1* представляет пространства на том же уровне, что и дочерние элементы заданного идентификатора пространства. Относительный уровень *n* представляет пространства ниже указанного пространства на *n* уровней.
   - Относительный уровень  *-1* представляет пространства на том же уровне, что и родительское пространство указанного пространства.

- **traverse**: Позволяет пройти в любом направлении от заданного идентификатора пространства, как указано следующими значениями.
   - **Нет**: Это значение по умолчанию фильтрирует данный идентификатор пространства.
   - **Вниз**: Это фильтры данный идентификатор пространства и его потомков. 
   - **Вверх**: Это фильтры данный идентификатор пространства и его предков. 
   - **Span**: Это фильтрует горизонтальную часть пространственного графика, на том же уровне, что и заданный идентификатор пространства. Для этого для параметров **minRelative** или **maxRelative** должно быть установлено значение true. 


### <a name="examples"></a>Примеры

В следующем списке приведены некоторые примеры навигации по API [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices). Обратите внимание, что заполнитель `YOUR_MANAGEMENT_API_URL` ссылается на URI программных интерфейсов Digital Twins в формате `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, где `YOUR_INSTANCE_NAME` — это имя экземпляра Azure Digital Twins, а `YOUR_LOCATION` — регион, в котором размещен экземпляр.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` возвращает все устройства, подключенные к корневым пространствам.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` возвращает все устройства, подключенные к пространствам на уровнях 2, 3 или 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` возвращает все устройства, непосредственно подключенные к mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` возвращает все устройства, подключенные к mySpaceId или одному из его потомков.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` возвращает все устройства, присоединенные к потомкам mySpaceId, за исключением mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` возвращает все устройства, подключенные к дочерним элементам первого уровня пространства mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` возвращает все устройства, подключенные к одному из предков mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` возвращает все устройства, подключенные к потомкам пространства (mySpaceId), уровень которых меньше или равен 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` возвращает все устройства, подключенные к пространствам, которые находятся на одном уровне с mySpaceId.


## <a name="odata-support"></a>Поддержка OData

Большинство программных интерфейсов, возвращающих коллекции, например, вызовы GET для пространств, поддерживают следующее подмножество универсальных параметров системного запроса [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData).  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** — если вы хотите отобразить всю коллекцию целиком, вы должны запросить ее как весь набор в одном вызове, а затем выполнить подкачку в приложении. 

> [!NOTE]
> Некоторые параметры OData (например, параметры запроса **$count,** **$expand**и **$search)** в настоящее время не поддерживаются.

### <a name="examples"></a>Примеры

Следующий список изображает несколько запросов с действительным синтаксисом OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о некоторых распространенных шаблонах запросов API см. в статье [Как запросить API Azure Digital Twins для выполнения общих задач](./how-to-query-common-apis.md).

Чтобы узнать больше о ваших конечных точках API, прочитайте [Как использовать Digital Twins Swagger](./how-to-use-swagger.md).

Для просмотра синтаксиса OData и доступных операторов сравнения читайте [операторы сравнения OData в Azure Cognitive Search](../search/search-query-odata-comparison-operators.md).
