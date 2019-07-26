---
title: Azure Active Directory проверки подлинности на основе сертификатов с помощью Azure Cosmos DB
description: Узнайте, как настроить удостоверение Azure AD для проверки подлинности на основе сертификата для доступа к ключам из Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 9d06cf334f08ba6ec9c47450d21d33733900ebe5
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356577"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Проверка подлинности на основе сертификата для удостоверения Azure AD для доступа к ключам из учетной записи Azure Cosmos DB

Проверка подлинности на основе сертификатов обеспечивает проверку подлинности клиентского приложения с помощью Azure Active Directory (Azure AD) с сертификатом клиента. Проверку подлинности на основе сертификатов можно выполнять на компьютере, где требуется удостоверение, например локальный компьютер или виртуальная машина в Azure. После этого приложение сможет считывать ключи Azure Cosmos DB без ключей непосредственно в приложении. В этой статье описывается, как создать пример приложения Azure AD, настроить его для проверки подлинности на основе сертификата, войти в Azure с помощью нового удостоверения приложения, а затем получить ключи из учетной записи Azure Cosmos. В этой статье для настройки удостоверений используется Azure PowerShell и предоставляется C# пример приложения, которое проверяет подлинность ключей и обращается к ключам из учетной записи Azure Cosmos.  

## <a name="prerequisites"></a>Предварительные требования

* Установите [последнюю версию](/powershell/azure/install-az-ps) Azure PowerShell.

* Если у вас еще нет [подписки Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.

## <a name="register-an-app-in-azure-ad"></a>Регистрация приложения в Azure AD

На этом шаге вы зарегистрируете пример веб-приложения в своей учетной записи Azure AD. Это приложение позже используется для чтения ключей из учетной записи Azure Cosmos DB. Чтобы зарегистрировать приложение, выполните следующие действия. 

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Откройте панель **Active Directory** Azure, перейдите в область регистрация приложений и выберите **Новая регистрация**. 

   ![Регистрация нового приложения в Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Заполните форму **Регистрация приложения** со следующими сведениями:  

   * **Имя** — укажите имя приложения, оно может иметь любое имя, например "sampleApp".
   * **Поддерживаемые типы учетных записей** — выберите **учетные записи в этом каталоге организации (каталог по умолчанию)** , чтобы разрешить ресурсам в текущем каталоге доступ к этому приложению. 
   * **URL-адрес перенаправления** — выберите приложение типа **Web** и укажите URL-адрес, по которому размещено ваше приложение. это может быть любой URL-адрес. В этом примере можно указать тестовый URL-адрес, например `https://sampleApp.com` , даже если приложение не существует.

   ![Регистрация примера веб-приложения](./media/certificate-based-authentication/register-sample-web-app.png)

1. Нажмите кнопку **зарегистрировать** после заполнения формы.

1. После регистрации приложения запишите **идентификатор приложения (клиента)** и **идентификатор объекта**. Эти сведения будут использоваться в следующих шагах. 

   ![Получение идентификаторов приложения и объекта](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Установка модуля AzureAD

На этом шаге будет установлен модуль Azure AD PowerShell. Этот модуль необходим для получения идентификатора приложения, зарегистрированного на предыдущем шаге, и связывания самозаверяющего сертификата с этим приложением. 

1. Откройте интегрированную среду сценариев Windows PowerShell с правами администратора. Установите модуль AZ PowerShell и подключитесь к подписке, если вы еще этого не сделали. Если у вас несколько подписок, можно задать контекст текущей подписки, как показано в следующих командах:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Установка и импорт модуля [AzureAD](/powershell/module/azuread/?view=azureadps-2.0)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Войдите в Azure AD.

Войдите в Azure AD, где вы зарегистрировали приложение. Используйте команду Connect-AzureAD для входа в учетную запись, введите учетные данные учетной записи Azure во всплывающем окне. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата

Откройте другой экземпляр интегрированной среды сценариев Windows PowerShell и выполните следующие команды, чтобы создать самозаверяющий сертификат и прочитать ключ, связанный с сертификатом:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Создание учетных данных на основе сертификата 

Затем выполните следующие команды, чтобы получить идентификатор объекта приложения и создать учетные данные на основе сертификата. В этом примере мы настроили срок действия сертификата через год, вы можете задать для него любую необходимую конечную дату.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Приведенная выше команда приводит к результату, как показано на снимке экрана ниже:

![Выходные данные создания учетных данных на основе сертификата](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Настройка учетной записи Azure Cosmos для использования нового удостоверения

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Перейдите к своей учетной записи Azure Cosmos, откройте колонку **управления доступом (IAM)** .

1. Выберите **Добавить** и **добавить назначение ролей**. Добавьте sampleApp, созданный на предыдущем шаге, с ролью **участника** , как показано на следующем снимке экрана:

   ![Настройка учетной записи Azure Cosmos для использования нового удостоверения](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Нажмите кнопку **сохранить** после заполнения формы.


## <a name="access-the-keys-from-powershell"></a>Доступ к ключам из PowerShell

На этом шаге вы будете входить в Azure с помощью приложения и созданного сертификата, а также получите доступ к ключам учетной записи Azure Cosmos. 

1. Сначала очистите учетные данные учетной записи Azure, которые использовались для входа в учетную запись. Удалить учетные данные можно с помощью следующей команды:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Затем убедитесь, что вы можете войти в портал Azure с помощью учетных данных приложения и получить доступ к Azure Cosmos DBным ключам:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Предыдущая команда отобразит первичный и вторичный главные ключи учетной записи Azure Cosmos. Вы можете просмотреть журнал действий учетной записи Azure Cosmos, чтобы убедиться, что запрос на получение ключей выполнен, а событие инициировано приложением "sampleApp". 
 
![Проверка вызова Get Keys в Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Доступ к ключам из C# приложения 

Этот сценарий также можно проверить путем доступа к ключам из C# приложения. Следующее C# консольное приложение, которое может получить доступ к ключам Azure Cosmos DB с помощью приложения, зарегистрированного в Active Directory. Перед запуском кода обязательно обновите ИД клиента, clientID, certName, имя группы ресурсов, идентификатор подписки, имя учетной записи Azure Cosmos. 

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
    }
}
```

Этот сценарий выводит первичные и вторичные главные ключи, как показано на следующем снимке экрана:

![вывод приложения CSharp](./media/certificate-based-authentication/csharp-application-output.png)

Как и в предыдущем разделе, журнал действий учетной записи Azure Cosmos можно просмотреть, чтобы убедиться, что событие запроса GET Keys инициировано приложением "sampleApp". 


## <a name="next-steps"></a>Следующие шаги

* [Защита ключей Azure Cosmos с помощью Azure Key Vault](access-secrets-from-keyvault.md)

* [Атрибуты безопасности для Azure Cosmos DB](cosmos-db-security-attributes.md)
