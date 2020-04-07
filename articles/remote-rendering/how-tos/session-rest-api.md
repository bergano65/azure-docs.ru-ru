---
title: Управление сеансами REST API
description: Описывает, как управлять сеансами
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681250"
---
# <a name="use-the-session-management-rest-api"></a>Использование aPI управления сеансами REST API

Для использования функциональности удаленного рендеринга Azure необходимо создать *сеанс.* Каждый сеанс соответствует виртуальной машине (VM), которая выделяется в Azure и ждет подключения клиентского устройства. При подключении устройства VM предоставляет запрашиваемые данные и служит результат у видеопотока. Во время создания сеанса вы выбрали, на каком сервере вы хотите работать, который определяет цены. После того, как сеанс больше не нужен, его следует прекратить. Если не остановить вручную, он будет автоматически закрыт по истечении *срока аренды* сеанса.

Мы предоставляем скрипт PowerShell в [репозитории образцов ARR](https://github.com/Azure/azure-remote-rendering) в папке *Скриптов* под названием *RenderingSession.ps1*, что демонстрирует использование нашего сервиса. Сценарий и его конфигурация описаны здесь: [Пример скриптов PowerShell](../samples/powershell-example-scripts.md)

> [!TIP]
> Команды PowerShell, перечисленные на этой странице, предназначены для дополнения друг друга. Если вы запустите все скрипты последовательно в рамках одного и того же запроса команды PowerShell, они будут строиться друг на друге.

## <a name="regions"></a>Регионы

Ознакомьтесь со [списком доступных регионов](../reference/regions.md) для базовых URL-адресов для отправки запросов.

Для примера сценариев ниже мы выбрали регион *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Пример сценария: Выберите конечную точку

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Учетные записи

Если у вас нет учетной записи удаленного рендеринга, [создайте ее.](create-an-account.md) Каждый ресурс идентифицируется *учетной*записьюId, который используется на протяжении всей сессии AIS.

### <a name="example-script-set-accountid-and-accountkey"></a>Пример сценария: Установите accountId и accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Общие заголовки запросов

* Заголовок *авторизации* должен иметь`Bearer TOKEN`значение ",`TOKEN`где " является токен омовения подлинности, [возвращенный службой Secure Token.](tokens.md)

### <a name="example-script-request-a-token"></a>Пример сценария: Запрос маркера

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Общие заголовки ответов

* Заголовок *MS-CV* может быть использован группой разработчиков для отслеживания вызова в службе.

## <a name="create-a-session"></a>Создание сеанса

Эта команда создает сеанс. Он возвращает идентификатор новой сессии. Идентификатор сеанса необходим для всех других команд.

| URI | Метод |
|-----------|:-----------|
| /v1/account/*accountId*accountId/сессии/создание | POST |

**Тело запроса:**

* maxLeaseTime (время): значение тайм-аута, когда VM будет списан автоматически
* модели (массив): URL-адреса контейнера активов для предварительной загрузки
* размер (строка): размер VM **("стандартный"** или **"премиум").** Смотрите конкретные [ограничения размера VM.](../reference/limits.md#overall-number-of-polygons)

**Ответы:**

| Код состояния | полезные данные JSON | Комментарии |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Успешно |

### <a name="example-script-create-a-session"></a>Пример сценария: Создание сеанса

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Выходные данные примера:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Пример сценария: Store sessionId

Ответ от запроса выше включает в себя **sessionId**, который вам нужно для всех запросов последующих.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Обновление сеанса

Эта команда обновляет параметры сеанса. В настоящее время вы можете только продлить время аренды сессии.

> [!IMPORTANT]
> Время аренды всегда дается как общее время с начала сессии. Это означает, что если вы создали сеанс с временем аренды в один час, и вы хотите продлить время аренды еще на час, вы должны обновить его maxLeaseTime до двух часов.

| URI | Метод |
|-----------|:-----------|
| /v1/account/*accountID/сессии/**sessionId* | PATCH |

**Тело запроса:**

* maxLeaseTime (время): значение тайм-аута, когда VM будет списан автоматически

**Ответы:**

| Код состояния | полезные данные JSON | Комментарии |
|-----------|:-----------|:-----------|
| 200 | | Успешно |

### <a name="example-script-update-a-session"></a>Пример сценария: Обновление сеанса

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Выходные данные примера:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Получайте активные сеансы

Эта команда возвращает список активных сеансов.

| URI | Метод |
|-----------|:-----------|
| /v1/account/*accountId/сессии* | GET |

**Ответы:**

| Код состояния | полезные данные JSON | Комментарии |
|-----------|:-----------|:-----------|
| 200 | - сеансы: массив свойств сеансов | см. раздел "Получить свойства сеанса" для описания свойств сеанса |

### <a name="example-script-query-active-sessions"></a>Пример сценария: Активные сеансы запроса

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Выходные данные примера:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Получить свойства сеансов

Эта команда возвращает информацию о сеансе, например, его хост-имя VM.

| URI | Метод |
|-----------|:-----------|
| /v1/account/*accountId*/sessions/*sessionId/properties* | GET |

**Ответы:**

| Код состояния | полезные данные JSON | Комментарии |
|-----------|:-----------|:-----------|
| 200 | - сообщение: строка<br/>- сессияElapsedTime: временной промежуток<br/>- sessionHostname: строка<br/>- sessionId: строка<br/>- sessionMaxLeaseTime: время промежутка времени<br/>- sessionSize: enum<br/>- сессияСтатус: enum | enum sessionStatus - запуск, готовность, остановка, остановка, истек, ошибка<br/>Если статус "ошибка" или "исчерпан", сообщение будет содержать больше информации |

### <a name="example-script-get-session-properties"></a>Пример сценария: Получить свойства сеанса

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Выходные данные примера:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Остановка сеанса

Эта команда останавливает сеанс. Выделенный VM будет восстановлен вскоре после этого.

| URI | Метод |
|-----------|:-----------|
| /v1/account/*accountId*/сессии/*sessionId* | DELETE |

**Ответы:**

| Код состояния | полезные данные JSON | Комментарии |
|-----------|:-----------|:-----------|
| 204 | | Успешно |

### <a name="example-script-stop-a-session"></a>Пример сценария: Остановка сеанса

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Выходные данные примера:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Дальнейшие действия

* [Пример скриптов PowerShell](../samples/powershell-example-scripts.md)
