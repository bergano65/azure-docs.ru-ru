---
title: Настройка регистрации и входа с учетной записью Microsoft
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Microsoft, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 123b36ba854bec8b363d59bbed5e70f18da1e578
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653713"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Microsoft через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Создание приложения для учетной записи Майкрософт

Чтобы использовать учетную запись Microsoft в качестве [поставщика удостоверений](openid-connect.md) для Azure Active Directory B2C (Azure AD B2C), необходимо создать приложение в клиенте Azure AD. Клиент Azure AD отличается от клиента Azure AD B2C. Если у вас нет учетной записи Microsoft, вы можете получить ее по адресу [https://www.live.com/](https://www.live.com/).

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог с клиентом Azure AD, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом Azure AD.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Новая регистрация**.
1. Введите значение **Name** (Имя) для приложения. Например, *MSAapp1*.
1. В поле **Поддерживаемые типы учетных записей** выберите вариант **Учетные записи в любом каталоге организации (любой каталог Azure AD — мультитенантный) и персональные учетные записи Майкрософт (например, Skype, Xbox)** .

   Дополнительные сведения о различных вариантах выбора типа учетной записи см. в статье [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](../active-directory/develop/quickstart-register-app.md)
1. В поле **URI перенаправления (необязательно)** выберите **Интернет** и введите `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` в текстовое поле. Замените `<tenant-name>` именем своего клиента Azure AD B2C.
1. Нажмите кнопку **Зарегистрировать**.
1. Запишите значение параметра **Идентификатор приложения (клиента)** , отображаемого на странице обзора приложения. Этот идентификатор потребуется при настройке поставщика удостоверений в следующем разделе.
1. Выберите **Сертификаты и секреты**.
1. Щелкните **Создать секрет клиента**.
1. В поле **Описание** введите описание секрета, например *Пароль приложения 1*, а затем нажмите кнопку **Добавить**.
1. Запишите пароль приложения, отображаемый в столбце **Значение**. Этот идентификатор потребуется при настройке поставщика удостоверений в следующем разделе.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Настройка учетной записи Microsoft в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **Поставщики удостоверений**, а затем — **Учетная запись Microsoft**.
1. Введите **Имя**. Например, *MSA*.
1. В качестве **идентификатора клиента** введите идентификатор приложения (клиента) для приложения Azure AD, созданного ранее.
1. В качестве **секрета клиента** введите секрет клиента, записанный ранее.
1. Щелкните **Сохранить**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Настройка необязательных утверждений

Чтобы получить утверждения `family_name` и `given_name` из Azure AD, можно настроить дополнительные утверждения для приложения в пользовательском интерфейсе портала Azure или манифесте приложения. Дополнительные сведения см. в статье о [предоставлении необязательных утверждений для приложения Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Войдите на [портал Azure](https://portal.azure.com). Найдите и выберите **Azure Active Directory**.
1. В разделе **Управление** выберите **Регистрация приложений**.
1. Выберите в списке приложение, для которого нужно настроить необязательные утверждения.
1. В разделе **Управление** выберите **Конфигурация токенов (предварительная версия)** .
1. Выберите **Добавить необязательное утверждение**.
1. Выберите тип токена, который нужно настроить.
1. Выберите необязательные утверждения для добавления.
1. Нажмите кнопку **Добавить**.

## <a name="create-a-policy-key"></a>Создание ключа политики

Теперь, когда вы создали приложение в клиенте Azure AD, необходимо сохранить секрет клиента этого приложения в клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите фильтр **Каталог и подписка** в верхнем меню и выберите каталог, который содержит ваш клиент.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **Ключи политики**, а затем щелкните **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `MSASecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
1. В поле **Секрет** введите секрет клиента, записанный в предыдущем разделе.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Чтобы разрешить пользователям входить в систему с помощью учетной записи Майкрософт, нужно определить учетную запись в качестве поставщика утверждений, с которым Azure AD B2C может взаимодействовать через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить Azure AD в качестве поставщика утверждений, добавив элемент **ClaimsProvider** в файл расширения политики.

1. Откройте файл политики *TrustFrameworkExtensions.xml*.
1. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
1. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Замените значение **client_id** *идентификатором приложения (клиента)* приложения Azure AD, записанным ранее.
1. Сохраните файл.

Политика настроена, так что Azure AD B2C знает, как взаимодействовать с приложением учетной записи Майкрософт в Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Прежде чем продолжить, отправьте измененную политику, чтобы проверить отсутствие в ней ошибок.

1. Перейдите к клиенту Azure AD B2C на портале Azure и выберите **Identity Experience Framework**.
1. На странице **Настраиваемые политики** выберите **Отправить настраиваемую политику**.
1. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
1. Щелкните **Отправить**.

Если на портале нет ошибок, перейдите к следующему разделу.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

К этому моменту поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат имеющегося шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений учетной записи Майкрософт.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
1. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
1. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
1. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
1. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экране регистрации или входа. Если вы добавите для учетной записи Майкрософт элемент **ClaimsProviderSelection**, при переходе пользователя на страницу отобразится новая кнопка.

1. В файле *TrustFrameworkExtensions.xml* найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
1. Добавьте следующий элемент в тэг **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `MicrosoftAccountExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью Майкрософт для получения токена.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
1. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора используется то же значение, которое использовалось для **TargetClaimsExchangeId**.

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Измените значение **TechnicalProfileReferenceId** так, чтобы оно совпадало со значением `Id` в элементе **TechnicalProfile** добавленного ранее поставщика утверждений. Например, `MSA-OIDC`.

1. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Добавление поставщика удостоверений Майкрософт в поток пользователя 

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
1. Щелкните пользовательский поток, который вы хотите использовать для поставщика удостоверений Майкрософт.
1. В разделе **поставщики удостоверений социальных сетей** выберите **учетная запись Майкрософт**.
1. Щелкните **Сохранить**.
1. Чтобы проверить политику, выберите пункт **выполнить пользовательскую последовательность**.
1. Для **приложения** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Щелкните **выполнить поток пользователя**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInMSA.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInMSA`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_msa`.
1. Обновите значение атрибута **ReferenceId** в элементе **DefaultUserJourney** так, чтобы оно соответствовало идентификатору созданного вами пути пользователя (SignUpSignInMSA).
1. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
1. Убедитесь, что созданное ранее приложение Azure AD B2C (например, *webapp1* или *testapp1*) выбрано в поле **Выберите приложение**, а затем протестируйте его, щелкнув **Запустить**.
1. Нажмите кнопку **Учетная запись Майкрософт** и выполните вход.

::: zone-end