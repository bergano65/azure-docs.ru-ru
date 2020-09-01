---
title: Получение маркеров доступа к службе
description: Описание создания маркеров для доступа к интерфейсам API служб ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e8e2f3f9dd49693faa26eaaab309fcad58f6f9f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076163"
---
# <a name="get-service-access-tokens"></a>Получение маркеров доступа к службе

Доступ к API-интерфейсам служб ARR () предоставляется только для полномочных пользователей. Чтобы доказать свою авторизацию, необходимо отправить *маркер доступа* вместе с запросами на другие запросы. Эти маркеры выдаются *службой маркеров безопасности* (STS) в Exchange для ключа учетной записи. Токены имеют **время существования 24 часов** и поэтому могут выдаваться пользователям без предоставления им полного доступа к службе.

В этой статье описывается, как создать такой маркер доступа.

## <a name="prerequisites"></a>Предварительные требования

[Создайте учетную запись arr](create-an-account.md), если она еще не создана.

## <a name="token-service-rest-api"></a>REST API службы маркеров

Для создания маркеров доступа *Служба маркеров безопасности* предоставляет один REST API. URL-адрес для службы STS-обслуживания — HTTPS: \/ /STS.mixedreality.Azure.com.

### <a name="get-token-request"></a>Запрос "получение токена"

| URI | Метод |
|-----------|:-----------|
| /аккаунтс/**accountId**/Token | GET |

| Заголовок | Значение |
|--------|:------|
| Авторизация | "Bearer, **accountId**:**accountKey**" |

Замените *accountId* и *accountKey* соответствующими данными.

### <a name="get-token-response"></a>Ответ "получить токен"

| Код состояния | полезные данные JSON | Примечания |
|-----------|:-----------|:-----------|
| 200 | AccessToken: строка | Успех |

| Заголовок | Цель |
|--------|:------|
| MS-КП | Это значение можно использовать для трассировки вызова в службе |

## <a name="getting-a-token-using-powershell"></a>Получение маркера с помощью PowerShell

В следующем примере кода PowerShell показано, как отправить в STS необходимый запрос на ОСТАВШУЮся работу. Затем он выводит маркер в командную строку PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Сценарий просто выводит маркер в выход, откуда его можно скопировать & вставить. Для реальных проектов этот процесс следует автоматизировать.

## <a name="next-steps"></a>Дальнейшие действия

* [Примеры скриптов PowerShell](../samples/powershell-example-scripts.md)
* [Интерфейсные API Azure](../how-tos/frontend-apis.md)
* [REST API управления сеансами](../how-tos/session-rest-api.md)
