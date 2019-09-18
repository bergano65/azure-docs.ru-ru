---
title: Обмен утверждениями REST API — Azure Active Directory B2C
description: Добавление REST API заявок на обмен утверждениями в пользовательские политики в Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 12ddbe9f43baf68f6c11c9b720a0f684316af46a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065320"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Добавление REST API обмена утверждениями в пользовательские политики в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Вы можете добавить взаимодействие с API RESTFUL к [пользовательским политикам](active-directory-b2c-overview-custom.md) в Azure Active Directory B2C (Azure AD B2C). В этой статье показано, как создать Azure AD B2C пути взаимодействия пользователя, взаимодействующего со службами RESTFUL.

Взаимодействие включает обмен данными между утверждениями REST API и Azure AD B2C. Обмен утверждениями имеет следующие характеристики.

- может разрабатываться как этап оркестрации;
- может вызывать внешнее действие, например записывать событие во внешнюю базу данных;
- может использоваться, чтобы получить значение, а затем сохранить его в базе данных пользователя.
- Может изменять поток выполнения.

Сценарий, представленный в этой статье, включает следующие действия.

1. Поиск пользователя во внешней системе.
2. Получение места регистрации пользователя.
3. Возвращение этого атрибута как утверждения обратно в приложение.

## <a name="prerequisites"></a>Предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Конечная точка REST API, с которой устанавливается взаимодействие. В этой статье в качестве примера используется простая функция Azure. Сведения о создании функции Azure см. [в разделе Создание первой функции в портал Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Подготовка API

В этом разделе вы подготовите функцию Azure для получения значения `email`, а затем возвращаем значение для `city` , которое может использоваться Azure AD B2C в качестве утверждения.

Измените файл run. CSX для созданной функции Azure, чтобы использовать следующий код:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Настройка обмена утверждениями

Технический профиль предоставляет конфигурацию для обмена заявками.

Откройте файл *TrustFrameworkExtensions. XML* и добавьте следующий элемент **поставщика утверждений** XML в элемент **клаимспровидерс** .

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Элемент **inputclaim** определяет утверждения, которые отправляются в службу RESTful. В этом примере значение утверждения `givenName` отправляется в службу RESTful в качестве утверждения. `email` Элемент **OutputClaims** определяет утверждения, ожидаемые от службы RESTful.

Комментарии выше `AuthenticationType` и `AllowInsecureAuthInProduction` указывают изменения, которые необходимо внести при переходе в рабочую среду. Чтобы узнать, как защитить интерфейсы API RESTFUL для рабочей среды, см. статью Защита API RESTful [с помощью базовой проверки подлинности](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) и [защищенных API-интерфейсов RESTful с проверкой подлинности сертификата](active-directory-b2c-custom-rest-api-netfw-secure-cert.md).

## <a name="add-the-claim-definition"></a>Добавление определения утверждения

Добавьте определение `city` внутри элемента **BuildingBlocks** . Этот элемент можно найти в начале файла TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Добавление шага оркестрации

Вызов REST API может использоваться как этап оркестрации в разных ситуациях. Как этап оркестрации он может использоваться в качестве обновления внешней системы после успешного выполнения пользователем задания, например первой регистрации или обновления профиля для синхронизации сведений. В этом случае вызов REST API используется для расширения сведений, предоставленных приложению после изменения профиля.

Добавьте шаг к пути взаимодействия пользователя для изменения профиля. После проверки подлинности пользователя (шаги оркестрации 1-4 в следующем коде XML) и пользователь предоставил обновленные сведения о профиле (шаг 5). Скопируйте XML-код пути редактирования профиля пользователя из файла *TrustFrameworkBase. XML* в файл *TrustFrameworkExtensions. XML* внутри элемента **усержаурнэйс** . Затем внесите изменения в шаге 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Окончательный формат XML для пути взаимодействия пользователя должен выглядеть, как в следующем примере:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Добавление утверждения

Измените файл *ProfileEdit. XML* и добавьте `<OutputClaim ClaimTypeReferenceId="city" />` в элемент **OutputClaims** .

После добавления нового утверждения технический профиль будет выглядеть следующим образом:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Передача изменений и проверка

1. (Необязательно.) Сохраните существующую версию (путем загрузки) файлов, прежде чем продолжать.
2. Передайте *TrustFrameworkExtensions. XML* и *ProfileEdit. XML* и выберите, чтобы перезаписать существующий файл.
3. Выберите **B2C_1A_ProfileEdit**.
4. Для параметра **выбрать приложение** на странице Обзор настраиваемой политики выберите веб-приложение с именем *APP1* , которое вы зарегистрировали ранее. Убедитесь, что **URL-адрес ответа** имеет значение `https://jwt.ms`.
4. Выберите **Запустить сейчас**. Выполните вход с использованием учетных данных своей учетной записи и нажмите кнопку **продолжить**.

Если все настроено правильно, маркер включает новое утверждение `city`со значением. `Redmond`

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Следующие шаги

Взаимодействие можно также реализовать как профиль проверки. Дополнительные сведения см. в статье [Пошаговое руководство. Интеграция обмена утверждениями REST API в пути взаимодействия пользователей Azure AD B2C как проверка входных данных](active-directory-b2c-rest-api-validation-custom.md).

[Azure Active Directory B2C. Создание и использование настраиваемых атрибутов в пользовательской политике изменения профиля](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Общие сведения Технический профиль RESTFUL](restful-technical-profile.md)

Чтобы узнать, как защитить API, см. следующие статьи:

* [Azure Active Directory B2C: Secure your RESTful services using HTTP basic authentication](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Azure Active Directory B2C: защита служб RESTful с использованием обычной проверки подлинности HTTP)
* [Azure Active Directory B2C. Защита служб RESTful с помощью сертификатов клиента](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
