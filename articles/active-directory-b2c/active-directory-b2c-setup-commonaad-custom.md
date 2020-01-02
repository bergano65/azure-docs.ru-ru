---
title: Настройка входа для нескольких клиентов Azure AD с помощью настраиваемых политик
titleSuffix: Azure AD B2C
description: Добавление поставщика удостоверений Azure AD с несколькими клиентами с помощью пользовательских политик в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: abac9152ff71c8fbf4ed53bfd663d29616382cf9
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947794"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа для мультитенантного Azure Active Directory с помощью пользовательских политик в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается настройка входа для пользователей с помощью мультитенантной конечной точки Azure Active Directory (Azure AD) с использованием [пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory B2C. Это позволяет пользователям из нескольких клиентов Azure AD входить в систему, используя Azure AD B2C, без необходимости настройки поставщика удостоверений для каждого клиента. Однако гостевые пользователи любого из этих тенантов **не смогут** войти в систему. Для этого необходимо [отдельно настроить каждый клиент](active-directory-b2c-setup-aad-custom.md).

## <a name="prerequisites"></a>Технические условия

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Регистрация приложения

Чтобы включить вход для пользователей из определенной организации Azure AD, вам необходимо зарегистрировать приложение в клиенте организации Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD Организации (например, contoso.com). Выберите **Фильтр каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий ваш клиент.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Новая регистрация**.
1. Введите значение **Name** (Имя) для приложения. Пример: `Azure AD B2C App`.
1. Выберите **учетные записи в любом каталоге Организации** для этого приложения.
1. В качестве **URI перенаправления**оставьте значение **Web**и введите следующий URL-адрес в строчных буквах, где `your-B2C-tenant-name` заменяется именем клиента Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Пример: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Выберите **Зарегистрировать**. Запишите **идентификатор приложения (клиента)** . Он вам потребуется в дальнейшем.
1. Выберите **сертификаты & секреты**, а затем выберите **новый секрет клиента**.
1. Введите **Описание** секрета, выберите срок действия, а затем нажмите кнопку **Добавить**. Запишите **значение** секрета для использования на следующем шаге.

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить ключ приложения, который вы создали в клиенте Azure AD B2C.

1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите **Фильтр каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. В разделе **политики**выберите **инфраструктура процедур идентификации**.
1. Выберите **ключи политики** и нажмите кнопку **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **Имя** ключа политики. Пример: `AADAppSecret`.  Префикс `B2C_1A_` автоматически добавляется к имени ключа при его создании, поэтому ссылка в XML-файле в следующем разделе будет *B2C_1A_AADAppSecret*.
1. В качестве **секрета**введите секрет клиента, записанный ранее.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если необходимо разрешить пользователям входить в систему с помощью Azure Active Directory, нужно определить Azure Active Directory в качестве поставщика утверждений, с которым Azure AD B2C может взаимодействовать через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить Azure AD в качестве поставщика утверждений, добавив Azure AD к элементу **ClaimsProvider** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
1. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
1. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. В элементе **ClaimsProvider** укажите для **Domain** уникальное значение, позволяющее отличить этот поставщик удостоверений от других.
1. В элементе **техническом профиле** обновите значение **DisplayName**, например `Contoso Employee`. Это значение отображается на кнопке входа на экране входа.
1. Задайте **client_id** идентификатору приложения для приложения с несколькими клиентами Azure AD, зарегистрированного ранее.
1. В разделе **криптографиккэйс**измените значение **идентификатором storagereferenceid** на имя созданного ранее ключа политики. Пример: `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Ограничение доступа

> [!NOTE]
> Если использовать `https://login.microsoftonline.com/` в качестве значения для **ValidTokenIssuerPrefixes**, все пользователи Azure AD смогут входить в ваше приложение.

Вам необходимо обновить список допустимых издателей маркеров и предоставить доступ только определенному списку пользователей клиента Azure AD, которые могут выполнить вход в систему.

Чтобы получить эти значения, просмотрите метаданные обнаружения OpenID Connect Connect для каждого клиента Azure AD, из которого будут входить пользователи. Формат URL-адреса метаданных аналогичен `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`, где `your-tenant` — это имя клиента Azure AD. Пример.

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Выполните следующие действия для каждого клиента Azure AD, который должен использоваться для входа:

1. Откройте браузер и перейдите по URL-адресу метаданных OpenID Connect Connect для клиента. Найдите объект **Issuer** и запишите его значение. Он должен выглядеть так же, как `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`.
1. Скопируйте и вставьте значение в ключ **ValidTokenIssuerPrefixes** . Несколько издателей разделяются запятыми. Пример с двумя издателями отображается в предыдущем примере `ClaimsProvider` XML.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь вы настроили политику так, чтобы Azure AD B2C знала, как взаимодействовать с каталогами Azure AD. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Щелкните **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат существующего шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений Azure AD.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Пример: `SignUpSignInContoso`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экранах регистрации и входа. Если вы добавите для учетной записи Azure AD элемент **ClaimsProviderSelection**, при переходе пользователя на страницу отобразится новая кнопка.

1. Найдите элемент **орчестратионстеп** , который включает `Order="1"` в пути взаимодействия пользователя, созданного в *TrustFrameworkExtensions. XML*.
1. Добавьте следующий элемент в тэг **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с Azure AD для получения токена. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений Azure AD.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для **Id** можно использовать то же значение, которое было использовано для **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение **Id** ранее созданного технического профиля. Пример: `Common-AAD`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Связь с Azure AD B2C происходит через приложение, регистрируемое в клиенте B2C. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны (RP), который инициирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignContoso.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Пример: `SignUpSignInContoso`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Пример: `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Обновите значение атрибута **ReferenceId** в **дефаултусержаурнэй** , чтобы оно совпадало с идентификатором пути взаимодействия пользователя, созданного ранее. Например, *сигнупсигнинконтосо*.
1. Сохраните изменения и отправьте файл.
1. В разделе **пользовательские политики**выберите новую политику в списке.
1. В раскрывающемся списке **Выбор приложения** выберите созданное ранее приложение Azure AD B2C. Например, *testapp1*.
1. Скопируйте **конечную точку "запустить сейчас** " и откройте ее в частном окне браузера, например в режиме режиме инкогнито в Google Chrome или в окне InPrivate в Microsoft погранично. Открытие в частном окне браузера позволяет протестировать полное путешествие пользователя, не используя кэшированные учетные данные Azure AD.
1. Выберите кнопку входа в Azure AD, например *сотрудник Contoso*, а затем введите учетные данные пользователя в одном из ваших клиентов организации Azure AD. Вам будет предложено авторизовать приложение, а затем ввести сведения для профиля.

Если вход выполнен успешно, браузер перенаправляется в `https://jwt.ms`, который отображает содержимое маркера, возвращенного Azure AD B2C.

Чтобы протестировать возможность входа с несколькими клиентами, выполните последние два шага, используя учетные данные пользователя, который уже существует в другом клиенте Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

При работе с пользовательскими политиками иногда может потребоваться дополнительная информация при устранении неполадок политики во время ее разработки.

Чтобы помочь в диагностике проблем, можно временно перевести политику в режим разработчика и собрать журналы с помощью Azure Application Insights. Узнайте, как в [Azure Active Directory B2C: сбор журналов](active-directory-b2c-troubleshoot-custom.md).
