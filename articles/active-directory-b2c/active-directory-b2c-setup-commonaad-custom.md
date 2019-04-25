---
title: Настройка входа для мультитенантного поставщика удостоверений Azure AD с помощью пользовательских политик в Azure Active Directory B2C | Документация Майкрософт
description: Добавление мультитенантного поставщика удостоверений Azure AD с помощью пользовательских политик в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 41b170ea66b1cb4c830ad0327ac2e1e3d2922b04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316822"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа для мультитенантного Azure Active Directory с помощью пользовательских политик в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается настройка входа для пользователей с помощью мультитенантной конечной точки Azure Active Directory (Azure AD) с использованием [пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory B2C. Это позволяет клиентам мультитенантного поставщика Azure AD подписываться на Azure AD B2C без настройки технического поставщика. Однако гостевые пользователи любого из этих тенантов **не смогут** войти в систему. Для этого необходимо [отдельно настроить каждый клиент](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>В следующих инструкциях клиент организации Azure AD будет называться `Contoso.com`, а клиент Azure AD B2C — `fabrikamb2c.onmicrosoft.com`.

## <a name="prerequisites"></a>Технические условия

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Регистрация приложения

Чтобы включить вход для пользователей из определенной организации Azure AD, вам необходимо зарегистрировать приложение в клиенте организации Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Убедитесь, что используете каталог, содержащий клиент организации Azure AD (contoso.com), щелкнув **фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Регистрация нового приложения**.
5. Введите имя приложения. Например, `Azure AD B2C App`.
6. В качестве **типа приложения** выберите `Web app / API`.
7. В поле **URL-адрес входа** введите следующий URL-адрес в нижнем регистре, где `your-tenant` заменяется именем вашего клиента Azure AD B2C (fabrikamb2c.onmicrosoft.com).

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```
    
8. Нажмите кнопку **Создать**. Скопируйте **идентификатор приложения** для использования в будущем.
9. Выберите приложение, а затем щелкните **Параметры**.
10. Выберите **Ключи**, введите описание ключа, установите срок действия и нажмите кнопку **Сохранить**. Скопируйте значение ключа, который отображается, для использования в будущем.
11. В разделе **Параметры** выберите **Свойства**, задайте для свойства **Мультитенантный** значение `Yes`, а затем нажмите кнопку **Сохранить**.

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить ключ приложения, который вы создали в клиенте Azure AD B2C.

1. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
2. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
3. На странице "Обзор" выберите **Identity Experience Framework — предварительная версия**.
4. Выберите **Ключи политики**, а затем щелкните **Добавить**.
5. Для пункта **Параметры** выберите `Manual`.
6. Введите **имя** ключа политики. Например, `ContosoAppSecret`.  Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
7. В поле **Секрет** введите ранее записанный ключ приложения.
8. Для параметра **Использование ключа** выберите `Signature`.
9. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если необходимо разрешить пользователям входить в систему с помощью Azure Active Directory, нужно определить Azure Active Directory в качестве поставщика утверждений, с которым Azure AD B2C может взаимодействовать через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя. 

Вы можете определить Azure AD в качестве поставщика утверждений, добавив Azure AD к элементу **ClaimsProvider** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
2. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
3. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

4. В элементе **ClaimsProvider** укажите для **Domain** уникальное значение, позволяющее отличить этот поставщик удостоверений от других.
5. В элементе **TechnicalProfile** обновите значение **DisplayName**. Это значение отображается на кнопке входа на экране входа.
6. Задайте для параметра **client_id** идентификатор приложения из регистрации мультитенантного приложения Azure AD.

### <a name="restrict-access"></a>Ограничение доступа

> [!NOTE]
> Если использовать `https://sts.windows.net` в качестве значения для **ValidTokenIssuerPrefixes**, все пользователи Azure AD смогут входить в ваше приложение.

Вам необходимо обновить список допустимых издателей маркеров и предоставить доступ только определенному списку пользователей клиента Azure AD, которые могут выполнить вход в систему. Чтобы получить значения, необходимо просмотреть метаданные для каждого клиента Azure AD, с помощью которого пользователи будут выполнять вход в систему. Данные имеют следующий формат: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, где `your-tenant` — имя вашего клиента Azure AD (contoso.com или любой другой клиент Azure AD).

1. Откройте браузер, перейдите по URL-адресу **метаданных**, найдите объект **issuer** и скопируйте его значение. Вы должны увидеть примерно следующее: `https://sts.windows.net/tenant-id/`.
2. Скопируйте и вставьте значение ключа **ValidTokenIssuerPrefixes**. Вы можете добавить несколько значений, разделив их запятой. Пример прокомментирован в образце XML выше.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

К этому моменту политика настроена, так что Azure AD B2C знает, как взаимодействовать с каталогом Azure AD. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Щелкните **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат существующего шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений Azure AD.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экранах регистрации и входа. Если вы добавите для учетной записи Azure AD элемент **ClaimsProviderSelection**, при переходе пользователя на страницу отобразится новая кнопка.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
2. Добавьте следующий элемент в тэг **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с Azure AD для получения токена. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений Azure AD.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути взаимодействия пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для **Id** можно использовать то же значение, которое было использовано для **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```
    
    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение **Id** ранее созданного технического профиля. Например, `Common-AAD`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Связь с Azure AD B2C осуществляется с помощью приложения, созданного в вашем клиенте. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. Щелкните **Приложения**, а затем выберите **Добавить**.
5. Задайте имя для приложения, например *testapp1*.
6. В пункте **Веб-приложение или веб-интерфейс API**выберите `Yes`, а затем в пункте **URL-адрес ответа** введите `https://jwt.ms`.
7. Нажмите кнопку **Создать**.

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignContoso.xml*.
2. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInContoso`.
3. Обновите значение **PublicPolicyUri**, указав URI для политики. Например, `http://contoso.com/B2C_1A_signup_signin_contoso`.
4. Обновите значение атрибута **ReferenceId** для элемента **DefaultUserJourney**, чтобы он соответствовал идентификатору созданного вами пути взаимодействия пользователя (SignUpSignContoso).
5. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
6. Убедитесь, что созданное приложение Azure AD B2C выбрано в поле **Выберите приложение**, а затем протестируйте его, щелкнув **Запустить сейчас**.
