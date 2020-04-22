---
title: Настройка регистрации для мультитенантного Azure AD с помощью пользовательских политик
titleSuffix: Azure AD B2C
description: Добавьте мультитенантный поставщик идентификационных данных Azure AD, использующий пользовательские политики в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 320723744e1366fdc73cd0593fb0ebece03367f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678108"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа для мультитенантного Azure Active Directory с помощью пользовательских политик в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье показано, как включить в систему для пользователей, использующих мультитенантную конечную точку для Azure Active Directory (Azure AD) с помощью [пользовательских политик](custom-policy-overview.md) в Azure AD B2C. Это позволяет пользователям из нескольких клиентов Azure AD войти в систему с помощью Azure AD B2C без необходимости настраивать поставщика идентификационных данных для каждого клиента. Однако гостевые пользователи любого из этих тенантов **не смогут** войти в систему. Для этого необходимо [отдельно настроить каждый клиент](identity-provider-azure-ad-single-tenant-custom.md).

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Регистрация приложения

Чтобы включить вход для пользователей из определенной организации Azure AD, вам необходимо зарегистрировать приложение в клиенте организации Azure AD.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий ваш организационный арендатор Azure AD (например, contoso.com). Выберите **фильтр подписки каталога в** верхнем меню, а затем выберите каталог, содержащий ваш арендатор.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Новая регистрация**.
1. Введите **имя** для вашего приложения. Например, `Azure AD B2C App`.
1. Выберите **учетные записи в любом каталоге организации** для этого приложения.
1. Для **перенаправления URI**примите значение **Web**и введите следующий URL `your-B2C-tenant-name` во всех буквах нижнего регистра, где он заменяется именем вашего арендатора Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Например, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Выберите **Зарегистрировать**. Запишите значение параметра **Идентификатор приложения (клиент)**. Оно вам потребуется в дальнейшем.
1. Выберите **Сертификаты & секреты,** а затем выберите **новый секрет клиента.**
1. Введите **описание** секрета, выберите истечение срока действия, а затем выберите **Добавить**. Запись **значения** секрета для использования в более позднем шаге.

## <a name="configuring-optional-claims"></a>Настройка необязательных утверждений

Если вы хотите `family_name` получить `given_name` и претензии от Azure AD, можно настроить дополнительные требования для приложения в uI или манифесте приложения портала Azure. Для получения дополнительной информации [см. Как предоставить дополнительные претензии к приложению Azure AD.](../active-directory/develop/active-directory-optional-claims.md)

