---
title: Настройка регистрации и входа с учетной записью Azure AD B2C из другого клиента Azure AD B2C
titleSuffix: Azure AD B2C
description: Обеспечение регистрации и входа для клиентов с учетными записями Azure AD B2C из другого клиента в приложениях с помощью Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/14/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b8be516051f8eed0649064ae0f7c29a4dde85675
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224499"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Настройка регистрации и входа с учетной записью Azure AD B2C из другого клиента Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Обзор

В этой статье описывается, как настроить федерацию с другим клиентом Azure AD B2C. Если приложения защищены с помощью Azure AD B2C, это позволяет пользователям из других Azure AD B2c входить в систему с использованием существующих учетных записей. На следующей схеме пользователи могут войти в приложение, защищенное Azure AD B2C *contoso*, с учетной записью, управляемой клиентом Azure AD B2C *компании Fabrikam*. 

![Azure AD B2C Федерации с другим клиентом Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Чтобы использовать учетную запись Azure AD B2C в качестве [поставщика удостоверений](openid-connect.md) в клиенте Azure AD B2C (например, Contoso), в другом Azure AD B2C (например, Fabrikam):

1. Создайте пользовательский [поток](tutorial-create-user-flows.md)или [пользовательскую политику](custom-policy-get-started.md).
1. Затем создайте приложение на Azure AD B2C, как описано в этом разделе. 

Для создания приложения.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий другой клиент Azure AD B2C (например, Fabrikam.com).
1. На портале Azure найдите и выберите **Azure AD B2C**.
1. Щелкните **Регистрация приложений** и выберите **Новая регистрация**.
1. Введите **имя** приложения. Например, *контосоапп*.
1. В разделе **Поддерживаемые типы учетных записей** выберите элемент **Accounts in any identity provider or organizational directory (for authenticating users with user flows)** (Учетные записи в любом поставщике удостоверений или каталоге организации (для аутентификации пользователей с использованием сведений о маршрутах пользователей)).
1. В разделе **URI перенаправления** выберите **веб**, а затем введите следующий URL-адрес в строчных буквах, где `your-B2C-tenant-name` заменяется именем клиента Azure AD B2C (например, Contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Например, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. В разделе Разрешения установите флажок **предоставить согласие администратора для OpenID Connect и offline_access разрешения** .
1. Выберите **Зарегистрировать**.
1. На странице **Azure AD B2C-регистрация приложений** выберите созданное приложение, например *контосоапп*.
1. Запишите значение параметра **Идентификатор приложения (клиента)** , отображаемого на странице обзора приложения. Этот идентификатор потребуется при настройке поставщика удостоверений в следующем разделе.
1. В меню слева в разделе **Управление** выберите **Сертификаты и секреты**.
1. Выберите **Создать секрет клиента**.
1. Введите описание секрета клиента в поле **Описание**. Например, *clientsecret1*.
1. В разделе **Истекает** выберите срок действия секрета, а затем выберите **Добавить**.
1. Запишите значение секрета в поле **Значение**. Этот идентификатор потребуется при настройке поставщика удостоверений в следующем разделе.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Настройка Azure AD B2C в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C, для которого требуется настроить федерацию (например, Contoso). Выберите фильтр **каталог и подписка** в верхнем меню и выберите каталог, содержащий клиент Azure AD B2C (например, Contoso).
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. Щелкните элемент **Поставщики удостоверений**, а затем выберите **Новый поставщик OpenID Connect**.
1. Введите **Имя**. Например, введите *Fabrikam*.
1. В поле **URL-адрес метаданных** введите следующий URL-адрес, заменяющий `{tenant}` доменное имя клиента Azure AD B2C (например, Fabrikam). Замените на `{policy}` имя политики, настроенной в другом клиенте:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Например, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. В поле **Идентификатор клиента** введите ранее записанное значение идентификатора приложения.
1. В поле **Секрет клиента** введите ранее записанное значение секрета клиента.
1. Для параметра **Область** введите значение `openid`.
1. Сохраните значения по умолчанию для параметров **Тип ответа** и **Режим ответа**.
1. Используемых В качестве указания **домена** введите имя домена, которое вы хотите использовать для [прямого входа](direct-signin.md#redirect-sign-in-to-a-social-provider). Например, *Fabrikam.com*.
1. В разделе **Сопоставление утверждений поставщика удостоверений** выберите следующие утверждения:

    - **Идентификатор пользователя**: *подсистема*
    - **Display name**: *name*;
    - **Given name**: *given_name*;
    - **Surname**: *family_name*;
    - **Электронная почта**: *Электронная почта*

1. Выберите **Сохранить**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Создание ключа политики

Необходимо сохранить ключ приложения, созданный ранее в клиенте Azure AD B2C.

1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C, для которого требуется настроить федерацию (например, Contoso). Выберите фильтр **каталог и подписка** в верхнем меню и выберите каталог, содержащий клиент Azure AD B2C (например, Contoso).
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. В разделе **Политики** выберите **Identity Experience Framework**.
1. Выберите **ключи политики** и нажмите кнопку **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `FabrikamAppSecret`.  Префикс `B2C_1A_` добавляется автоматически к имени ключа при его создании, поэтому ссылка в XML-файле в следующем разделе *B2C_1A_FabrikamAppSecret*.
1. В качестве **секрета** введите секрет клиента, записанный ранее.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Нажмите кнопку **создания**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Чтобы пользователи могли выполнять вход с помощью других Azure AD B2C (Fabrikam), необходимо определить другие Azure AD B2C как поставщик утверждений, который Azure AD B2C может взаимодействовать с помощью конечной точки. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить Azure AD B2C в качестве поставщика утверждений, добавив Azure AD B2C в элемент **поставщика утверждений** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
1. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
1. Добавьте новый элемент **ClaimsProvider** следующим образом.
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Fabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Обновите следующие XML-элементы с помощью соответствующего значения:

    |Элемент XML  |Значение  |
    |---------|---------|
    |клаимспровидер\домаин  | Имя домена, используемое для [прямого входа](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Введите имя домена, которое будет использоваться при прямом входе. Например, *Fabrikam.com*. |
    |течникалпрофиле\дисплайнаме|Это значение будет отображаться на кнопке входа на экране входа в систему. Например, *Fabrikam*. |
    |Метаданные \ client_id|Идентификатор приложения поставщика удостоверений. Обновите идентификатор клиента с помощью идентификатора приложения, созданного ранее в другом клиенте Azure AD B2C.|
    |метадата\метадата|URL-адрес, указывающий на документ конфигурации поставщика удостоверений OpenID Connect Connect, который также называется OpenID Connect хорошо известной конечной точкой конфигурации. Введите следующий URL-адрес, заменив `{tenant}` именем домена другого Azure AD B2C клиента (Fabrikam). Замените `{tenant}` именем политики, настроенным в другом клиенте, и `{policy]` именем политики: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Например, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Измените значение **идентификатором storagereferenceid** на имя созданного ранее ключа политики. Например, `B2C_1A_FabrikamAppSecret`.| 
    

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь вы настроили политику так, чтобы Azure AD B2C знала, как взаимодействовать с другим Azure AD B2C клиентом. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
1. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
1. Щелкните **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений настроен, но еще не доступен ни на одной из страниц регистрации и входа. Чтобы сделать его доступным, создайте дубликат существующего шаблона, а затем измените его, чтобы у него также был поставщик удостоверений Azure AD:

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
1. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
1. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
1. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
1. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInFabrikam`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **клаимспровидерселектион** аналогичен кнопке поставщика удостоверений на странице регистрации или входа. Если добавить элемент **клаимспровидерселектион** для Azure AD B2C, то при переходе пользователя на страницу появится новая кнопка.

1. Найдите элемент **орчестратионстеп** , содержащийся `Order="1"` в пути взаимодействия пользователя, созданного в *TrustFrameworkExtensions.xml*.
1. Добавьте следующий элемент под элементом **ClaimsProviderSelections**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `FabrikamExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="FabrikamExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. Действие, в данном случае, предназначено для Azure AD B2C для взаимодействия с другими Azure AD B2C получения маркера. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений Azure AD B2C:

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
1. Добавьте следующий элемент **ClaimsExchange** , убедившись, что используется то же значение **идентификатора** , которое использовалось для **таржетклаимсексчанжеид**:

    ```xml
    <ClaimsExchange Id="FabrikamExchange" TechnicalProfileReferenceId="Fabrikam-OpenIdConnect" />
    ```

    Измените значение **течникалпрофилереференцеид** на идентификатор созданного ранее **идентификатора** технического профиля. Например, `Fabrikam-OpenIdConnect`.

1. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Добавление поставщика удостоверений Azure AD B2C в поток пользователя 

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
1. Щелкните поток пользователя, который требуется добавить Azure AD B2C поставщика удостоверений.
1. В разделе **поставщики удостоверений социальных сетей** выберите **Fabrikam**.
1. Выберите **Сохранить**.
1. Чтобы проверить политику, выберите пункт **выполнить пользовательскую последовательность**.
1. Для **приложения** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Щелкните **выполнить поток пользователя**
1. На странице Регистрация или вход выберите *Fabrikam* , чтобы войти с другим клиентом Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте его в *SignUpSignInFabrikam.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInFabrikam`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например, `http://contoso.com/B2C_1A_signup_signin_fabrikam`.
1. Обновите значение атрибута **ReferenceId** в **дефаултусержаурнэй** , чтобы оно совпадало с идентификатором пути взаимодействия пользователя, созданного ранее. Например, *сигнупсигнинфабрикам*.
1. Сохраните изменения и отправьте файл.
1. В разделе **пользовательские политики** выберите новую политику в списке.
1. В раскрывающемся списке **Выбор приложения** выберите созданное ранее приложение Azure AD B2C. Например, *testapp1*.
1. Выберите " **Запустить сейчас** " 
1. На странице Регистрация или вход выберите *Fabrikam* , чтобы войти с другим клиентом Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [передать в приложение другой токен Azure AD B2C](idp-pass-through-user-flow.md).
