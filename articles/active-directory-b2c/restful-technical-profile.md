---
title: Определение технического профиля RESTful в пользовательской политике
titleSuffix: Azure AD B2C
description: Определение технического профиля RESTful в пользовательской политике Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332610"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля RESTful в пользовательской политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Активный каталог Azure B2C (Azure AD B2C) обеспечивает поддержку интеграции собственной службы RESTful. Azure AD B2C отправляет данные в службу RESTful в виде коллекции входящих утверждений и получает ответы в виде коллекции исходящих утверждений. Для получения дополнительной информации смотрите [обмен претензий Integrate REST API в пользовательской политике Azure AD B2C.](custom-policy-rest-api-intro.md)  

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, которое используется Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Следующий пример демонстрирует технический профиль RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Входящие утверждения

Элемент **InputClaims** содержит список утверждений для отправки в REST API. Также вы можете сопоставить имя утверждения с именем, заданным в REST API. Следующий пример демонстрирует сопоставление между политикой и REST API. Утверждение **GivenName** отправляется в REST API с именем **firstName**, а **surname** — с именем **lastName**. Утверждение **email** сохраняется неизменным.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Элемент **InputClaimsTransformations** может содержать коллекцию элементов **InputClaimsTransformation**, которые используются для изменения входных утверждений или создания новых перед отправкой их в REST API.

## <a name="send-a-json-payload"></a>Отправить полезную нагрузку JSON

Технический профиль REST API позволяет отправлять сложную полезную нагрузку JSON в конечную точку.

Для отправки сложной полезной нагрузки JSON:

1. Создайте полезную нагрузку JSON с помощью преобразования [претензий GenerateJson.](json-transformations.md)
1. В техническом профиле REST API:
    1. Добавьте преобразование ввода претензий со ссылкой на преобразование претензий. `GenerateJson`
    1. Установите `SendClaimsIn` опцию метаданных на`body`
    1. Установите `ClaimUsedForRequestPayload` опцию метаданных на название претензии, содержащей полезную нагрузку JSON.
    1. В претензию о входе добавьте ссылку на претензию о входе, содержащую полезную нагрузку JSON.

