---
title: Защита API-интерфейсов с помощью аутентификации на основе сертификата клиента в службе управления API Azure | Документация Майкрософт
description: Узнайте, как обеспечить безопасный доступ к API-интерфейсам с помощью сертификатов клиентов
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427349"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Защита API-интерфейсов с помощью аутентификации на основе сертификата клиента в службе управления API Azure

Служба управления API помогает защитить доступ к API-интерфейсам (например, осуществляемый клиентом к службе управления API) с помощью сертификатов клиента. Можно проверить входящий сертификат и проверьте свойства сертификата от нужные значения, с помощью выражений политики.

Сведения о защите доступа к серверной службе API с помощью сертификатов клиентов (т. е. Управление API к серверной части), см. в разделе [защита внутренних служб, с помощью клиента проверки подлинности сертификата](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Для получения и проверка сертификатов клиента на уровне потребления необходимо сначала включить «Запрос сертификата клиента» параметр в колонке «Личные домены», как показано ниже.

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
> Чтобы отключить проверку использования списка отзыва сертификатов `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат является самозаверяющим, root (или средний уровень) сертификатов ЦС должен быть [отправлен](api-management-howto-ca-certificates.md) к службе управления API для `context.Request.Certificate.Verify()` и `context.Request.Certificate.VerifyNoRevocation()` для работы.

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
> Чтобы отключить проверку использования списка отзыва сертификатов `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат является самозаверяющим, root (или средний уровень) сертификатов ЦС должен быть [отправлен](api-management-howto-ca-certificates.md) к службе управления API для `context.Request.Certificate.Verify()` и `context.Request.Certificate.VerifyNoRevocation()` для работы.

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
> Чтобы отключить проверку использования списка отзыва сертификатов `context.Request.Certificate.VerifyNoRevocation()` вместо `context.Request.Certificate.Verify()`.
> Если сертификат является самозаверяющим, root (или средний уровень) сертификатов ЦС должен быть [отправлен](api-management-howto-ca-certificates.md) к службе управления API для `context.Request.Certificate.Verify()` и `context.Request.Certificate.VerifyNoRevocation()` для работы.

> [!TIP]
> Клиент сертификата взаимоблокировки, описанную в этом [статье](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) могут проявляться несколькими способами, например запросы закрепить, результатов запросов `403 Forbidden` код состояния после истечения времени ожидания, `context.Request.Certificate` является `null`. Обычно эта проблема мешает `POST` и `PUT` запросы с длиной содержимого приблизительно 60 КБ или больше.
> Во избежание этой проблемы необходимо включить параметр «Согласование сертификата клиента» для нужного имен узлов в колонке «Личные домены», как показано ниже. Эта функция доступна не на уровне потребления.

![Согласование сертификата клиента](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Дальнейшие действия

-   [Как защитить серверные службы с помощью аутентификации на основе сертификата клиента](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Как передавать сертификаты](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
