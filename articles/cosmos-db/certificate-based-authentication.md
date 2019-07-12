---
title: Аутентификация Azure Active Directory на основе сертификатов с помощью Azure Cosmos DB
description: Узнайте, как выполнить настройку удостоверения Azure AD для проверки подлинности на основе сертификатов к ключам доступа из Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: cc39cc09259c1ae681e1fee070777575e2788323
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827850"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Проверка подлинности на основе сертификата для удостоверения Azure AD к ключам доступа из учетной записи Azure Cosmos DB

Проверка подлинности на основе сертификата позволяет вашему приложению клиента должны проходить проверку подлинности с помощью Azure Active Directory (Azure AD) с помощью сертификата клиента. Можно выполнить проверку подлинности на основе сертификата на компьютере, где требуется удостоверение, например на локальном компьютере или виртуальной машине в Azure. Приложение затем может прочитать ключи Azure Cosmos DB без необходимости ключи непосредственно в приложении. В этой статье описывается создание примера приложения Azure AD, настройте его для проверки подлинности на основе сертификата, войдите в Azure с помощью нового удостоверения приложения, и затем извлекает ключи из учетной записи Azure Cosmos. В этой статье используется Azure PowerShell для настройки удостоверений и предоставляет C# пример приложения, который проверяет подлинность и обращается к ключи из учетной записи Azure Cosmos.  

## <a name="prerequisites"></a>Предварительные требования

* Установка [последней версии](/powershell/azure/install-az-ps) из Azure PowerShell.

* Если у вас еще нет [подписки Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.

## <a name="register-an-app-in-azure-ad"></a>Регистрация приложения в Azure AD

На этом шаге вы будете регистрировать пример веб-приложения на учетную запись Azure AD. Это приложение позже используется для чтения ключи из учетной записи Azure Cosmos DB. Следуйте инструкциям ниже для регистрации приложения: 

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Откройте Azure **Active Directory** области перейдите на панель регистрации приложения и выберите **Регистрация нового**. 

   ![Регистрация нового приложения в Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Заливка **зарегистрировать приложение** формы со следующими параметрами:  

   * **Имя** — укажите имя для приложения, это может быть любое имя, например «sampleApp».
   * **Поддерживаемые типы учетных записей** — Выбор **учетных записей в этой организации только каталог (каталог по умолчанию)** позволяет ресурсам в текущем каталоге доступ к этому приложению. 
   * **URL-адрес перенаправления** — выберите приложение типа **Web** и укажите URL-адрес, где размещается приложение, это может быть любой URL-адрес. Например, можно ввести URL-адрес теста например `https://sampleApp.com` допустимо даже если приложение не существует.

   ![Регистрация примера веб-приложения](./media/certificate-based-authentication/register-sample-web-app.png)

1. Выберите **зарегистрировать** после заполнения формы.

1. После регистрации приложения, запомните или запишите **идентификатор Application(client)** и **идентификатор объекта**, будут использовать эти данные в следующих шагах. 

   ![Получить идентификаторы приложения и объекта](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Установить модуль Azure AD

На этом шаге будет установить модуль Azure AD PowerShell. Этот модуль необходимо получить идентификатор приложения, зарегистрированного на предыдущем шаге и свяжите самозаверяющий сертификат к этому приложению. 

1. Откройте Windows PowerShell ISE с правами администратора. Если вы еще не сделали, установите модуль AZ PowerShell и подключитесь к своей подписке. Если у вас несколько подписок, можно задать контекст текущей подписки, как показано в следующих командах:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Установите и импортируйте [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) модуля

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Вход в Azure AD

Войдите в Azure AD, где вы зарегистрировали приложение. Выполните команду Connect-AzureAD, чтобы войти в учетную запись, введите свои учетные данные учетной записи Azure во всплывающем окне. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата

Откройте другой экземпляр интегрированной среды сценариев Windows PowerShell и выполните следующие команды для создания самозаверяющего сертификата и прочитать ключ, связанный с сертификатом:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Создание учетных данных на основе сертификата 

Затем выполните следующие команды, чтобы получить идентификатор объекта приложения и создать учетные данные на основе сертификатов. В этом примере задается сертификат, срок действия истекает через год, можно разместить его на любую дату окончания требуется.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Приведенная выше команда приводит результат, аналогичный приведенному на снимке экрана ниже:

![Результат создания учетных данных на основе сертификата](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Настройка учетной записи Azure Cosmos для использования нового удостоверения

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Перейдите к учетной записи Azure Cosmos, откройте **управление доступом (IAM)** колонке.

1. Выберите **добавить** и **добавить назначение роли**. Добавление sampleApp, созданный на предыдущем шаге, с **участник** роли, как показано на следующем снимке экрана:

   ![Настройка учетной записи Azure Cosmos для использования нового удостоверения](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Выберите **Сохранить** после заполнения формы


## <a name="access-the-keys-from-powershell"></a>Доступ к ключам с помощью PowerShell

На этом шаге будут входить в Azure с помощью приложения и сертификат вы создали и доступ к ключам учетной записи Azure Cosmos. 

1. Изначально снимите учетные данные учетной записи Azure, которая использовалась для входа в учетную запись. Учетные данные можно удалить, выполнив следующую команду:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Затем проверьте, можно войти в портал Azure с помощью учетных данных приложения и доступ к ключам Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Предыдущая команда показывает первичный и вторичный главные ключи учетной записи Azure Cosmos. Можно просмотреть журнал активности вашей учетной записи Azure Cosmos, чтобы проверить успешное выполнение запроса get ключи и событие инициируется приложением «sampleApp». 
 
![Проверка вызова get ключи в Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Доступ к ключам из C# приложения 

Можно также проверить этот сценарий с доступа к ключам из C# приложения. Следующие C# консольное приложение, с доступом к ключам Azure Cosmos DB с помощью приложения, зарегистрированного в Active Directory. Не забудьте обновить tenantId, clientID, certName, имя группы ресурсов, идентификатор подписки, имя сведений, учетной записи Azure Cosmos, перед выполнением кода. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Этот скрипт выводит первичный и вторичный главные ключи, как показано на следующем снимке экрана:

![выходные данные приложения c#](./media/certificate-based-authentication/csharp-application-output.png)

Как и в предыдущем разделе, можно просмотреть журнал активности вашей учетной записи Azure Cosmos для проверки того, что ключи запроса get событие инициируется приложением «sampleApp». 


## <a name="next-steps"></a>Следующие шаги

* [Обеспечение безопасности ключей Azure Cosmos с помощью хранилища ключей Azure](access-secrets-from-keyvault.md)

* [Атрибуты безопасности для Azure Cosmos DB](cosmos-db-security-attributes.md)
