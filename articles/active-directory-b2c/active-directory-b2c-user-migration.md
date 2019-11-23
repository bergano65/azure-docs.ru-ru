---
title: User migration approaches in Azure Active Directory B2C
description: Discusses both core and advanced concepts on user migration using the Azure AD Graph API, and optionally using Azure AD B2C custom policies.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9c01e22cfa96321994c16df6b61a52ebd4137549
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322920"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>Migrate users to Azure Active Directory B2C

When you migrate your identity provider to Azure Active Directory B2C (Azure AD B2C), you might also need to migrate the user accounts. В этой статье описывается процедура переноса имеющихся учетных записей пользователей из любого поставщика удостоверений в Azure AD B2C. The article is not meant to be prescriptive, but rather, it describes a few scenarios. За пригодность каждого подхода отвечает разработчик.

## <a name="user-migration-flows"></a>Потоки миграции пользователей

With Azure AD B2C, you can migrate users through the [Azure AD Graph API][B2C-GraphQuickStart]. Процесс миграции пользователей подразделяется на два потока.

- **Действия, выполняемые перед миграцией.** Этот поток применяется, когда у вас есть свободный доступ к учетным данным пользователя (имя пользователя и пароль) или учетные данные зашифрованы, но их можно расшифровать. Этот процесс включает в себя считывание пользователей старого поставщика удостоверений и создание учетных записей в каталоге Azure AD B2C.

- **Действия, выполняемые перед миграцией, и сброс пароля.** Этот поток применяется, когда пароль пользователя недоступен. Пример.
  - Пароль сохраняется в формате хэша.
  - Пароль хранится в недоступном поставщике удостоверений. Старый поставщик удостоверений проверяет учетные данные пользователя путем вызова веб-службы.

В обоих случаях сначала требуется запустить процесс, выполняемый перед миграцией, считать пользователей из старого поставщика удостоверений, а затем создать учетные записи в каталоге Azure AD B2C. If you don't have the password, you create the account by using a password that's generated randomly. Затем предложите пользователям изменить их пароли. Или Azure AD B2C просит пользователя сбросить пароль при первой регистрации.

## <a name="password-policy"></a>Политика паролей

Политика паролей Azure AD B2C (для локальных учетных записей) основана на политике Azure AD. The Azure AD B2C sign-up or sign-in and password reset policies use the "strong" password strength and don't expire any passwords. Дополнительные сведения см. в статье [Политики и ограничения для паролей в Azure Active Directory][AD-PasswordPolicies].

Если у переносимых учетных записей надежность пароля слабее, чем [высокая надежность пароля, обеспечиваемая Azure AD B2C][AD-PasswordPolicies], можно отключить требование надежного пароля. Чтобы изменить политику паролей по умолчанию, задайте для свойства `passwordPolicies` значение `DisableStrongPassword`. Например, можно изменить запрос создания пользователя следующим образом:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Шаг 1. Миграция пользователей с помощью Azure AD Graph API

Вы создадите учетную запись пользователя Azure AD B2C с помощью API Graph (с паролем или со случайным паролем). В этом разделе описывается процесс создания учетных записей пользователей в каталоге Azure AD B2C с помощью API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Шаг 1.1. Регистрация приложения в клиенте

Для взаимодействия с API Graph сначала необходимо иметь учетную запись службы с правами администратора. In Azure AD, you register an application and enable write access to the directory. The application credentials are the **Application ID** and **Application Secret**. Приложение вызывает API Graph, действуя само по себе, а не под именем конкретного пользователя.

First, register an application that you can use for management tasks like user migration.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Step 1.2: Grant administrative permission to your application

Next, grant the application the Azure AD Graph API permissions required for writing to the directory.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Step 1.3: Create the application secret

Create a client secret (key) for use by the user migration application that you configure in a later step.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Now you have an application with permissions to create, read, and update users in your Azure AD B2C tenant.

### <a name="step-14-optional-environment-cleanup"></a>Шаг 1.4. Очистка среды (необязательно)

