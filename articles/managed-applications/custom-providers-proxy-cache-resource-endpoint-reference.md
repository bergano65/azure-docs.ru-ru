---
title: Справочник по кэшу пользовательских ресурсов
description: Справочник по кэша настраиваемого ресурса для поставщиков ресурсов Azure Custom. В этой статье будет проходить через требования для конечных точек, реализация кэша настраиваемые ресурсы.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6a3d570d9695516a293b601b3d34c2bcba6b058d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478892"
---
# <a name="custom-resource-cache-reference"></a>Справочник по кэша настраиваемого ресурса

В этой статье будет проходить через требования для конечных точек, реализация кэша настраиваемые ресурсы. Если вы не знакомы с пользовательскими поставщиками ресурсов Azure, см. в разделе [Обзор на собственных поставщиков ресурсов](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Как определить конечную точку кэша ресурсов

Прокси-ресурс можно создать, указав **routingType** «Прокси-сервер, кэша».

Пример собственного поставщика ресурсов:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Создание конечной точки ресурса прокси-сервера

**Конечной точки** , реализующий ресурс «Прокси-сервера, кэш» **конечной точки** необходимо обрабатывать запрос и ответ для нового API в Azure. В этом случае **resourceType** создаст новый ресурс Azure API для `PUT`, `GET`, и `DELETE` для выполнения CRUD в одном ресурсе, а также `GET` для получения всех существующих ресурсов:

> [!NOTE]
> Azure API создаст методы запроса `PUT`, `GET`, и `DELETE`, но кэш **конечной точки** достаточно для обработки `PUT` и `DELETE`.
> Мы рекомендуем **конечной точки** также реализует `GET`.

### <a name="create-a-custom-resource"></a>Создание настраиваемого ресурса

Azure API входящего запроса:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Этот запрос будет затем перенаправлен к **конечной точки** в форме:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Аналогичным образом, ответ от **конечной точки** затем передается обратно клиенту. Вы получите ответ от конечной точки:

- Документ допустимый объект JSON. Все массивы и строки должно быть размещено под верхний объект.
- `Content-Type` Заголовок должен быть установлен в «application/json; CharSet = utf-8 ".
- Для собственного поставщика ресурсов приведет к перезаписи `name`, `type`, и `id` полей для запроса.
- Поставщик настраиваемого ресурса возвратит только поля в разделе `properties` объекта для конечной точки кэша.

**Конечная точка** ответа:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

`name`, `id`, И `type` поля будут автоматически создаваться для настраиваемого ресурса с собственных поставщиков ресурсов.

Ответ поставщика Azure настраиваемого ресурса:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Удалить настраиваемый ресурс

Azure API входящего запроса:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Этот запрос будет затем перенаправлен к **конечной точки** в форме:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Аналогичным образом, ответ от **конечной точки** затем передается обратно клиенту. Вы получите ответ от конечной точки:

- Документ допустимый объект JSON. Все массивы и строки должно быть размещено под верхний объект.
- `Content-Type` Заголовок должен быть установлен в «application/json; CharSet = utf-8 ".
- Настраиваемый поставщик ресурсов Azure будет только удалить элемент из кэша, если возвращается ответ 200-уровень. Даже если ресурс не существует, **конечной точки** должен возвращать 204.

**Конечная точка** ответа:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Ответ поставщика Azure настраиваемого ресурса:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Получение пользовательских ресурсов

Azure API входящего запроса:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Запрос будет **не** переадресовываться **конечная точка**.

Ответ поставщика Azure настраиваемого ресурса:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Перечислить все настраиваемые ресурсы

Azure API входящего запроса:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Этот запрос будет **не** переадресовываться **конечная точка**.

Ответ поставщика Azure настраиваемого ресурса:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о Azure собственных поставщиков ресурсов](./custom-providers-overview.md)
- [Учебник. Создание пользовательского поставщика ресурсов Azure и развертывание настраиваемых ресурсов](./create-custom-provider.md)
- [Практическое руководство. Добавление настраиваемых действий в Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Общие сведения Справочник по настраиваемого ресурса прокси-сервера](./custom-providers-proxy-resource-endpoint-reference.md)
