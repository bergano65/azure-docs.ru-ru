---
title: Настройка регистрации с учетной записью Azure AD с помощью пользовательских политик
titleSuffix: Azure AD B2C
description: Настройка входа в службу Azure Active Directory B2C с помощью учетной записи Azure Active Directory с использованием пользовательских политик.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5a6c85ebed7271655745de45694542fb359836e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188416"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка входа в Azure Active Directory B2C с помощью учетной записи Azure Active Directory с использованием пользовательских политик

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье показано, как включить в систему для пользователей из организации Azure Active Directory (Azure AD) [пользовательские политики](custom-policy-overview.md) в Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Регистрация приложения

Чтобы включить вход для пользователей из определенной организации Azure AD, вам необходимо зарегистрировать приложение в клиенте организации Azure AD.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий ваш организационный арендатор Azure AD (например, contoso.com). Выберите **фильтр подписки каталога в** верхнем меню, а затем выберите каталог, содержащий ваш арендатор Azure AD.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Новая регистрация**.
1. Введите значение **Name** (Имя) для приложения. Например, `Azure AD B2C App`.
1. Примите выбор **учетных записей** по умолчанию в этом каталоге организации только для этого приложения.
1. Для **перенаправления URI**примите значение **Web**и введите следующий URL `your-B2C-tenant-name` во всех буквах нижнего регистра, где он заменяется именем вашего арендатора Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Например, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Выберите **Зарегистрировать**. Запишите значение параметра **Идентификатор приложения (клиент)**. Оно вам потребуется в дальнейшем.
1. Выберите **Сертификаты & секреты,** а затем выберите **новый секрет клиента.**
1. Введите **описание** секрета, выберите истечение срока действия, а затем выберите **Добавить**. Запись **значения** секрета для использования в более позднем шаге.

## <a name="configuring-optional-claims"></a>Настройка необязательных утверждений

Если вы хотите `family_name` получить `given_name` и претензии от Azure AD, можно настроить дополнительные требования для приложения в uI или манифесте приложения портала Azure. Для получения дополнительной информации [см. Как предоставить дополнительные претензии к приложению Azure AD.](../active-directory/develop/active-directory-optional-claims.md)