The *Read and write directory data* permission does *not* include the right to delete users. Чтобы предоставить приложению эту возможность (для очистки среды), необходимо выполнить дополнительный шаг. Этот шаг заключается в запуске PowerShell для установки разрешений администратора учетной записи пользователя. В противном случае можно перейти к следующему разделу.

> [!IMPORTANT]
> Необходимо использовать учетную запись администратора клиента B2C, которая является *локальной* по отношению к клиенту B2C. The account name syntax is *admin\@contosob2c.onmicrosoft.com*.

In this PowerShell script, which requires the [Azure AD PowerShell V2 module][AD-Powershell], do the following:

1. Подключитесь к веб-службе. Для этого выполните командлет `Connect-AzureAD` в командной строке Windows PowerShell и предоставьте свои учетные данные.

1. Используйте **идентификатор приложения** для назначения приложению роли администратора учетной записи пользователя. Идентификаторы этих ролей известны, поэтому можно просто ввести **идентификатор приложения** в сценарий.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Измените значение `$AppId` на свой **идентификатор приложения** Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Шаг 2. Пример приложения перед началом миграции

You can find the pre-migration code sample in the community-maintained `azure-ad-b2c/user-migration` GitHub repository:

[azure-ad-b2c/user-migration/pre-migration][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Шаг 2.1. Изменение файла данных миграции

Пример приложения использует JSON-файл, содержащий данные фиктивных пользователей. После успешного запуска примера можно изменить код для получения данных из собственной базы данных. Вы также можете экспортировать профиль пользователя в JSON-файл и настроить приложение для использования этого файла.

Чтобы изменить JSON-файл, откройте решение Visual Studio `AADB2C.UserMigration.sln`. В проекте `AADB2C.UserMigration` откройте файл `UsersData.json`.

![Portion of UsersData.json file showing JSON blocks of two users](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Как видите, файл содержит список сущностей пользователей. Каждая сущность пользователя имеет следующие свойства.

- email
- displayName
- firstName
- lastName
- пароль (может быть пустым).

> [!NOTE]
> Во время компиляции Visual Studio копирует файл в каталог `bin`.

### <a name="step-22-configure-the-application-settings"></a>Шаг 2.2. Настройка параметров приложения

В проекте `AADB2C.UserMigration` откройте файл *App.config*. Замените следующие параметры приложения собственными значениями.

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Использование строки подключения таблицы Azure описано в следующих разделах.
> - В качестве имени вашего клиента B2C используется домен, указанный при создании клиента. Это имя отображается на портале Azure. Имя клиента обычно заканчивается суффиксом *.onmicrosoft.com* (например, *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Шаг 2.3. Запуск процесса подготовки к миграции

Щелкните решение `AADB2C.UserMigration` правой кнопкой мыши, а затем еще раз создайте пример кода. Если вам удастся выполнить это действие, в каталоге `AADB2C.UserMigration\bin\Debug\net461` появится исполняемый файл `UserMigration.exe`. Чтобы запустить процесс миграции, используйте один из следующих параметров командной строки:

- Чтобы **перенести пользователей с паролями**, выполните команду `UserMigration.exe 1`.

- Чтобы **перенести пользователей со случайными паролями**, выполните команду `UserMigration.exe 2`. Эта операция также создает сущность таблицы Azure. Далее настройте политику для вызова службы REST API. Служба использует таблицу Azure для отслеживания процесса миграции и управления им.

![Command Prompt window showing output of UserMigration.exe command](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Шаг 2.4. Проверка процесса подготовки к миграции

Для проверки миграции используйте один из следующих двух методов:

- Чтобы найти пользователя по отображаемому имени, воспользуйтесь порталом Azure.

   1. Open **Azure AD B2C**, and then select **Users**.
   1. В поле поиска введите отображаемое имя пользователя и просмотрите профиль пользователя.

- To retrieve a user by sign-in email address, use the sample application:

   1. Выполните следующую команду:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > Кроме того, вы можете получить данные пользователя по отображаемому имени, выполнив следующую команду: `UserMigration.exe 4 "<Display name>"`.

   1. Откройте файл UserProfile.json в редакторе JSON, чтобы просмотреть сведения о пользователе.

      ![UserProfile.json file open in the Visual Studio Code editor](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Шаг 2.5. Очистка среды (необязательно)

Если вы хотите очистить клиент Azure AD и удалить пользователей из каталога Azure AD, выполните команду `UserMigration.exe 5`.

> [!NOTE]
> * Чтобы очистить клиент, настройте разрешения администратора учетной записи пользователя для приложения.
> * Пример приложения для миграции удаляет всех пользователей, указанных в файле JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Шаг 2.6. Выполнение входа для перенесенных пользователей (с паролем)

После выполнения процесса подготовки к миграции с паролями пользователей учетные записи готовы к использованию и пользователи могут выполнять вход в приложение с помощью Azure AD B2C. При отсутствии доступа к паролям пользователей перейдите к следующему разделу.

## <a name="step-3-help-users-reset-their-password"></a>Шаг 3. Помощь пользователям в сбросе пароля

Если вы переносите пользователей со случайными паролями, им необходимо сбросить пароль. Чтобы помочь им сбросить пароль, отправьте приветственное сообщение электронной почты со ссылкой для сброса пароля.

To get the link to your password reset policy, follow these steps. This procedure assumes you've previously created a password reset [custom policy](active-directory-b2c-get-started-custom.md).

1. Select the directory containing your Azure AD B2C tenant by using the **Directory + subscription** filter in the upper-right section of the [Azure portal](https://portal.azure.com).
1. Select **Azure AD B2C** in the left-hand menu (or from within **All services**).
1. Under **Policies**, select **Identity Experience Framework**.
1. Select your password reset policy. For example, *B2C_1A_PasswordReset*.
1. Select your application in the **Select application** drop-down.

    > [!NOTE]
    > **Run now** requires at least one application to be registered in your tenant. To learn how to register applications, see [Tutorial: Register an application in Azure Active Directory B2C][B2C-AppRegister].

1. Copy the URL shown in the **Run now endpoint** text box, and then send it to your users.

    ![Password reset policy page with Run now endpoint highlighted](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Шаг 4. Изменение политики для проверки и установки состояния миграции пользователя (необязательно)

> [!NOTE]
> Чтобы проверить и изменить состояние миграции пользователей, необходимо использовать настраиваемую политику. The set-up instructions from [Get started with custom policies][B2C-GetStartedCustom] must be completed.

При попытке пользователя войти без сброса пароля в первый раз политика должна возвращать дружественное сообщение об ошибке. Пример.

> *Your password has expired. To reset it, select the Reset Password link.*

Этот необязательный шаг требует использования Azure AD B2C с пользовательскими политиками, как описано в статье [Azure Active Directory B2C: начало работы с настраиваемыми политиками][B2C-GetStartedCustom].

В этом разделе измените политику для проверки состояния миграции пользователя при выполнении входа. Если пользователь не изменил пароль, отправьте сообщение об ошибке HTTP 409. Пользователю будет предложено щелкнуть ссылку **Forgot your password?** (Забыли пароль?).

Чтобы отслеживать изменение пароля, используйте таблицу Azure. При запуске процесса подготовки к миграции с параметром командной строки `2` вы создаете сущность пользователя в таблице Azure. Ваша служба выполняет следующие действия:

- Когда выполняется вход, политика Azure AD B2C вызывает службу миграции RESTful, отправляя сообщение электронной почты в виде входного утверждения. Служба ищет адрес электронной почты в таблице Azure. Если адрес существует, служба выводит сообщение: *You must change password* (Требуется изменить пароль).

- Когда пользователь успешно изменит пароль, удалите сущность из таблицы Azure.

> [!NOTE]
> Мы используем таблицу Azure для упрощения примера. Состояние миграции можно хранить в любой базе данных или как пользовательское свойство в учетной записи Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1. Обновление параметров приложения

1. Чтобы протестировать демоверсию RESTful API, откройте `AADB2C.UserMigration.sln` в Visual Studio.
1. In the `AADB2C.UserMigration.API` project, open the *Web.config* file. Замените параметр настроенным на [шаге 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Шаг 4.2. Развертывание веб-приложения в службе приложений Azure

Щелкните правой кнопкой мыши `AADB2C.UserMigration.API` в обозревателе решений и выберите "Опубликовать...". Следуйте инструкциям по публикации в службе приложений Azure. Дополнительные сведения см. в статье [Развертывание локального репозитория Git в службе приложений Azure][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Шаг 4.3. Добавление технического профиля и его проверки в политику

1. В обозревателе решений разверните узел "Элементы решения" и откройте файл политики *TrustFrameworkExtensions.xml*.
1. Измените поля `TenantId`, `PublicPolicyUri` и `<TenantId>` с `yourtenant.onmicrosoft.com` именем клиента.
1. Under the `<TechnicalProfile Id="login-NonInteractive">` element, replace all instances of `ProxyIdentityExperienceFrameworkAppId` and `IdentityExperienceFrameworkAppId` with the Application IDs configured in [Getting started with custom policies][B2C-GetStartedCustom].
1. В узле `<ClaimsProviders>` найдите следующий фрагмент кода XML. Измените значение `ServiceUrl` для указания URL-адреса службы приложений Azure.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Предыдущий технический профиль определяет одно входящее утверждение: `signInName` (отправить по электронной почте). Когда выполняется вход, утверждение отправляется в конечную точку RESTful.

After you define the technical profile for your RESTful API, configure the existing `SelfAsserted-LocalAccountSignin-Email` technical profile to additionally call your REST API technical profile by overriding it within your *TrustFrameworkExtensions.xml* file:

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

Then, change the `Id` of the `LocalAccountSignIn` technical profile to `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Шаг 4.4. Отправка политики в клиент

1. На [портале Azure][Portal] переключитесь в [контекст клиента Azure AD B2C][B2C-NavContext] и выберите **Azure AD B2C**.
1. Выберите **Инфраструктура процедур идентификации**.
1. Выберите **Все политики**.
1. Щелкните **Отправить политику**.
1. Установите флажок **Перезаписать политику, если она существует**.
1. Отправьте файл *TrustFrameworkExtensions.xml* и немного подождите, чтобы удостовериться, что он прошел проверку.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Шаг 4.5. Тестирование пользовательской политики с помощью команды Run Now (Запустить сейчас)

1. Select **Azure AD B2C**, and then select **Identity Experience Framework**.
1. Откройте *B2C_1A_signup_signin*, отправленную вами пользовательскую политику проверяющей стороны, а затем выберите **Запустить сейчас**.
1. Enter the credentials of one of the migrated users, and then select **Sign In**. В REST API должно отобразиться следующее сообщение об ошибке:

    ![Sign-in Sign-up page showing the change password error message](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Шаг 4.6. Устранение неполадок REST API (необязательно)

Вы можете просматривать и отслеживать данные ведения журналов в режиме реального времени.

1. В меню параметров приложения RESTful в разделе **Мониторинг** выберите **Журналы диагностики**.
1. Установите для параметра **Вход в приложения (файловая система)** значение **Вкл.**
1. Установите для параметра **Уровень** значение **Verbose** (Подробный).
1. Нажмите кнопку **Сохранить**.

    ![Diagnostics logs configuration page in Azure portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. В меню **параметров** выберите **Поток журналов**.
1. Проверьте выходные данные RESTful API.

> [!IMPORTANT]
> Журналы диагностики следует использовать только во время разработки и тестирования. Выходные данные RESTful API могут содержать конфиденциальные сведения, которые не должны быть раскрыты в рабочей среде.

## <a name="optional-download-the-complete-policy-files"></a>Скачивание полного текста файлов политики (необязательно)

After you complete the [Get started with custom policies][B2C-GetStartedCustom] walk-through, we recommend that you build your scenario by using your own custom policy files. For your reference, we have provided [sample policy files][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
