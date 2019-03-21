---
title: Подключение ActiveDirectoryInteractive к SQL | Документация Майкрософт
description: Пример кода C# с пояснениями для подключения к службе "База данных SQL Azure" с использованием режима SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: 5f089148bafbab21721c83c0d4b6977a7db86320
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834623"
---
# <a name="connect-to-azure-sql-database-with-active-directory-mfa"></a>Подключение к Базе данных SQL Azure с помощью многофакторной проверки подлинности Active Directory

В этой статье содержится программа C#, которая подключается к службе "База данных SQL Microsoft Azure". В программе используется интерактивный режим проверки подлинности, который поддерживает [многофакторную проверку подлинности (MFA) Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Дополнительные сведения о поддержке MFA для средств SQL см. в статье [Azure Active Directory support in SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory) (Поддержка Azure Active Directory в SQL Server Data Tools (SSDT)).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Многофакторная проверка подлинности для Базы данных SQL Azure

Начиная с .NET Framework версии 4.7.2, перечисление [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) имеет новое значение — `ActiveDirectoryInteractive`. В клиентской программе C# это значение перечисления указывает системе использовать интерактивный режим Azure AD, поддерживающий многофакторную проверку подлинности для подключения к службе "База данных SQL Azure". Пользователь, который затем запускает программу, увидит следующие диалоговые окна:

* Диалоговое окно с отображением имени пользователя Azure AD, в котором запрашивается пароль пользователя.

   Если домен пользователя включен в федерацию с Azure AD, это диалоговое окно не отображается, потому что в таком случае пароль не требуется.

   Если политика Azure AD вводит многофакторную проверку подлинности для пользователя, отобразятся два следующих диалоговых окна.

* Первый раз, когда пользователь проходит MFA, система отображает диалоговое окно, запрашивающее номер мобильного телефона для отправки текстовых сообщений. Каждое сообщение содержит *код проверки*, который пользователь должен ввести в следующем диалоговом окне.

* Диалоговое окно, где запрашивается код проверки MFA, который система отправила на мобильный телефон.

Сведения о том, как настроить Azure AD таким образом, чтоб требовалась аутентификация MFA, см. в статье [Getting started with Azure Multi-Factor Authentication in the cloud](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud) (Начало работы с MFA Azure в облаке).

Снимки экрана этих диалоговых окон см. в статье [Configure multi-factor authentication for SQL Server Management Studio and Azure AD](sql-database-ssms-mfa-authentication-configure.md) (Настройка MFA для SQL Server Management Studio и Azure AD).

> [!TIP]
> Вы можете искать API .NET Framework на странице **обозревателя .NET API**.
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Вы также можете выполнять поиск напрямую с помощью дополнительного параметра **?term=&lt;искомое значение&gt;**:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>Настройка приложения C# на портале Azure

Перед началом работы вам необходимо убедиться, что [сервер службы "База данных SQL Azure"](sql-database-get-started-portal.md) создан и доступен.

### <a name="register-your-app-and-set-permissions"></a>Регистрация приложения и установка разрешений


Чтобы использовать проверку подлинности Azure AD, ваша программа C# должна быть зарегистрирована как приложение Azure AD. Чтобы зарегистрировать приложение, необходимо быть администратором Azure AD или пользователем с назначенной ролью *разработчика приложений* Azure AD. Дополнительные сведения по назначению ролей см. в статье [Назначение ролей администратора и других ролей пользователям с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

 По завершении регистрации приложения создается и отображается **идентификатор приложения**. Для возможности подключения программа должна включать этот идентификатор.


Чтобы зарегистрировать приложение и задать для него необходимые разрешения:

