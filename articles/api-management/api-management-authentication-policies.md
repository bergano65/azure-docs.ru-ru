---
title: Политики аутентификации в службе управления API Azure | Документация Майкрософт
description: Сведения о политиках аутентификации, доступных для использования в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 5ca9bd4964cf190eaa2be6d66d57c7ada971d675
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442398"
---
# <a name="api-management-authentication-policies"></a>Политики аутентификации в службе управления API
В этой статье рассматриваются приведенные ниже политики управления API. Дополнительные сведения о добавлении и настройке политик см. в статье о [политиках в управлении API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a> Политики аутентификации

-   [Обычная проверка подлинности](api-management-authentication-policies.md#Basic) – обычная проверка подлинности внутренней службы.

-   [Аутентификация с помощью сертификата клиента](api-management-authentication-policies.md#ClientCertificate) – аутентификация внутренней службы с помощью сертификатов клиентов.

-   Проверка подлинности [с помощью управляемого удостоверения](api-management-authentication-policies.md#ManagedIdentity) — проверка подлинности с помощью [управляемого удостоверения](../active-directory/managed-identities-azure-resources/overview.md) для службы управления API.

##  <a name="Basic"></a> Обычная проверка подлинности
 Используйте политику `authentication-basic` для обычной проверки подлинности внутренней службы. Эта политика задает для заголовка авторизации HTTP значение, соответствующее учетным данным, предоставленным в политике.

### <a name="policy-statement"></a>Правило политики

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Пример

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Элементы

|Название|Описание|Обязательное значение|
|----------|-----------------|--------------|
|authentication-basic|Корневой элемент.|Да|

### <a name="attributes"></a>Атрибуты

|Название|Описание|Обязательное значение|Значение по умолчанию|
|----------|-----------------|--------------|-------------|
|username|Задает имя пользователя для обычных учетных данных.|Да|Н/Д|
|password|Задает пароль для обычных учетных данных.|Да|Н/Д|

### <a name="usage"></a>Использование
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.

-   **Области политики:** все области.

##  <a name="ClientCertificate"></a> Аутентификация с помощью сертификата клиента
 Используйте политику `authentication-certificate` для аутентификации внутренней службы с помощью сертификата клиента. Сертификат сначала должен быть [установлен в службу управления API](https://go.microsoft.com/fwlink/?LinkID=511599) и идентифицирован по его отпечатку.

### <a name="policy-statement"></a>Правило политики

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Примеры

В этом примере сертификат клиента идентифицируется по его отпечатку.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
В этом примере сертификат клиента идентифицируется по имени ресурса.
```xml
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />
```

### <a name="elements"></a>Элементы

|Имя|Описание|Обязательное значение|
|----------|-----------------|--------------|
|authentication-certificate|Корневой элемент.|Да|

### <a name="attributes"></a>Атрибуты

|Название|Описание|Обязательное значение|Значение по умолчанию|
|----------|-----------------|--------------|-------------|
|thumbprint|Отпечаток для сертификата клиента.|Должен присутствовать либо `thumbprint`. `certificate-id`|Н/Д|
|ИД сертификата|Имя ресурса сертификата.|Должен присутствовать либо `thumbprint`. `certificate-id`|Н/Д|

### <a name="usage"></a>Использование
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.

-   **Области политики:** все области.

##  <a name="ManagedIdentity"></a>Проверка подлинности с помощью управляемого удостоверения
 `authentication-managed-identity` Используйте политику для аутентификации в серверной службе с помощью управляемого удостоверения службы управления API. Эта политика эффективно использует управляемое удостоверение для получения маркера доступа из Azure Active Directory для доступа к указанному ресурсу.

### <a name="policy-statement"></a>Правило политики

```xml
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>
```

### <a name="example"></a>Пример

```xml
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" />
```

### <a name="elements"></a>Элементы

|Название|Описание|Обязательное значение|
|----------|-----------------|--------------|
|Проверка подлинности — управляемое удостоверение |Корневой элемент.|Да|

### <a name="attributes"></a>Атрибуты

|Название|Описание|Обязательное значение|Значение по умолчанию|
|----------|-----------------|--------------|-------------|
|ресурс|Строка. URI идентификатора приложения целевого веб-API (защищенный ресурс) в Azure Active Directory.|Да|Н/Д|
|Output-Token-переменная-имя|Строка. Имя переменной контекста, которая будет принимать значение токена в качестве типа `string`объекта.|Нет|Н/Д|
|ignore-error|Логическое значение. Если задано `true`значение, конвейер политики продолжит выполняться, даже если маркер доступа не получен.|Нет|False|

### <a name="usage"></a>Использование
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.

-   **Области политики:** все области.

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).
