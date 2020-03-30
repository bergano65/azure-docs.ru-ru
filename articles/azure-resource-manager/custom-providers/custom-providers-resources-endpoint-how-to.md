---
title: Добавление настраиваемых ресурсов в Azure REST API
description: Узнайте, как добавить пользовательские ресурсы в API Azure REST. В этой статье будут разработаны требования и рекомендации для конечных точек, которые хотят реализовать пользовательские ресурсы.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650530"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Добавление пользовательских ресурсов в API Azure REST

В этой статье будут изыскваны требования и рекомендации по созданию конечных точек поставщика пользовательских ресурсов Azure, в которых реализуется пользовательские ресурсы. Если вы не знакомы с поставщиками пользовательских ресурсов Azure, смотрите [обзор поставщиков пользовательских ресурсов.](overview.md)

## <a name="how-to-define-a-resource-endpoint"></a>Как определить конечную точку ресурса

**Конечная точка** — это URL-адрес, который указывает на службу, которая реализует базовый контракт между ней и Azure. Конечная точка определяется в пользовательском поставщике ресурсов и может быть любым общедоступным URL- адресом. В приведенном ниже примере `endpointURL`есть **ресурсType,** называемый `myCustomResource` реализованным .

Пример **Ресурсоснабжающей**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Создание конечная точка ресурса

**Конечная точка,** реализуемый **ресурсомType,** должна обрабатывать запрос и ответ для нового API в Azure. При создании пользовательского поставщика ресурсов с **помощью resourceType** он будет генерировать новый набор AI в Azure. В этом случае **ресурсType** будет генерировать новый API `GET`ресурсов `DELETE` Azure для `PUT`, и для `GET` выполнения CRUD на одном ресурсе, а также для извлечения всех существующих ресурсов:

Манипулируйте`PUT`единым ресурсом (, `GET`и): `DELETE`

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Извлекать все`GET`ресурсы ( ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Для пользовательских ресурсов поставщики пользовательских ресурсов предлагают два`Proxy`типа`Proxy, Cache` **routingTypes**: « и «

### <a name="proxy-routing-type"></a>прокси тип реуктора

Прокси-запросы`Proxy` **routingType** все методы запроса до **точки конечных** данных, указанные в пользовательском поставщике ресурсов. Когда использовать`Proxy`" ":

- Необходим полный контроль над ответом.
- Интеграция систем с существующими ресурсами.

Подробнее о`Proxy`ресурсах читайте [в справке о прокси-сервере пользовательского ресурса](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>тип прокси-кэша

"Только`Proxy, Cache` **прокси-запросы routingType** `PUT` и `DELETE` запрос методов в **конечную точку,** указанную в пользовательском поставщике ресурсов. Поставщик пользовательских ресурсов `GET` автоматически возвращает запросы в зависимости от того, что он хранит в своем кэше. Если пользовательский ресурс отмечен кэшом, поставщик пользовательских ресурсов также добавит /перезавернет поля в ответ, чтобы сделать AI Azure совместимыми. Когда использовать`Proxy, Cache`" ":

- Создание новой системы, которая не имеет существующих ресурсов.
- Работайте с существующей экосистемой Azure.

Подробнее о`Proxy, Cache`ресурсах читайте [в справке из пользовательского кэша ресурсов](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Создание пользовательского ресурса

Существует два основных способа создания пользовательского ресурса от пользовательского поставщика ресурсов:

- Azure CLI
- Шаблоны Azure Resource Manager

### <a name="azure-cli"></a>Azure CLI

Создание настраиваемого ресурса:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Параметр | Обязательно | Описание
---|---|---
full-object | *Да* | Указывает, что объект свойств включает в себя другие параметры, такие как местоположение, теги, sku и/или план.
идентификатор | *Да* | Идентификатор ресурса настраиваемого ресурса. Это должно существовать вне **ResourceProvider**
properties | *Да* | Орган запроса, который будет отправлен в **конечную точку.**

Удаление настраиваемого ресурса Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Параметр | Обязательно | Описание
---|---|---
идентификатор | *Да* | Идентификатор ресурса настраиваемого ресурса. Это должно существовать вне **ResourceProvider**.

Получение настраиваемого ресурса Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Параметр | Обязательно | Описание
---|---|---
идентификатор | *Да* | Идентификатор ресурса настраиваемого ресурса. Это должно существовать вне **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

> [!NOTE]
> Ресурсы требуют, чтобы `id`ответ `name`содержал `type` соответствующий, и с **точки зрения.**

Шаблоны диспетчера ресурсов `id`Azure требуют, `name`что, и `type` возвращаются правильно из нисходящем конце. Ответ на возврат ресурсов должен быть в форме:

Пример **ответа на конечную точку:**

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Пример шаблона Диспетчера ресурсов Azure:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Параметр | Обязательно | Описание
---|---|---
resourceTypeName | *Да* | **Название** **ресурсаТип** определяется в пользовательском провайдере.
resourceProviderName | *Да* | Имя пользовательского поставщика ресурсов.
customResourceName | *Да* | Имя настраиваемого ресурса.

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор поставщиков пользовательских ресурсов Azure](overview.md)
- [Быстрый запуск: Создайте поставщика пользовательских ресурсов Azure и развернуть пользовательские ресурсы](./create-custom-provider.md)
- [Учебник: Создавайте пользовательские действия и ресурсы в Azure](./tutorial-get-started-with-custom-providers.md)
- [Как: Добавление пользовательских действий в API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Справка: Пользовательские ресурсы Прокси Ссылка](proxy-resource-endpoint-reference.md)
- [Справка: Ссылка на пользовательский кэш ресурсов](proxy-cache-resource-endpoint-reference.md)
