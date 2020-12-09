---
title: REST API конфигурации приложений Azure — авторизация HMAC
description: Используйте HMAC для авторизации в конфигурации приложения Azure с помощью REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932665"
---
# <a name="hmac-authorization---rest-api-reference"></a>Справочник по авторизации HMAC-REST API

При использовании проверки подлинности HMAC операции попадают в одну из двух категорий: чтение или запись. Ключи доступа для чтения и записи предоставляют разрешение на вызов всех операций. Ключи доступа только для чтения предоставляют разрешение на вызов только операций чтения. Определяет, доступен ли ключ доступа только для чтения или для чтения и записи, с помощью его `readOnly` Свойства. Любая попытка сделать запрос на запись с помощью ключа доступа только для чтения приведет к несанкционированному доступу.

## <a name="obtaining-access-keys"></a>Получение ключей доступа

Спецификация, описывающая ключи доступа и API, используемые для их получения, подробно описана в спецификации поставщика ресурсов конфигурации приложений [Azure.](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json) Ключи доступа получаются с помощью операции "ConfigurationStores_ListKeys".

## <a name="errors"></a>ошибки

```http
HTTP/1.1 403 Forbidden
```

**Причина:** Ключ доступа, используемый для проверки подлинности запроса, не предоставляет необходимые разрешения для выполнения запрошенной операции.
**Решение:** Получение ключа доступа, который предоставляет разрешение на выполнение запрошенной операции и его использование для проверки подлинности запроса.
