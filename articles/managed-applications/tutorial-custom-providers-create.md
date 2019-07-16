---
title: Создание и использование настраиваемых поставщиков Azure
description: Из этого руководства вы узнаете, как создать и использовать настраиваемого поставщика.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799133"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Создание конечной точки RESTful для настраиваемых поставщиков

Настраиваемые поставщики позволяют настраивать рабочие процессы в Azure. Настраиваемый поставщик представляет собой контракт между Azure и `endpoint`. Это руководство содержит описание процесса создания настраиваемого поставщика. Если вы не знакомы с настраиваемыми поставщиками Azure, перейдите к этой [обзорной статье](./custom-providers-overview.md).

Это руководство разделено на следующие разделы:

- что такое настраиваемый поставщик;
- определение пользовательских действий и ресурсов;
- развертывание настраиваемого поставщика.

Это руководство основано на следующих руководствах:

- [Создание конечной точки RESTful для настраиваемых поставщиков](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Создание настраиваемого поставщика

> [!NOTE]
> В этом руководстве не будет рассматриваться создание конечной точки. Следуйте [Руководству по созданию конечных точек RESTful](./tutorial-custom-providers-function-authoring.md), если у вас нет конечной точки RESTful.

После создания `endpoint` можно сгенерировать создание настраиваемого поставщика для генерации договора между ним и `endpoint`. Настраиваемый поставщик позволяет указывать список определений конечных точек.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Свойство | Обязательно | ОПИСАНИЕ
---|---|---
name | *да* | Имя определения конечной точки. Azure предоставит это имя через свой API-интерфейс в папке "/subscriptions/{ИД_подписки}/resourceGroups/{Имя_группы_ресурсов}/providers/Microsoft.CustomProviders/<br>resourceProviders/{Имя_поставщика ресурсов}/{Имя_определения_конечной_точки}"
routingType | *нет* | Определяет тип договора с `endpoint`. Если не указан, по умолчанию будет «Прокси».
endpoint | *да* | Конечная точка для направления запросов. Ответ будет обработан так же, как и любые побочные эффекты запроса.

В этом случае `endpoint` — это URL-адрес триггера функции Azure. `<yourapp>`, `<funcname>` и `<functionkey>` следует заменить значениями для созданной вами функции.

## <a name="defining-custom-actions-and-resources"></a>Определение пользовательских действий и ресурсов

Настраиваемый поставщик содержит список определений конечных точек, смоделированных в `actions` и `resourceTypes`. `actions` сопоставляется с настраиваемыми действиями, предоставляемыми настраиваемым поставщиком, а `resourceTypes` здесь — это настраиваемые ресурсы. В этом руководстве мы определяем, что настраиваемый поставщик с `action` вызывается `myCustomAction`, а `resourceType` вызывается `myCustomResources`.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

Замените `endpoint` URL-адресом триггера из функции, созданной ранее в предыдущем руководстве.

## <a name="deploying-the-custom-provider"></a>Развертывание настраиваемого поставщика

> [!NOTE]
> `endpoint` следует заменить на URL-адрес функции.

Указанный выше настраиваемый поставщик может быть развернут с использованием шаблона Azure Resource Manager.

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>Использование пользовательских действий и ресурсов

После создания настраиваемого поставщика можно использовать новые API-интерфейсы Azure. В следующем разделе объясняется, как вызвать и использовать настраиваемый поставщик.

### <a name="custom-actions"></a>Настраиваемые действия

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` и `{resourceGroupName}` нужно заменить значениями subscription и resourceGroup, где должен быть развернут пользовательский поставщик.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Параметр | Обязательно | ОПИСАНИЕ
---|---|---
action | *да* | Имя действия, определенного в созданном настраиваемом поставщике.
ids | *да* | Идентификатор ресурса созданного настраиваемого поставщика.
request-body | *нет* | Текст запроса, который будет отправляться `endpoint`.

# <a name="templatetabtemplate"></a>[Шаблон](#tab/template)

Отсутствует.

---

### <a name="custom-resources"></a>Настраиваемые ресурсы

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` и `{resourceGroupName}` нужно заменить значениями subscription и resourceGroup, где должен быть развернут пользовательский поставщик.

Создание настраиваемого ресурса:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Параметр | Обязательно | ОПИСАНИЕ
---|---|---
full-object | *да* | Указывает, что объект свойств включает в себя другие параметры, такие как местоположение, теги, sku и/или план.
id | *да* | Идентификатор ресурса настраиваемого ресурса. Он должен существовать вне созданного настраиваемого ресурса.
properties | *да* | Текст запроса, который будет отправляться `endpoint`.

Удаление настраиваемого ресурса Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Параметр | Обязательно | ОПИСАНИЕ
---|---|---
id | *да* | Идентификатор ресурса настраиваемого ресурса. Он должен существовать вне созданного настраиваемого ресурса.

Получение настраиваемого ресурса Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Параметр | Обязательно | ОПИСАНИЕ
---|---|---
id | *да* | Идентификатор ресурса настраиваемого ресурса. Он должен существовать вне созданного настраиваемого ресурса.

# <a name="templatetabtemplate"></a>[Шаблон](#tab/template)

Пример шаблона Диспетчера ресурсов Azure:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Параметр | Обязательно | ОПИСАНИЕ
---|---|---
resourceTypeName | *да* | `name` из *resourceType*, определенного в настраиваемом поставщике.
resourceProviderName | *да* | Имя экземпляра настраиваемого поставщика.
customResourceName | *да* | Имя настраиваемого ресурса.

---

> [!NOTE]
> После завершения развертывания и использования настраиваемого поставщика не забудьте очистить все созданные ресурсы, включая функцию Azure.

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали о настраиваемых поставщиках. Перейдите к следующей статье, чтобы создать настраиваемый поставщик.

- [Практическое руководство. Добавление настраиваемых действий в Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Практическое руководство. Добавление настраиваемых ресурсов в Azure REST API](./custom-providers-resources-endpoint-how-to.md)
