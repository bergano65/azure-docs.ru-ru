---
title: Справочник по кэшу пользовательских ресурсов
description: Пользовательская ссылка на кэш ресурсов для поставщиков пользовательских ресурсов Azure. В этой статье будут выполняться требования к конечным точкам реализации пользовательских ресурсов кэша.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650387"
---
# <a name="custom-resource-cache-reference"></a>Ссылка на пользовательский кэш ресурсов

В этой статье будут выполняться требования к конечным точкам реализации пользовательских ресурсов кэша. Если вы не знакомы с поставщиками пользовательских ресурсов Azure, смотрите [обзор поставщиков пользовательских ресурсов.](overview.md)

## <a name="how-to-define-a-cache-resource-endpoint"></a>Как определить конечную точку ресурса кэша

Прокси-ресурс может быть создан путем указания **routingType** на "Прокси, кэш".

Пример поставщика пользовательских ресурсов:

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

## <a name="building-proxy-resource-endpoint"></a>Создание конечная точка прокси-ресурса

**Конечная точка,** реализуемый **в конечном итоге** ресурса "Прокси, кэш", должна обрабатывать запрос и ответ для нового API в Azure. В этом случае **ресурсType** будет генерировать новый API `GET`ресурсов `DELETE` Azure для `PUT`, и для `GET` выполнения CRUD на одном ресурсе, а также для извлечения всех существующих ресурсов:

> [!NOTE]
> API Azure `PUT`будет генерировать методы `GET`запроса, и, `DELETE`но **конечная** `PUT` точка кэша требует только обработки и `DELETE`
> Мы рекомендовали, чтобы **конечная точка** также реализует `GET`.

### <a name="create-a-custom-resource"></a>Создание настраиваемого ресурса

Входящий запрос API Azure API:

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

Затем этот запрос будет направлен в **конечную точку** в форме:

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

Аналогичным образом, ответ с **конечной точки** затем пересылается клиенту. Ответ с конечной точки должен вернуться:

- Действительный документ объекта JSON. Все массивы и строки должны быть вложены под верхний объект.
- Заголовок `Content-Type` должен быть установлен на "приложение / Json; charset'utf-8".
- Поставщик пользовательских ресурсов перезавержает `name` `type`запрос и `id` поля.
- Поставщик пользовательских ресурсов вернет `properties` поля под объект только для конечных точек кэша.

**Конечная точка** Ответ:

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

`id` `type` Поля `name`и поля будут автоматически сгенерированы для пользовательского ресурса поставщиком пользовательских ресурсов.

Ответ поставщика пользовательских ресурсов Azure:

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

### <a name="remove-a-custom-resource"></a>Удаление настраиваемого ресурса

Входящий запрос API Azure API:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Затем этот запрос будет направлен в **конечную точку** в форме:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Аналогичным образом, ответ с **конечной точки** затем пересылается обратно клиенту. Ответ с конечной точки должен вернуться:

- Действительный документ объекта JSON. Все массивы и строки должны быть вложены под верхний объект.
- Заголовок `Content-Type` должен быть установлен на "приложение / Json; charset'utf-8".
- Поставщик пользовательских ресурсов Azure удаляет элемент из кэша только в случае возврата 200-уровней ответа. Даже если ресурса не существует, **конечная точка** должна вернуть ся204.

**Конечная точка** Ответ:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Ответ поставщика пользовательских ресурсов Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Извлечение настраиваемого ресурса

Входящий запрос API Azure API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Запрос **не** будет перенаправлен в **конечную точку.**

Ответ поставщика пользовательских ресурсов Azure:

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

### <a name="enumerate-all-custom-resources"></a>Перечислить все пользовательские ресурсы

Входящий запрос API Azure API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Этот запрос **не** будет перенаправлен в **конечную точку.**

Ответ поставщика пользовательских ресурсов Azure:

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

- [Обзор поставщиков пользовательских ресурсов Azure](overview.md)
- [Быстрый запуск: Создайте поставщика пользовательских ресурсов Azure и развернуть пользовательские ресурсы](./create-custom-provider.md)
- [Учебник: Создавайте пользовательские действия и ресурсы в Azure](./tutorial-get-started-with-custom-providers.md)
- [Как: Добавление пользовательских действий в API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Справка: Пользовательские ресурсы Прокси Ссылка](proxy-resource-endpoint-reference.md)
