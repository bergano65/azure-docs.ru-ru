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
ms.openlocfilehash: 9ee4a9fb5c63061eed32389b5672652aad01208a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994955"
---
# <a name="api-management-authentication-policies"></a>Политики аутентификации в службе управления API
В этой статье рассматриваются приведенные ниже политики управления API. Дополнительные сведения о добавлении и настройке политик см. в статье о [политиках в управлении API](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Политики аутентификации  
  
-   [Обычная проверка подлинности](api-management-authentication-policies.md#Basic) – обычная проверка подлинности внутренней службы.  
  
-   [Аутентификация с помощью сертификата клиента](api-management-authentication-policies.md#ClientCertificate) – аутентификация внутренней службы с помощью сертификатов клиентов.  

-   [Проверка подлинности с помощью управляемого удостоверения](api-management-authentication-policies.md#ManagedIdentity) -проверка подлинности с помощью [управляемого удостоверения](../active-directory/managed-identities-azure-resources/overview.md) для службы управления API.  
  
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
  
|Name|ОПИСАНИЕ|Обязательно для заполнения|  
|----------|-----------------|--------------|  
|authentication-basic|Корневой элемент.|Yes|  
  
### <a name="attributes"></a>Атрибуты  
  
|Name|ОПИСАНИЕ|Обязательно для заполнения|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|Имя пользователя|Задает имя пользователя для обычных учетных данных.|Yes|Н/Д|  
|password|Задает пароль для обычных учетных данных.|Yes|Н/Д|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики**. API  
  
##  <a name="ClientCertificate"></a> Аутентификация с помощью сертификата клиента  
 Используйте политику `authentication-certificate` для аутентификации внутренней службы с помощью сертификата клиента. Сертификат сначала должен быть [установлен в службу управления API](https://go.microsoft.com/fwlink/?LinkID=511599) и идентифицирован по его отпечатку.  
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Пример  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Элементы  
  
|Name|ОПИСАНИЕ|Обязательно для заполнения|  
|----------|-----------------|--------------|  
|authentication-certificate|Корневой элемент.|Yes|  
  
### <a name="attributes"></a>Атрибуты  
  
|Name|ОПИСАНИЕ|Обязательно для заполнения|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Отпечаток для сертификата клиента.|Yes|Н/Д|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики**. API  

##  <a name="ManagedIdentity"></a> Проверка подлинности с помощью управляемого удостоверения  
 Используйте `authentication-managed-identity` политики для проверки подлинности с серверной службой с помощью управляемого удостоверения службы управления API. Эта политика эффективно использует управляемое удостоверение для получения маркера доступа из Azure Active Directory для доступа к указанному ресурсу. 
  
### <a name="policy-statement"></a>Правило политики  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Пример  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Элементы  
  
|Name|ОПИСАНИЕ|Обязательно для заполнения|  
|----------|-----------------|--------------|  
|управляемое удостоверение проверки подлинности |Корневой элемент.|Yes|  
  
### <a name="attributes"></a>Атрибуты  
  
|Name|ОПИСАНИЕ|Обязательно для заполнения|значение по умолчанию|  
|----------|-----------------|--------------|-------------|  
|resource|Строка. URI идентификатора приложения целевого веб-API (защищенный ресурс) в Azure Active Directory.|Yes|Н/Д|  
|выходные данные — токен variable-name|Строка. Имя переменной контекста, который будет получать значение маркера в качестве типа объекта `string`.|Нет |Н/Д|  
|ignore-error|Логическое значение. Если значение `true`, канал будет продолжать выполняться, даже если маркер доступа не будет получена.|Нет |false|  
  
### <a name="usage"></a>Использование  
 Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Разделы политики:** inbound.  
  
-   **Области политики:** global, product, API, operation.  

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).   
