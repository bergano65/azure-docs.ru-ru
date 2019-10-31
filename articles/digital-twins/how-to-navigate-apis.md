---
title: Переход по API-интерфейсам Azure Digital Twins | Документация Майкрософт
description: Сведения об общих шаблонах запросов API управления Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: 5df8bb0ba0323692bddf94ae2888e8fcf1ad24a9
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063269"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Как использовать API управления Azure Digital Twins

API управления Azure Digital Twins предоставляют эффективные функциональные возможности для приложений Интернета вещей. В этой статье показано, как перемещаться по структуре API.  

## <a name="api-summary"></a>Сводные данные API

В следующем списке показаны компоненты API-интерфейсов Digital Twins.

* [/спацес](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces). Эти интерфейсы API взаимодействуют с физическими расположениями в программе установки. Они помогают создавать и удалять цифровые сопоставления физических расположений, а также управлять ими в виде [пространственного графа](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/девицес](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices). Эти интерфейсы API взаимодействуют с устройствами в программе установки. Эти устройства могут управлять одними или несколькими датчиками. Например, устройством может быть телефон, датчик Raspberry Pi, шлюз Lora и т. д.

* [/СЕНСОРС](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors). Эти API помогают взаимодействовать с датчиками, связанными с вашими устройствами и физическими местоположениями. Датчики записывают и отправляют значения окружающей среды, которые затем можно использовать, чтобы управлять условиями окружающей среды.  

* [/ресаурцес](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources). Эти API помогают настроить ресурсы, например центр Интернета вещей, для своего экземпляра Digital двойников.

* [/типес](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types). Эти API позволяют связать расширенные типы с объектами Digital двойников, чтобы добавить определенные характеристики в эти объекты. Эти типы позволяют легко фильтровать и группировать объекты в пользовательском интерфейсе и настраиваемых функциях, обрабатывающих данные телеметрии. Примерами расширенных типов являются *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType* и другие.

* [/онтологиес](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies). Эти API помогают управлять онтологиес, которые являются коллекциями расширенных типов. Онтологии предоставляют имена для типов объектов в соответствии с физической средой, которую они представляют. Например, онтология *BACnet* содержит конкретные имена *типов датчиков*, *типов данных*, *подтипов данных* и *типов блоков данных*. Создание онтологий и управление ими осуществляется с помощью службы. Пользователи могут загружать и выгружать онтологии. При загрузке онтологии все связанные с ней имена типов включаются и могут быть подготовлены в пространственном графе. 

* [/пропертикэйс](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys). Эти API можно использовать для создания пользовательских свойств для *модулей*, *устройств*, *пользователей*и *датчиков*. Эти свойства создаются в виде пар "ключ — значение". Тип данных для этих свойств можно определить с помощью параметра *PrimitiveDataType*. Например, можно определить свойство с именем *BasicTemperatureDeltaProcessingRefreshtime* типа *uint* для датчиков, а затем для каждого из датчиков присвоить значение этого свойства. При создании свойства можно также добавить ограничения для этих значений, например, диапазоны *Min* и *Max*, а также допустимые значения, такие как *ValidationData*.

