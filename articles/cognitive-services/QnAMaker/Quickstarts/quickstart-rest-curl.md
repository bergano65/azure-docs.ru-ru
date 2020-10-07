---
title: Краткое руководство. Использование cURL и REST для управления базой знаний — QnA Maker
description: В этом кратком руководстве показано, как создавать, публиковать и запрашивать базу знаний с помощью REST API.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 04/13/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 11a7bd8655d1b5606c3b53ed78e796bc42f85b2e
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777422"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Краткое руководство. Использование cURL и REST для управления базой знаний

В этом кратком руководстве показано, как создавать, публиковать и запрашивать базу знаний. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/knowledge-base.md). Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Текущая версия [cURL](https://curl.haxx.se/). В этом кратком руководстве используются несколько параметров командной строки, которые указаны в [документации cURL](https://curl.haxx.se/docs/manpage.html).
* Для использования ключа и имени ресурса необходимо иметь [QnA Maker ресурса](../How-To/set-up-qnamaker-service-azure.md). Вы указали **имя** ресурса во время его создания, после чего был создан ключ. Имя ресурса используется в качестве поддомена для конечной точки. Чтобы получить ключ и имя ресурса, на портале Azure для своего ресурса выберите **Быстрое начало**. Имя ресурса является первым поддоменом в URL-адресе конечной точки:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> В следующих примерах BASH используется символ продолжения строки `\`. Если консоль или терминал использует другой символ продолжения строки, используйте этот символ.

## <a name="create-a-knowledge-base"></a>Создание базы знаний

Чтобы создать базу знаний с помощью REST API и cURL, необходимо иметь следующие сведения:

|Сведения|Конфигурация cURL|Назначение|
|--|--|--|
|Имя ресурса QnA Maker|URL-адрес|Используется для создания URL-адреса|
|Ключ ресурса QnA Maker|Параметр `-h` для заголовка `Ocp-Apim-Subscription-Key`|Проверка подлинности в службе QnA Maker|
|Формат JSON, описывающий базу знаний|Параметр `-d`|[Примеры](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) JSON|
|Размер JSON-файла в байтах|Параметр `-h` для заголовка `Content-Size`||

Команда cURL выполняется из оболочки BASH. Измените эту команду, указав собственные имя ресурса, ключ ресурса, значения и размер JSON-файла.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Ответ cURL от QnA Maker содержит `operationId`, необходимый для [получения состояния операции](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Получение состояния операции

При создании базы знаний, так как операция является асинхронной, ответ содержит сведения для определения состояния.

|Сведения|Конфигурация cURL|Назначение|
|--|--|--|
|Имя ресурса QnA Maker|URL-адрес|Используется для создания URL-адреса|
|Идентификатор операции|Маршрут URL-адреса|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Ключ ресурса QnA Maker|Параметр `-h` для заголовка `Ocp-Apim-Subscription-Key`|Проверка подлинности в службе QnA Maker|

Команда cURL выполняется из оболочки BASH. Измените эту команду, указав имя ресурса, ключ ресурса и идентификатор операции.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Ответ cURL содержит состояние. Если состояние операции — "выполнено успешно", `resourceLocation` содержит идентификатор базы знаний.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Публикация базы знаний

Перед отправкиой запроса в базу знаний необходимо выполнить следующие действия:
* Публикация базы знаний
* Получение ключа конечной точки среды выполнения

Эта задача публикует базу знаний. Получение ключа конечной точки среды выполнения — это [отдельная задача](#get-published-knowledge-bases-runtime-endpoint-key).

|Сведения|Конфигурация cURL|Назначение|
|--|--|--|
|Имя ресурса QnA Maker|URL-адрес|Используется для создания URL-адреса|
|Ключ ресурса QnA Maker|Параметр `-h` для заголовка `Ocp-Apim-Subscription-Key`|Проверка подлинности в службе QnA Maker|
|Идентификатор базы знаний|Маршрут URL-адреса|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Команда cURL выполняется из оболочки BASH. Измените эту команду, указав имя ресурса, ключ ресурса и идентификатор базы знаний.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Состояние ответа имеет значение 204 (без результатов). Используйте параметр командной строки `-v`, чтобы просмотреть подробные выходные данные для команды cURL. Они включают в себя состояние HTTP.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Получение ключа конечной точки среды выполнения для опубликованной базы знаний

Перед отправкиой запроса в базу знаний необходимо выполнить следующие действия:
* Публикация базы знаний
* Получение ключа конечной точки среды выполнения

Эта задача получает ключ конечной точки среды выполнения. Публикация базы знаний — это [отдельная](#publish-knowledge-base) задача.

Ключ конечной точки среды выполнения — это один и тот же ключ для всех баз знаний, использующих ресурс QnA Maker.

|Сведения|Конфигурация cURL|Назначение|
|--|--|--|
|Имя ресурса QnA Maker|URL-адрес|Используется для создания URL-адреса|
|Ключ ресурса QnA Maker|Параметр `-h` для заголовка `Ocp-Apim-Subscription-Key`|Проверка подлинности в службе QnA Maker|

Команда cURL выполняется из оболочки BASH. Измените эту команду, указав собственное имя ресурса и ключ ресурса.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


В ответе cURL содержатся ключи конечной точки среды выполнения. Используйте только один из ключей при отправке запроса, чтобы получить ответ от базы знаний.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Запрос ответа из опубликованной базы знаний

Получение ответа из базы знаний выполняется из отдельной среды выполнения, которая не управляет базой знаний. Так как это отдельная среда выполнения, необходимо пройти проверку подлинности с помощью ключа среды выполнения.

|Сведения|Конфигурация cURL|Назначение|
|--|--|--|
|Имя ресурса QnA Maker|URL-адрес|Используется для создания URL-адреса|
|Ключ среды выполнения QnA Maker|Параметр `-h` для заголовка `Authorization`|Ключ является частью строки, содержащей слово `Endpointkey `. Проверка подлинности в службе QnA Maker|
|Идентификатор базы знаний|Маршрут URL-адреса|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|Запрос описания JSON|Параметр `-d`|[Параметры текста запроса](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) и [примеры](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) JSON|
|Размер JSON-файла в байтах|Параметр `-h` для заголовка `Content-Size`||

Команда cURL выполняется из оболочки BASH. Измените эту команду, указав имя ресурса, ключ ресурса и идентификатор базы знаний.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Успешный ответ включает в себя наиболее подходящий ответ, а также другие сведения, которые клиентское приложение (например, чат-бот) должно отображать в качестве ответа пользователю.

## <a name="delete-knowledge-base"></a>Удаление базы знаний

После завершения использования базы знаний удалите ее.

|Сведения|Конфигурация cURL|Назначение|
|--|--|--|
|Имя ресурса QnA Maker|URL-адрес|Используется для создания URL-адреса|
|Ключ ресурса QnA Maker|Параметр `-h` для заголовка `Ocp-Apim-Subscription-Key`|Проверка подлинности в службе QnA Maker|
|Идентификатор базы знаний|Маршрут URL-адреса|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Команда cURL выполняется из оболочки BASH. Измените эту команду, указав имя ресурса, ключ ресурса и идентификатор базы знаний.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Состояние ответа имеет значение 204 (без результатов). Используйте параметр командной строки `-v`, чтобы просмотреть подробные выходные данные для команды cURL. Они включают в себя состояние HTTP.

## <a name="additional-resources"></a>Дополнительные ресурсы

* Справочная документация по [разработке](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase).
* Справочная документация по [среде выполнения](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/).
* [Примеры скриптов BASH с использованием cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))
