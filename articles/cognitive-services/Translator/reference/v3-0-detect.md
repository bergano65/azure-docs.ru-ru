---
title: Метод Detect в API перевода текстов
titlesuffix: Azure Cognitive Services
description: Использование метода Detect в API перевода текстов.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 6698960cca39fb49fe8ba6e79b957be469ea7c50
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126128"
---
# <a name="translator-text-api-30-detect"></a>API перевода текстов 3.0: Detect

Определяет язык для фрагмента текста.

## <a name="request-url"></a>Request URL (URL-адрес запроса)

Отправьте запрос `POST` на следующий адрес.

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Параметры запроса

В таблице ниже приведены параметры, которые передаются в строке запроса.

<table width="100%">
  <th width="20%">Параметр запроса</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>api-version</td>
    <td>*Обязательный параметр.*<br/>Версия API, запрошенная клиентом. Должно быть такое значение: `3.0`.</td>
  </tr>
</table> 

Заголовки запроса:

<table width="100%">
  <th width="20%">Заголовки</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>_Один заголовок_<br/>_авторизации_</td>
    <td>*Обязательный заголовок запроса*.<br/>См. [описание доступных способов аутентификации](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Обязательный заголовок запроса*.<br/>Указывает тип содержимого для полезных данных. Возможные значения: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Обязательный заголовок запроса*.<br/>Длина текста запроса.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Необязательный*.<br/>Созданный клиентом идентификатор GUID, позволяющий уникально идентифицировать запрос. Обратите внимание, что этот заголовок можно опустить, если в строке запроса указан идентификатор трассировки в параметре с именем `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Тело запроса

Текст запроса является массивом в формате JSON. Каждый элемент этого массива представляет собой объект JSON со строковым свойством `Text`. Определение языка применяется к значению свойства `Text`. Ниже представлен пример текста запроса.

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Действительны следующие ограничения.

* Массив может содержать не более 100 элементов.
* Длина текстового значения в одном элементе массива не должна превышать 10 000 символов, включая пробелы.
* Общий объем текста запроса не должен превышать 50 000 символов, включая пробелы.

## <a name="response-body"></a>Тело ответа

Успешный ответ возвращается в формате массива JSON с одним результатом для каждой строки входного массива. Объект результатов содержит следующие свойства:

  * `language` — код обнаруженного языка.

  * `score` — значение с плавающей запятой, обозначающее достоверность результата. Может принимать ноль или единицу, где низкая оценка обозначает низкую достоверность.

  * `isTranslationSupported` — логическое значение, которое имеет значение True, если обнаруженный язык поддерживается для перевода текста.

  * `isTransliterationSupported` — логическое значение, которое имеет значение True, если обнаруженный язык поддерживается для транслитерации.
  
  * `alternatives` — массив других возможных языков. Каждый элемент этого массива содержит другой объект с такими же свойствами, как перечислено выше: `language`, `score`, `isTranslationSupported` и `isTransliterationSupported`.

Пример ответа в формате JSON:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Заголовки ответов

<table width="100%">
  <th width="20%">Заголовки</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>X-RequestId</td>
    <td>Сформированное службой значение для идентификации запроса. Оно используется для устранения неполадок.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Коды состояния ответа

Ниже приведены возможные коды состояния HTTP, которые возвращает запрос. 

<table width="100%">
  <th width="20%">Код состояния</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>200</td>
    <td>Успешно.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Один из параметров запроса отсутствует или имеет недопустимое значение. Исправьте параметры запроса и повторите попытку.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Не удалось выполнить аутентификацию запроса. Убедитесь, что указаны допустимые учетные данные.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Запрос не авторизован. Подробные сведения можно найти в сообщении об ошибке. Чаще всего это означает, что достигнут лимит бесплатных операций перевода, включенных в пробную подписку.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Вызывающая сторона отправляет слишком много запросов.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Произошла непредвиденная ошибка. Если ошибка сохраняется, передайте отчет о ней, включив следующие данные: дата и время сбоя, идентификатор запроса из заголовка ответа `X-RequestId` и идентификатор клиента из заголовка запроса `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Сервер временно недоступен. Повторите запрос. Если ошибка сохраняется, передайте отчет о ней, включив следующие данные: дата и время сбоя, идентификатор запроса из заголовка ответа `X-RequestId` и идентификатор клиента из заголовка запроса `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Примеры

В следующем примере показано, как получить список языков, поддерживаемых для перевода текста.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```

---
