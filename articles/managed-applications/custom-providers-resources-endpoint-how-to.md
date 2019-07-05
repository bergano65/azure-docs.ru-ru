---
title: Добавление настраиваемых ресурсов в Azure REST API
description: Узнайте, как добавить пользовательские ресурсы в Azure REST API. В этой статье показано, как требования и рекомендации для конечных точек, которые желают реализовать настраиваемые ресурсы.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b94d59b55a62797e142768dc84ec499d714bd067
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479022"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Добавление настраиваемых ресурсов в Azure REST API

В этой статье, рассмотрим требования и рекомендации по созданию пользовательских поставщиков ресурсов Azure конечные точки, реализующие настраиваемые ресурсы. Если вы не знакомы с пользовательскими поставщиками ресурсов Azure, см. в разделе [Обзор на собственных поставщиков ресурсов](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Как определить конечную точку ресурса

**Конечной точки** является URL-адресом, который указывает службу, которая реализует базовый контракт между ним и Azure. Конечная точка определяется в собственных поставщиков ресурсов и может быть любой общедоступный URL-адрес. В следующем примере имеет **resourceType** вызывается `myCustomResource` реализуется `endpointURL`.

Пример **ResourceProvider**:

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

## <a name="building-a-resource-endpoint"></a>Создание конечной точки ресурса

**Конечной точки** , реализующий **resourceType** необходимо обрабатывать запрос и ответ для нового API в Azure. Когда собственный поставщик ресурсов с **resourceType** будет создан, он создаст новый набор API-интерфейсов в Azure. В этом случае **resourceType** создаст новый ресурс Azure API для `PUT`, `GET`, и `DELETE` для выполнения CRUD в одном ресурсе производительны `GET` для получения всех существующих ресурсов:

Управлять один ресурс (`PUT`, `GET`, и `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Получить все ресурсы (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Для настраиваемых ресурсов собственных поставщиков ресурсов предоставляют два типа **routingTypes**: "`Proxy`«и»`Proxy, Cache`«.

### <a name="proxy-routing-type"></a>Тип маршрутизации прокси-сервера

"`Proxy`" **RoutingType** учетные записи-посредники, все методы, для запроса **конечной точки** указан в собственных поставщиков ресурсов. Когда следует использовать "`Proxy`«:

- Необходим полный контроль над ответа.
- Интеграция систем с существующими ресурсами.

Дополнительные сведения о "`Proxy`" ресурсы, см. в разделе [ссылку настраиваемого ресурса прокси-сервера](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>Тип маршрутизации кэша прокси-сервера

"`Proxy, Cache`" **RoutingType** только для учетных записей-посредников `PUT` и `DELETE` методы для запроса **конечной точки** указан в собственных поставщиков ресурсов. Поставщик настраиваемого ресурса автоматически возвращает `GET` запросы зависимости от того, что он сохранен в кэше. Если настраиваемый ресурс помечен с кэшем, собственных поставщиков ресурсов также добавьте / перезаписать поля в ответе, чтобы сделать API-интерфейсы Azure совместимым. Когда следует использовать "`Proxy, Cache`«:

- Создание новой системы, не имеет существующих ресурсов.
- Работать с существующей экосистемы Azure.

Дополнительные сведения о "`Proxy, Cache`" ресурсы, см. в разделе [Справочник по кэша настраиваемого ресурса](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Создание настраиваемого ресурса

Существует два основных способа создания настраиваемого ресурса из собственный поставщик ресурсов:

- Инфраструктура CLI Azure
- Шаблоны Azure Resource Manager

### <a name="azure-cli"></a>Инфраструктура CLI Azure

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

Параметр | Обязательно для заполнения | Описание
---|---|---
— full объект | *Да* | Указывает, что объект свойств содержит другие параметры, такие как расположение, теги, sku или плана.
id | *Да* | Идентификатор ресурса настраиваемого ресурса. Это должна существовать за пределами класса **ResourceProvider**
properties | *Да* | Текст запроса, который будет отправляться **конечная точка**.

Удалите пользовательский ресурс Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Параметр | Обязательно для заполнения | Описание
---|---|---
id | *Да* | Идентификатор ресурса настраиваемого ресурса. Это должна существовать за пределами класса **ResourceProvider**.

Получение пользовательского ресурса Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Параметр | Обязательно для заполнения | Описание
---|---|---
id | *Да* | Идентификатор ресурса настраиваемого ресурса. Это должна существовать за пределами класса **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

> [!NOTE]
> Ресурсы требуют наличия соответствующей ответ `id`, `name`, и `type` из **конечная точка**.

Шаблоны Azure Resource Manager требуется, чтобы `id`, `name`, и `type` возвращаются правильно из подчиненных конечной точки. Ответ возвращаемый ресурс должен быть в форме:

Пример **конечной точки** ответа:

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

Пример шаблона Azure Resource Manager:

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

Параметр | Обязательно для заполнения | Описание
---|---|---
resourceTypeName | *Да* | **Имя** из **resourceType** определенные в пользовательского поставщика.
resourceProviderName | *Да* | Имя экземпляра поставщика настраиваемого ресурса.
customResourceName | *Да* | Имя настраиваемого ресурса.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о Azure собственных поставщиков ресурсов](./custom-providers-overview.md)
- [Учебник. Создание пользовательского поставщика ресурсов Azure и развертывание настраиваемых ресурсов](./create-custom-provider.md)
- [Практическое руководство. Добавление настраиваемых действий в Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Общие сведения Справочник по настраиваемого ресурса прокси-сервера](./custom-providers-proxy-resource-endpoint-reference.md)
- [Общие сведения Справочник по кэша настраиваемого ресурса](./custom-providers-proxy-cache-resource-endpoint-reference.md)
