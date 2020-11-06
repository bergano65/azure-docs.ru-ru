---
title: Azure Active Directory REST API-Test с помощью инструкции POST
description: Используйте POST для тестирования конфигурации приложения Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 9690678fc7b794c694e588a7993cb131d8264a72
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424487"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Тестирование REST API конфигурации приложений Azure с помощью POST

Чтобы протестировать REST API с помощью [процедуры POST](https://www.getpostman.com/), необходимо включить в запросы заголовки HTTP, необходимые для [проверки подлинности](./rest-api-authentication-hmac.md) . Вот как можно настроить POST для тестирования REST API, автоматически создавая заголовки проверки подлинности:

1. Создать новый [запрос](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)
1. Добавьте `signRequest` функцию из [примера проверки подлинности JavaScript](./rest-api-authentication-hmac.md#javascript) в [Скрипт предварительного запроса](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) для запроса.
1. Добавьте следующий код в конец скрипта, выполняемого перед запросом. Обновите ключ доступа, как указано в комментарии TODO.

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. Отправка запроса
