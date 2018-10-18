---
title: Получение событий работоспособности ресурсов Azure с помощью REST API | Документация Майкрософт
description: Используйте REST API Azure для получения событий работоспособности ресурсов Azure.
services: Resource health
author: rloutlaw
ms.reviewer: routlaw
manager: angerobe
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.author: routlaw
ms.openlocfilehash: bbbaa4c44a7c0d6da189f0c49d73adfa6142cdee
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095797"
---
# <a name="get-resource-health-using-the-rest-api"></a>Получение сведений о работоспособности ресурсов с помощью REST API 

В примере, приведенном в этой статье, показано, как извлечь список событий работоспособности для ресурсов Azure в подписке с помощью [REST API Azure](/rest/api/azure/).

Полная справочная документация и дополнительные образцы для REST API можно найти в [Azure Monitor REST API reference](/rest/api/monitor) (Справочник по REST API Azure Monitor). 

## <a name="build-the-request"></a>Создание запроса

Используйте следующий HTTP-запрос `GET`, чтобы получить список событий работоспособности, относящихся в вашей подписке, для диапазона времени между `2018-05-16` и `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Заголовки запросов

Ниже приведены обязательные заголовки. 

|Заголовок запроса|ОПИСАНИЕ|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный элемент. Задайте значение `application/json`.|  
|*Authorization:*|Обязательный элемент. Задайте в качестве значения [допустимый токен доступа](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |  

### <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| ИМЯ | ОПИСАНИЕ |
| :--- | :---------- |
| subscriptionId | Идентификатор подписки Azure. Если у вас несколько подписок, см. раздел [Использование нескольких подписок](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). |
| api-version | Версия API для использования в запросе.<br /><br /> В этом документе рассматривается API версии `2015-04-01`, которая включена в приведенный выше URL-адрес.  |
| $filter | Параметр фильтрации для уменьшения набора возвращаемых результатов. Допустимые шаблоны для этого параметра доступны [в справочнике по работе с журналом действий](/rest/api/monitor/activitylogs/list#uri-parameters). В примере ниже показана запись всех событий в диапазоне времени между 2018-05-16 и 2018-06-20. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Тело запроса

Для этой операции текст запроса не требуется.

## <a name="handle-the-response"></a>Обработка ответа

Код состояния 200 возвращается со списком значений событий работоспособности, соответствующих параметру фильтра, а также URI `nextlink` для получения следующей страницы результатов.

## <a name="example-response"></a>Пример ответа 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```