---
title: Сетка событий Azure. Устранение неполадок при проверке подписки
description: В этой статье показано, как устранять неполадки при проверках подписок.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630191"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Устранение ошибок службы "Сетка событий Azure"
В этом руководство по устранению неполадок содержатся сведения об устранении неполадок с подпиской на события. 


## <a name="troubleshoot-event-subscription-validation"></a>Устранение неполадок при проверке подписки на события

Если при создании подписки на событие отображается сообщение об ошибке `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`, например, это означает, что в подтверждении проверки произошла ошибка. Чтобы устранить эту ошибку, проверьте следующие аспекты:

- Выполните запрос HTTP POST к URL-адресу перехватчика с примером текста запроса [SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) с помощью инструкции POST, или аналогичного средства.
- Если веб-перехватчик реализует механизм подтверждения синхронной проверки, убедитесь, что ValidationCode возвращается как часть ответа.
- Если веб-перехватчик реализует механизм подтверждения по асинхронной проверке, убедитесь, что HTTP POST возвращает значение 200 OK.
- Если веб-перехватчик возвращает 403 (запрещено) в ответе, проверьте, находится ли веб-перехватчик за шлюзом приложений Azure или брандмауэром веб-приложения. Если это так, вам нужно отключить эти правила брандмауэра и выполнить HTTP-запрос POST еще раз:

  920300 (в запросе отсутствует заголовок Accept, мы можем исправить это)

  942430 (ограничение на обнаружение аномалий символов SQL (args): Превышено число специальных символов (12))

  920230 (обнаружено несколько кодировок URL-адресов)

  942130 (атака путем внедрения кода SQL: обнаружен SQL Таутологи.)

  931130 (возможная атака с удаленными файлами (RFI) = не является ссылкой на домен или ссылкой)

> [!IMPORTANT]
> Подробные сведения о проверке конечных точек для веб-перехватчиков см. в статье о [доставке событий веб-перехватчика](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Проверка подписки на события сетки событий с помощью POST
Ниже приведен пример использования процедуры POST для проверки подписки веб-перехватчика события сетки событий. 

![Проверка подписки на события сетки событий с помощью POST](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Ниже приведен пример SubscriptionValidationEvent JSON:

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

Пример успешного ответа:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Дополнительные сведения о проверке событий в службе "Сетка событий" для веб-перехватчиков см. в статье [Проверка конечных точек с помощью событий сетки событий](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="validate-cloud-event-subscription-using-postman"></a>Проверка подписки на облачные события с помощью POST
Ниже приведен пример использования процедуры POST для проверки подписки веб-перехватчика облачного события. 

![Проверка подписки на облачные события с помощью POST](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Используйте метод **http Options** для проверки с облачными событиями. Дополнительные сведения о проверке облачных событий для веб-перехватчиков см. в статье [Проверка конечных точек с помощью облачных событий](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="event-grid-event-subscription-validation-using-curl"></a>Проверка подписки на события сетки событий с помощью функции фигурной скобки 
Вот пример команды для проверки подписки веб-перехватчика события сетки событий: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>Дальнейшие действия
Если вам нужна дополнительная помощь, опубликуйте ее на [Stack overflow форуме](https://stackoverflow.com/questions/tagged/azure-eventgrid) или откройте запрос в [службу поддержки](https://azure.microsoft.com/support/options/). 
