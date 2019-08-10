---
title: Метод Detect в API перевода текстов
titleSuffix: Azure Cognitive Services
description: Использование метода Detect в API перевода текстов.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: ba73b75e30639dd3f5cf5523124c926ea3442fa1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932021"
---
# <a name="translator-text-api-30-detect"></a>API перевода текстов 3.0: Обнаружить

Определяет язык для фрагмента текста.

## <a name="request-url"></a>URL-адрес запроса

Отправьте запрос `POST` на следующий адрес.

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Параметры запроса

В таблице ниже приведены параметры, которые передаются в строке запроса.

<table width="100%">
  <th width="20%">Параметр запроса</th>
  <th>Описание</th>
  <tr>
    <td>api-version</td>
    <td>*Обязательный параметр.*<br/>Версия API, запрошенная клиентом. Должно быть такое значение: `3.0`.</td>
  </tr>
</table> 

Заголовки запроса:

<table width="100%">
  <th width="20%">Заголовки</th>
  <th>Описание</th>
  <tr>
    <td>Заголовки проверки подлинности</td>
    <td><em>Обязательный заголовок запроса</em>.<br/>См. <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">описание доступных способов аутентификации</a>.</td>
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

## <a name="request-body"></a>Текст запроса

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

Успешный ответ возвращается в формате массива JSON с одним результатом для каждой строки входного массива. Объект результата содержит следующие свойства.

  * `language`. код распознанного языка.

  * `score`. значение с плавающей запятой, обозначающее достоверность результата. Может принимать ноль или единицу, где низкая оценка обозначает низкую достоверность.

  * `isTranslationSupported`. логическое значение, которое имеет значение True, если обнаруженный язык поддерживается для перевода текста.

  * `isTransliterationSupported`. логическое значение, которое имеет значение True, если обнаруженный язык поддерживается для транслитерации.
  
  * `alternatives`. массив других возможных языков. Каждый элемент этого массива содержит другой объект с такими же свойствами, как перечислено выше: `language`, `score`, `isTranslationSupported` и `isTransliterationSupported`.

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
  <th>Описание</th>
  <tr>
    <td>X-RequestId</td>
    <td>Сформированное службой значение для идентификации запроса. Оно используется для устранения неполадок.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Коды состояния ответа

Ниже приведены возможные коды состояния HTTP, которые возвращает запрос. 

<table width="100%">
  <th width="20%">Код состояния</th>
  <th>Описание</th>
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
    <td>Не удалось выполнить проверку подлинности запроса. Убедитесь, что указаны допустимые учетные данные.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Запрос не авторизован. Подробные сведения можно найти в сообщении об ошибке. Чаще всего это означает, что достигнут лимит бесплатных операций перевода, включенных в пробную подписку.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Сервер отклонил запрос, так как клиент превысил лимиты запросов.</td>
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

Если возникнет ошибка, запрос также вернет ответ JSON с ошибкой. Код ошибки представляет собой число из 6 знаков, первые 3 из которых являются кодом состояния HTTP, а оставшиеся 3 цифры определяют категорию ошибки. Коды распространенных ошибок можно найти на [странице справочника по API перевода текстов версии 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Примеры

В следующем примере показано, как получить список языков, поддерживаемых для перевода текста.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
