---
title: Поток на платформе Microsoft Identity & OAuth 2.0 от имени пользователя | Службы
description: В этой статье описывается, как использовать HTTP-сообщения для проверки подлинности между службами с помощью потока On-Behalf-Of в OAuth 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 811fc7a4fc5d8ffba894bad837e95d6b27ecc8c3
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689416"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Платформа Microsoft Identity и OAuth 2,0 от имени потока

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Поток On-Behalf-Of (OBO) в OAuth 2.0 используется в том случае, когда приложение вызывает службу или веб-API, который, в свою очередь, должен вызывать другую службу или веб-API. Идея состоит в том, чтобы распространить делегированное удостоверение пользователя и разрешения с помощью цепочки запросов. Чтобы служба среднего уровня выполняла запросы с проверкой подлинности к подчиненной службе, ей необходимо защитить маркер доступа от платформы Microsoft Identity, от имени пользователя.

В этой статье описывается, как программировать непосредственно по протоколу в приложении.  По возможности рекомендуется использовать поддерживаемые библиотеки проверки подлинности Майкрософт (MSAL) вместо [получения маркеров и вызова защищенных веб-API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Также ознакомьтесь с [примерами приложений, которые используют MSAL](sample-v2-code.md).

> [!NOTE]
>
> - Конечная точка платформы Microsoft Identity не поддерживает все сценарии и функции. Чтобы определить, следует ли использовать конечную точку платформы идентификации Майкрософт, ознакомьтесь с [ограничениями платформы удостоверений Майкрософт](active-directory-v2-limitations.md). 
> - По состоянию на май 2018 года какой-либо производный маркер неявного потока `id_token` не может использоваться для потока OBO. Вместо этого одностраничные приложения (SPA) должны передавать свои маркеры **доступа** в конфиденциальный клиент среднего уровня для выполнения потоков OBO. Дополнительные сведения о том, какие клиенты могут выполнять вызовы OBO, см. в разделе об [ограничениях](#client-limitations).

## <a name="protocol-diagram"></a>Схема протокола

Предположим, что пользователь прошел проверку подлинности в приложении, используя [поток предоставления кода авторизации OAuth 2,0](v2-oauth2-auth-code-flow.md) или другой поток входа. На этом этапе приложение содержит маркер доступа для *API А* (маркер A) с утверждениями пользователя и разрешением на доступ к веб-API среднего уровня (API A). Теперь API A должен выполнить запрос к веб-API нижнего уровня (API B) с проверкой подлинности.

Последующие шаги образуют поток OBO. Эти шаги поясняются на следующей схеме.

![Показывает поток от имени по протоколу OAuth 2.0](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Клиентское приложение отправляет запрос к API A с маркером A (с утверждением `aud` API А).
1. API A выполняет проверку подлинности конечной точки выдачи маркера платформы идентификации Майкрософт и запрашивает маркер для доступа к API B.
1. Конечная точка выдачи маркера платформы идентификации Майкрософт проверяет учетные данные API а вместе с маркером а и выдает маркер доступа для API B (токен B) API A.
1. Токен B задается API A в заголовке авторизации запроса к API B.
1. Данные из защищенного ресурса возвращаются API B в API а и от него в клиенте.

> [!NOTE]
> В этом сценарии служба среднего уровня получает согласие пользователя на доступ к API нижнего уровня без взаимодействия с пользователем. Поэтому согласие на доступ к API нижнего уровня необходимо получить заранее при принятии условий во время проверки подлинности. Чтобы узнать, как настроить это для приложения, ознакомьтесь с разделом [Получение согласия для приложения среднего уровня](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Запрос маркера взаимного доступа между службами

Чтобы запросить маркер доступа, выполните HTTP-запрос POST к конечной точке маркера платформы Microsoft Identity для конкретного клиента со следующими параметрами.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Существуют два сценария. Их выбор зависит от типа защиты клиентского приложения — с помощью общего секрета или с помощью сертификата.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Первый сценарий: запрос маркера доступа с помощью общего секрета

При использовании общего секрета запрос маркера взаимного доступа между службами содержит следующие параметры:

| Параметр |  | Description |
| --- | --- | --- |
| `grant_type` | Обязательно для заполнения | Тип запроса маркера. Для запроса с использованием JWT нужно указать значение `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Обязательно для заполнения | Идентификатор приложения (клиента), назначенный приложению [портал Azure регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) странице. |
| `client_secret` | Обязательно для заполнения | Секрет клиента, созданный для приложения на странице портал Azure-Регистрация приложений. |
| `assertion` | Обязательно для заполнения | Значение токена, используемого в запросе.  Этот маркер должен иметь аудиторию приложения, делающего этот запрос OBO (приложение, обозначенное полем `client-id`). |
| `scope` | Обязательно для заполнения | Список областей для запроса токена, разделенный пробелами. Дополнительные сведения см. в разделе [Области](v2-permissions-and-consent.md). |
| `requested_token_use` | Обязательно для заполнения | Указывает, как должен быть обработан запрос. В потоке OBO нужно указать значение `on_behalf_of`. |

#### <a name="example"></a>Пример

Следующий HTTP-запрос POST запрашивает маркер и маркер обновления доступа с областью `user.read` для веб-API https://graph.microsoft.com.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Второй сценарий: запрос маркера доступа с помощью сертификата

Запрос маркера взаимного доступа между службами с помощью сертификата содержит следующие параметры:

| Параметр |  | Description |
| --- | --- | --- |
| `grant_type` | Обязательно для заполнения | Тип запроса токена. Для запроса с использованием JWT нужно указать значение `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Обязательно для заполнения |  Идентификатор приложения (клиента), назначенный приложению [портал Azure регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) странице. |
| `client_assertion_type` | Обязательно для заполнения | Значение должно быть равно `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Обязательно для заполнения | Утверждение (JSON Web Token), которое необходимо создать и подписать с помощью сертификата, зарегистрированного как учетные данные для приложения. Ознакомьтесь с информацией об [учетных данных сертификата](active-directory-certificate-credentials.md), чтобы узнать, как зарегистрировать сертификат и задать формат утверждения. |
| `assertion` | Обязательно для заполнения | Значение токена, используемого в запросе. |
| `requested_token_use` | Обязательно для заполнения | Указывает, как должен быть обработан запрос. В потоке OBO нужно указать значение `on_behalf_of`. |
| `scope` | Обязательно для заполнения | Список областей для запроса маркера, разделенный пробелами. Дополнительные сведения см. в разделе [Области](v2-permissions-and-consent.md).|

Обратите внимание на то, что параметры являются почти такими же, как и при использовании запроса с помощью общего секрета, за исключением параметра `client_secret`, который заменяется двумя параметрами: `client_assertion_type` и `client_assertion`.

#### <a name="example"></a>Пример

Ниже приведен HTTP-запрос POST маркера доступа с областью `user.read` для веб-API https://graph.microsoft.com с сертификатом.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Ответ для токена доступа между службами

Если доступ предоставлен, ответ будет содержать JSON-файл OAuth 2.0 со следующими параметрами.

| Параметр | Description |
| --- | --- |
| `token_type` | Указывает значение типа маркера. Единственным типом, поддерживаемым платформой Microsoft Identity, является `Bearer`. Дополнительные сведения о токенах носителя см. в разделе [инфраструктура авторизации OAuth 2,0: использование токена носителя (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Область доступа, предоставляемая токеном. |
| `expires_in` | Срок действия маркера доступа (в секундах). |
| `access_token` | Запрашиваемый маркер доступа. Вызывающая служба может использовать этот токен для проверки подлинности принимающей службы. |
| `refresh_token` | Токен обновления для запрошенного токена доступа. Вызывающая служба может использовать этот токен для запроса другого токена доступа после того, как срок действия текущего токена доступа истек. Маркер обновления предоставляется только в том случае, если запрошена область `offline_access`. |

### <a name="success-response-example"></a>Пример ответа с успешным предоставлением доступа

В следующем примере показано сообщение о предоставлении доступа в ответ на запрос маркера доступа к веб-API https://graph.microsoft.com.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Маркер доступа выше имеет формат версии 1.0. Это обусловлено тем, что маркер предоставляется на основе **ресурса** , к которому осуществляется доступ. Microsoft Graph настроен на принятие маркеров версии 1.0, поэтому платформа Microsoft Identity создает маркеры доступа v 1.0, когда клиент запрашивает маркеры для Microsoft Graph. Только приложениям следует просматривать маркеры доступа. Клиенты **не должны** их проверять.

### <a name="error-response-example"></a>Пример ответа с сообщением об ошибке

Ответ об ошибке возвращается конечной точкой токена при попытке получить маркер доступа для подчиненного API, если для подчиненного API задана политика условного доступа (например, многофакторная проверка подлинности). Служба среднего уровня должна отображать эту ошибку в клиентском приложении, чтобы клиентское приложение могла обеспечить взаимодействие с пользователем в соответствии с политикой условного доступа.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Использование токена доступа для доступа к защищенному ресурсу

Теперь служба среднего уровня может использовать полученный выше токен для запросов к веб-API нижнего уровня с проверкой подлинности путем установки токена в заголовке `Authorization`.

### <a name="example"></a>Пример

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Получение согласия для приложения среднего уровня

В зависимости от архитектуры или использования приложения можно использовать различные стратегии для обеспечения успешного выполнения потока OBO. Во всех случаях конечная цель заключается в обеспечении правильного согласия, чтобы клиентское приложение мог вызвать приложение среднего уровня, а приложение среднего уровня имеет разрешение на вызов ресурса серверной части. 

> [!NOTE]
> Ранее учетная запись Майкрософтная система (личные учетные записи) не поддерживала поле "известное клиентское приложение" и не может показывать комбинированное согласие.  Это было добавлено, и все приложения на платформе Microsoft Identity могут использовать известный подход клиентского приложения для согласия жеттигн для вызовов OBO. 

### <a name="default-and-combined-consent"></a>Область /.default и объединенное согласие

Приложение среднего уровня добавляет клиент в список известных клиентских приложений в своем манифесте, после чего клиент может активировать поток объединенного согласия как для себя, так и для приложения среднего уровня. На конечной точке платформы идентификации Майкрософт это делается с помощью [области`/.default`](v2-permissions-and-consent.md#the-default-scope). При запуске экрана согласия с помощью известных клиентских приложений и `/.default`на экране согласия отображаются **разрешения для клиента на доступ к API** среднего уровня, а также запрашиваются все необходимые разрешения API среднего уровня. Пользователь дает согласие для обоих приложений, после чего выполняется поток OBO.

### <a name="pre-authorized-applications"></a>Предварительно авторизованные приложения

Ресурсы могут указывать, что у данного приложения всегда есть разрешение на получение определенных областей. Это особенно удобно для упрощения подключений между интерфейсным клиентом и внутренним ресурсом. Ресурс может объявить несколько предварительно авторизованных приложений. Любое из них может запросить соответствующие разрешения в потоке OBO и получить их без согласия пользователя.

### <a name="admin-consent"></a>Согласие администратора

Администратор клиента может обеспечить приложения разрешениями для вызова необходимых им API, предоставив согласие администратора для приложения среднего уровня. Для этого администратор может найти приложение среднего уровня в своем клиенте, открыть страницу необходимых разрешений и выбрать предоставляемые разрешения для приложения. Дополнительные сведения о согласии администратора доступны в [документации о согласии и разрешениях](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Использование одного приложения

В некоторых сценариях может использоваться только одна пара приложения среднего уровня и интерфейсного клиента. В этом случае вам может быть проще создать одно приложение, избавившись от потребности в приложении среднего уровня. Для аутентификации между внешним интерфейсом и веб-API можно использовать файлы cookie, маркер id_token или маркер доступа, запрошенный для приложения. Затем можно запросить согласие у этого приложения для доступа к внутреннему ресурсу.

## <a name="client-limitations"></a>Ограничения клиентов

Если клиент использует неявный поток для получения id_token и этот клиент также имеет подстановочные знаки в URL-адресе ответа, id_token нельзя использовать для потока OBO.  Тем не менее, конфиденциальный клиент может активировать маркеры доступа, полученные с помощью потока неявного предоставления, даже если для инициирующего клиента зарегистрирован URL-адрес ответа с подстановочными знаками.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о протоколе OAuth 2.0 и другом способе проверки подлинности между службами с использованием учетных данных клиента.

* [Предоставление учетных данных клиента OAuth 2,0 на платформе Microsoft Identity](v2-oauth2-client-creds-grant-flow.md)
* [Поток кода OAuth 2,0 на платформе Microsoft Identity](v2-oauth2-auth-code-flow.md)
* [Использование области `/.default`](v2-permissions-and-consent.md#the-default-scope)
