---
title: Справочник по маркерам ИДЕНТИФИКАТОРов платформ Microsoft Identity | Документация Майкрософт
description: Узнайте, как использовать id_tokens, генерируемые конечными точками Azure AD версии 1.0 и платформы Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7e910faaf9875b6791135c8721090fa801a7e08
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294190"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Маркеры ИДЕНТИФИКАТОРов платформы Microsoft Identity

`id_tokens` отправляются клиентскому приложению как часть потока [Подключение OpenID](v1-protocols-openid-connect-code.md). Они могут отправляться вместе или вместо маркера доступа и использоваться клиентом для проверки подлинности пользователя.

## <a name="using-the-id_token"></a>Использование маркера идентификатора

Токены идентификации следует использовать для проверки того, что пользователь должен получить дополнительную полезную информацию о них, и не должен использоваться для авторизации вместо [маркера доступа](access-tokens.md). Предоставляемые им заявки можно использовать для UX в приложении, в качестве ключей в базе данных и предоставления доступа к клиентскому приложению.  При создании ключей для базы данных `idp` не следует использовать, так как они изменяют гостевые сценарии.  Ключ должен выполняться только в `sub` (который всегда уникален), при этом `tid` используется для маршрутизации, если это необходимо.  Если требуется совместное использование данных в разных службах, `oid`+`sub`+`tid` будет работать, так как несколько служб получают одинаковые `oid`.

## <a name="claims-in-an-id_token"></a>Утверждения в маркере идентификатора

