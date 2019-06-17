---
title: Утверждениями REST API — Azure Active Directory B2C | Документация Майкрософт
description: Добавьте утверждениями REST API в пользовательские политики в Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bc0cea765816bfac066b05aca65f668fbce0c8ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508769"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Добавить утверждениями REST API в пользовательские политики в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Вы можете добавить взаимодействие с API-интерфейсов RESTful для вашего [пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory (Azure AD) B2C. В этой статье показано, как создать путь взаимодействия пользователя Azure AD B2C, который взаимодействует со службами RESTful.

Взаимодействие включает утверждениями обмена данными между утверждениями REST API и Azure AD B2C. Утверждениями имеют следующие характеристики:

- может разрабатываться как этап оркестрации;
- может вызывать внешнее действие, например записывать событие во внешнюю базу данных;
- может использоваться, чтобы получить значение, а затем сохранить его в базе данных пользователя.
- Можно изменить поток выполнения. 

Сценарий, представленный в этой статье, включает следующие действия:

1. Поиск пользователя во внешней системе.
2. Получение места регистрации пользователя.
3. Возвращение этого атрибута как утверждения обратно в приложение.

## <a name="prerequisites"></a>Технические условия

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Конечная точка REST API, с которой устанавливается взаимодействие. В этом статье используется простой Azure работать в качестве примера. Чтобы создать функцию Azure, см. в разделе [Создание первой функции на портале Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Подготовка API

В этом разделе вы готовитесь функцию Azure для получения значения для `email`и затем вернуть значение для `city` , можно использовать с помощью Azure AD B2C в качестве утверждения.

Изменение файла run.csx для функции Azure, созданные для использования в следующем коде: 

```
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

Технический профиль предоставляет конфигурацию для обмена утверждения. 

Откройте *TrustFrameworkExtensions.xml* файл и добавьте следующие элементы XML внутри **ClaimsProvider** элемент.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
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

**InputClaims** элемент определяет утверждения, которые отправляются в службу REST. В этом примере значение утверждения `givenName` отправляется в службу REST как утверждение `email`. **OutputClaims** элемент определяет утверждения, которые ожидаются от службы REST.

## <a name="add-the-claim-definition"></a>Добавьте определение утверждений

Добавьте определение для `city` внутри **BuildingBlocks** элемент. Этот элемент можно найти в начале файла TrustFrameworkExtensions.xml.

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

## <a name="add-an-orchestration-step"></a>Добавить этап оркестрации

Вызов REST API может использоваться как этап оркестрации в разных ситуациях. Как этап оркестрации он может использоваться в качестве обновления внешней системы после успешного выполнения пользователем задания, например первой регистрации или обновления профиля для синхронизации сведений. В этом случае вызов REST API используется для расширения сведений, предоставленных приложению после изменения профиля.

Добавление шага в пути взаимодействия пользователя редактирования профиля. После пользователь прошел проверку подлинности (этапы оркестрации 1 – 4 в следующем XML) и предоставления обновленных сведений о профиле (шаг 5). Скопируйте профиля для изменения XML-код пути взаимодействия пользователя с *TrustFrameworkBase.xml* файл вашей *TrustFrameworkExtensions.xml* внутри **UserJourneys** элемент. Внесите изменения в качестве шага 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Конечный XML-код пути взаимодействия пользователя должен выглядеть как в следующем примере:

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
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Добавление утверждения

Изменить *ProfileEdit.xml* файл и добавьте `<OutputClaim ClaimTypeReferenceId="city" />` для **OutputClaims** элемент.

После добавления нового утверждения Технический профиль выглядит следующим образом:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Передача изменений и проверка

1. (Необязательно.) Сохраните существующую версию файлов перед началом работы (скачав ее).
2. Отправка *TrustFrameworkExtensions.xml* и *ProfileEdit.xml* и выберите, чтобы перезаписать существующий файл.
3. Выберите **B2C_1A_ProfileEdit**.
4. Для **выберите приложение** на странице "Обзор" пользовательской политики, выберите веб-приложение с именем *webapp1* , которую вы ранее зарегистрировали. Убедитесь, что **URL-адрес ответа** является `https://jwt.ms`.
4. Выберите **запустить сейчас**. Войдите, используя учетные данные учетной записи и нажмите кнопку **Продолжить**.

Если все настроено правильно, токен включает новое утверждение `city`, со значением `Redmond`.

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

## <a name="next-steps"></a>Дальнейшие действия

- Взаимодействие можно также реализовать как профиль проверки. Дополнительные сведения см. в статье [Пошаговое руководство. Интеграция обмена утверждениями REST API в пути взаимодействия пользователей Azure AD B2C как проверка входных данных](active-directory-b2c-rest-api-validation-custom.md).
- [Azure Active Directory B2C. Создание и использование настраиваемых атрибутов в пользовательской политике изменения профиля](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
