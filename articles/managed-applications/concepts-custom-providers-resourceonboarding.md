---
title: Адаптация ресурсов настраиваемых поставщиков Azure
description: Сведения о адаптации ресурсов с помощью настраиваемых поставщиков Azure для применения управления или настройки к другим типам ресурсов Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609155"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Обзор адаптации ресурсов настраиваемых поставщиков Azure

Адаптация ресурсов настраиваемых поставщиков Azure — это модель расширяемости для типов ресурсов Azure. Она позволяет применять операции или управление в существующих ресурсах Azure в нужном масштабе. Дополнительные сведения см. в статье [как настраиваемые поставщики Azure могут расширить Azure](./custom-providers-overview.md). В этой документации описывается следующее:

- Возможности адаптации ресурсов.
- Основные сведения о адаптации ресурсов и способах ее использования.
- Где найти руководства и примеры кода, чтобы приступить к работе.

> [!IMPORTANT]
> В настоящее время настраиваемые поставщики находятся в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Возможности адаптации ресурсов

Как и настраиваемые [ресурсы настраиваемого поставщика Azure](./custom-providers-resources-endpoint-how-to.md), адаптация ресурсов определяет контракт, который будет выполнять запросы прокси-сервера к конечной точке. В отличие от настраиваемых ресурсов, при адаптации ресурсов не создается новый тип ресурса, а разрешается расширение существующих типов ресурсов. Кроме того, адаптация ресурсов работает с политикой Azure, поэтому управление и Настройка ресурсов можно выполнять в масштабе. Некоторые примеры рабочих процессов адаптации ресурсов:

- Установка расширений виртуальных машин и управление ими.
- Отправка и Настройка значений по умолчанию для учетных записей хранения Azure.
- Включение базовых параметров диагностики в масштабе.

## <a name="resource-onboarding-basics"></a>Основные сведения о адаптации ресурсов

Адаптация ресурсов настраивается с помощью настраиваемых поставщиков Azure, использующих типы ресурсов Microsoft. Кустомпровидерс/Ресаурцепровидерс и Microsoft. Кустомпровидерс/Associations. Чтобы включить подключение ресурсов для настраиваемого поставщика, в процессе настройки создайте **ResourceType** с именем "Associations" и **раутингтипе** , включающий "Extension". Кроме того, "Microsoft. Кустомпровидерс/Associations" и "Microsoft. Кустомпровидерс/Ресаурцепровидерс" не должны принадлежать к одной группе ресурсов.

Пример настраиваемого поставщика Azure:

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

Свойство | Обязательно | Description (Описание)
---|---|---
name | *да* | Имя определения конечной точки. Для адаптации ресурсов имя должно иметь значение "Associations".
routingType | *да* | Определяет тип контракта с **конечной точкой**. Для адаптации ресурсов допустимыми **раутингтипес** являются "прокси-сервер, кэш, расширение" и "веб-перехватчик, кэш, расширение".
endpoint | *да* | Конечная точка для направления запросов. Ответ будет обработан так же, как и любые побочные эффекты запроса.

После создания пользовательского поставщика с типом ресурса "Associations" можно ориентироваться с помощью "Microsoft. Кустомпровидерс/Associations". "Microsoft. Кустомпровидерс/Associations" — это расширение, которое может расширить любой другой ресурс Azure. При создании экземпляра "Microsoft. Кустомпровидерс/Associations" будет использоваться свойство **targetResourceId**, которое должно быть ДОПУСТИМЫм идентификатором ресурса Microsoft. Кустомпровидерс/Ресаурцепровидерс или Microsoft. Solutions/Applications. В этих случаях запрос будет перенаправлен к типу ресурса "Associations" («связи») для созданного нами экземпляра Microsoft. Кустомпровидерс/Ресаурцепровидерс.

> [!Note]
> Если идентификатор ресурса Microsoft. Solutions/Applications указан в качестве **targetResourceId**, то в управляемой группе ресурсов должен быть развернут "Microsoft. Кустомпровидерс/ресаурцепровидерс" с именем "Public".

Пример сопоставления настраиваемого поставщика Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Свойство | Обязательно | Description (Описание)
---|---|---
targetResourceId | *да* | Идентификатор ресурса Microsoft. Кустомпровидерс/Ресаурцепровидерс или Microsoft. Solutions/Applications.

## <a name="how-to-use-resource-onboarding"></a>Использование адаптации ресурсов

Адаптация ресурсов работает путем расширения других ресурсов с помощью ресурса расширения Microsoft. Кустомпровидерс/Associations. В следующем примере запрос будет выполнен для виртуальной машины, но любой ресурс можно будет расширить.

Сначала необходимо создать настраиваемый ресурс поставщика с типом ресурса "Associations". При этом будет объявлен URL-адрес обратного вызова, который будет использоваться при создании соответствующего ресурса Microsoft. Кустомпровидерс/Associations, предназначенного для настраиваемого поставщика.

Пример запроса на создание "Microsoft. Кустомпровидерс/Ресаурцепровидерс":

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

После создания пользовательского поставщика теперь можно ориентироваться на другие ресурсы и применить к ним побочные эффекты пользовательского поставщика.

Пример запроса на создание "Microsoft. Кустомпровидерс/Associations":

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

Затем этот запрос будет перенаправлен в конечную точку, указанную в первоначально созданном настраиваемом поставщике, на который ссылается "targetResourceId" в форме:

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

Конечная точка должна реагировать на "Application/JSON" `Content-Type` и допустимый текст ответа JSON. Поля, возвращаемые в объекте "Properties" JSON, будут добавлены в ответ на возвращаемый запрос ассоциации.

## <a name="looking-for-help"></a>Требуется помощь?

Если у вас возникли вопросы по разработке поставщика настраиваемых ресурсов Azure, попробуйте запрашивать [Stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Подобный вопрос, возможно, уже был задан, поэтому перед его публикацией сначала проверьте наличие ответа. Чтобы быстрее получить ответ, добавьте к вопросу тег ```azure-custom-providers```!

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали о настраиваемых поставщиках. Перейдите к следующей статье, чтобы создать настраиваемый поставщик.

- [Учебник. Адаптация ресурсов с помощью настраиваемых поставщиков](./tutorial-custom-providers-resource-onboarding.md)
- [Руководство. Создание настраиваемых действий и ресурсов в Azure](./tutorial-custom-providers-101.md)
- [Краткое руководство. Создание настраиваемого поставщика ресурсов Azure и развертывание настраиваемых ресурсов](./create-custom-provider.md)
- [Как добавлять настраиваемые действия в Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Как добавлять пользовательские ресурсы в Azure REST API](./custom-providers-resources-endpoint-how-to.md)