1. Портал Azure > **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**

    ![Регистрация приложения](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    После создания регистрации приложения создается и отображается значение **идентификатора приложения**.

    ![Отображение идентификатора приложения](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **Зарегистрированное приложение** > **Параметры** > **Требуемые разрешения** > **Добавить**

    ![Параметры разрешений для зарегистрированного приложения](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **Требуемые разрешения** > **Добавить** > **Select an API** (Выбор API) > **База данных SQL Azure**

    ![Добавление доступа через API для службы "База данных SQL Azure"](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. **Доступ к API** > **Выберите разрешения** > **Делегированные разрешения**

    ![Делегирование разрешений API для службы "База данных SQL Azure"](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Определение администратора Azure AD для сервера службы "База данных SQL"


Чтобы ваша программа C# выполнялась, администратору сервера SQL Azure необходимо присвоить роль администратора Azure AD для сервера SQL Azure. 

 * **SQL Server** > **Администратор Active Directory** > **Задать администратора**

Дополнительные сведения об администраторах и пользователях Azure AD для службы "База данных SQL Azure" см. на снимках экрана в статье [Configure and manage Azure Active Directory authentication with SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) (Настройка и управление аутентификацией Azure Active Directory с использованием службы "База данных SQL"), раздел **Provision an Azure Active Directory administrator for your Azure SQL Database server** (Подготовка администратора Azure Active Directory для сервера службы "База данных SQL Azure").

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Добавление пользователя без прав администратора для конкретной базы данных (необязательно)

Администратор Azure AD для сервера Базы данных SQL может запустить пример программы C#. Пользователь Azure AD может запустить программу, если она находится в базе данных. Добавить пользователя может администратор SQL Azure AD или пользователь Azure AD, который уже существует в базе данных и имеет на нее разрешение `ALTER ANY USER`.

Вы можете добавить пользователя в базу данных с помощью команды SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql). Например, `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Дополнительные сведения см. в статье об [аутентификации Azure Active Directory с помощью службы "База данных SQL Azure", Управляемого экземпляра или хранилища данных SQL](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Новое значение перечисления проверки подлинности

В примере C# используется пространство имен [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient). Особый интерес для многофакторной проверки подлинности представляет перечисление `SqlAuthenticationMethod`, которое имеет следующие значения:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Используйте это значение с именем пользователя Azure AD для реализации многофакторной проверки подлинности. Значение подробно описано в этой статье. Оно обеспечивает интерактивное взаимодействие с помощью диалоговых окон для ввода пароля пользователя, а затем аутентификацию MFA, если она принудительно задана для этого пользователя. Это значение доступно начиная с .NET Framework версии 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Это значение следует использовать для *федеративной* учетной записи. При использовании федеративной учетной записи имя пользователя известно домену Windows. Этот метод проверки подлинности не поддерживает многофакторную проверку подлинности.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Используйте это значение для проверки подлинности, требующей имени пользователя и пароля Azure AD. Проверка подлинности выполняется в службе "База данных SQL Azure". Этот метод не поддерживает MFA.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Установка значений параметров C# с портала Azure

Для успешного выполнения программы C# необходимо назначить соответствующие значения статическим полям. Здесь отображаются поля с примерами значений. Кроме того, показаны расположения на портале Azure, откуда можно получить необходимые значения:

| Имя статического поля | Пример значения | Расположение на портале Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Серверы SQL Server** > **Фильтровать по имени** |
| AzureAD_UserID | «auser\@abc.onmicrosoft.com» | **Azure Active Directory** > **Пользователь** > **Новый гостевой пользователь** |
| Initial_DatabaseName | "myDatabase" | **Серверы SQL Server** > **Базы данных SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **Регистрация приложений** > **Поиск по имени** > **Идентификатор приложения** |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure Active Directory** > **Регистрация приложений** > **Поиск по имени** > *[регистрация вашего приложения]* > **Параметры** > **Значения RedirectURI**<br /><br />Для выполнения задач этой статьи для RedirectUri подойдет любое допустимое значение, которое не используется здесь. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio-ssms"></a>Проверка с помощью SQL Server Management Studio (SSMS)

Прежде чем выполнять программу C#, стоит проверить правильность установки и конфигураций в SSMS. Любой сбой программы C# можно затем свести к исходному коду.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Проверка IP-адресов брандмауэра службы "База данных SQL"

Запустите SSMS с того же компьютера в том же здании, где вы планируете выполнять программу C#. Для этого теста подходит любой режим **проверки подлинности**. Если что-либо указывает на то, что брандмауэр сервера баз данных не принимает ваш IP-адрес, ознакомьтесь с [правилами брандмауэра уровня сервера и уровня базы данных службы "База данных SQL Azure"](sql-database-firewall-configure.md), чтобы получить помощь.

### <a name="verify-azure-active-directory-mfa"></a>Проверка многофакторной проверки подлинности Azure Active Directory

Снова запустите SSMS, на этот раз для **аутентификации** должно быть установлено значение **Active Directory - Universal with MFA support** (Active Directory — универсальная с поддержкой MFA). Этот параметр требует SSMS версии 17.5 или более поздней.

Дополнительные сведения см. в разделе о [настройке многофакторной проверки подлинности для SSMS и Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Если вы являетесь гостевым пользователем в базе данных, необходимо также предоставить доменное имя Azure AD для базы данных: **Параметры** > **AD domain name or tenant ID** (Доменное имя AD или идентификатор клиента). Чтобы найти имя домена на портале Azure, выберите **Azure Active Directory** > **Имена личных доменов**. В программе C# можно не предоставлять доменное имя.

## <a name="c-code-example"></a>Пример кода C#

Пример программы C# основан на сборке библиотеки DLL [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory).

Чтобы установить этот пакет, в Visual Studio выберите **Проект** > **Управление пакетами NuGet**. Выполните поиск библиотеки **Microsoft.IdentityModel.Clients.ActiveDirectory** и установите ее.

### <a name="c-source-code"></a>Исходный код C#

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Фактические выходные данные тестовой программы на C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но все будущие разработки — для модуля Az.Sql. Для этих командлетов см. в разделе [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модуле AzureRm практически идентичны.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)