1. Войдите на [портал Azure](https://portal.azure.com). Найдите и выберите **Azure Active Directory**.
1. Из раздела **«Управление»** выберите **регистрацию приложений.**
1. Выберите приложение, для которого вы хотите настроить дополнительные претензии в списке.
1. Из раздела **«Управление»** выберите **конфигурацию токенов (предварительный просмотр).**
1. Выберите **Добавить необязательный claim**.
1. Выберите тип маркера, который вы хотите настроить.
1. Выберите дополнительные претензии для добавления.
1. Нажмите кнопку **Добавить**.

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить ключ приложения, который вы создали в клиенте Azure AD B2C.

1. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите **фильтр подписки каталога** в верхнем меню, а затем выберите каталог, содержащий ваш клиент Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. В соответствии с **политиками**выберите **рамки опыта идентификации.**
1. Выберите **ключи политики,** а затем выберите **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `ContosoAppSecret`.  Приставка `B2C_1A_` добавляется автоматически к имени вашего ключа при его создании, поэтому его ссылка в XML в следующем разделе должна *B2C_1A_ContosoAppSecret.*
1. В **Secret**, введите ваш клиент секрет, который вы записали ранее.
1. Для параметра **Использование ключа** выберите `Signature`.
1. Выберите **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если необходимо разрешить пользователям входить в систему с помощью Azure Active Directory, нужно определить Azure Active Directory в качестве поставщика утверждений, с которым Azure AD B2C может взаимодействовать через конечную точку. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить Azure AD в качестве поставщика утверждений, добавив Azure AD к элементу **ClaimsProvider** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
2. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
3. Добавьте новый элемент **ClaimsProvider** следующим образом.
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
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

4. В элементе **ClaimsProvider** укажите для **Domain** уникальное значение, позволяющее отличить этот поставщик удостоверений от других. Например, `Contoso`. Не помещайте `.com` в конец этого параметра домена.
5. В элементе **ClaimsProvider** обновите значение **DisplayName**, указав понятное имя поставщика утверждений. В настоящее время это значение не используется.

### <a name="update-the-technical-profile"></a>Обновление технического профиля

Чтобы получить токен из конечной точки Azure AD, вам необходимо определить протоколы, используемые Azure AD B2C для взаимодействия с Azure AD. Этот выполняется в элементе **TechnicalProfile** в **ClaimsProvider**.

1. Обновите идентификатор элемента **TechnicalProfile**. Этот идентификатор используется для обозначения этого технического `OIDC-Contoso`профиля из других частей политики, например.
1. Обновите значение **DisplayName**. Это значение будет отображаться на кнопке входа на экране входа в систему.
1. Обновите значение **Description**.
1. Azure AD использует протокол OpenID Connect, поэтому для параметра **Protocol** должно быть задано значение `OpenIdConnect`.
1. Задайте для параметра **METADATA** значение `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`, где `tenant-name` — это имя клиента Azure AD. Например `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
1. Задайте для параметра **client_id** значение идентификатора приложения из регистрации приложения.
1. В **рамках CryptographicKeys**обновите значение **StorageReferenceId** до имени ключа политики, созданного ранее. Например, `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

К этому моменту политика настроена, так что Azure AD B2C знает, как взаимодействовать с каталогом Azure AD. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
1. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
1. Нажмите кнопку **Отправка**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На данный момент провайдер идентификационных данных настроен, но он еще не доступен ни на одной из страниц регистрации/регистрации. Чтобы сделать его доступным, создайте дубликат существующего пользовательского путешествия шаблона, а затем измените его так, чтобы он также был поставщиком идентификационных данных Azure AD:

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
1. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
1. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
1. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
1. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** аналогис к кнопке поставщика идентификационных данных на странице регистрации/регистрации. Если вы добавите для учетной записи Azure AD элемент **ClaimsProviderSelection**, при переходе пользователя на страницу отобразится новая кнопка.

1. Найдите элемент **OrchestrationStep,** который включает `Order="1"` в себя пользовательское путешествие, созданное в *TrustFrameworkExtensions.xml.*
1. Добавьте следующий элемент под элементом **ClaimsProviderSelections**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с Azure AD для получения токена. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений Azure AD.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
1. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для **Id** можно использовать то же значение, которое было использовано для **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение **Id** ранее созданного технического профиля. Например, `OIDC-Contoso`.

1. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Связь с Azure AD B2C происходит через приложение, которое регистрируется в вашем арендаторе B2C. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInContoso.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInContoso`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Обновите значение атрибута **ReferenceId** в **DefaultUserJourney** в соответствии с идентификатором пользовательского путешествия, созданного ранее. Например, *SignUpSignInContoso*.
1. Сохраните изменения и отправьте файл.
1. В соответствии с **пользовательскими политиками**выберите новую политику в списке.
1. В приложении **Select** выпадок выберите приложение Azure AD B2C, созданное ранее. Например, *testapp1*.
1. Копируйте **конечную точку Run** и откройте ее в окне частного браузера, например, incognito Mode в Google Chrome или окно InPrivate в Microsoft Edge. Открытие в личном окне браузера позволяет протестировать полное путешествие пользователя, не используя в настоящее время кэшированные учетные данные Azure AD.
1. Выберите веком Azure AD в кнопке, например, *Contoso Employee,* а затем введите учетные данные для пользователя в организационном арендаторе Azure AD. Вас просят авторизовать приложение, а затем ввести информацию для вашего профиля.

Если в процессе велась работа, ваш `https://jwt.ms`браузер перенаправляется в , который отображает содержимое токена, возвращенного Azure AD B2C.

## <a name="next-steps"></a>Дальнейшие действия

При работе с пользовательскими политиками иногда может потребоваться дополнительная информация при устранении неполадок в ходе ее разработки.

Чтобы помочь диагностировать проблемы, можно временно поместить политику в "режим разработчика" и собрать журналы с помощью Azure Application Insights. Узнайте, как в [Azure Active Directory B2C: Сбор журналов](troubleshoot-with-application-insights.md).
