---
title: Подключение ресурсов
description: Узнайте об выполнении операторских затрат ресурсов с помощью пользовательских поставщиков Azure для применения управления или конфигурации к другим типам ресурсов Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650413"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Обзор ресурсов пользовательских поставщиков Azure

Ресурс пользовательских провайдеров Azure является моделью расширяемости для типов ресурсов Azure. Это позволяет применять операции или управление в существующих ресурсах Azure в масштабе. Для получения дополнительной информации смотрите, [как пользовательские поставщики Azure могут расширить Azure](overview.md). В этой статье рассматриваются следующие вопросы:

- Какой ресурс на борту может сделать.
- Ресурс ы на посадочных ресурсах и как им пользоваться.
- Где найти руководства и образцы кода, чтобы начать работу.

> [!IMPORTANT]
> Пользовательские поставщики в настоящее время в публичном предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем ее для производственных нагрузок. Некоторые функции могут быть неподдерживаемыми или иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Что может сделать ресурс по найму?

Подобно [пользовательским ресурсам пользовательских поставщиков Azure,](./custom-providers-resources-endpoint-how-to.md)наончаемый ресурс определяет контракт, который будет прокси-запросов на "находку" до конечной точки. В отличие от пользовательских ресурсов, наонная посадка ресурсов не создает новый тип ресурса. Вместо этого он позволяет расширить существующие типы ресурсов. А анализ ресурсов работает с политикой Azure, поэтому управление и конфигурация ресурсов могут осуществляться в масштабе. Некоторые примеры рабочих процессов по посадке ресурсов:

- Устанавливайте и управляйте на виртуальных машинах.
- Загрузка и настройка по умолчанию на учетные записи хранения Azure.
- Включите базовые диагностические настройки в масштабе.

## <a name="resource-onboarding-basics"></a>Основы находки ресурсов

Вы настраиваете ресурс на настройку через пользовательских поставщиков Azure с помощью Microsoft.CustomProviders/resourceProviders и типов ресурсов Microsoft.CustomProviders/associations. Для включения в действие ресурса для пользовательского поставщика в процессе настройки создайте **ресурсType,** называемый «ассоциацией» с **routingType,** который включает в себя «Расширение». Microsoft.CustomProviders/associations и Microsoft.CustomProviders/resourceProviders не должны принадлежать к той же группе ресурсов.

Вот пример пользовательского поставщика Azure:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Свойство | Обязательно? | Описание
---|---|---
name | Да | Имя определения конечной точки. Для ресурса, находивающейся в посадке, название должно быть "ассоциации".
routingType | Да | Определяет тип договора с конечной точкой. Для посадки ресурсов действительными **routingTypes** являются "Прокси,Кэш, Расширение" и "Webhook, Cache, Extension".
endpoint | Да | Конечная точка для направления запросов. Это позволит обрабатывать ответ и любые побочные эффекты запроса.

После создания пользовательского поставщика с типом ресурсов ассоциаций можно настроить таргетинг с помощью Microsoft.CustomProviders/associations. Microsoft.CustomProviders/associations — это ресурс расширения, который может расширить любой другой ресурс Azure. При создании экземпляра Microsoft.CustomProviders/associations потребуется **свойство targetResourceId,** которое должно быть действительным Microsoft.CustomProviders/resourceProviders или Microsoft.Solutions/applications идентификатором ресурсов. В этих случаях запрос будет перенаправлен в тип ресурса ассоциаций на созданном вами экземпляре Microsoft.CustomProviders/resourceProviders.

> [!NOTE]
> Если идентификатор ресурса Microsoft.Solutions/applications предоставляется в качестве **целевогоресурсаResourceId,** то в управляемой группе ресурсов должен быть развернут идентификатор ресурсов Microsoft.Solutions/applications с именем "общественный".

Пример ассоциации пользовательских провайдеров Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Свойство | Обязательно? | Описание
---|---|---
целевойРесурсИд | Да | Идентификатор ресурсов Microsoft.CustomProviders/resourceProviders или Microsoft.Solutions/applications.

## <a name="how-to-use-resource-onboarding"></a>Как использовать ресурс на борту

Ресурсная настройка работает, расширяя другие ресурсы с ресурсом расширения Microsoft.CustomProviders/associations. В следующем примере запрос делается для виртуальной машины, но любой ресурс может быть расширен.

Во-первых, необходимо создать пользовательский ресурс поставщика с типом ресурсов ассоциаций. Это объявит URL-адрес обратного вызова, который будет использоваться при создании соответствующего ресурса Microsoft.CustomProviders/associations, который нацелен на пользовательского поставщика.

Пример Microsoft.CustomProviders/resourceProviders создает запрос:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

После создания пользовательского провайдера можно нацелиться на другие ресурсы и применить к ним побочные эффекты пользовательского провайдера.

Пример Microsoft.CustomProviders/ассоциации создают запрос:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Затем этот запрос будет перенаправлен в конечную точку, указанную в созданном вами пользовательском провайдере, на который ссылается **targetResourceId** в этой форме:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Конечная точка должна отвечать с `Content-Type` помощью приложения/json и действительного органа реагирования JSON. Поля, которые возвращаются под объектом **свойств** JSON, будут добавлены в ответ на возвращение ассоциации.

## <a name="getting-help"></a>Получение справки

Если у вас есть вопросы о разработке поставщиков пользовательских ресурсов Azure, попробуйте задать их в [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Подобный вопрос, возможно, уже был задан, поэтому перед его публикацией сначала проверьте наличие ответа. Чтобы быстрее получить ответ, добавьте к вопросу тег ```azure-custom-providers```!

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали о настраиваемых поставщиках. Смотрите эти статьи, чтобы узнать больше:

- [Учебник: Ресурсная посадка с пользовательскими поставщиками](./tutorial-resource-onboarding.md)
- [Учебник: Создавайте пользовательские действия и ресурсы в Azure](./tutorial-get-started-with-custom-providers.md)
- [Быстрый запуск: Создайте поставщика пользовательских ресурсов и разместите пользовательские ресурсы](./create-custom-provider.md)
- [Как: Добавление пользовательских действий в API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Как: Добавление пользовательских ресурсов в API Azure REST](./custom-providers-resources-endpoint-how-to.md)