`id_tokens` — это [JWT](https://tools.ietf.org/html/rfc7519) для идентификации Майкрософт, они состоят из заголовка, полезных данных и подписи. Заголовок и подпись можно использовать для проверки подлинности маркера тогда, когда полезные данные содержат сведения о пользователе, запрашиваемые клиентом. Исключением будут случаи, когда все утверждения, перечисленные здесь, отображаются как в маркерах версии 1.0, так и в версии 2.0.

### <a name="v10"></a>Версия 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Просмотрите этот пример маркера версии 1.0 в [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>Версия 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Просмотрите этот пример маркера версии 2.0 в [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Утверждения заголовка

|Утверждение | Формат | Description |
|-----|--------|-------------|
|`typ` | Строка — всегда JWT | Указывает, что маркер имеет тип JWT.|
|`alg` | String | Обозначает алгоритм, с помощью которого был подписан маркер. Пример: "RS256" |
|`kid` | String | Отпечаток для открытого ключа, используемого для подписи этого маркера. Выпущенные `id_tokens` версий 1.0 и 2.0. |
|`x5t` | String | Также как и `kid` (использование и значение). Тем не менее, это устаревшее утверждение, выпускаемое для обеспечения совместимости только `id_tokens` версии 1.0. |

### <a name="payload-claims"></a>Утверждения полезных данных

В этом списке отображаются утверждения, которые в большинстве id_tokens по умолчанию (за исключением указанных случаев).  Однако приложение может использовать [необязательные утверждения](active-directory-optional-claims.md) для запроса дополнительных утверждений в id_token.  Они могут варьироваться от `groups` утверждения до сведений об имени пользователя.

|Утверждение | Формат | Description |
|-----|--------|-------------|
|`aud` |  Строка с URI идентификатора приложения | Определяет целевого получателя маркера. Аудитория — это идентификатор вашего приложения в `id_tokens`, назначенный приложению на портале Azure. Приложение должно проверить это значение и отклонить маркер, если он ему не соответствует. |
|`iss` |  Строка с URI службы токенов безопасности | Определяет службу маркеров безопасности (STS), которая создает и возвращает маркер, а также клиент Azure AD, в котором пользователь прошел аутентификацию. Если маркер был выпущен конечной точкой версии 2.0, универсальный код ресурса (URI) заканчивается значением `/v2.0`.  GUID, который указывает, что пользователь является потребителем с учетной записью Майкрософт: `9188040d-6c67-4c5b-b112-36a304b66dad`. Приложению также следует использовать часть утверждения, содержащую GUID, для ограничения списка клиентов, которым разрешено входить в приложение, если это применимо. |
|`iat` |  int, метка времени UNIX | Значение Issued At (Выпущено в) показывает, когда произошла проверка подлинности этого маркера.  |
|`idp`|Строка, обычно — URI STS | Фиксирует поставщика удостоверений, который проверил подлинность субъекта маркера. Это значение идентично значению утверждения издателя, за исключением случаев, когда учетная запись пользователя и издатель принадлежат разным клиентам (например, гости). Если утверждение отсутствует, это означает, что вместо него можно использовать значение `iss`.  Для пользовательских учетных записей, используемых в контексте организации (например, приглашение пользовательской учетной записи в клиент Azure AD), утверждение `idp` может быть live.com или URI STS, содержащим клиент учетной записи Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, метка времени UNIX | Утверждение "nbf" (не ранее) определяет время, до которого маркер JWT НЕ ДОЛЖЕН приниматься в обработку.|
|`exp` |  int, метка времени UNIX | Утверждение "exp" (время окончания срока действия) указывает время окончания срока действия или время, после которого маркер JWT НЕ ДОЛЖЕН приниматься в обработку.  Важно отметить, что ресурс может отклонить маркер до этого времени, если, например, требуется изменение проверки подлинности или обнаружен отзыв маркера. |
| `c_hash`| String |Хэш-код включается в состав маркеров идентификации, только если маркер идентификации выдается вместе с кодом авторизации OAuth 2.0. Его можно использовать для проверки подлинности кода авторизации. Дополнительные сведения о выполнении этой проверки можно найти в [спецификации OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) . |
|`at_hash`| String |Хэш маркера доступа включается в состав маркеров идентификации, только если маркер выдается вместе с маркером доступа OAuth 2.0. Его можно использовать для проверки подлинности маркера доступа. Дополнительные сведения о выполнении этой проверки можно найти в [спецификации OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) . |
|`aio` | Непрозрачная строка | Внутреннее утверждение, в котором Azure AD сохраняет данные для повторного использования маркеров. Можно пропустить.|
|`preferred_username` | String | Основное имя пользователя, представляющее пользователя. Это может быть адрес электронной почты, телефонный номер или универсальное имя пользователя без определенного формата. Его значение не является неизменяемым и может меняться с течением времени. Так как это значение является изменяемым, его нельзя использовать для принятия решений об авторизации. Для получения этого утверждения требуется область `profile`.|
|`email` | String | Утверждение `email` по умолчанию указывается для гостевых учетных записей, у которых есть адрес электронной почты.  Приложение может запросить утверждение электронной почты для управляемых пользователей (тех, которые находятся в том же клиенте, что и ресурс), используя [необязательное утверждение](active-directory-optional-claims.md)`email`.  На конечной точке версии 2.0 приложение может также запросить область OpenID Connect `email`. Чтобы получить утверждение, вам не нужно запрашивать необязательное утверждение и область.  Утверждение электронной почты поддерживает только адресуемую почту из профиля со сведениями о пользователе. |
|`name` | String | Утверждение `name` предоставляет удобное для восприятия значение, определяющее субъект маркера. Значение не обязательно должно быть уникальным, оно является изменяемым и предназначено для использования только в целях показа. Для получения этого утверждения требуется область `profile`. |
|`nonce`| String | Функция nonce соответствует параметру, включенному в исходный запрос или запрос авторизации в IDP. Если он не соответствует, приложению следует отклонить маркер. |
|`oid` | Строка с идентификатором GUID | Неизменяемый идентификатор объекта в системе идентификации Майкрософт. В данном случае это учетная запись пользователя. Этот идентификатор уникально идентифицирует пользователя в приложениях. Если один пользователь войдет в два различных приложения с помощью двух разных идентификаторов клиента, эти приложения получат одинаковое значение в утверждении `oid`. Microsoft Graph возвратит этот идентификатор в качестве свойства `id` указанной учетной записи. Поскольку `oid` позволяет нескольким приложениям сопоставлять пользователей, для получения этого утверждения требуется область `profile`. Обратите внимание, что если один пользователь существует в нескольких клиентах, он будет содержать разные ИДЕНТИФИКАТОРы объектов в каждом клиенте — они считаются разными учетными записями, даже если пользователь входит в каждую учетную запись с теми же учетными данными. Утверждение `oid` является идентификатором GUID и не может быть использовано повторно. |
|`roles`| Массив строк | Набор ролей, назначенных пользователю, который входит в систему. |
|`rh` | Непрозрачная строка |Внутреннее утверждение, используемое Azure для повторной проверки маркеров. Можно пропустить. |
|`sub` | Строка с идентификатором GUID | Субъект, в отношении которого маркер утверждает сведения, например данные о пользователе приложения. Это значение является неизменяемым и не может быть переназначено или повторно использовано. Субъект — это попарный идентификатор, который является уникальным для определенного идентификатора приложения. Если один пользователь входит в два разных приложения с использованием двух разных идентификаторов клиентов, эти приложения получат два разных значения для утверждения субъекта. Это может быть или не может быть в зависимости от требований к архитектуре и конфиденциальности. |
|`tid` | Строка с идентификатором GUID | Идентификатор GUID, представляющий клиент Azure AD пользователя. Для рабочих и учебных учетных записей значением GUID является неизменяемый идентификатор клиента организации, к которой принадлежит пользователь. Для личных учетных записей значением является `9188040d-6c67-4c5b-b112-36a304b66dad`. Для получения этого утверждения требуется область `profile`. |
|`unique_name` | String | Предоставляет удобное для восприятия значение, которое идентифицирует субъект маркера. Это значение является уникальным в любой момент времени, но по мере повторного использования сообщений электронной почты и других идентификаторов это значение может появиться в других учетных записях, и поэтому должно использоваться только в целях отображения. Выпущен только `id_tokens` версии 1.0. |
|`uti` | Непрозрачная строка | Внутреннее утверждение, используемое Azure для повторной проверки маркеров. Можно пропустить. |
|`ver` | Строка со значением 1.0 или 2.0 | Указывает номер версии маркера "id_token". |


> [!NOTE]
> Версии v1 и v2 id_token имеют различия в объеме информации, которая будет рассматриваться в приведенных выше примерах. В версии фактически указана конечная точка платформы Azure AD, из которой она была выдана. [Реализация OAuth Azure AD](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) развивалась по годам. В настоящее время у нас есть две разные конечные точки oAuth для приложений AzureAD. Можно использовать любую из новых конечных точек, которые относятся к категории версии 2 или старой, которая называется v1. Конечные точки OAuth для обоих типов различаются. Конечная точка версии 2 — это новая версия, в которой мы пытаемся перенести все функции конечной точки версии 1 и рекомендовать новым разработчикам использовать конечную точку версии 2. 
> - V1: Azure Active Directory конечные точки: `https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: конечные точки платформы Microsoft Identity: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Проверка маркера id_token

Проверка `id_token` аналогична первому этапу [проверки маркера доступа](access-tokens.md#validating-tokens) . Клиент должен проверить, что правильный издатель отправил ответ на маркер и что он не был изменен. Поскольку маркер `id_tokens` ​​всегда является JWT, существует множество библиотек для проверки этих маркеров. Мы рекомендуем использовать одну из них, а не создавать их самостоятельно.

Чтобы вручную проверить маркер, см. в разделе [Проверка маркера доступа](access-tokens.md#validating-tokens). После проверки подписи маркера, следует проверить следующие утверждения в маркере "id_token" (это также можно сделать в библиотеке проверки маркеров).

* Метки времени. Метки времени `iat`, `nbf`, и `exp` следует соответственно выполнить до или после текущего момента времени. 
* Аудитория. Утверждение `aud` должно соответствовать идентификатору вашего приложения.
* Nonce. Утверждение `nonce` в полезных данных должно соответствовать параметру nonce, переданному в конечную точку, или авторизации конечной точки во время первоначального запроса.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [маркерах доступа](access-tokens.md)
* Настройте утверждения в id_token с помощью [необязательных утверждений](active-directory-optional-claims.md).
