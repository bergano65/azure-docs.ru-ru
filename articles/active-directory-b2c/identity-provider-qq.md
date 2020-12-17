---
title: Настройка регистрации и входа с учетной записью QQ через Azure Active Directory B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями QQ, используя Azure Active Directory B2C.
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
ms.openlocfilehash: b497176deff896e785387f4b64a8e66ff4d6d58e
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654325"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью QQ через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-qq-application"></a>Создание приложения QQ

Чтобы использовать учетную запись QQ в качестве поставщика удостоверений в Azure Active Directory B2C (Azure AD B2C), необходимо создать в своем клиенте приложение, которое его представляет. Если у вас еще нет учетной записи QQ, вы можете зарегистрироваться по адресу [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Регистрация в программе разработчиков QQ

1. Войдите на [портал разработчиков QQ](http://open.qq.com) с учетными данными от учетной записи QQ.
1. После входа перейдите на страницу, [https://open.qq.com/reg](https://open.qq.com/reg) чтобы зарегистрироваться в качестве разработчика.
1. Выберите **个人**(Индивидуальный разработчик).
1. Введите необходимые сведения и щелкните **下一步** (Следующий шаг).
1. Выполните проверку по электронной почте. После регистрации в качестве разработчика вам придется подождать утверждения в течение нескольких дней.

### <a name="register-a-qq-application"></a>Регистрация приложения QQ

1. Перейдите по адресу [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Выберите **应用管理**(Управление приложениями).
1. Выберите **创建应用**(Создать приложение) и введите необходимые сведения.
1. Введите значение `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` в поле **授权回调域** (URL-адрес обратного вызова). Например, если `tenant_name` — contoso, задайте URL-адрес `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Выберите **创建应用** (Создать приложение).
1. На странице подтверждения выберите **应用管理** (Управление приложениями), чтобы вернуться на страницу управления приложениями.
1. Выберите **查看** (Просмотр) рядом с только что созданным приложением.
1. Выберите **修改** (Редактирование).
1. Скопируйте **идентификатор приложения** и **ключ приложения**. Оба этих значения потребуются при добавлении поставщика удостоверений для вашего клиента.

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>Настройка QQ в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Выберите значок **Каталог и подписка** в верхней панели инструментов портала, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. На портале Azure найдите и выберите **Azure AD B2C**.
1. Выберите **поставщики удостоверений**, а затем выберите **QQ (Предварительная версия)**.
1. Введите **Имя**. Например, *QQ*.
1. В поле **идентификатор клиента** введите идентификатор приложения QQ, созданного ранее.
1. В качестве **секрета клиента** введите ЗАПИСАННЫЙ ключ приложения.
1. Щелкните **Сохранить**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить секрет клиента, который ранее был записан в клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите фильтр **Каталог и подписка** в верхнем меню и выберите каталог, который содержит ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
4. На странице "Обзор" выберите **Identity Experience Framework**.
5. Выберите **Ключи политики**, а затем щелкните **Добавить**.
6. Для пункта **Параметры** выберите `Manual`.
7. Введите **имя** ключа политики. Например, `QQSecret`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
8. В поле **Секрет** введите ранее записанный секрет клиента.
9. Для параметра **Использование ключа** выберите `Signature`.
10. Нажмите кнопку **Создать**.

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если вы хотите, чтобы пользователи вошли с помощью учетной записи QQ, необходимо определить учетную запись в качестве поставщика утверждений, который Azure AD B2C может взаимодействовать с помощью конечной точки. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить учетную запись QQ в качестве поставщика утверждений, добавив ее в элемент **клаимспровидерс** в файле расширения политики.

1. Откройте файл *TrustFrameworkExtensions.xml*.
2. Найдите элемент **ClaimsProviders**. Если он не существует, добавьте его в корневой элемент.
3. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAUTH">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. Задайте для параметра **client_id** значение идентификатора приложения из регистрации приложения.
5. Сохраните файл.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь вы настроили политику так, чтобы Azure AD B2C знала, как взаимодействовать с вашей учетной записью QQ. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
2. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
3. Щелкните **Отправить**.

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, создайте дубликат существующего шаблона пути взаимодействия пользователя, а затем измените его, чтобы у него также был поставщик удостоверений QQ.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney**, в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys**. Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys**.
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInQQ`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экранах регистрации и входа. Если добавить элемент **клаимспровидерселектион** для учетной записи QQ, то при переходе пользователя на страницу появится новая кнопка.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="1"` в созданном пути взаимодействия пользователя.
2. Добавьте следующий элемент в тэг **ClaimsProviderSelects**. Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `QQExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. Действие, в данном случае, предназначено для Azure AD B2C для взаимодействия с учетной записью QQ для получения маркера.

1. Найдите элемент **OrchestrationStep**, содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange**, убедившись, что для идентификатора можно использовать то же значение, которое было использовано для **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth" />
    ```

    Обновите значение **TechnicalProfileReferenceId**, присвоив ему значение идентификатора ранее созданного технического профиля. Например, `QQ-OAuth`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-qq-identity-provider-to-a-user-flow"></a>Добавление поставщика удостоверений QQ в поток пользователя 

1. В клиенте Azure AD B2C выберите **Потоки пользователей**.
1. Щелкните пользовательский поток, который вы хотите использовать для поставщика удостоверений QQ.
1. В разделе **поставщики удостоверений социальных сетей** выберите **QQ**.
1. Щелкните **Сохранить**.
1. Чтобы проверить политику, выберите пункт **выполнить пользовательскую последовательность**.
1. Для **приложения** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Щелкните **выполнить поток пользователя**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте его в *SignUpSignInQQ.xml*.
1. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Например, `SignUpSignInQQ`.
1. Обновите значение **PublicPolicyUri**, указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_QQ`.
1. Обновите значение атрибута **ReferenceId** в **дефаултусержаурнэй** , чтобы оно совпадало с идентификатором нового пути взаимодействия пользователя, созданного пользователем (сигнупсигнкк).
1. Сохраните изменения и отправьте файл.
1. В разделе **Настраиваемые политики** выберите **B2C_1A_signup_signin**.
1. В поле **выберите приложение** выберите веб-приложение с именем *testapp1* , которое вы зарегистрировали ранее. В поле **URL-адрес ответа** должно содержаться значение `https://jwt.ms`.
1. Выберите **Запустить сейчас** и выберите QQ, чтобы войти в систему с помощью QQ и проверить настраиваемую политику.

::: zone-end
