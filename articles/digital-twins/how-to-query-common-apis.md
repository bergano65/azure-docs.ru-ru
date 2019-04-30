---
title: Общие шаблоны запросов Azure Digital Twins | Документация Майкрософт
description: Изучите общие шаблоны запросов API-интерфейсов управления Azure Digital Twins.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924742"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Как запросить API Azure Digital Twins для выполнения общих задач

В этой статье показаны шаблоны запросов, которые помогут вам реализовать общие сценарии для вашего экземпляра Azure Digital Twins. Предполагается, что ваш экземпляр Digital Twins уже запущен. Вы можете использовать любой клиент REST, например Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Запросы пространств и типов

В этом разделе показаны примеры запросов для получения дополнительной информации о подготовленных пространствах. Выполните аутентифицированные HTTP-запросы GET с помощью примеров запросов, заменив заполнители значениями из вашей конфигурации. 

- Получите сведения о пространствах, которые являются корневыми узлами.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Получите пространство по имени и включите устройства, датчики, вычисленные значения и значения датчиков. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Получите сведения о пространствах и устройстве или датчике, родительским элементом которых является заданный идентификатор пространства, на уровнях со 2 по 5 [относительно указанного пространства](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Получите пространство с заданным идентификатором и включите вычисленные значения и значения датчика.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Получите ключи свойств для определенного пространства.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Получите пространства с ключом свойства с именем *AreaInSqMeters*. Его значение — 30. Вы также можете выполнять операции со строками, например получать пространства, содержащие ключ свойства с `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Получите все имена с именем *Temperature* и связанными зависимостями и онтологиями.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Запросы ролей и назначений ролей

В этом разделе показаны некоторые запросы для получения дополнительной информации о ролях и их назначениях. 

- Получите все роли, поддерживаемые Azure Digital Twins.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Получите все назначения ролей в своем экземпляре Digital Twins. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Получите назначения ролей на конкретном пути.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Запросы для устройств

В этом разделе приведены некоторые примеры того, как вы можете использовать API-интерфейсы управления для получения конкретной информации об устройствах. Все вызовы API должны быть аутентифицированными HTTP-запросами GET.

- Получите сведения обо всех устройствах.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Найдите состояния всех устройств.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Получите сведения о конкретном устройстве.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Подключите все устройства к корневому пространству.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Подключите все устройства к пространствам на уровнях со 2 по 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Получите все устройства, напрямую подключенные к определенному идентификатору пространства.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Получите все устройства, подключенные к определенному пространству и его потомкам.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Получите все устройства, подключенные к потомкам пространства, исключая это пространство.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Подключите все устройства к прямым потомкам пространства.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Подключите все устройства к одному из предков пространства.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Получите все устройства, подключенные к потомкам пространства, уровень которых меньше или равен 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Получите все устройства, подключенные к пространствам, которые находятся на одном уровне с пространством с идентификатором *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Получите строку подключения Центра Интернета вещей для устройства.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Получите устройство с указанным идентификатором оборудования, включая подключенные датчики.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Получите датчики для определенных типов данных, в этом случае для типов *Motion* и *Temperature*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Запросы сопоставлений и определяемых пользователем функций 

- Получите все подготовленные сопоставления и их идентификаторы.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Получите подробную информацию о конкретном сопоставлении, включая связанные с ним пространства и определяемые пользователем функции.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Оцените сопоставление по отношению к датчику и включите ведение журнала в целях отладки. В выходных данных возвращенного HTTP-сообщения GET указано, относятся ли сопоставление и датчик к определенному типу данных. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Получите идентификатор определяемой пользователем функции. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Получите содержимое конкретной определяемой пользователем функции. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Запросы пользователей

В этом разделе показаны некоторые примеры запросов API для управления пользователями в Azure Digital Twins. Выполните запрос HTTP GET, заменив заполнители значениями из вашей конфигурации. 

- Получите список всех пользователей. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Получите конкретного пользователя.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Дальнейшие действия

Сведения об аутентификации с помощью API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).

Чтобы увидеть все конечные точки API, ознакомьтесь со статьей [Справочная документация о Swagger с Azure Digital Twins](./how-to-use-swagger.md).
