---
title: Добавление настраиваемых действий в Azure REST API
description: Узнайте, как добавить пользовательские действия в API Azure REST. В этой статье будут разработаны требования и рекомендации для конечных точек, которые хотят реализовать пользовательские действия.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650400"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Добавление пользовательских действий в API Azure REST

В этой статье будут изыскваны требования и рекомендации по созданию конечных точек поставщика пользовательских ресурсов Azure, которые реализуют пользовательские действия. Если вы не знакомы с поставщиками пользовательских ресурсов Azure, смотрите [обзор поставщиков пользовательских ресурсов.](overview.md)

## <a name="how-to-define-an-action-endpoint"></a>Как определить конечную точку действия

**Конечная точка** — это URL-адрес, который указывает на службу, которая реализует базовый контракт между ней и Azure. Конечная точка определяется в пользовательском поставщике ресурсов и может быть любым общедоступным URL- адресом. В приведенном ниже `myCustomAction` примере `endpointURL`есть **действие,** называемое реализованным .

Пример **Ресурсоснабжающей**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
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

## <a name="building-an-action-endpoint"></a>Создание конечная точка действия

**Конечная точка,** реализуемый в **действии,** должна обрабатывать запрос и ответ для нового API в Azure. При создании пользовательского поставщика ресурсов с **действием** он будет генерировать новый набор AAP в Azure. В этом случае действие создаст новый API `POST` действия Azure для вызовов:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Входящий запрос API Azure API:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Затем этот запрос будет направлен в **конечную точку** в форме:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Аналогичным образом, ответ с **конечной точки** затем пересылается клиенту. Ответ с конечной точки должен вернуться:

- Действительный документ объекта JSON. Все массивы и строки должны быть вложены под верхний объект.
- Заголовок `Content-Type` должен быть установлен на "приложение / Json; charset'utf-8".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Ответ поставщика пользовательских ресурсов Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Вызов пользовательских действий

Существует два основных способа вызова пользовательского действия от поставщика пользовательских ресурсов:

- Azure CLI
- Шаблоны Azure Resource Manager

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Параметр | Обязательно | Описание
---|---|---
action | *Да* | Название действия определено в **ResourceProvider.**
ids | *Да* | Идентификатор ресурса **ResourceProvider**.
request-body | *Нет* | Орган запроса, который будет отправлен в **конечную точку.**

### <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

> [!NOTE]
> Действия имеют ограниченную поддержку в шаблонах менеджеров ресурсов Azure. Для того, чтобы действие было вызвано внутри [`list`](../templates/template-functions-resource.md#list) шаблона, оно должно содержать префикс в его названии.

Пример **Ресурсоснабжающей** со списком действий:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Пример шаблона Диспетчера ресурсов Azure:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Параметр | Обязательно | Описание
---|---|---
resourceIdentifier | *Да* | Идентификатор ресурса **ResourceProvider**.
версия_API | *Да* | Версия API времени выполнения ресурса. Это всегда должно быть "2018-09-01-предварительный просмотр".
functionValues | *Нет* | Орган запроса, который будет отправлен в **конечную точку.**

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор поставщиков пользовательских ресурсов Azure](overview.md)
- [Быстрый запуск: Создайте поставщика пользовательских ресурсов Azure и развернуть пользовательские ресурсы](./create-custom-provider.md)
- [Учебник: Создавайте пользовательские действия и ресурсы в Azure](./tutorial-get-started-with-custom-providers.md)
- [Как: Добавление пользовательских ресурсов в API Azure REST](./custom-providers-resources-endpoint-how-to.md)
