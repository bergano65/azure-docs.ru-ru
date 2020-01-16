---
title: Защита API с помощью проверки подлинности на основе сертификата клиента в управлении API
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
ms.openlocfilehash: 3680473209b3d65d616879dfbb9080dfbb9c80f4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75967400"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Защита API-интерфейсов с помощью аутентификации на основе сертификата клиента в службе управления API Azure

Служба управления API помогает защитить доступ к API-интерфейсам (например, осуществляемый клиентом к службе управления API) с помощью сертификатов клиента. Вы можете проверить входящий сертификат и проверить свойства сертификата по нужным значениям с помощью выражений политики.

Сведения о защите доступа к внутренней службе API с помощью сертификатов клиента (т. е. управления API до серверной части) см. в статье [Защита серверных служб с помощью проверки подлинности на основе сертификата клиента](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) .

> [!IMPORTANT]
> Чтобы получить и проверить сертификаты клиента по протоколу HTTP/2 на уровнях Developer, Basic, Standard или Premium, необходимо включить параметр "согласовать сертификат клиента" в колонке "личные домены", как показано ниже.

![Согласование сертификата клиента](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Чтобы получить и проверить сертификаты клиента на уровне потребления, необходимо включить параметр "запросить сертификат клиента" в колонке "личные домены", как показано ниже.

![Запросить сертификат клиента](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

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
> Чтобы отключить проверку списка отзыва сертификатов, используйте `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат клиента является самозаверяющим, необходимо [Отправить](api-management-howto-ca-certificates.md) корневой (или промежуточный) сертификат ЦС в Управление API, чтобы `context.Request.Certificate.Verify()` и `context.Request.Certificate.VerifyNoRevocation()` работать.

## <a name="checking-the-thumbprint"></a>Проверка отпечатка

Следующие политики можно настроить для проверки отпечатка сертификата клиента:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Чтобы отключить проверку списка отзыва сертификатов, используйте `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат клиента является самозаверяющим, необходимо [Отправить](api-management-howto-ca-certificates.md) корневой (или промежуточный) сертификат ЦС в Управление API, чтобы `context.Request.Certificate.Verify()` и `context.Request.Certificate.VerifyNoRevocation()` работать.

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
> Чтобы отключить проверку списка отзыва сертификатов, используйте `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат клиента является самозаверяющим, необходимо [Отправить](api-management-howto-ca-certificates.md) корневой (или промежуточный) сертификат ЦС в Управление API, чтобы `context.Request.Certificate.Verify()` и `context.Request.Certificate.VerifyNoRevocation()` работать.

> [!TIP]
> Проблема взаимоблокировки сертификата клиента, описанная в этой [статье](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) , может проявляться несколькими способами, например запросы на замораживание, запросы, вызывающие `403 Forbidden` код состояния после истечения времени ожидания, `context.Request.Certificate` `null`. Эта проблема обычно влияет на `POST` и `PUT` запросы с длиной содержимого приблизительно 60KB или выше.
> Чтобы предотвратить возникновение этой проблемы, включите параметр "согласовать сертификат клиента" для нужных имен узлов в колонке "личные домены", как показано ниже. Эта функция недоступна в уровне потребления.

![Согласование сертификата клиента](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Дальнейшие действия

-   [Как защитить серверные службы с помощью аутентификации на основе сертификата клиента](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Как передавать сертификаты](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
