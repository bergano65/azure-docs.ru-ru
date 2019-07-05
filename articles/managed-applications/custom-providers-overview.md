---
title: Общие сведения о Azure собственных поставщиков ресурсов
description: Узнайте о поставщиков ресурсов Azure Custom и способы расширения плоскости Azure API в соответствии с рабочих процессов.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: f418cd6c5470740ce123448ddbbe54cb6e89dabe
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67475938"
---
# <a name="azure-custom-resource-providers-overview"></a>Общие сведения о поставщиках настраиваемых ресурсов Azure

Azure настраиваемые поставщики ресурсов — это платформа расширяемости в Azure. Он позволяет определять пользовательские API, который может использоваться для обогащения по умолчанию взаимодействие с Azure. В настоящей документации описывается:

- Как создавать и развертывать поставщика ресурсов Azure Custom.
- Как использовать настраиваемые поставщики ресурсов на Azure для расширения существующих рабочих процессов.
- Где можно найти руководства и примеры кода, чтобы приступить к работе.

![Обзор пользовательского поставщика](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Настраиваемые поставщики сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Что можно делать собственных поставщиков ресурсов

Ниже приведены некоторые примеры можно добиться с поставщиками ресурсов Azure Custom.

- Расширения REST API диспетчера ресурсов Azure, включая внутренние и внешние службы.
- Включите пользовательские сценарии на основе существующих рабочих процессов для Azure.
- Настройка управления шаблонов Azure Resource Manager и эффект.

## <a name="what-is-a-custom-resource-provider"></a>Что такое собственный поставщик ресурсов

Поставщики ресурсов Azure Custom производится путем создания контракта между Azure и конечной точки. В этом контракте определен список новых ресурсов и действий через новый ресурс, **Microsoft.CustomProviders/resourceProviders**. Для собственного поставщика ресурсов, то будет предоставлять эти новые интерфейсы API в Azure. Поставщики ресурсов Azure пользовательские состоят из трех частей: собственного поставщика ресурсов **конечные точки**и настраиваемые ресурсы.

## <a name="how-to-build-custom-resource-providers"></a>Способы создания собственных поставщиков ресурсов

Поставщики ресурсов представляют собой список всех контрактов между Azure и конечными точками. Этот контракт описывает, как Azure должна взаимодействовать с конечной точкой. Действует поставщик ресурсов, такие как прокси-сервер и будет пересылать запросов и ответов из указанного **конечная точка**. Поставщик ресурсов можно указать два типа контрактов: [ **выводимое** ](./custom-providers-resources-endpoint-how-to.md) и [ **действия**](./custom-providers-action-endpoint-how-to.md). Их можно включить в определения конечных точек. Определение конечной точки состоит из трех полей: **имя**, **routingType**, и **конечная точка**.

Образец конечной точки:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Свойство | Обязательно для заполнения | ОПИСАНИЕ
---|---|---
name | *Да* | Имя определения конечной точки. Azure будет предоставлять это имя через API в разделе "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders / {resourceProviderName} / {endpointDefinitionName} "
routingType | *Нет* | Определяет тип контракта с **конечная точка**. Если не указан, по умолчанию будет «Прокси».
endpoint | *Да* | Конечная точка для перенаправления запросов. Это будет обрабатывать ответ, а также никаких побочных эффектов запроса.

### <a name="building-custom-resources"></a>Создание настраиваемых ресурсов

**Выводимое** описывают новые пользовательские ресурсы, которые добавляются в Azure. Они предоставляют основные методы RESTful CRUD. См. в разделе [дополнительная информация о создании настраиваемых ресурсов](./custom-providers-resources-endpoint-how-to.md)

Пример настраиваемого поставщика ресурсов с **выводимое**:

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
  "location": "eastus"
}
```

API, добавляемые в Azure, выше примера:

HttpMethod | Пример URI | Описание
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | Вызов Azure REST API для создания нового ресурса.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | Вызов Azure REST API для удаления существующего ресурса.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | Вызов Azure REST API для извлечения существующего ресурса.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? api-version = 2018-09-01-preview | Вызов Azure REST API для извлечения списка существующих ресурсов.

### <a name="building-custom-actions"></a>Создание настраиваемых действий

**Действия** описания новых действий, которые добавляются в Azure. Они предоставляются поверх поставщика ресурсов или вложен в узел **resourceType**. См. в разделе [дополнительная информация о создании настраиваемых действий](./custom-providers-action-endpoint-how-to.md)

Пример настраиваемого поставщика ресурсов с **действия**:

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
  "location": "eastus"
}
```

API, добавляемые в Azure, выше примера:

HttpMethod | Пример URI | Описание
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? api-version = 2018-09-01-preview | При вызове Azure REST API активировать действие.

## <a name="looking-for-help"></a>Требуется помощь

Если у вас есть вопросы о разработке поставщика ресурсов Azure Custom, попробуйте задать вопрос [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Аналогично вопрос может уже были запрос и ответ, поэтому следите сначала перед учета. Добавьте тег ```azure-custom-providers``` получить быстрый ответ!

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали о настраиваемых поставщиков. Перейдите к следующей статье, чтобы создать пользовательский поставщик.

- [Учебник. Создание пользовательского поставщика ресурсов Azure и развертывание настраиваемых ресурсов](./create-custom-provider.md)
- [Практическое руководство. Добавление настраиваемых действий в Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Практическое руководство. Добавление настраиваемых ресурсов в Azure REST API](./custom-providers-resources-endpoint-how-to.md)
