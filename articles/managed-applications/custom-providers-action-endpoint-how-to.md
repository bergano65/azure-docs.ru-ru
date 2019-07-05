---
title: Добавление настраиваемых действий Azure REST API
description: Сведения о добавлении пользовательских действий в Azure REST API. В этой статье показано, как требования и рекомендации для конечных точек, которые желают реализовать пользовательские действия.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 1bfc0be81d42e922c47755543fb65aa413ec73a9
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478762"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Добавление настраиваемых действий в Azure REST API

В этой статье, рассмотрим требования и рекомендации по созданию пользовательских поставщиков ресурсов Azure конечные точки, реализующие настраиваемые действия. Если вы не знакомы с пользовательскими поставщиками ресурсов Azure, см. в разделе [Обзор на собственных поставщиков ресурсов](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Как определить конечную точку действие

**Конечной точки** является URL-адресом, который указывает службу, которая реализует базовый контракт между ним и Azure. Конечная точка определяется в собственных поставщиков ресурсов и может быть любой общедоступный URL-адрес. В следующем примере имеет **действие** вызывается `myCustomAction` реализуется `endpointURL`.

Пример **ResourceProvider**:

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

## <a name="building-an-action-endpoint"></a>Создание конечной точки действия

**Конечной точки** , реализующий **действие** необходимо обрабатывать запрос и ответ для нового API в Azure. Когда собственный поставщик ресурсов с **действие** будет создан, он создаст новый набор API-интерфейсов в Azure. В этом случае действие создаст новое действие Azure API для `POST` вызовы:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API входящего запроса:

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

Этот запрос будет затем перенаправлен к **конечной точки** в форме:

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

Аналогичным образом, ответ от **конечной точки** затем передается обратно клиенту. Вы получите ответ от конечной точки:

- Документ допустимый объект JSON. Все массивы и строки должно быть размещено под верхний объект.
- `Content-Type` Заголовок должен быть установлен в «application/json; CharSet = utf-8 ".

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

Ответ поставщика Azure настраиваемого ресурса:

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

## <a name="calling-a-custom-action"></a>Вызов настраиваемого действия

Существует два основных способа вызова пользовательских действий из собственный поставщик ресурсов:

- Инфраструктура CLI Azure
- Шаблоны Azure Resource Manager

### <a name="azure-cli"></a>Инфраструктура CLI Azure

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

Параметр | Обязательно для заполнения | Описание
---|---|---
action | *Да* | Имя действия, определенные в **ResourceProvider**.
идентификаторы | *Да* | Идентификатор ресурса **ResourceProvider**.
текст запроса | *Нет* | Текст запроса, который будет отправляться **конечная точка**.

### <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

> [!NOTE]
> Действия имеют ограниченную поддержку в шаблонах Azure Resource Manager. Чтобы действие, которое необходимо вызвать внутри шаблона, он должен содержать [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) префикс в имени.

Пример **ResourceProvider** с действие списка:

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

Пример шаблона Azure Resource Manager:

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

Параметр | Обязательно для заполнения | Описание
---|---|---
Идентификатор_ресурса | *Да* | Идентификатор ресурса **ResourceProvider**.
apiVersion | *Да* | Версия API среды выполнения ресурса. Это всегда должно быть «2018-09-01-preview».
functionValues | *Нет* | Текст запроса, который будет отправляться **конечная точка**.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о Azure собственных поставщиков ресурсов](./custom-providers-overview.md)
- [Учебник. Создание пользовательского поставщика ресурсов Azure и развертывание настраиваемых ресурсов](./create-custom-provider.md)
- [Практическое руководство. Добавление настраиваемых ресурсов в Azure REST API](./custom-providers-resources-endpoint-how-to.md)
