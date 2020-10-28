---
title: Добавление AD FS в качестве поставщика удостоверений SAML с помощью настраиваемых политик
titleSuffix: Azure AD B2C
description: Настройка AD FS 2016 с помощью протокола SAML и пользовательских политик в Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8cd761131fba23e89d1f72aed018a3e1dfd27e60
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668742"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Добавление AD FS в качестве поставщика удостоверений SAML с помощью пользовательских политик в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье показано, как включить вход для учетной записи AD FS пользователя с помощью [пользовательских политик](custom-policy-overview.md) в Azure Active Directory B2C (Azure AD B2C). Вход в систему включается путем добавления [технического профиля поставщика удостоверений SAML](saml-identity-provider-technical-profile.md) в пользовательскую политику.

## <a name="prerequisites"></a>Предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md).
- Убедитесь, что у вас есть доступ к PFX-файлу сертификата с закрытым ключом. Можно создать собственный подписанный сертификат и передать его в Azure AD B2C. Azure AD B2C использует этот сертификат для подписи запроса SAML, отправляемого поставщику удостоверений SAML. Дополнительные сведения о создании сертификата см. в разделе [Создание сертификата для подписи](identity-provider-salesforce-custom.md#generate-a-signing-certificate).
- Чтобы служба Azure принимала пароль PFX-файла, пароль должен быть зашифрован с помощью параметра TripleDES – SHA1 в служебной программе экспорта хранилища сертификатов Windows, а не AES256-SHA256.

## <a name="create-a-policy-key"></a>Создание ключа политики

Вам необходимо сохранить сертификат в клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. Выберите фильтр **Каталог и подписка** в верхнем меню и выберите каталог, который содержит ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C** .
4. На странице "Обзор" выберите **Identity Experience Framework** .
5. Выберите **Ключи политики** , а затем щелкните **Добавить** .
6. Для пункта **Параметры** выберите `Upload`.
7. Введите **имя** ключа политики. Например, `ADFSSamlCert`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
8. Найдите и выберите PFX-файл сертификата с закрытым ключом.
9. Нажмите кнопку **Создать** .

## <a name="add-a-claims-provider"></a>Добавление поставщика утверждений

Если вы хотите, чтобы пользователи вошли с помощью учетной записи AD FS, необходимо определить учетную запись как поставщика утверждений, который Azure AD B2C может взаимодействовать с помощью конечной точки. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Вы можете определить учетную запись AD FS как поставщика утверждений, добавив ее в элемент **клаимспровидерс** в файле расширения политики. Дополнительные сведения см. в разделе об [определении технического профиля поставщика удостоверений SAML](saml-identity-provider-technical-profile.md).

1. Откройте файл *TrustFrameworkExtensions.xml* .
1. Найдите элемент **ClaimsProviders** . Если он не существует, добавьте его в корневой элемент.
1. Добавьте новый элемент **ClaimsProvider** следующим образом.

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Замените `your-AD-FS-domain` именем домена AD FS и замените значение исходящего утверждения **IDENTITYPROVIDER** на DNS (произвольное значение, указывающее ваш домен).

1. Откройте раздел `<ClaimsProviders>` и добавьте следующий фрагмент кода XML. Если политика уже содержит технический профиль `SM-Saml-idp`, перейдите к следующему шагу. Дополнительные сведения см. в разделе об [управлении сеансом единого входа](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Сохраните файл.

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь вы настроили политику так, чтобы Azure AD B2C знала, как взаимодействовать с учетной записью AD FS. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке.

1. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику** .
2. Включите функцию **Перезаписать политику, если она уже существует** , а затем найдите и выберите файл *TrustFrameworkExtensions.xml* .
3. Щелкните **Отправить** .

> [!NOTE]
> Расширение Visual Studio Code B2C использует "СоЦиалидпусерид". Для AD FS также требуется политика социальных сетей.
>

## <a name="register-the-claims-provider"></a>Регистрация поставщика утверждений

На этом этапе поставщик удостоверений уже настроен, но еще недоступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, создайте дубликат существующего шаблона пути взаимодействия пользователя, а затем измените его, чтобы у него также был поставщик удостоверений AD FS.

1. Откройте файл *TrustFrameworkBase.xml* из начального пакета.
2. Найдите и скопируйте все содержимое элемента **UserJourney** , в котором присутствует запись `Id="SignUpOrSignIn"`.
3. Откройте файл *TrustFrameworkExtensions.xml* и найдите элемент **UserJourneys** . Если элемент не существует, добавьте его.
4. Вставьте все скопированное содержимое элемента **UserJourney** в качестве дочернего элемента в элемент **UserJourneys** .
5. Переименуйте идентификатор пути взаимодействия пользователя. Например, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на экране регистрации или входа. Если добавить элемент **клаимспровидерселектион** для учетной записи AD FS, то при переходе пользователя на страницу появится новая кнопка.

1. Найдите элемент **OrchestrationStep** , содержащий `Order="1"` в созданном пути взаимодействия пользователя.
2. Добавьте следующий элемент под элементом **ClaimsProviderSelections** . Установите для параметра **TargetClaimsExchangeId** соответствующее значение, например `ContosoExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. Действие, в данном случае, предназначено для Azure AD B2C для взаимодействия с учетной записью AD FS для получения маркера.

1. Найдите элемент **OrchestrationStep** , содержащий `Order="2"` в пути пользователя.
2. Добавьте следующий элемент **ClaimsExchange** , убедившись, что для идентификатора используется то же значение, которое использовалось для **TargetClaimsExchangeId** .

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Обновите значение **TechnicalProfileReferenceId** , присвоив ему значение идентификатора ранее созданного технического профиля. Например, `Contoso-SAML2`.

3. Сохраните файл *TrustFrameworkExtensions.xml* и повторно отправьте его для проверки.


## <a name="configure-an-ad-fs-relying-party-trust"></a>Настройка отношения доверия с проверяющей стороной AD FS

Чтобы использовать AD FS в качестве поставщика удостоверений в Azure AD B2C, необходимо создать AD FS отношения доверия с проверяющей стороной с метаданными SAML Azure AD B2C. В указанном ниже примере показан URL-адрес метаданных SAML, связанных с техническим профилем Azure AD B2C.

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Измените следующие значения:

- **ваш клиент** с именем клиента, например Your-tenant.onmicrosoft.com.
- **your-policy** — именем собственной политики. Например, B2C_1A_signup_signin_adfs.
- **ваш-технический профиль** с именем технического профиля поставщика удостоверений SAML. Например, Contoso-SAML2.

Откройте браузер и перейдите по URL-адресу. Убедитесь, что ввели правильный URL-адрес и что имеете доступ к XML-файлу метаданных. Чтобы добавить новое отношение доверия с проверяющей стороной с помощью оснастки управления AD FS и вручную настроить параметры, выполните следующую процедуру на сервере федерации. Для выполнения этой процедуры требуется как минимум членство в группах **Администраторы** или эквивалент на локальном компьютере.

1. В диспетчер сервера выберите **инструменты** , а затем выберите **AD FS управление** .
2. Выберите **Добавить отношение доверия с проверяющей стороной** .
3. На странице **приветствия** выберите **Поддерживающие утверждения** и нажмите кнопку **Запустить** .
4. На странице **выбора источника данных** выберите параметр **импорта данных о проверяющей стороне, опубликованных в Интернете или локальной сети** , укажите URL-адрес метаданных Azure AD B2C и нажмите кнопку **Далее** .
5. На странице **Указание отображаемого имени** введите **отображаемое имя** . В поле **Примечания** введите описание для этого отношения доверия с проверяющей стороной и нажмите кнопку **Далее** .
6. На странице **Выбрать политику управления доступом** выберите политику и нажмите кнопку **Далее** .
7. На странице **Готовность для добавления отношения доверия** проверьте параметры, а затем нажмите кнопку **Далее** , чтобы сохранить сведения об отношениях доверия с проверяющей стороной.
8. На странице **Готово** щелкните **Закрыть** . При этом автоматически откроется диалоговое окно **Edit Claim Rules** (Изменение правил утверждений).
9. Выберите **Добавить правило** .
10. В разделе **Claim rule template** (Шаблон правила утверждения) выберите **Send LDAP attributes as claims** (Отправка атрибутов LDAP как утверждений).
11. Укажите **имя правила утверждения** . В поле **хранилище атрибутов** выберите **выбрать Active Directory** , добавьте следующие утверждения, а затем нажмите кнопку **Готово** и **ОК** .

    | Атрибут LDAP | Тип исходящего утверждения |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | электронная почта |
    | Display-Name | name |

    Обратите внимание, что эти имена не отображаются в раскрывающемся списке Тип исходящего утверждения. Их необходимо ввести вручную в. (Раскрывающийся список фактически доступен для редактирования).

12.  В зависимости от типа сертификата может потребоваться задать хэш-алгоритм. В окне свойств отношения доверия с проверяющей стороной (демоверсия B2C) перейдите на вкладку **Дополнительно** , измените **алгоритм SHA** на `SHA-256` и нажмите кнопку **ОК** .
13. В диспетчер сервера выберите **инструменты** , а затем выберите **AD FS управление** .
14. Выберите созданное отношение доверия проверяющей стороны, щелкните **Update from Federation Metadata** (Обновить с метаданных федерации), а затем выберите **Обновить** .

## <a name="create-an-azure-ad-b2c-application"></a>Создание приложения Azure AD B2C

Взаимодействие с Azure AD B2C осуществляется с помощью приложения, зарегистрированного в клиенте B2C. В этом разделе перечислены необязательные действия, которые можно выполнить, чтобы создать тестовое приложение, если вы его еще не создали.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Обновление и тестирование файла проверяющей стороны

Обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте ее. Например, переименуйте ее в *SignUpSignInADFS.xml* .
2. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy** , указав уникальное значение. Например, `SignUpSignInADFS`.
3. Обновите значение **PublicPolicyUri** , указав URI для политики. Например: `http://contoso.com/B2C_1A_signup_signin_adfs`.
4. Обновите значение атрибута **ReferenceId** для элемента **DefaultUserJourney** , чтобы он соответствовал идентификатору созданного вами нового пути взаимодействия пользователя (SignUpSignInADFS).
5. Сохраните изменения, отправьте файл, а затем выберите в списке новую политику.
6. Убедитесь, что созданное приложение Azure AD B2C выбрано в поле **Выберите приложение** , а затем протестируйте его, щелкнув **Запустить сейчас** .

## <a name="troubleshooting-ad-fs-service"></a>Устранение неполадок службы AD FS  

AD FS настроен для использования журнала приложений Windows. При возникновении проблем с настройкой AD FS в качестве поставщика удостоверений SAML с помощью пользовательских политик в Azure AD B2C может потребоваться проверить журнал AD FS событий:

1. На **панели поиска** Windows введите **Просмотр событий** , а затем выберите классическое приложение **Просмотр событий** .
1. Чтобы просмотреть журнал с другого компьютера, щелкните правой кнопкой мыши элемент **Просмотр событий (локальных)** . Выберите пункт **Подключение к другому компьютеру** и заполните поля в диалоговом окне **Выбор компьютера** .
1. В **средстве просмотра событий** откройте **журналы приложений и служб** .
1. Выберите **AD FS** , а затем щелкните **Администратор** . 
1. Чтобы просмотреть дополнительные сведения о событии, дважды щелкните событие.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>Запрос SAML не подписан с ожидаемым событием алгоритма подписи

Эта ошибка означает, что запрос SAML, отправленный Azure AD B2C, не подписан с использованием ожидаемого алгоритма подписи, настроенного в AD FS. Например, запрос SAML подписан с помощью алгоритма подписи `rsa-sha256` , но ожидаемым алгоритмом подписи является `rsa-sha1` . Чтобы устранить эту проблему, убедитесь, что для обеих Azure AD B2C и AD FS настроен один и тот же алгоритм подписи.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Вариант 1. Задание алгоритма подписи в Azure AD B2C  

Вы можете настроить способ подписи запроса SAML в Azure AD B2C. Метаданные [ксмлсигнатуреалгорисм](saml-identity-provider-technical-profile.md#metadata) контролирует значение `SigAlg` параметра (строка запроса или параметр POST) в запросе SAML. В следующем примере настраивается Azure AD B2C для использования `rsa-sha256` алгоритма подписи.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Вариант 2. Задание алгоритма подписи в AD FS 

Кроме того, можно настроить ожидаемый алгоритм подписи запроса SAML в AD FS.

1. В диспетчер сервера выберите **инструменты** , а затем выберите **AD FS управление** .
1. Выберите ранее созданное **отношение доверия с проверяющей стороной** .
1. Выберите **Свойства** , а затем выберите **Предварительные**
1. Настройте **алгоритм SHA** и нажмите кнопку **ОК** , чтобы сохранить изменения.

