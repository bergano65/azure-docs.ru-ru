---
title: Переход по API-интерфейсам Azure Digital Twins | Документация Майкрософт
description: Сведения об общих шаблонах запросов API управления Azure Digital Twins.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: 9cfcdc879e36b93e21bff6f91886536d799553bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60924500"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Как использовать API управления Azure Digital Twins

API управления Azure Digital Twins предоставляют эффективные функциональные возможности для приложений Интернета вещей. В этой статье показано, как перемещаться по структуре API.  

## <a name="api-summary"></a>Сводные данные API

В следующем списке показаны компоненты API-интерфейсов Digital Twins.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces). Эти программные интерфейсы взаимодействуют с физическими расположениями в вашем экземпляре настройки. Они помогают создавать и удалять цифровые сопоставления физических расположений, а также управлять ими в виде [пространственного графа](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices). Эти программные интерфейсы взаимодействуют с устройствами в экземпляре настройки. Эти устройства могут управлять одними или несколькими датчиками. Например, устройством может быть телефон, датчик Raspberry Pi, шлюз Lora и т. д.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors). Эти программные интерфейсы помогают взаимодействовать с датчиками, связанными с устройствами и физическими расположениями. Датчики записывают и отправляют значения окружающей среды, которые затем можно использовать, чтобы управлять условиями окружающей среды.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources). Эти программные интерфейсы помогают настроить ресурсы, такие как центр Интернета вещей, для экземпляра Digital Twins.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types). Эти программные интерфейсы позволяют связать расширенные типы с объектами Digital Twins, чтобы добавить к ним определенные характеристики. Эти типы позволяют легко фильтровать и группировать объекты в пользовательском интерфейсе и настраиваемых функциях, обрабатывающих данные телеметрии. Примерами расширенных типов являются *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType* и другие.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies). Эти API помогают управлять онтологиями, которые представляют собой коллекции расширенных типов. Онтологии предоставляют имена для типов объектов в соответствии с физической средой, которую они представляют. Например, онтология *BACnet* содержит конкретные имена *типов датчиков*, *типов данных*, *подтипов данных* и *типов блоков данных*. Создание онтологий и управление ими осуществляется с помощью службы. Пользователи могут загружать и выгружать онтологии. При загрузке онтологии все связанные с ней имена типов включаются и могут быть подготовлены в пространственном графе. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys). Эти программные интерфейсы можно использовать, чтобы создавать настраиваемые свойства для *пространств*, *устройств*, *пользователей* и *датчиков*. Эти свойства создаются в виде пар "ключ — значение". Тип данных для этих свойств можно определить с помощью параметра *PrimitiveDataType*. Например, можно определить свойство с именем *BasicTemperatureDeltaProcessingRefreshtime* типа *uint* для датчиков, а затем для каждого из датчиков присвоить значение этого свойства. При создании свойства можно также добавить ограничения для этих значений, например, диапазоны *Min* и *Max*, а также допустимые значения, такие как *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers). Эти программные интерфейсы позволяют указать условия, которые необходимо оценить по входящим данным с устройства. Дополнительные сведения см. в [этой статье](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions). Эти программные интерфейсы позволяют создавать, удалять или обновлять настраиваемые функции, которые будут выполняться при возникновении условий, определенных в типе *matchers*, чтобы обработать данные, поступающие из экземпляра настройки. Дополнительные сведения об этих настраиваемых функциях, которые также называются *определяемыми пользователем*, см. [в этой статье](concepts-user-defined-functions.md#user-defined-functions). 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints). Эти программные интерфейсы позволяют создавать конечные точки, чтобы решение Digital Twins могло взаимодействовать с другими службами Azure для хранения и анализа данных. Дополнительные сведения см. в [этой статье](concepts-events-routing.md). 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores). Эти программные интерфейсы позволяют управлять хранилищами ключей безопасности для пространств. Эти хранилища могут содержать коллекцию ключей безопасности и позволяют легко извлекать последние действительные ключи.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users). Эти программные интерфейсы позволяют связывать пользователей с пространствами и при необходимости находить этих пользователей. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System). Эти программные интерфейсы позволяют управлять общесистемными параметрами, такими как типы пространств и датчиков по умолчанию. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments). Эти программные интерфейсы позволяют связывать роли с такими сущностями, как идентификатор пользователя, идентификатор определяемой пользователем функции и т. д. Каждое назначение роли включает в себя идентификатор ассоциируемой сущности, тип сущности, идентификатор ассоциируемой роли, идентификатор клиента и путь, который определяет верхний предел ресурсов, к которому сущность может получить доступ с помощью этой ассоциации. Дополнительные сведения см. в [этой статье](security-role-based-access-control.md).


## <a name="api-navigation"></a>Переход между API

Программные интерфейсы Digital Twins поддерживают фильтрацию и навигацию по пространственному графу с использованием следующих параметров:

- **spaceId**. API отфильтрует результаты по заданному идентификатору пространства. Кроме того, к API [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) можно применить логический флаг **useParentSpace**, который указывает, что данный идентификатор пространства ссылается на родительское пространство вместо текущего. 

- **minLevel** и **maxLevel**. Корневые пространства находятся на уровне 1. Пространства в родительском пространстве на уровне *n* располагаются на уровне *n+1*. С помощью этих значений можно фильтровать результаты по определенных уровнях. При установке эти значения становятся инклюзивными. Устройства, датчики и другие объекты находятся на одном уровне с ближайшим пространством. Чтобы получить все объекты на определенном уровне, задайте для параметров **minLevel** и **maxLevel** одинаковое значение.

- **minRelative** и **maxRelative**. Когда эти фильтры заданы, соответствующий уровень указывается относительно уровня заданного идентификатора пространства.
   - Относительный уровень *0* соответствует тому же уровню, что и заданный идентификатор пространства.
   - Относительный уровень *1* представляет пространства на том же уровне, что и дочерние элементы заданного идентификатора пространства. Относительный уровень *n* представляет пространства ниже указанного пространства на *n* уровней.
   - Относительный уровень  *-1* представляет пространства на том же уровне, что и родительское пространство указанного пространства.

- **traverse**. Позволяет перемещаться в любом направлении от заданного идентификатора пространства, как указано с помощью следующих значений.
   - **Нет**. Это значение по умолчанию фильтрует по заданному идентификатору пространства.
   - **Down**. Это значение фильтрует по заданному идентификатору пространства и его потомкам. 
   - **Up**. Это значение фильтрует по заданному идентификатору пространства и его предкам. 
   - **Span**. Это значение фильтрует горизонтальную часть пространственного графа на том же уровне, что и заданный идентификатор пространства. Для этого для параметров **minRelative** или **maxRelative** должно быть установлено значение true. 


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
* **$top**.
* **$skip** — если вы хотите отобразить всю коллекцию целиком, вы должны запросить ее как весь набор в одном вызове, а затем выполнить подкачку в приложении. 

Обратите внимание, что другие параметры запроса, такие как $count, $expand и $search, не поддерживаются.

### <a name="examples"></a>Примеры

В следующем списке приведены примеры запросов, использующих параметры системных запросов OData.

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о некоторых распространенных шаблонах запросов API см. в статье [Как запросить API Azure Digital Twins для выполнения общих задач](how-to-query-common-apis.md).


