---
title: Безопасные API с помощью аутентификации сертификата клиента в управлении API
titleSuffix: Azure API Management
description: Узнайте, как обеспечить безопасный доступ к API-интерфейсам с помощью сертификатов клиентов
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713145"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Защита API-интерфейсов с помощью аутентификации на основе сертификата клиента в службе управления API Azure

Служба управления API помогает защитить доступ к API-интерфейсам (например, осуществляемый клиентом к службе управления API) с помощью сертификатов клиента. Вы можете проверить входящие свойства сертификата и проверить свойства сертификата на желаемые значения, используя выражения политики.

Для получения информации об обеспечении доступа к бэк-энд-сервису API с использованием сертификатов клиента (т.е. API Management to backend) см. [Как обеспечить безопасность бэк-энд-сервисов с помощью аутентификации сертификата клиента](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Для получения и проверки сертификатов клиента по http/2 в уровнях разработчика, базового, стандартного или премиум-класса необходимо включить параметр "Переговоры о клиенте" на лезвии "Таможенные домены", как показано ниже.

![Переговорный сертификат клиента](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Для получения и проверки сертификатов клиента в уровне Потребления необходимо включить параметр "Запрос сертификата клиента" на лезвии "Таможенные домены", как показано ниже.

![Запрос сертификата клиента](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Проверка издателя и субъекта

Следующие политики можно настроить для проверки издателя и субъекта сертификата клиента:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Чтобы отключить проверку отзыва сертификата список использовать `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат клиента является самоподписанным, корневой (или промежуточный) сертификат CA `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` (ы) должен быть [загружен](api-management-howto-ca-certificates.md) в API Management для работы и для работы.

## <a name="checking-the-thumbprint"></a>Проверка отпечатка

Следующие политики можно настроить для проверки отпечатка сертификата клиента:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Чтобы отключить проверку отзыва сертификата список использовать `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат клиента является самоподписанным, корневой (или промежуточный) сертификат CA `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` (ы) должен быть [загружен](api-management-howto-ca-certificates.md) в API Management для работы и для работы.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Проверка отпечатка на соответствие сертификатам, переданным в службу управления API

В примере ниже показано, как проверить отпечаток сертификата клиента на соответствие сертификатам, переданным в службу управления API.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Чтобы отключить проверку отзыва сертификата список использовать `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат клиента является самоподписанным, корневой (или промежуточный) сертификат CA `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` (ы) должен быть [загружен](api-management-howto-ca-certificates.md) в API Management для работы и для работы.

> [!TIP]
> Проблема взаимоблокировки сертификата клиента, описанная в этой [статье,](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) может проявляться `403 Forbidden` несколькими способами, например, замораживание запросов, запросы приводят к коду состояния после тайм-аута, `context.Request.Certificate` есть. `null` Эта проблема обычно `POST` `PUT` затрагивает и запросы с длиной содержимого около 60KB или больше.
> Чтобы предотвратить возникновение этой проблемы, включите настройки "Переговоры о клиентском сертификате" для желаемых имен хоста на лезвии "Custom domains", как показано ниже. Эта функция недоступна в уровне потребления.

![Переговорный сертификат клиента](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Дальнейшие действия

-   [Как защитить серверные службы с помощью аутентификации на основе сертификата клиента](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Как передавать сертификаты](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
