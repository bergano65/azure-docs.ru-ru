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
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473563"
---
# <a name="api-management-authentication-policies"></a>Политики аутентификации в службе управления API
В этой статье рассматриваются приведенные ниже политики управления API. Дополнительные сведения о добавлении и настройке политик см. в статье о [политиках в управлении API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>Политика аутентификации

-   [Обычная проверка подлинности](api-management-authentication-policies.md#Basic) – обычная проверка подлинности внутренней службы.

-   [Аутентификация с помощью сертификата клиента](api-management-authentication-policies.md#ClientCertificate) – аутентификация внутренней службы с помощью сертификатов клиентов.

-   [Authenticate с управляемой идентификацией](api-management-authentication-policies.md#ManagedIdentity) - Authenticate с [управляемой идентификацией](../active-directory/managed-identities-azure-resources/overview.md) для службы управления API.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Обычная проверка подлинности
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

|name|Описание|Обязательно|
|----------|-----------------|--------------|
|authentication-basic|Корневой элемент.|Да|

### <a name="attributes"></a>Атрибуты

|name|Описание|Обязательно|Значение по умолчанию|
|----------|-----------------|--------------|-------------|
|username|Задает имя пользователя для обычных учетных данных.|Да|Недоступно|
|password|Задает пароль для обычных учетных данных.|Да|Недоступно|

### <a name="usage"></a>Использование
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.

-   **Области политики:** все области.

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Аутентификация с помощью сертификата клиента
 Используйте политику `authentication-certificate` для аутентификации внутренней службы с помощью сертификата клиента. Сертификат сначала должен быть [установлен в службу управления API](https://go.microsoft.com/fwlink/?LinkID=511599) и идентифицирован по его отпечатку.

### <a name="policy-statement"></a>Правило политики

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Примеры

В этом примере сертификат клиента идентифицируется по отпечатку пальца.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
В этом примере сертификат клиента идентифицируется по имени ресурса.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Элементы  
  
|name|Описание|Обязательно|  
|----------|-----------------|--------------|  
|authentication-certificate|Корневой элемент.|Да|  
  
### <a name="attributes"></a>Атрибуты  
  
|name|Описание|Обязательно|Значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Отпечаток для сертификата клиента.|`thumbprint` Либо, `certificate-id` либо должно присутствовать.|Недоступно|  
|сертификат-id|Имя ресурса сертификата.|`thumbprint` Либо, `certificate-id` либо должно присутствовать.|Недоступно|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики:** все области.  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Authenticate с управляемой идентичности  
 Используйте `authentication-managed-identity` политику для проверки подлинности с помощью службы бэкэнда, используя управляемую идентификацию службы управления API. Эта политика по существу использует управляемый инообразный доступ для получения токена доступа из каталога Azure Active Для доступа к указанному ресурсу. После успешного получения токена политика установит значение токена в заголовке `Authorization` с помощью схемы. `Bearer`
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Пример  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Используйте управляемые итог для проверки подлинности с помощью бэкэнд-сервиса
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Использование управляемой идентификации в политике отправки запроса
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Элементы  
  
|name|Описание|Обязательно|  
|----------|-----------------|--------------|  
|аутентификация-управляемая-идентификация |Корневой элемент.|Да|  
  
### <a name="attributes"></a>Атрибуты  
  
|name|Описание|Обязательно|Значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|ресурс|Строка. Идентификатор приложения целевого веб-API (защищенный ресурс) в Active Directory Azure.|Да|Недоступно|  
|выходно-переменный-имя вывода|Строка. Имя переменной контекста, которая получит значение `string`маркера в качестве типа объекта. |нет|Недоступно|  
|ignore-error|Логическое. При установке `true`конвейера политики будет продолжать выполняться, даже если токен доступа не получен.|нет|false|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики:** все области.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).
