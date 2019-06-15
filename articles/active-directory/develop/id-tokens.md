---
title: Microsoft identity платформы идентификатор справочнике по маркерам | Документация Майкрософт
description: Узнайте, как использовать маркеры "id_token", выдаваемых Azure AD версии 1.0 и Microsoft identity платформы (версии 2.0), конечные точки.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms:custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25408b2120a9ac9f38e7959ef8e9dbbb34df7c2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962569"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Маркеры Идентификации платформы удостоверений Microsoft

`id_tokens` отправляются клиентскому приложению как часть потока [Подключение OpenID](v1-protocols-openid-connect-code.md). Они могут отправляться вместе или вместо маркера доступа и использоваться клиентом для проверки подлинности пользователя.

## <a name="using-the-idtoken"></a>Использование маркера идентификатора

Маркеры Идентификации следует использовать для проверки того, что пользователь является, выдают и получить дополнительную полезную информацию о них, — не должны использоваться для авторизации вместо [маркер доступа](access-tokens.md). Утверждения, которые он предоставляет, могут быть использованы для пользовательского интерфейса в приложении, для добавления ключей базы данных и обеспечения доступа к клиентскому приложению.

## <a name="claims-in-an-idtoken"></a>Утверждения в маркере идентификатора

`id_tokens` — это [JWT](https://tools.ietf.org/html/rfc7519) для идентификации Майкрософт, они состоят из заголовка, полезных данных и подписи. Заголовок и подпись можно использовать для проверки подлинности маркера тогда, когда полезные данные содержат сведения о пользователе, запрашиваемые клиентом. Исключением будут случаи, когда все утверждения, перечисленные здесь, отображаются как в маркерах версии 1.0, так и в версии 2.0.

### <a name="v10"></a>Версия 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Просмотрите этот пример маркера версии 1.0 в [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>Версия 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Просмотрите этот пример маркера версии 2.0 в [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Утверждения заголовка

|Утверждение | Формат | Описание |
|-----|--------|-------------|
|`typ` | Строка — всегда JWT | Указывает, что маркер имеет тип JWT.|
|`alg` | String | Обозначает алгоритм, с помощью которого был подписан маркер. Пример: "RS256" |
|`kid` | String | Отпечаток для открытого ключа, используемого для подписи этого маркера. Выпущенные `id_tokens` версий 1.0 и 2.0. |
|`x5t` | String | Также как и `kid` (использование и значение). Тем не менее, это устаревшее утверждение, выпускаемое для обеспечения совместимости только `id_tokens` версии 1.0. |

### <a name="payload-claims"></a>Утверждения полезных данных

В списке отображаются утверждения, которые находятся в большинстве маркеры "id_token" по умолчанию (за исключением оговоренных случаев).  Тем не менее, приложение может использовать [необязательные утверждения](active-directory-optional-claims.md) запрашивать дополнительные утверждения в id_token.  Они могут простираться от `groups` заявляют, что сведения об имени пользователя.

|Утверждение | Формат | Описание |
|-----|--------|-------------|
|`aud` |  Строка с URI идентификатора приложения | Определяет целевого получателя маркера. Аудитория — это идентификатор вашего приложения в `id_tokens`, назначенный приложению на портале Azure. Приложение должно проверить это значение и отклонить маркер, если он ему не соответствует. |
|`iss` |  Строка с URI службы токенов безопасности | Определяет службу маркеров безопасности (STS), которая создает и возвращает маркер, а также клиент Azure AD, в котором пользователь прошел аутентификацию. Если маркер был выпущен конечной точкой версии 2.0, универсальный код ресурса (URI) заканчивается значением `/v2.0`.  GUID, который указывает, что пользователь является потребителем с учетной записью Майкрософт: `9188040d-6c67-4c5b-b112-36a304b66dad`. Приложению также следует использовать часть утверждения, содержащую GUID, для ограничения списка клиентов, которым разрешено входить в приложение, если это применимо. |
|`iat` |  int, метка времени UNIX | Значение Issued At (Выпущено в) показывает, когда произошла проверка подлинности этого маркера.  |
|`idp`|Строка, обычно — URI STS | Фиксирует поставщика удостоверений, который проверил подлинность субъекта маркера. Это значение идентично значению утверждения издателя, за исключением случаев, когда учетная запись пользователя и издатель принадлежат разным клиентам (например, гости). Если утверждение отсутствует, это означает, что значение `iss` взамен можно использовать.  Для пользовательских учетных записей, используемых в контексте организации (например, приглашение пользовательской учетной записи в клиент Azure AD), утверждение `idp` может быть live.com или URI STS, содержащим клиент учетной записи Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, метка времени UNIX | Утверждение "nbf" (не ранее) определяет время, до которого маркер JWT НЕ ДОЛЖЕН приниматься в обработку.|
|`exp` |  int, метка времени UNIX | Утверждение "exp" (время окончания срока действия) указывает время окончания срока действия или время, после которого маркер JWT НЕ ДОЛЖЕН приниматься в обработку.  Важно отметить, что ресурс может отклонить маркер, до этого времени также -, если, к примеру, когда требуется изменение в проверке подлинности или обнаружена отзыве маркеров. |
| `c_hash`| String |Хэш-код включается в состав маркеров идентификации, только если маркер идентификации выдается вместе с кодом авторизации OAuth 2.0. Его можно использовать для проверки подлинности кода авторизации. Дополнительные сведения о выполнении этой проверки можно найти в [спецификации OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) . |
|`at_hash`| String |Хэш маркера доступа включается в состав маркеров идентификации, только если маркер выдается вместе с маркером доступа OAuth 2.0. Его можно использовать для проверки подлинности маркера доступа. Дополнительные сведения о выполнении этой проверки можно найти в [спецификации OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) . |
|`aio` | Непрозрачная строка | Внутреннее утверждение, используемое Azure AD для записи данных для повторного использования маркеров. Можно пропустить.|
|`preferred_username` | String | Основное имя пользователя, представляющее пользователя. Это может быть адрес электронной почты, телефонный номер или универсальное имя пользователя без определенного формата. Его значение не является неизменяемым и может меняться с течением времени. Так как это значение является изменяемым, его нельзя использовать для принятия решений об авторизации. `profile` Необходимо указать область для получения этого утверждения.|
|`email` | String | Утверждение `email` по умолчанию указывается для гостевых учетных записей, у которых есть адрес электронной почты.  Приложение может запрашивать утверждение электронной почты для управляемых пользователей (пользователи из того же клиента, что и ресурс) с помощью [необязательного утверждения](active-directory-optional-claims.md) `email`.  На конечной точке версии 2.0 приложение может также запросить область OpenID Connect `email`. Чтобы получить утверждение, вам не нужно запрашивать необязательное утверждение и область.  Утверждение электронной почты поддерживает только адресуемую почту из профиля со сведениями о пользователе. |
|`name` | String | Утверждение `name` предоставляет удобное для восприятия значение, определяющее субъект маркера. Гарантируется, что значение не должно быть уникальным, оно является изменяемым и может использоваться только для отображения. `profile` Необходимо указать область для получения этого утверждения. |
|`nonce`| String | Функция nonce соответствует параметру, включенному в исходный запрос или запрос авторизации в IDP. Если он не соответствует, приложению следует отклонить маркер. |
|`oid` | Строка с идентификатором GUID | Неизменяемый идентификатор объекта в системе идентификации Майкрософт. В данном случае это учетная запись пользователя. Этот идентификатор уникально идентифицирует пользователя в приложениях. Если один пользователь войдет в два различных приложения с помощью двух разных идентификаторов клиента, эти приложения получат одинаковое значение в утверждении `oid`. Microsoft Graph возвратит этот идентификатор в качестве свойства `id` указанной учетной записи. Так как `oid` позволяет нескольким приложениям сопоставлять пользователей, `profile` необходимо указать область для получения этого утверждения. Обратите внимание, что если один пользователь существует в нескольких клиентах, пользователь будет содержать разные Идентификаторы объекта в каждом клиенте считаются разными учетными записями, несмотря на то, что пользователь входит в каждой учетной записи с теми же учетными данными. |
|`roles`| Массив строк | Набор ролей, которые были назначены пользователю, который выполняет вход. |
|`rh` | Непрозрачная строка |Внутреннее утверждение, используемое Azure для повторной проверки маркеров. Можно пропустить. |
|`sub` | Строка с идентификатором GUID | Субъект, в отношении которого маркер утверждает сведения, например данные о пользователе приложения. Это значение является неизменяемым и не может быть переназначено или повторно использовано. Субъект — это попарный идентификатор, который является уникальным для определенного идентификатора приложения. Если один пользователь войдет в два различных приложения с помощью двух разных идентификаторов клиента, эти приложения получат два разных значения в утверждении субъекта. Это может или не может быть нежелательным в зависимости от требований архитектуры и конфиденциальности. |
|`tid` | Строка с идентификатором GUID | Идентификатор GUID, представляющий клиент Azure AD пользователя. Для рабочих и учебных учетных записей значением GUID является неизменяемый идентификатор клиента организации, к которой принадлежит пользователь. Для личных учетных записей значением является `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Необходимо указать область для получения этого утверждения. |
|`unique_name` | String | Предоставляет удобное для восприятия значение, которое идентифицирует субъект маркера. Это значение необязательно является уникальным в пределах клиента и должен использоваться только для отображения. Выпущен только `id_tokens` версии 1.0. |
|`uti` | Непрозрачная строка | Внутреннее утверждение, используемое Azure для повторной проверки маркеров. Можно пропустить. |
|`ver` | Строка, версия 1.0 или 2.0 | Указывает номер версии маркера "id_token". |

## <a name="validating-an-idtoken"></a>Проверка маркера id_token

Проверка `id_token` аналогичны первым шагом [проверка маркера доступа](access-tokens.md#validating-tokens) - клиент необходимо проверить, правильного издателя отправил обратно маркера и, оно не было изменено. Поскольку маркер `id_tokens` ​​всегда является JWT, существует множество библиотек для проверки этих маркеров. Мы рекомендуем использовать одну из них, а не создавать их самостоятельно.

Чтобы вручную проверить маркер, см. в разделе [Проверка маркера доступа](access-tokens.md#validating-tokens). После проверки подписи маркера, следует проверить следующие утверждения в маркере "id_token" (это также можно сделать в библиотеке проверки маркеров).

* Метки времени. Метки времени `iat`, `nbf`, и `exp` следует соответственно выполнить до или после текущего момента времени. 
* Аудитория. Утверждение `aud` должно соответствовать идентификатору вашего приложения.
* Nonce. Утверждение `nonce` в полезных данных должно соответствовать параметру nonce, переданному в конечную точку, или авторизации конечной точки во время первоначального запроса.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [маркеры доступа](access-tokens.md)
* Настроить утверждения в id_token с помощью [необязательные утверждения](active-directory-optional-claims.md).