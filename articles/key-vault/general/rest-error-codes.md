---
title: Коды ошибок REST API - Убежище ключей Azure
description: Эти коды ошибок могут быть возвращены с помощью операции на веб-службе Azure Key Vault.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: bbb30c0ad41babca4158391c9e4e5c5d4d25cbf9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432064"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Коды aPI API API Azure Key Vault REST
 
Следующие коды ошибок могут быть возвращены с помощью операции на веб-службе Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Непроверенный запрос

401 означает, что запрос не является недостоверным для Key Vault. 

Запрос проверяется, если:

- Хранилище ключей знает личность вызывающего абонента; И
- Звонящему разрешено пытаться получить доступ к ресурсам Key Vault. 

Есть несколько различных причин, почему запрос может вернуть 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Нет маркера проверки подлинности, прикрепленного к запросу. 

Вот пример put-запроса, устанавливающий значение секрета:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

Заголовок "Авторизация" — это токен доступа, который требуется при каждом вызове в Key Vault для операций на плоскости данных. Если заголовок отсутствует, то ответ должен быть 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Токену не хватает правильного ресурса, связанного с ним. 

При запросе токена доступа из точки azure OAUTH параметр под названием "ресурс" является обязательным. Значение важно для поставщика токенов, поскольку оно предусматривает использование токена для его целевого использования. Ресурс для **всех** токенов для доступа к Key Vault *—\/https: /vault.keyvault.net* (без отставания).

### <a name="the-token-is-expired"></a>Срок действия токена истек

Токены закодированы на базе64, а значения могут быть [http://jwt.calebb.net](http://jwt.calebb.net)декодированы на таких веб-сайтах, как . Вот приведенный выше маркер расшифророван:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Мы видим много важных частей в этом маркере:

- aud (аудитория): Ресурс токена. Обратите внимание, <https://vault.azure.net>что это . Этот маркер НЕ будет работать для любого ресурса, который явно не соответствует этому значению, например графика.
- iat (выдается по): Количество тиков с начала эпохи, когда токен был выпущен.
- nbf (не раньше): Количество тиков с начала эпохи, когда этот маркер становится действительным.
- exp (истечение): Количество тиков с начала эпохи, когда истекает срок действия этого маркера.
- appid (идентификатор приложения): GUID для идентификатора приложения, делающих этот запрос.
- tid (идентификатор арендатора): GUID для идентификатора арендатора основного, делающих этот запрос

Важно, чтобы все значения были правильно идентифицированы в маркере для того, чтобы запрос работал. Если все правильно, то запрос не приведет к 401.

### <a name="troubleshooting-401"></a>Устранение неполадок 401

401s должны быть исследованы с точки зрения генерации токенов, прежде чем запрос сделан на хранилище ключей. Обычно код используется для запроса маркера. После получения токена он передается в запрос Key Vault. Если код работает локально, можно использовать Fiddler для захвата `https://login.microsoftonline.com`запроса/ответа на запрос. Запрос выглядит следующим образом:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Следующие пользовательские данные информации месиво быть правильным:

- Идентификатор клиента хранилища ключевых идентификаторов
- Значение ресурса установлено на https%3A%2F%2Fvault.azure.net (URL закодирован)
- Идентификатор клиента
- Секрет клиента

Убедитесь, что остальная часть запроса почти идентична.

Если вы можете получить только токен доступа к ответу, вы можете расшифровать его (как показано выше), чтобы убедиться в идентификаторе клиента, идентификаторе клиента (идентификатор приложения) и ресурсе.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Недостаточное разрешение

HTTP 403 означает, что запрос был проверен (он знает личность запроса), но личность не имеет разрешения на доступ к запрашиваемому ресурсу. Есть две причины:

- Политика доступа к идентификационной информации отсутствует.
- IP-адрес запрашивающего ресурса не указан в белом списке в настройках брандмауэра хранилища ключей.

HTTP 403 часто возникает, когда приложение клиента не использует идентификатор клиента, который, по его мнению, является так. Обычно это означает, что политики доступа неправильно настроены для фактической идентификации вызова.

### <a name="troubleshooting-403"></a>Устранение неполадок 403

Во-первых, включите лесозаготовки. Для получения инструкций о том, как это сделать, [см.](logging.md)

После включения журнала можно определить, связано ли 403 с политикой доступа или политикой брандмауэра.

#### <a name="error-due-to-firewall-policy"></a>Ошибка из-за политики брандмауэра

"Адрес клиента (00.00.00.00) не авторизован, и абонент не является доверенным лицом"

Существует ограниченный список "Azure Trusted Services". Веб-сайты Azure **не** являются надежной службой Azure. Для получения дополнительной информации смотрите в блоге [Key Vault Firewall доступ к Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Для того, чтобы он работал, необходимо добавить IP-адрес веб-сайта Azure в Хранилище ключей.

Если из-за политики доступа: найдите идентификатор объекта для запроса и убедитесь, что идентификатор объекта соответствует объекту, которому пользователь пытается присвоить политику доступа. Там часто будет несколько объектов в AAD, которые имеют то же имя, так что выбор правильного очень важно. Удалив и повторно добавив политику доступа, можно увидеть, существуют ли несколько объектов с одинаковым именем.

Кроме того, большинство политик доступа не требуют использования "Авторизованного приложения", как показано на портале. Авторизованное приложение используется для сценариев проверки подлинности ,от имени, которые встречаются редко. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Слишком много запросов

Дроссевание происходит, когда количество запросов превышает заявленный максимум для таймфрейма. Если происходит регулирование, ответ Омналь-ключ будет HTTP 429. Есть заявленные максимумы для типов запросов. Например: создание ключа HSM 2048-битный 5 запросов за 10 секунд, но все другие транзакции HSM имеют ограничение запроса 1000/10 секунд. Поэтому важно понимать, какие типы вызовов делаются при определении причины регулирования.
Как правило, запросы в Key Vault ограничены 2000 запросами/10 секундами. Исключениями являются ключевые операции, как это описано в [лимитах службы Key Vault](service-limits.md)

### <a name="troubleshooting-429"></a>Устранение неполадок 429
Throttling работает вокруг используя эти методы:

- Уменьшите количество запросов, поданных в Хранилище ключей, определяя, есть ли шаблоны запрашиваемого ресурса и пытаясь кэшировать их в вызываемом приложении. 

- При задушии Key Vault адаптируйте запрашивающий код для использования экспоненциального отката для повторной попытки. Алгоритм объясняется здесь: [Как задушить ваше приложение](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Если количество запросов не может быть уменьшено путем кэширования и приуроченный обратный отработок не работает, то рассмотрите возможность разделения ключей на несколько ключей Vaults. Ограничение обслуживания для одной подписки составляет 5x индивидуальный предел Key Vault. При использовании более 5 ключевых Vaults следует учитывать использование нескольких подписок. 

Подробное руководство, включая запрос на увеличение лимитов, можно найти здесь: [Key Vault регулирования руководства](overview-throttling.md)


