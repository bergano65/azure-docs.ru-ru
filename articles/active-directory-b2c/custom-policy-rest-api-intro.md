---
title: REST API утверждает об обмене в пользовательской политике B2C
titleSuffix: Azure AD B2C
description: Введение в создание путешествия пользователя Azure AD B2C, которое взаимодействует с службами RESTful.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337419"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Интеграция обменов REST API в пользовательскую политику Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Рамочная система identity Experience, лежащая в основе Azure Active Directory B2C (Azure AD B2C), может интегрироваться с RESTful AIS в рамках пользовательского путешествия. В этой статье показано, как создать пользовательский путь, который взаимодействует с службой RESTful с помощью [технического профиля RESTful.](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)

Используя Azure AD B2C, вы можете добавить свою собственную бизнес-логику в путешествие пользователя, позвонив в свой собственный сервис RESTful. Рамочка опыта идентификации может отправлять и получать данные из службы RESTful для обмена претензиями. Например, администратор может сделать следующее:

- **Проверка входных данных пользователя.** Например, можно убедиться, что адрес электронной почты, предоставленный пользователем, существует в базе данных клиента, а если нет, то можно обнаружить ошибку.
- **Процесс претензий**. Если пользователь вводит свое имя во всех буквах нижнего регистра или во всех буквах верхнего регистра, ваш REST API может отформатировать имя только с первой буквой, капитализированной, и вернуть его в Azure AD B2C.
- **Обогащать пользовательские данные за счет дальнейшей интеграции с корпоративными приложениями line-of-business.** Например, служба RESTful может получить адрес электронной почты пользователя, обратиться к базе данных клиента и вернуть в Azure AD B2C номер личного счета пользователя. Затем возвратные запросы могут храниться в учетной записи Azure AD пользователя, оцениваться в следующих шагах оркестровки или включены в маркер доступа.
- **Запуск пользовательской бизнес-логики**. Вы можете отправлять push-уведомления, обновлять корпоративные базы данных, запускать процесс миграции пользователей, управлять разрешениями, проверять базы данных и выполнять любые другие рабочие процессы.

