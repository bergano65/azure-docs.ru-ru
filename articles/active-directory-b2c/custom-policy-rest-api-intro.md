---
title: REST API обмена утверждениями в настраиваемой политике B2C
titleSuffix: Azure AD B2C
description: Общие сведения о создании Azure AD B2C пути взаимодействия пользователя, взаимодействующего со службами RESTFUL.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337419"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Интеграция REST APIных запросов обмена утверждениями в Azure AD B2C настраиваемой политике

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Инфраструктура процедур идентификации, которая зависит от Azure Active Directory B2C (Azure AD B2C), может интегрироваться с интерфейсами API RESTFUL в пути взаимодействия пользователя. В этой статье показано, как создать путь взаимодействия пользователя, взаимодействующий со службой RESTFUL с помощью [технического профиля RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

С помощью Azure AD B2C можно добавить собственную бизнес-логику в путь взаимодействия пользователя, вызвав собственную службу RESTFUL. Инфраструктура процедур идентификации позволяет отправлять и получать данные от службы RESTFUL для обмена утверждениями. Например, с их помощью можно выполнять следующее.

- **Проверка входных данных пользователя**. Например, можно проверить, что адрес электронной почты, предоставленный пользователем, существует в базе данных клиента, и, если нет, появится сообщение об ошибке.
- **Обработка утверждений**. Если пользователь вводит свое имя во все строчные или прописные буквы, REST API может отформатировать имя, используя только первую букву в верхнем регистре, и вернуть ее в Azure AD B2C.
- **Обогащение пользовательских данных за счет дальнейшей интеграции с корпоративными бизнес-приложениями**. Например, служба RESTful может получить адрес электронной почты пользователя, обратиться к базе данных клиента и вернуть в Azure AD B2C номер личного счета пользователя. Затем возвращаемые утверждения могут храниться в учетной записи пользователя Azure AD, оцениваться на следующих этапах оркестрации или входить в маркер доступа.
- **Выполните пользовательскую бизнес-логику**. Вы можете отправлять push-уведомления, обновлять корпоративные базы данных, выполнять процесс миграции пользователей, управлять разрешениями, выполнять аудит баз данных и выполнять любые другие рабочие процессы.

![Схема обмена утверждениями службы RESTFUL](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Вызов службы RESTFUL

Взаимодействие включает обмен данными между утверждениями REST API и Azure AD B2C. Интеграцию со службами RESTful можно спроектировать одним из следующих способов:

- **Технический профиль проверки**. Вызов службы RESTFUL происходит в рамках [технического профиля проверки](validation-technical-profile.md) указанного [самостоятельного технического профиля](self-asserted-technical-profile.md)или [контрольного элемента](display-control-verification.md) [управления отображением.](display-controls.md) Этот профиль проверяет данные, предоставленные пользователем, в начале пути взаимодействия пользователя. С помощью технического профиля проверки вы можете:

  - Отправка утверждений в REST API.
  - Проверьте утверждения и вызовите пользовательские сообщения об ошибках, которые будут отображаться для пользователя.
  - Отправка утверждений от REST API к последующим действиям оркестрации.

- **Обмен утверждениями**. Обмен прямыми утверждениями можно настроить, вызвав REST API технический профиль непосредственно на этапе оркестрации [пути взаимодействия пользователя](userjourneys.md). Может только:

  - Отправка утверждений в REST API.
  - Проверьте утверждения и вызывайте пользовательские сообщения об ошибках, возвращаемые приложению.
  - Отправка утверждений от REST API к последующим действиям оркестрации.

Вы можете добавить вызов REST API в любой шаг пути взаимодействия пользователя, который определен пользовательской политикой. Например, вызовы к REST API можно выполнять:

- Во время входа в систему непосредственно перед Azure AD B2C проверяет учетные данные.
- Сразу после входа.
- Прежде чем Azure AD B2C создаст новую учетную запись в каталоге.
- После Azure AD B2C создает новую учетную запись в каталоге.
- Прежде чем Azure AD B2C выдаст маркер доступа.

![Проверка коллекции технического профиля](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Отправка данных

В [техническом профиле RESTful](restful-technical-profile.md) `InputClaims` элемент содержит список утверждений для отправки в службу RESTful. Вы можете связать имя утверждения с именем, определенным в службе RESTFUL, задать значение по умолчанию и использовать [арбитры утверждений](claim-resolver-overview.md).

Вы можете настроить отправку входных утверждений в поставщик утверждений RESTFUL с помощью атрибута Сендклаимсин. Допустимые значения:

- **Текст**, отправленный в тексте запроса HTTP POST в формате JSON.
- **Форма**отправляется в тексте запроса HTTP POST в формате значения ключа, разделенного амперсандом "&".
- **, Отправленный в заголовке**запроса HTTP GET.
- **Строка запроса**запроса на получение HTTP.

Если параметр **Body** настроен, REST API технический профиль позволяет отправить в конечную точку сложные полезные данные JSON. Дополнительные сведения см. [в разделе Отправка полезных данных JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Получение данных

`OutputClaims` Элемент [технического профиля RESTful](restful-technical-profile.md) содержит список утверждений, возвращенных REST API. Может потребоваться сопоставить имя утверждения, определенное в вашей политике, с именем, определенным в REST API. Можно также включить утверждения, которые не возвращаются поставщиком удостоверений REST API, при условии, что задан атрибут DefaultValue.

Выходные утверждения, проанализированные поставщиком утверждений RESTFUL, всегда должны анализировать плоский ответ текста JSON, например:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Выходные утверждения должны выглядеть следующим образом:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Чтобы проанализировать вложенный ответ текста JSON, установите для метаданных Ресолвежсонпассинжсонтокенс значение true. В выходном утверждении задайте Партнерклаимтипе в качестве элемента пути JSON, который нужно вывести.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Выходные утверждения должны выглядеть следующим образом:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Замечания по безопасности

Необходимо защитить конечную точку REST API, чтобы с ней могли взаимодействовать только клиенты, прошедшие проверку подлинности. REST API должен использовать конечную точку HTTPS. Задайте для метаданных AuthenticationType один из следующих методов проверки подлинности:

- **Сертификат клиента** разрешает доступ с помощью проверки подлинности на основе сертификата клиента. Доступ к API могут получить только службы, имеющие соответствующие сертификаты. Сертификат клиента сохраняется в ключе политики Azure AD B2C. Узнайте больше о том [, как защитить службу RESTful с помощью сертификатов клиентов](secure-rest-api.md#https-client-certificate-authentication).
- Уровень " **базовый** " обеспечивает безопасность REST API с обычной проверкой подлинности HTTP. Доступ к API могут получить только проверенные пользователи, включая Azure AD B2C. Имя пользователя и пароль хранятся в ключах политики Azure AD B2C. Узнайте, как [защитить службы RESTful с помощью обычной проверки подлинности HTTP](secure-rest-api.md#http-basic-authentication).
- **Носитель** разрешает доступ с помощью маркера доступа клиента OAuth2. Маркер доступа хранится в ключе политики Azure AD B2C. Узнайте больше о том [, как защитить службу RESTful с помощью токена носителя](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Платформа REST API
REST API могут быть основаны на любой платформе и написаны на любом языке программирования, при условии, что он защищен и может отправлять и получать заявки, как указано в [техническом профиле RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Локализация REST API
В техническом профиле RESTFUL может потребоваться отправить язык и региональные параметры текущего сеанса, а при необходимости вызвать локализованное сообщение об ошибке. С помощью [сопоставителя утверждений](claim-resolver-overview.md)можно отправить контекстное утверждение, например язык пользователя. В следующем примере показан технический профиль RESTFUL, демонстрирующий этот сценарий.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Обработка сообщений об ошибках

REST API может потребоваться вернуть сообщение об ошибке, например "пользователь не был найден в системе CRM". При возникновении ошибки REST API должен вернуть сообщение об ошибке HTTP 409 (код состояния ответа с конфликтом). Дополнительные сведения см. в [техническом профиле RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Это можно сделать только путем вызова технического профиля REST API из технического профиля проверки. Это позволяет пользователю исправить данные на странице и повторно выполнить проверку после отправки страницы.

Ответ HTTP 409 необходим для предотвращения обработки всех последующих технических профилей проверки на этом этапе оркестрации.

Если вы ссылаетесь на REST API технический профиль непосредственно из пути взаимодействия пользователя, пользователь перенаправляется обратно в приложение проверяющей стороны с соответствующим сообщением об ошибке.

## <a name="publishing-your-rest-api"></a>Публикация REST API

Запрос к службе REST API поступает от Azure AD B2C серверов. Служба REST API должна быть опубликована в общедоступной конечной точке HTTPS. Вызовы REST API будут поступать от IP-адреса центра обработки данных Azure.

Разработайте службу REST API и ее базовые компоненты (например, базу данных и файловую систему), чтобы обеспечить высокую доступность.

## <a name="next-steps"></a>Дальнейшие действия

Примеры использования технического профиля RESTFUL см. в следующих статьях:

- [Пошаговое руководство. интеграция обмена REST APIми утверждениями в Azure AD B2C пути взаимодействия пользователя с проверкой вводимых пользователем данных](custom-policy-rest-api-claims-validation.md)
- [Пошаговое руководство. Добавление REST API обмена утверждениями в пользовательские политики в Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Защита REST APIных служб](secure-rest-api.md)
- [Справочник: технический профиль RESTFUL](restful-technical-profile.md)