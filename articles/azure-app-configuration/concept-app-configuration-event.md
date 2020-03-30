---
title: Реагирование на события ключевой ценности конфигурации приложений Azure
description: Используйте Azure Event Grid, чтобы подписаться на события конфигурации приложений.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523804"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Реагирование на события конфигурации приложений Azure

События Конфигурации приложений Azure позволяют приложениям реагировать на изменения в ключевых значениях. Это делается без необходимости в сложном коде или дорогостоящих и неэффективных службах опроса. Вместо этого события проталкиваются через [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) для подписчиков, таких как [Azure Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)или даже для вашего собственного пользовательского слушателя http. Критически, вы только оплачиваете для чего вы используете.

События Azure App Configuration отправляются в Azure Event Grid, которая предоставляет надежные услуги доставки вашим приложениям посредством богатых политик повторений и доставки букв. Чтобы узнать больше, смотрите [доставку сообщений Event Grid и повторную попытку.](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)

Общие сценарии событий конфигурации приложений включают обновление конфигурации приложения, запуск развертывания или любой рабочий процесс, ориентированный на конфигурацию. Когда изменения вносятся редко, а сценарий требует немедленного реагирования, архитектура на основе событий может быть особенно эффективной.

Взгляните на [события Configuration App Configuration Route Azure в пользовательскую конечную точку веб-страницы - CLI](./howto-app-configuration-event.md) для быстрого примера. 

![Модель Сетки событий](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Доступные события конфигурации приложений Azure
Сетка событий использует [подписки на события](../event-grid/concepts.md#event-subscriptions) для маршрутизации сообщений о событиях подписчикам. Подписка на события Azure App Configuration может включать два типа событий:  

> |Название мероприятия|Описание|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Работается при создании или замене ключевого значения|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Уволено при удалении значения ключа|

## <a name="event-schema"></a>Схема событий
События конфигурации приложений Azure содержат всю информацию, необходимую для ответа на изменения в данных. Вы можете определить событие конфигурации приложения, потому что свойство eventType начинается с "Microsoft.AppConfiguration". Дополнительные сведения об использовании свойств событий Сетки событий приводятся в статье [Схема событий службы "Сетка событий Azure"](../event-grid/event-schema.md).  

> |Свойство|Тип|Описание|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |Раздел|строка|Полный идентификатор ресурсов Azure конфигурации приложения, испускающей событие.|
> |subject|строка|URI ключевой ценности, которая является предметом события.|
> |eventTime|строка|Дата/время, когда событие было создано, в формате ISO 8601.|
> |eventType|строка|"Microsoft.AppConfiguration.KeyValueModified" или "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Идентификатор|строка|Уникальный идентификатор этого события.|
> |dataVersion|строка|Версия схемы для объекта данных.|
> |metadataVersion|строка|Версия схемы свойств верхнего уровня.|
> |.|объект|Сбор конкретных событий, данных о конфигурации приложений Azure|
> |data.key|строка|Ключ ключевой значения, который был изменен или удален.|
> |data.label|строка|Метка, если таковая имеется, из ключевой значения, которая была изменена или удалена.|
> |data.etag|строка|Для `KeyValueModified` etag нового ключевого значения. Для `KeyValueDeleted` etag ключевой значения, которая была удалена.|

Вот пример события KeyValueModified:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Для получения дополнительной информации смотрите [схему событий Azure App Configuration](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Рекомендации по потреблению событий
Приложения, обрабатывающие события конфигурации приложений, должны следовать следующим рекомендуемым методам:
> [!div class="checklist"]
> * Несколько подписок могут быть настроены для маршрутизаний событий в один и тот же обработчик событий, поэтому не думайте, что события находятся в определенном источнике. Вместо этого проверьте тему сообщения, чтобы обеспечить отправку события экземпляром конфигурации приложения.
> * Проверьте eventType и не думайте, что все события, которые вы получите, будут типами, которые вы ожидаете.
> * Используйте поля etag, чтобы понять, если ваша информация об объектах по-прежнему актуальна.  
> * Используйте поля секвенсора, чтобы понять порядок событий на каком-либо конкретном объекте.
> * Используйте поле объекта для доступа к измененному значению ключа.


## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о Event Grid и попробуйте события Конфигурации приложений Azure:

- [An introduction to Azure Event Grid](../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure")
- [Маршрут Azure App Configuration событий на пользовательской конечную точку веб](./howto-app-configuration-event.md)