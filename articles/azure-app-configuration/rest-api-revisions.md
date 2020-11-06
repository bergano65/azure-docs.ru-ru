---
title: REST API конфигурации приложений Azure — "ключ — значение"
description: Справочные страницы по работе с редакциями "ключ — значение" с использованием конфигурации приложения Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7d1990d6bc524a69de2b22b4f7c5aeec88c3ce9d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424480"
---
# <a name="key-value-revisions"></a>Редакции "ключ — значение"

версия API: 1,0

**Редакция "ключ-значение** " определяет историческое представление ресурса "ключ-значение". Срок действия исправлений истекает через 7 дней для хранилищ бесплатных уровней или 30 дней для хранилищ уровня "Стандартный". Исправления поддерживают следующие операции:

- Список

Для всех операций ``key`` — необязательный параметр. Если этот параметр опущен, он подразумевает **любой** ключ.
Для всех операций ``label`` — необязательный параметр. Если этот параметр опущен, он подразумевает **любую** метку.

## <a name="prerequisites"></a>Обязательные условия

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Вывод списка редакций

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Правляют**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Разбиение на страницы

Если число возвращаемых элементов превышает предельное значение, то разбивка на страницы отводится. Следуйте указаниям в необязательном ``Link`` заголовке ответа и используйте ``rel="next"`` для навигации.  Кроме того, содержимое предоставляет следующую ссылку в форме ``@nextLink`` Свойства.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Ответ**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>Перечисление подмножества редакций

Используйте `Range` заголовок запроса. Ответ будет содержать `Content-Range` заголовок. Если сервер не может удовлетворить запрошенный диапазон, он будет отвечать HTTP `416` (ранженотсатисфиабле)

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Ответ**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Фильтрация

`key`Поддерживается сочетание фильтров и `label` .
Используйте необязательные `key` `label` Параметры и строки запроса.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Поддерживаемые фильтры

|Фильтр ключей|Действие|
|--|--|
|`key` не указан или имеет значение `key=*`|Соответствует **любому** ключу|
|`key=abc`|Соответствует ключу с именем  **ABC**|
|`key=abc*`|Соответствует именам ключей, начинающимся с **ABC**|
|`key=*abc`|Соответствует именам ключей, заканчивающимся на **ABC**|
|`key=*abc*`|Совпадает с именами ключей, содержащими **ABC**|
|`key=abc,xyz`|Соответствует именам ключей **ABC** или **XYZ** (не более 5 CSV)|

|Фильтр меток|Действие|
|--|--|
|`label` не указан или имеет значение `label=`|Совпадает с записью без метки|
|`label=*`|Соответствует **любой** метке|
|`label=prod`|Соответствует метке " **произ** .|
|`label=prod*`|Совпадает с метками, начинающимися с **произв** .|
|`label=*prod`|Соответствует меткам, заканчивающимся на " **произв** ."|
|`label=*prod*`|Соответствует меткам, содержащим **произ** .|
|`label=prod,test`|Совпадает с **метками** "Рабочая или **Тестовая** " (ограничение в 5 CSV)|

### <a name="reserved-characters"></a>Зарезервированные символы

`*`, `\`, `,`

Если зарезервированный символ является частью значения, он должен быть экранирован с помощью `\{Reserved Character}` . Незарезервированные символы также могут быть экранированы.

### <a name="filter-validation"></a>Проверка фильтра

Если возникает ошибка проверки фильтра, ответом является HTTP `400` со сведениями об ошибке:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Примеры

- Все

    ```http
    GET /revisions
    ```

- Элементы, в которых имя ключа начинается с **ABC**

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Элементы, где имя ключа — **ABC** или **XYZ** , а метки содержат « **произ** .»

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Запрашивать определенные поля

Используйте необязательный `$select` параметр строки запроса и укажите список запрошенных полей с разделителями-запятыми. Если `$select` параметр пропущен, ответ содержит набор по умолчанию.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Доступ к Time-Based

Получить представление результата, которое было в прошлый раз. См. [раздел об](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Ответ**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