![Диаграмма обмена претензий на услуги RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Вызов службы RESTful

Взаимодействие включает обмен информацией между претензиями REST API и Azure AD B2C. Интеграцию со службами RESTful можно спроектировать одним из следующих способов:

- **Технический профиль проверки**. Вызов в службу RESTful происходит в [рамках технического профиля проверки](validation-technical-profile.md) указанного [самоутвержденного технического профиля](self-asserted-technical-profile.md)или [контроля дисплея](display-control-verification.md) [дисплея.](display-controls.md) Этот профиль проверяет данные, предоставленные пользователем, в начале пути взаимодействия пользователя. С помощью технического профиля проверки вы можете:

  - Отправка претензий на aPI REST.
  - Проверка утверждений и выбрасывает пользовательские сообщения об ошибках, отображаемые пользователю.
  - Отправка обратной заявки от REST API на последующие шаги оркестровки.

- **Обмен претензиями**. Обмен прямыми претензиями можно настроить, позвонив в технический профиль REST API непосредственно с шага оркестровки [пользовательского путешествия.](userjourneys.md) Может только:

  - Отправка претензий на aPI REST.
  - Проверка утверждений и выбросайте пользовательские сообщения об ошибках, которые возвращаются в приложение.
  - Отправка обратной заявки от REST API на последующие шаги оркестровки.

Вы можете добавить вызов REST API в любой шаг пути взаимодействия пользователя, который определен пользовательской политикой. Например, вызовы к REST API можно выполнять:

- Во время входиний, как раз перед Azure AD B2C проверяет учетные данные.
- Сразу после вхастывки.
- Перед тем, как Azure AD B2C создает новую учетную запись в каталоге.
- После Azure AD B2C создает новую учетную запись в каталоге.
- До того, как Azure AD B2C выпускает токен доступа.

![Сбор технических профилей валидации](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Отправка данных

В `InputClaims` [техническом профиле RESTful](restful-technical-profile.md)элемент содержит список претензий для отправки в службу RESTful. Вы можете сопоставить имя своей претензии к имени, определенному в службе RESTful, установить значение по умолчанию и использовать [разрешители претензий.](claim-resolver-overview.md)

Вы можете настроить, как претензии ввода отправляются поставщику RESTful заявок с помощью атрибута SendClaimsIn. Вы можете выбрать

- **Тело**, отправленное в органе запроса HTTP POST в формате JSON.
- **Форма**, отправленная в орган запроса HTTP POST в формате амперсанда «&» разделенного ключевого значения.
- **Заголовок,** отправленный в заголовке запроса HTTP GET.
- **QueryString**, отправленный в строке запроса HTTP GET.

При настройке опции **Body** технический профиль REST API позволяет отправлять сложную полезную нагрузку JSON в конечную точку. Для получения дополнительной информации [см.](restful-technical-profile.md#send-a-json-payload)

## <a name="receiving-data"></a>Получение данных

Элемент `OutputClaims` [технического профиля RESTful](restful-technical-profile.md) содержит список претензий, возвращенных API REST. Может потребоваться сопоставить имя утверждения, определенное в вашей политике, с именем, определенным в REST API. Вы также можете включить требования, которые не возвращаются поставщиком идентификационных данных REST API, при посеве атрибута DefaultValue.

Вывод претензий разогнаны RESTful претензий провайдер всегда ожидать, чтобы разобрать плоский ответ JSON органа, таких как:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Претензии на выводные данные должны выглядеть следующим образом:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Чтобы разобрать вложенный ответ тела JSON, установите метаданные ResolveJsonPathsInJsonTokens на истину. В претензии вывода установите PartnerClaimType в элемент пути JSON, который вы хотите вывести.

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


Претензии на выводные данные должны выглядеть следующим образом:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Замечания по безопасности

Вы должны защитить свою конечную точку REST API, чтобы с ней могли общаться только проверенные клиенты. API REST должен использовать конечную точку HTTPS. Установите метаданные AuthenticationType на один из следующих методов проверки подлинности:

- **Сертификат клиента** ограничивает доступ, используя аутентификацию сертификата клиента. Доступ к API могут получить только службы, которые имеют соответствующие сертификаты. Сертификат клиента хранится в ключе политики Azure AD B2C. Узнайте больше о том, как [обезопасить службу RESTful с помощью сертификатов клиентов.](secure-rest-api.md#https-client-certificate-authentication)
- **Basic** обеспечивает защиту REST API с помощью базовой аутентификации HTTP. Доступ к API могут получить только проверенные пользователи, включая Azure AD B2C. Имя пользователя и пароль хранятся в ключах политики Azure AD B2C. Узнайте, как [обезопасить свои службы RESTful с помощью базовой аутентификации HTTP.](secure-rest-api.md#http-basic-authentication)
- **Носитель** ограничивает доступ с помощью токена доступа клиента OAuth2. Конкен доступа хранится в ключе политики Azure AD B2C. Узнайте больше о том, как [обезопасить службу RESTful, используя токен Bearer.](secure-rest-api.md#oauth2-bearer-authentication)

## <a name="rest-api-platform"></a>Платформа REST API
Ваш REST API может быть основан на любой платформе и написан на любом языке программирования, если он безопасен и может отправлять и получать претензии, указанные в [техническом профиле RESTful.](restful-technical-profile.md)

## <a name="localize-the-rest-api"></a>Локализация API REST
В техническом профиле RESTful можно отправить язык/локализацию текущей сессии и при необходимости поднять локальное сообщение об ошибке. Используя [разрешитель претензий,](claim-resolver-overview.md)вы можете отправить контекстуальную претензию, например язык пользователя. Следующий пример показывает технический профиль RESTful, демонстрирующий этот сценарий.

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

Вашему REST API может потребоваться возврат сообщения об ошибке, например, "Пользователь не был найден в системе CRM". При возникновении ошибки API REST должен вернуть сообщение об ошибке HTTP 409 (код статуса ответа конфликта). Для получения дополнительной [RESTful technical profile](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message)информации, см.

Этого можно достичь только путем вызова технического профиля REST API из технического профиля проверки. Это позволяет пользователю корректировать данные на странице и запускать проверку снова при отправке страницы.

Ответ HTTP 409 необходим для предотвращения обработки любых последующих технических профилей проверки в рамках этого шага оркестровки.

Если вы ссылаетесь на технический профиль REST API непосредственно из путешествия пользователя, пользователь перенаправляется обратно в приложение для опирающихся сторон с соответствующим сообщением об ошибке.

## <a name="publishing-your-rest-api"></a>Публикация aPI REST

Запрос на службу REST API поступает с серверов Azure AD B2C. Служба REST API должна быть опубликована в общедоступной конечной точке HTTPS. Вызовы REST API будут поступать с IP-адреса центра обработки данных Azure.

Дизайн службы REST API и ее базовых компонентов (таких как база данных и файловая система) будет очень доступным.

## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующие статьи для примеров использования технического профиля RESTful:

- [Пошаговая прогулка: Интеграция rest API утверждает обмены в вашем путешествии пользователя Azure AD B2C в качестве проверки пользовательского ввода](custom-policy-rest-api-claims-validation.md)
- [Пошаговая прогулка: Добавление обменов требованиями REST API к пользовательским политикам в Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Обезопажьте службы REST API](secure-rest-api.md)
- [Справка: Технический профиль RESTful](restful-technical-profile.md)