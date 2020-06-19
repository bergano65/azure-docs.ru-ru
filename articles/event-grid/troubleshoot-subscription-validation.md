---
title: Сетка событий Azure. Устранение неполадок при проверке подписки
description: В этой статье описывается, как устранять неполадки при проверках подписок.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: spelluru
ms.openlocfilehash: f292d70eaaca29e714ea35b4f61a141b2d5cd2b6
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83778714"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Устранение неполадок при проверках подписок
В этой статье содержатся сведения об устранении неполадок при проверках подписок на события. 

> [!IMPORTANT]
> Подробные сведения о проверке конечных точек для веб-перехватчиков см. в разделе [Доставка событий веб-перехватчика](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Проверка подписки на события Сетки событий с помощью Postman
Ниже приведен пример использования Postman для проверки подписки веб-перехватчика на события Сетки событий. 

![Проверка подписки на события Сетки событий с помощью Postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Ниже приведен пример кода JSON **SubscriptionValidationEvent**.

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Вот пример успешного ответа.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Чтобы узнать больше о проверке событий Сетки событий для веб-перехватчиков, ознакомьтесь с разделом [Проверка конечной точки с событиями сетки событий](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Проверка подписки на события Сетки событий с помощью Curl 
Ниже приведен пример использования Curl для проверки подписки веб-перехватчика на события Сетки событий. 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Проверка подписки на облачные события с помощью Postman
Ниже приведен пример использования Postman для проверки подписки веб-перехватчика на облачные события. 

![Проверка подписки на облачные события с помощью Postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Используйте метод **HTTP OPTIONS** для проверки облачных событий. Чтобы узнать больше о проверке облачных событий для веб-перехватчиков, ознакомьтесь с разделом о [проверке конечных точек с облачными событиями](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="error-code-403"></a>Код ошибки: 403
Если веб-перехватчик возвращает ошибку 403 ("Запрещено") в ответе, проверьте, не защищен ли этот веб-перехватчик Шлюзом приложений Azure или брандмауэром веб-приложения. Если это так, необходимо отключить приведенные ниже правила брандмауэра и выполнить HTTP-запрос POST еще раз:

  - 920300 (В запросе отсутствует заголовок Accept, это можно исправить.)
  - 942430 (Ограничение обнаружения аномальных знаков SQL (аргументы): # превышено количество специальных знаков (12).)
  - 920230 (Обнаружено несколько типов кодирования URL-адресов.)
  - 942130 (Атака путем внедрения кода SQL. Обнаружена тавтология SQL.)
  - 931130 (Возможная атака с включением удаленного файла (RFI): ссылка вне домена.)

## <a name="next-steps"></a>Дальнейшие действия
Если вам нужна дополнительная помощь, опубликуйте свой вопрос на [форуме Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) или отправьте [запрос в службу поддержки](https://azure.microsoft.com/support/options/). 