1. Войдите на [портал Azure](https://portal.azure.com). Найдите и выберите **Azure Active Directory**.
1. Из раздела **«Управление»** выберите **регистрацию приложений.**
1. Выберите приложение, для которого вы хотите настроить дополнительные претензии в списке.
1. Из раздела **«Управление»** выберите **конфигурацию токенов.**
1. Выберите **Добавить необязательный claim**.
1. Для **типа токен**, выберите **ID.**
1. Выберите дополнительные претензии `given_name`для добавления и `family_name` .
1. Нажмите кнопку **Добавить**.

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить ключ приложения, который вы создали в клиенте Azure AD B2C.

1. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите **фильтр подписки каталога** в верхнем меню, а затем выберите каталог, содержащий ваш клиент Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. В соответствии с **политиками**выберите **рамки опыта идентификации.**
1. Выберите **ключи политики,** а затем выберите **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `AADAppSecret`.  Приставка `B2C_1A_` добавляется автоматически к названию ключа при его создании, поэтому его ссылка в XML в следующем разделе должна *B2C_1A_AADAppSecret.*
1. В **Secret**, введите ваш клиент секрет, который вы записали ранее.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Нажмите кнопку **создания**.

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
1. Под элементом **TechnicalProfile** обновите значение для `Contoso Employee` **DisplayName,** например, . Это значение отображается на кнопке входа на экране входа.
1. Установите **client_id** идентификатор приложения мультитенантного приложения Azure AD, который вы зарегистрировали ранее.
1. Под **CryptographicKeys**обновите значение **StorageReferenceId** до имени ключа политики, созданного ранее. Например, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Ограничение доступа

> [!NOTE]
> Если использовать `https://login.microsoftonline.com/` в качестве значения для **ValidTokenIssuerPrefixes**, все пользователи Azure AD смогут входить в ваше приложение.

Вам необходимо обновить список допустимых издателей маркеров и предоставить доступ только определенному списку пользователей клиента Azure AD, которые могут выполнить вход в систему.

Чтобы получить значения, посмотрите на метаданные обнаружения OpenID Connect для каждого из арендаторов Azure AD, от которых вы хотели бы, чтобы пользователи включились в систему. Формат URL-адреса метаданных `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`аналогичен `your-tenant` тому, где находится имя арендатора Azure AD. Пример:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Выполните следующие действия для каждого клиента Azure AD, которые должны быть использованы для вху-ввена:

1. Откройте браузер и перейдите на URL-адрес метаданных OpenID Connect для арендатора. Найдите объект **эмитента** и запишите его значение. Он должен выглядеть как `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`.
1. Копировать и вставить значение в ключ **ValidTokenIssuerPrefixes.** Отдельно несколько эмитентов с запятой. Пример с двумя эмитентами отображается в предыдущем `ClaimsProvider` образце XML.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

К настоящему времени вы настроили политику таким образом, чтобы Azure AD B2C знал, как общаться с вашими каталогами Azure AD. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Выберите команду **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат существующего шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений Azure AD.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экранах регистрации и входа. Если вы добавите для учетной записи Azure AD элемент **ClaimsProviderSelection**, при переходе пользователя на страницу отобразится новая кнопка.

1. Найдите элемент **OrchestrationStep,** который включает `Order="1"` в себя пользовательское путешествие, созданное в *TrustFrameworkExtensions.xml.*
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

    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение **Id** ранее созданного технического профиля. Например, `Common-AAD`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Связь с Azure AD B2C происходит через приложение, которое регистрируется в вашем арендаторе B2C. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновление файла relying party (RP), который инициирует созданное вами путешествие пользователя:

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignContoso.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInContoso`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Обновите значение атрибута **ReferenceId** в **DefaultUserJourney** в соответствии с идентификатором пользовательского путешествия, созданного ранее. Например, *SignUpSignInContoso*.
1. Сохраните изменения и отправьте файл.
1. В соответствии с **пользовательскими политиками**выберите новую политику в списке.
1. В приложении **Select** выпадок выберите приложение Azure AD B2C, созданное ранее. Например, *testapp1*.
1. Копируйте **конечную точку Run** и откройте ее в окне частного браузера, например, incognito Mode в Google Chrome или окно InPrivate в Microsoft Edge. Открытие в личном окне браузера позволяет протестировать полное путешествие пользователя, не используя в настоящее время кэшированные учетные данные Azure AD.
1. Выберите веком Azure AD, например, *Contoso Employee,* а затем введите учетные данные для пользователя в одном из ваших арендаторов Azure AD. Вас просят авторизовать приложение, а затем ввести информацию для вашего профиля.

Если в процессе велась работа, ваш `https://jwt.ms`браузер перенаправляется в , который отображает содержимое токена, возвращенного Azure AD B2C.

Чтобы протестировать возможность входной системы мультитенантов, выполните последние два шага, используя учетные данные для пользователя, который существует другой арендатор Azure AD.

## <a name="next-steps"></a>Следующие шаги

При работе с пользовательскими политиками иногда может потребоваться дополнительная информация при устранении неполадок в ходе ее разработки.

Чтобы помочь диагностировать проблемы, можно временно поместить политику в "режим разработчика" и собрать журналы с помощью Azure Application Insights. Узнайте, как в [Azure Active Directory B2C: Сбор журналов](troubleshoot-with-application-insights.md).
