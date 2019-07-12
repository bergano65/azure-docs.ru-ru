---
title: Справочник по прокси-серверу пользовательских ресурсов
description: Ссылка на прокси-сервера настраиваемых ресурсов для поставщиков ресурсов Azure Custom. В этой статье будет проходить через требования для конечных точек, реализация настраиваемые ресурсы прокси-сервера.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: fe470165d11b24ffb1df704ecaa9804663fc9e5c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796031"
---
# <a name="custom-resource-proxy-reference"></a>Справочник по настраиваемого ресурса прокси-сервера

В этой статье будет проходить через требования для конечных точек, реализация настраиваемые ресурсы прокси-сервера. Если вы не знакомы с пользовательскими поставщиками ресурсов Azure, см. в разделе [Обзор на собственных поставщиков ресурсов](./custom-providers-overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Как определить конечную точку прокси-сервера ресурсов

Прокси-ресурс можно создать, указав **routingType** «Прокси-сервер».

Пример собственного поставщика ресурсов:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
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

**Конечной точки** , реализующий ресурс «Прокси» **конечной точки** необходимо обрабатывать запрос и ответ для нового API в Azure. В этом случае **resourceType** создаст новый ресурс Azure API для `PUT`, `GET`, и `DELETE` для выполнения CRUD в одном ресурсе, а также `GET` для получения всех существующих ресурсов.

> [!NOTE]
> `id`, `name`, И `type` поля не являются обязательными, но необходимые для интеграции настраиваемого ресурса с существующей экосистемы Azure.

Пример ресурсов:

``` JSON
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

Ссылка на параметр:

Свойство | Образец | Описание
---|---|---
name | «{myCustomResourceName}» | Имя настраиваемого ресурса.
type | "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}" | Пространство имен типа ресурса.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {myCustomResourceName} " | Идентификатор ресурса.

### <a name="create-a-custom-resource"></a>Создание настраиваемого ресурса

Azure API входящего запроса:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Конечная точка** ответа:

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

Аналогичным образом ответ от **конечной точки** затем передается обратно клиенту. Вы получите ответ от конечной точки:

- Допустимый документ объект JSON. Все массивы и строки должно быть размещено под верхний объект.
- `Content-Type` Заголовок должен быть установлен в «application/json; CharSet = utf-8 ".

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

Этот запрос будет затем перенаправлен к **конечной точки** в форме:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Аналогичным образом, ответ от **конечной точки** затем передается обратно клиенту. Вы получите ответ от конечной точки:

- Документ допустимый объект JSON. Все массивы и строки должно быть размещено под верхний объект.
- `Content-Type` Заголовок должен быть установлен в «application/json; CharSet = utf-8 ".

**Конечная точка** ответа:

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

Этот запрос будет затем перенаправлен к **конечной точки** в форме:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Аналогичным образом, ответ от **конечной точки** затем передается обратно клиенту. Вы получите ответ от конечной точки:

- Документ допустимый объект JSON. Все массивы и строки должно быть размещено под верхний объект.
- `Content-Type` Заголовок должен быть установлен в «application/json; CharSet = utf-8 ".
- Следует поместить в список ресурсов верхнего уровня `value` свойство.

**Конечная точка** ответа:

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

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о Azure собственных поставщиков ресурсов](./custom-providers-overview.md)
- [Краткое руководство Создание пользовательского поставщика ресурсов Azure и развертывание настраиваемых ресурсов](./create-custom-provider.md)
- [Учебник. Создание пользовательских действий и ресурсов в Azure](./tutorial-custom-providers-101.md)
- [Практическое руководство. Добавление настраиваемых действий в Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Общие сведения Справочник по кэша настраиваемого ресурса](./custom-providers-proxy-cache-resource-endpoint-reference.md)
