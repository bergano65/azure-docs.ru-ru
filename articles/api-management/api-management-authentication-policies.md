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
ms.openlocfilehash: 572d8c4b59622156e8b3aca4565bbc206367f6d4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514857"
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

|Name|Описание|Обязательно для заполнения|
|----------|-----------------|--------------|
|authentication-basic|Корневой элемент.|ДА|

### <a name="attributes"></a>Атрибуты

|Name|Описание|Обязательно для заполнения|значение по умолчанию|
|----------|-----------------|--------------|-------------|
|Имя пользователя|Задает имя пользователя для обычных учетных данных.|ДА|Н/Д|
|пароль|Задает пароль для обычных учетных данных.|ДА|Н/Д|

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
  
|Name|Описание|Обязательно для заполнения|  
|----------|-----------------|--------------|  
|authentication-certificate|Корневой элемент.|ДА|  
  
### <a name="attributes"></a>Атрибуты  
  
|Name|Описание|Обязательно для заполнения|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Отпечаток для сертификата клиента.|Необходимо указать либо `thumbprint`, либо `certificate-id`.|Н/Д|  
|ИД сертификата|Имя ресурса сертификата.|Необходимо указать либо `thumbprint`, либо `certificate-id`.|Н/Д|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики:** все области.  

##  <a name="ManagedIdentity"></a>Проверка подлинности с помощью управляемого удостоверения  
 Используйте политику `authentication-managed-identity` для аутентификации в серверной службе с помощью управляемого удостоверения службы управления API. Эта политика по сути использует управляемое удостоверение для получения маркера доступа из Azure Active Directory для доступа к указанному ресурсу. После успешного получения маркера политика задаст значение маркера в заголовке `Authorization` с помощью схемы `Bearer`.
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Пример  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Использование управляемого удостоверения для проверки подлинности в серверной службе
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
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
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Использование управляемого удостоверения в политике отправки запросов
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Элементы  
  
|Name|Описание|Обязательно для заполнения|  
|----------|-----------------|--------------|  
|Проверка подлинности — управляемое удостоверение |Корневой элемент.|ДА|  
  
### <a name="attributes"></a>Атрибуты  
  
|Name|Описание|Обязательно для заполнения|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|resource|Строка. URI идентификатора приложения целевого веб-API (защищенный ресурс) в Azure Active Directory.|ДА|Н/Д|  
|Output-Token-переменная-имя|Строка. Имя переменной контекста, которая будет принимать значение токена как тип объекта `string`. |Нет|Н/Д|  
|ignore-error|Логическое значение. Если задано значение `true`, то конвейер политики продолжит выполняться, даже если маркер доступа не получен.|Нет|false|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики:** все области.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md)
