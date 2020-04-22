---
title: Получение маркеров доступа к службе
description: Описывает, как создать токены для доступа к ARR REST AIS
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687071"
---
# <a name="get-service-access-tokens"></a>Получение маркеров доступа к службе

Доступ к ARR REST AIS предоставляется только авторизованным пользователям. Чтобы доказать свою авторизацию, необходимо отправить *токен доступа* вместе с запросами REST. Эти токены выдаются *Службой безопасных токенов* (STS) в обмен на ключ к учетной записи. Токены имеют **срок службы 24 часа** и, таким образом, могут быть выданы пользователям, не предоставляя им полный доступ к сервису.

В этой статье описывается, как создать такой маркер доступа.

## <a name="prerequisites"></a>Предварительные требования

[Создайте учетную запись ARR,](create-an-account.md)если у вас ее еще нет.

## <a name="token-service-rest-api"></a>Сервис токенов REST API

Для создания токенов доступа *служба Secure Token* предоставляет один API REST. URL-адрес службы ARR STS:\//sts.mixedreality.azure.com.

### <a name="get-token-request"></a>Запрос 'Получить токен'

| URI | Метод |
|-----------|:-----------|
| /счета/**accountId**/token | GET |

| Заголовок | Значение |
|--------|:------|
| Авторизация | "Bearer **accountId**:**accountKey**" |

Замените *accountId* и *accountKey* с соответствующими данными.

### <a name="get-token-response"></a>Ответ «Получить токен»

| Код состояния | полезные данные JSON | Комментарии |
|-----------|:-----------|:-----------|
| 200 | AccessToken: строка | Успешно |

| Заголовок | Назначение |
|--------|:------|
| МС-CV | Это значение можно использовать для отслеживания вызова в службе |

## <a name="getting-a-token-using-powershell"></a>Получение токена с помощью PowerShell

Приведенный ниже код PowerShell демонстрирует, как отправить необходимый запрос REST в STS. Затем он печатает маркер в запросе PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Скрипт просто печатает маркер на выходе, откуда можно скопировать & вставить его. Для реального проекта, вы должны автоматизировать этот процесс.

## <a name="next-steps"></a>Следующие шаги

* [Примеры скриптов PowerShell](../samples/powershell-example-scripts.md)
* [AIS Azure Frontend](../how-tos/frontend-apis.md)
* [Управление сеансом REST API](../how-tos/session-rest-api.md)
