---
title: Конфигурация приложений Azure REST API — управление версиями
description: Справочные страницы для управления версиями с помощью REST API конфигурации приложений Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 90d131cdc7c496853f2520951c95b9903d69f8fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424472"
---
# <a name="versioning"></a>Управление версиями

версия API: 1,0

Каждый клиентский запрос должен предоставлять явную версию API в качестве параметра строки запроса. Пример: `https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version` выражается в формате SemVer (основной. дополнительный). Согласование диапазона или версии не поддерживается.

## <a name="error-response"></a>Сообщение об ошибке

Ниже приведена сводка возможных сообщений об ошибках, возвращаемых сервером, если запрашиваемая версия API не может быть сопоставлена.

### <a name="api-version-unspecified"></a>Версия API не указана

Происходит, когда клиент выполняет запрос без предоставления версии API.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

### <a name="unsupported-api-version"></a>Неподдерживаемая версия API

Происходит, когда запрошенная версия API клиента не соответствует ни одной из поддерживаемых версий API на сервере.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="invalid-api-version"></a>Недопустимая версия API

Происходит, когда клиент выполняет запрос с версией API, но значение имеет неправильный формат или не может быть проанализировано сервером.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="ambiguous-api-version"></a>Неоднозначная версия API

Происходит, когда клиент запрашивает версию API, неоднозначность для сервера. Например, несколько различных значений.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