Следующий `TechnicalProfile` пример отправляет письмо проверки с помощью сторонней службы электронной почты (в данном случае, SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список утверждений, возвращаемых из REST API. Может потребоваться сопоставить имя утверждения, определенное в вашей политике, с именем, определенным в REST API. Вы также можете включить утверждения, которые не возвращаются поставщиком удостоверений REST API, установив атрибут `DefaultValue`.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

В следующем примере показан формат утверждения, возвращаемого REST API.

- Утверждение **MembershipId**, которое сопоставляется с именем утверждения **loyaltyNumber**.

Технический профиль также возвращает утверждения, которые не возвращаются поставщиком удостоверений:

- Утверждение **LoyaltyNumberIsNew**, для которого установлено значение по умолчанию `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| ServiceUrl | Да | URL-адрес конечной точки REST API. |
| authenticationType | Да | Тип аутентификации, выполняемый поставщиком утверждений RESTful. Возможные значения: `None`, `Basic`, `Bearer` или `ClientCertificate`. Значение `None` указывает, что REST API не является анонимным. Значение `Basic` указывает, что REST API защищен помощью базовой проверки подлинности HTTP. Доступ к API могут получить только проверенные пользователи, включая Azure AD B2C. Значение `ClientCertificate` (рекомендуемое) указывает на то, что REST API ограничивает доступ с помощью проверки подлинности сертификата клиента. Доступ к API могут получить только службы, которые имеют соответствующие сертификаты, например Azure AD B2C. Значение `Bearer` указывает на то, что REST API ограничивает доступ с помощью токена клиента OAuth2 Bearer. |
| AllowInsecureInInProduction| нет| Указывает, `AuthenticationType` может ли `none` быть установлен`DeploymentMode` в производственной среде `Production` [(TrustFrameworkPolicy](trustframeworkpolicy.md) установлен, или не указано). Возможные значения: истинные или ложные (по умолчанию). |
| SendClaimsIn | нет | Указывает, как отправляются входящие утверждения поставщику утверждений RESTful. Возможные значения: `Body` (по умолчанию), `Form`, `Header` или `QueryString`. Значение `Body` обозначает входящее утверждение, которое отправляется в тексте запроса в формате JSON. Значение `Form` обозначает входящее утверждение, которое отправляется в тексте запроса в формате пар "ключ-значение", разделенных символом амперсанда (&). Значение `Header` обозначает входящее утверждение, которое отправляется в заголовке запроса. Значение `QueryString` обозначает входящее утверждение, которое отправляется в строке запроса. Глаголы HTTP, на которые ссылаются каждый из них:<br /><ul><li>`Body`: ПОЧТА</li><li>`Form`: ПОЧТА</li><li>`Header`: ПОЛУЧИТЬ</li><li>`QueryString`: ПОЛУЧИТЬ</li></ul> |
| ClaimsFormat | нет | В настоящее время не используется, может быть проигнорировано. |
| ClaimUsedForRequestPayload| нет | Название строки, содержащей полезную нагрузку, которая будет отправлена на API REST. |
| DebugMode | нет | Выполняет технический профиль в режиме отладки. Возможные значения: `true` `false` или (по умолчанию). В режиме отладки REST API может возвращать больше информации. Смотрите раздел [сообщения об ошибке «Возвращение».](#returning-error-message) |
| IncludeclaimРазрешениеОбработкапретензий  | нет | Для претензий на ввод и вывод ы указывается, включено ли [разрешение претензий](claim-resolver-overview.md) в технический профиль. Возможные значения: `true` `false`  или (по умолчанию). Если вы хотите использовать решатели претензий в `true`техническом профиле, установите это на . |
| ResolveJsonPathsInJsonTokens  | нет | Указывает, разрешает ли технический профиль пути JSON. Возможные значения: `true` `false` или (по умолчанию). Используйте эти метаданные для чтения данных из вложенного элемента JSON. В [выводе элемента вывода На](technicalprofiles.md#outputclaims)выходе установите `PartnerClaimType` элемент пути JSON, который вы хотите вывести. Например: `firstName.localized`, `data.0.to.0.email`или .|
| UseClaimAsBearerToken| нет| Название претензии, содержащей маркер носителя.|

## <a name="cryptographic-keys"></a>Криптографические ключи

Если тип аутентификации имеет значение `None`, элемент **CryptographicKeys** не используется.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Если тип аутентификации имеет значение `Basic`, элемент **CryptographicKeys** содержит следующие атрибуты:

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Да | Имя пользователя, которое используется для аутентификации. |
| BasicAuthenticationPassword | Да | Пароль, который используется для аутентификации. |

Следующий пример демонстрирует технический профиль с базовой проверкой подлинности:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Если тип аутентификации имеет значение `ClientCertificate`, элемент **CryptographicKeys** содержит следующий атрибут:

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| ClientCertificate | Да | Сертификат X509 (набор ключей RSA), используемый для аутентификации. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Если тип аутентификации имеет значение `Bearer`, элемент **CryptographicKeys** содержит следующий атрибут:

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| BearerAuthenticationToToken | нет | The OAuth 2.0 Bearer Token. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Возвращаемое сообщение об ошибке

REST API может возвращать сообщения об ошибках, например The user was not found in the CRM system (Пользователь не найден в системе CRM). При возникновении ошибки API REST должен вернуть сообщение об ошибке HTTP 4xx, например, 400 (плохой запрос) или код состояния ответа 409 (конфликт). Тело ответа содержит сообщение об ошибке, отформатированное в JSON:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| version | Да | Ваша версия REST API. Например: 1.0.1 |
| status | Да | Должно быть 409 |
| код | нет | Код ошибки, полученный от поставщика конечной точки RESTful, который отображается при включении `DebugMode`. |
| requestId | нет | Идентификатор запроса, полученный от поставщика конечной точки RESTful, который отображается при включении `DebugMode`. |
| userMessage | Да | Сообщение об ошибке, которое отображается для пользователя. |
| developerMessage | нет | Подробное описание проблемы и методов ее исправления, которое отображается при включении `DebugMode`. |
| moreInfo | нет | URI, который указывает на дополнительную информацию, которая отображается при включении `DebugMode`. |


Следующий пример демонстрирует класс C#, который возвращает сообщение об ошибке:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующие статьи для примеров использования технического профиля RESTful:

- [Интеграция обменов REST API в пользовательскую политику Azure AD B2C](custom-policy-rest-api-intro.md)
- [Пошаговая прогулка: Интеграция rest API утверждает обмены в вашем путешествии пользователя Azure AD B2C в качестве проверки пользовательского ввода](custom-policy-rest-api-claims-validation.md)
- [Пошаговая прогулка: Добавление обменов требованиями REST API к пользовательским политикам в Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Обезопажьте службы REST API](secure-rest-api.md)

