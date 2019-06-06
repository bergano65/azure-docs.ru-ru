---
title: Настройка смены пароля в настраиваемых политиках в Azure Active Directory B2C | Документация Майкрософт
description: Сведения о том, как разрешить пользователям изменять пароль с помощью настраиваемых политик в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a49f62b6fc1ea00084266d4c5405f8bf96d034cb
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509263"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка смены пароля в настраиваемых политиках в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В Azure Active Directory (Azure AD) B2C можно разрешить пользователям, выполнившим вход с помощью локальной учетной записи, изменять их пароли без необходимости подтверждать подлинность по электронной почте. Если время сеанса истекает, когда пользователь входит в последовательность смены пароля, такому пользователю предлагается выполнить вход еще раз. В этой статье рассказывается о настройке смены пароля в [настраиваемых политиках](active-directory-b2c-overview-custom.md). Также можно настроить [самостоятельный сброс пароля](active-directory-b2c-reference-sspr.md) в последовательностях пользователей.

## <a name="prerequisites"></a>Технические условия

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Добавление элементов 

1. Откройте файл *TrustframeworkExtensions.xml* и добавьте элемент **ClaimType** с идентификатором `oldPassword` в элемент [ClaimsSchema](claimsschema.md): 

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Элемент [ClaimsProvider](claimsproviders.md) содержит технический профиль, с помощью которого выполняется проверка подлинности пользователя. Добавьте следующие поставщики утверждений в элемент **ClaimsProviders**.

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Замените `IdentityExperienceFrameworkAppId` идентификатором созданного в рамках предыдущего руководства приложения IdentityExperienceFramework. Замените `ProxyIdentityExperienceFrameworkAppId` идентификатором созданного ранее приложения ProxyIdentityExperienceFramework.

3. Элемент [UserJourney](userjourneys.md) определяет путь взаимодействия пользователя с приложением. Добавьте элемент **UserJourneys**, если он не существует. Присвойте **UserJourney** идентификатор `PasswordChange`.

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Сохраните файл политики *TrustFrameworkExtensions.xml*.
5. Скопируйте файл *ProfileEdit.xml*, который был скачан вместе с начальным пакетом, и присвойте ему имя *ProfileEditPasswordChange.xml*.
6. Откройте новый файл и задайте для атрибута **PolicyId** уникальное значение. Это значение определяет имя вашей политики. Например, *B2C_1A_profile_edit_password_change*.
7. Задайте для атрибута **ReferenceId** в `<DefaultUserJourney>` идентификатор созданного пути взаимодействия пользователя. Например, *PasswordChange*.
8. Сохраните изменения.

Пример политики можно найти [здесь](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 

## <a name="test-your-policy"></a>Тестирование политики

При тестировании приложений в Azure AD B2C может потребоваться вернуть маркер Azure AD B2C в `https://jwt.ms` для просмотра в нем утверждений.

### <a name="upload-the-files"></a>Передача файлов

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Выберите **Инфраструктура процедур идентификации**.
5. На странице "Настраиваемые политики" щелкните **Отправить политику**.
6. Выберите **Перезаписать политику, если она существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
7. Щелкните **Отправить**.
8. Повторите шаги с 5 по 7 для файла проверяющей стороны, например *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Запуск политики

1. Откройте измененную политику. Например, *B2C_1A_profile_edit_password_change*.
2. В разделе **Приложение** выберите зарегистрированное ранее приложение. Чтобы маркер отображался, **URL-адрес ответа** должен быть следующим: `https://jwt.ms`.
3. Щелкните **Запустить сейчас**. Выполните вход с помощью ранее созданной учетной записи. Теперь вы сможете изменять пароль. 

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в разделе [Настройка сложности пароля в настраиваемых политиках в Azure Active Directory B2C](active-directory-b2c-reference-password-complexity-custom.md). 
