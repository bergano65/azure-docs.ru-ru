---
title: Определение технического профиля RESTFUL в пользовательской политике
titleSuffix: Azure AD B2C
description: Определение технического профиля RESTful в пользовательской политике Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa14854807727506f5d697d7871c97e219c096a3
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950890"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля RESTful в пользовательской политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) обеспечивает поддержку собственной службы RESTFUL. Azure AD B2C отправляет данные в службу RESTful в виде коллекции входящих утверждений и получает ответы в виде коллекции исходящих утверждений. Благодаря интеграции со службой RESTful вы можете:

- **Проверять входные данные от пользователя**, чтобы предотвратить сохранение в Azure AD B2C данных в недопустимом формате. Если значение, полученное от пользователя, является недопустимым, служба RESTful возвращает сообщение об ошибке, которое инструктирует пользователя предоставить запись. Например, вы можете проверить, что адрес электронной почты, предоставленный пользователем, существует в базе данных ваших клиентов.
- **Перезаписывать входящие утверждения**, чтобы изменить формат значений во входящих утверждениях. Например, если пользователь вводит имя только строчными или заглавными буквами, вы можете изменить его формат, оставив прописной только первую букву.
- **Дополнять данные пользователя**, чтобы расширять возможности интеграции с корпоративными бизнес-приложениями. Например, служба RESTful может получить адрес электронной почты пользователя, обратиться к базе данных клиента и вернуть в Azure AD B2C номер личного счета пользователя. Возвращаемые утверждения можно сохранять, вычислять в следующих шагах оркестрации или включать в маркеры доступа.
- **Выполнять пользовательскую бизнес-логику**, например отправлять push-уведомления, обновлять корпоративные базы данных, управлять разрешениями, выполнять миграцию пользователей или аудит баз данных и многое другое.

Политика может отправлять входящие утверждения в интерфейс REST API. REST API также может возвращать исходящие утверждения, которые вы примените далее в той же политике, или создавать сообщения об ошибках. Интеграцию со службами RESTful можно спроектировать одним из следующих способов:

- **Технический профиль проверки** — технический профиль проверки вызывает службы RESTful. Этот профиль проверяет данные, предоставленные пользователем, прежде чем продолжать путь взаимодействия пользователя. С помощью технического профиля проверки сообщение об ошибке отображается на самозаверенной странице и возвращается в исходящих утверждениях.
- **Обмен утверждениями** — вызов службы RESTful выполняется через этап оркестрации. В этом сценарии нет пользовательского интерфейса, где можно было бы отобразить сообщение об ошибке. Если REST API возвращает ошибку, пользователь перенаправляется обратно в приложению проверяющей стороны, где он увидит сообщение об ошибке.

## <a name="protocol"></a>Протокол

Для атрибута **Name** элемента **Protocol** необходимо задать значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, которое используется Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

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

## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список утверждений, возвращаемых из REST API. Может потребоваться сопоставить имя утверждения, определенное в вашей политике, с именем, определенным в REST API. Вы также можете включить утверждения, которые не возвращаются поставщиком удостоверений REST API, установив атрибут `DefaultValue`.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения выходных утверждений или создания новых.

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

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ServiceUrl | ДА | URL-адрес конечной точки REST API. |
| authenticationType | ДА | Тип аутентификации, выполняемый поставщиком утверждений RESTful. Допустимые значения: `None`, `Basic` или `ClientCertificate`. Значение `None` указывает, что REST API не является анонимным. Значение `Basic` указывает, что REST API защищен помощью базовой проверки подлинности HTTP. Доступ к API могут получить только проверенные пользователи, включая Azure AD B2C. Значение `ClientCertificate` (рекомендуется) указывает, что REST API ограничивает доступ с помощью проверки подлинности н основе сертификата клиента. Доступ к службе могут получить только службы, у которых есть соответствующие сертификаты, например Azure AD B2C. |
| SendClaimsIn | Нет | Указывает, как отправляются входящие утверждения поставщику утверждений RESTful. Возможные значения: `Body` (по умолчанию), `Form`, `Header` или `QueryString`. Значение `Body` обозначает входящее утверждение, которое отправляется в тексте запроса в формате JSON. Значение `Form` обозначает входящее утверждение, которое отправляется в тексте запроса в формате пар "ключ-значение", разделенных символом амперсанда (&). Значение `Header` обозначает входящее утверждение, которое отправляется в заголовке запроса. Значение `QueryString` обозначает входящее утверждение, которое отправляется в строке запроса. |
| ClaimsFormat | Нет | Задает формат для исходящих утверждений. Возможные значения: `Body` (по умолчанию), `Form`, `Header` или `QueryString`. Значение `Body` обозначает исходящее утверждение, которое отправляется в тексте запроса в формате JSON. Значение `Form` обозначает исходящее утверждение, которое отправляется в тексте запроса в формате пар "ключ-значение", разделенных символом амперсанда (&). Значение `Header` обозначает исходящее утверждение, которое отправляется в заголовке запроса. Значение `QueryString` обозначает исходящее утверждение, которое отправляется в строке запроса. |
| DebugMode | Нет | Выполняет технический профиль в режиме отладки. В режиме отладки REST API может возвращать больше информации. См. раздел, посвященный возвращаемому сообщению об ошибке. |

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

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | ДА | Имя пользователя, которое используется для аутентификации. |
| BasicAuthenticationPassword | ДА | Пароль, который используется для аутентификации. |

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

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ClientCertificate | ДА | Сертификат X509 (набор ключей RSA), используемый для аутентификации. |

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

## <a name="returning-error-message"></a>Возвращаемое сообщение об ошибке

REST API может возвращать сообщения об ошибках, например The user was not found in the CRM system (Пользователь не найден в системе CRM). Если происходит ошибка, REST API возвращает сообщение об ошибке HTTP 409 (код ответа "Конфликт") со следующими атрибутами.

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| версия | ДА | 1.0.0 |
| status | ДА | 409 |
| Код | Нет | Код ошибки, полученный от поставщика конечной точки RESTful, который отображается при включении `DebugMode`. |
| requestId | Нет | Идентификатор запроса, полученный от поставщика конечной точки RESTful, который отображается при включении `DebugMode`. |
| userMessage | ДА | Сообщение об ошибке, которое отображается для пользователя. |
| developerMessage | Нет | Подробное описание проблемы и методов ее исправления, которое отображается при включении `DebugMode`. |
| moreInfo | Нет | URI, который указывает на дополнительную информацию, которая отображается при включении `DebugMode`. |

Следующий пример демонстрирует REST API, который возвращает сообщение об ошибке в формате JSON:

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

## <a name="examples"></a>Примеры.
- [Интеграция обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверка входных данных](active-directory-b2c-custom-rest-api-netfw.md)
- [Защита служб RESTful с использованием обычной проверки подлинности HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Защита служб RESTful с помощью сертификатов клиента](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Пошаговое руководство. Интеграция обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверка входных данных](active-directory-b2c-rest-api-validation-custom.md)

 