* [/матчерс](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers). Эти API позволяют указать условия, которые необходимо оценить на основе данных входящих устройств. Дополнительные сведения см. в [этой статье](concepts-user-defined-functions.md#matchers). 

* [/усердефинедфунктионс](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions). Эти API-интерфейсы позволяют создавать, удалять или обновлять пользовательские функции, которые будут выполняться при возникновении условий, определенных *соответствующими критериями* , для обработки данных, поступающих из программы установки. Дополнительные сведения об этих настраиваемых функциях, которые также называются *определяемыми пользователем*, см. [в этой статье](concepts-user-defined-functions.md#user-defined-functions). 

* [/ендпоинтс](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints). Эти API позволяют создавать конечные точки, чтобы ваше решение Digital двойников может взаимодействовать с другими службами Azure для хранения и аналитики данных. Дополнительные сведения см. в [этой статье](concepts-events-routing.md). 

* [/кэйсторес](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores). Эти API позволяют управлять хранилищами ключей безопасности для ваших модулей. Эти хранилища могут содержать коллекцию ключей безопасности и позволяют легко извлекать последние действительные ключи.

* [/Users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users). Эти API позволяют связать пользователей с вашими модулями, чтобы при необходимости их можно было разместить. 

* [/System](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): эти API позволяют управлять параметрами на уровне системы, такими как типы пробелов и датчиков по умолчанию. 

* [/ролеассигнментс](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments). Эти API позволяют связывать роли с сущностями, такими как идентификатор пользователя, идентификатор определяемой пользователем функции и т. д. Каждое назначение роли включает идентификатор сущности для связывания, тип сущности, идентификатор связываемой роли, идентификатор клиента и путь, определяющий верхний предел ресурса, к которому сущность может обращаться с этой ассоциацией. Дополнительные сведения см. в [этой статье](security-role-based-access-control.md).


## <a name="api-navigation"></a>Переход между API

Программные интерфейсы Digital Twins поддерживают фильтрацию и навигацию по пространственному графу с использованием следующих параметров:

- **спацеид**: API будет фильтровать результаты по заданному идентификатору пространства. Кроме того, к API [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) можно применить логический флаг **useParentSpace**, который указывает, что данный идентификатор пространства ссылается на родительское пространство вместо текущего. 

- **минлевел** и **МАКСЛЕВЕЛ**: корневые пространства считаются на уровне 1. Пространства в родительском пространстве на уровне *n* располагаются на уровне *n+1*. С помощью этих значений можно фильтровать результаты по определенных уровнях. При установке эти значения становятся инклюзивными. Устройства, датчики и другие объекты находятся на одном уровне с ближайшим пространством. Чтобы получить все объекты на определенном уровне, задайте для параметров **minLevel** и **maxLevel** одинаковое значение.

- **минрелативе** и **максрелативе**. при указании этих фильтров соответствующий уровень ОПРЕДЕЛЯЕТСЯ относительно уровня данного идентификатора пространства:
   - Относительный уровень *0* соответствует тому же уровню, что и заданный идентификатор пространства.
   - Относительный уровень *1* представляет пространства на том же уровне, что и дочерние элементы заданного идентификатора пространства. Относительный уровень *n* представляет пространства ниже указанного пространства на *n* уровней.
   - Относительный уровень  *-1* представляет пространства на том же уровне, что и родительское пространство указанного пространства.

- **обход**: позволяет выполнять обход в любом направлении от заданного идентификатора пространства, как указано в следующих значениях.
   - **Нет**: это значение по умолчанию фильтруется по заданному идентификатору пространства.
   - **Down**: фильтрует по заданному идентификатору пространства и его потомкам. 
   - **Up**: фильтрует по заданному идентификатору пространства и его предкам. 
   - **Span**: фильтрует горизонтальную часть пространственного графа на том же уровне, что и заданный идентификатор пространства. Для этого для параметров **minRelative** или **maxRelative** должно быть установлено значение true. 


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

> [!NOTE]
> Некоторые параметры OData (такие как параметры запроса **$Count**, **$expand**и **$Search**) в настоящее время не поддерживаются.

### <a name="examples"></a>Примеры

В следующем списке показаны несколько запросов с допустимым синтаксисом OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о некоторых распространенных шаблонах запросов API см. в статье [Как запросить API Azure Digital Twins для выполнения общих задач](./how-to-query-common-apis.md).

Дополнительные сведения о конечных точках API см. в статье [Использование цифрового двойников Swagger](./how-to-use-swagger.md).

Чтобы просмотреть синтаксис OData и доступные операторы сравнения, прочитайте [Операторы сравнения OData в службе поиска Azure](../search/search-query-odata-comparison-operators.md).
