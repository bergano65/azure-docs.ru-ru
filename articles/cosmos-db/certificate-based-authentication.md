---
title: Проверка подлинности на основе сертификатов с помощью Azure Cosmos DB и Active Directory
description: Узнайте, как настроить иноезначение Azure AD для проверки подлинности сертификатов для доступа к ключам из Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365785"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Проверка подлинности на основе сертификатов для идентификации Azure AD для доступа к ключам из учетной записи Azure Cosmos DB

Проверка подлинности на основе сертификатов позволяет выполнить проверку подлинности клиентского приложения с помощью Azure Active Directory (Azure AD) на основе сертификата клиента. Вы можете выполнить проверку подлинности на основе сертификатов на компьютере, где требуется идентификатор, например на локальном компьютере или виртуальной машине в Azure. Затем приложение может читать dB-клавиши Azure Cosmos DB, не имея ключей непосредственно в приложении. В этой статье описывается, как создать образец приложения Azure AD, настроить его для проверки подлинности на основе сертификатов, войти в Azure с помощью нового идентификатора приложения, а затем получить ключи из учетной записи Azure Cosmos. В этой статье используется Azure PowerShell для настройки идентификаторов и предоставляется приложение для проверки подлинности и доступ к ключам из учетной записи Azure Cosmos.  

## <a name="prerequisites"></a>Предварительные требования

* Установите [последнюю версию](/powershell/azure/install-az-ps) Azure PowerShell.

* Если у вас нет [подписки Azure,](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) перед началом.

## <a name="register-an-app-in-azure-ad"></a>Регистрация приложения в Azure AD

На этом этапе вы зарегистрируете пример веб-приложения в своей учетной записи Azure AD. Это приложение позже используется для чтения ключей из вашей учетной записи Azure Cosmos DB. Используйте следующие шаги для регистрации приложения: 

1. Войти на [портал Azure](https://portal.azure.com/).

1. Откройте панель Active **Directory,** перейдите на панель **регистрации приложений** и выберите **новую регистрацию.** 

   ![Новая регистрация заявлений в Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Заполните форму **заявки** регистра следующими сведениями:  

   * **Имя** - Укажите имя для вашего приложения, это может быть любое имя, такое как "sampleApp".
   * **Поддерживаемые типы учетных записей** — Выберите **учетные записи только в этом каталоге организации (Каталог по умолчанию),** чтобы позволить ресурсам в текущем каталоге получить доступ к этому приложению. 
   * **Перенаправить URL** - Выберите приложение типа **Web** и предоставьте URL-, где размещается ваше приложение, это может быть любой URL. В этом примере можно предоставить `https://sampleApp.com` URL-адрес теста, например, если приложение не существует.

   ![Регистрация примера веб-приложения](./media/certificate-based-authentication/register-sample-web-app.png)

1. Выберите **Регистрацию** после заполнения формы.

1. После регистрации приложения сделайте примечание **идентификатора приложения (клиента)** и **идентификатора объекта,** вы будете использовать эти данные в следующих шагах. 

   ![Получение идентию приложений и идентиютелй объектов](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Установка модуля AzureAD

На этом этапе вы установите модуль Azure AD PowerShell. Этот модуль необходим для получения идентификатора приложения, зарегистрированного на предыдущем этапе, и ассоциировать с этим приложением сертификат, подписанный самостоятельно. 

1. Открыть Windows PowerShell ISE с правами администратора. Если вы еще не сделали это, установите модуль A'PowerShell и подключитесь к подписке. Если у вас несколько подписок, можно настроить контекст текущей подписки, показанный в следующих командах:

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

## <a name="sign-into-your-azure-ad"></a>Войти в aD Azure

Вопийте в Azure AD, где вы зарегистрировали приложение. Используйте команду Connect-AzureAD, чтобы войти в свою учетную запись и ввести учетные данные Учетной записи Azure во всплывающем окне. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата

Откройте другой экземпляр Windows PowerShell ISE и запустите следующие команды для создания сертификата с самостоятельной подписью и чтения ключа, связанного с сертификатом:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Создание учетных данных на основе сертификатов 

Затем запустите следующие команды, чтобы получить идентификатор объекта вашего приложения и создать учетные данные на основе сертификатов. В этом примере мы устанавливаем сертификат, срок действия которых истекает через год, и вы можете установить его на любую требуемую дату окончания.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Приведенная выше команда приводит к выводу, аналогичному скриншоту ниже:

![Вывод о создании учетных данных на основе сертификатов](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Нанастройка учетной записи Azure Cosmos для использования новой идентификации

1. Войти на [портал Azure](https://portal.azure.com/).

1. Перейдите к учетной записи Azure Cosmos, откройте лезвие **управления доступом (IAM).**

1. Выберите **Назначение** **ролей.** Добавьте sampleApp, созданный на предыдущем этапе, с **ролью вкладчика,** показанный на следующем скриншоте:

   ![Настройка учетной записи Azure Cosmos для использования новой идентификации](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Выберите **Сохранить** после заполнения формы

## <a name="register-your-certificate-with-azure-ad"></a>Регистрация сертификат в Azure AD

Учетные данные на основе сертификатов можно связать с клиентским приложением в Azure AD с портала Azure. Чтобы связать учетные данные, необходимо загрузить файл сертификата со следующими шагами:

При регистрации приложения Azure для клиентского приложения сделайте следующее.

1. Войти на [портал Azure](https://portal.azure.com/).

1. Откройте панель Active **Directory,** перейдите на панель **регистраций приложений** и откройте созданное на предыдущем этапе приложение, созданное на предыдущем этапе. 

1. Выберите **Сертификаты & секреты,** а затем **загрузить сертификат**. Просмотрите файл сертификата, созданный на предыдущем этапе для загрузки.

1. Нажмите кнопку **Добавить**. После загрузки сертификата отображаются отпечаток пальца, дата начала и значения истечения срока действия.

## <a name="access-the-keys-from-powershell"></a>Доступ к ключам от PowerShell

На этом этапе вы подпишете контракт с Azure, используя созданное приложение и созданный сертификат, и получите доступ к ключам учетной записи Azure Cosmos. 

1. Первоначально очистите учетные данные учетной записи Azure, которые вы использовали для входиных учетных записей в свою учетную запись. Вы можете очистить учетные данные, используя следующую команду:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Затем проверьте, что вы можете войти в портал Azure, используя учетные данные приложения и получить доступ к клавишам Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Предыдущая команда будет отображать основные и вторичные основные основные основные основные ключевые ключи вашей учетной записи Azure Cosmos. Вы можете просмотреть журнал активности вашей учетной записи Azure Cosmos, чтобы подтвердить, что запрос на получение ключей выполнен успешно и событие инициировано приложением "sampleApp".

![Проверка вызова ключей получения в Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>Доступ к ключам из приложения C- 

Вы также можете проверить этот сценарий, доступ к ключам из приложения C.' Следующее приложение консоли СЗ, которое может получить доступ к клавишам Azure Cosmos DB с помощью приложения, зарегистрированного в Active Directory. Не забудьте обновить clientId, clientID, certName, название группы ресурсов, идентификатор подписки, данные об имени учетной записи Azure Cosmos перед запуском кода. 

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

Этот сценарий выводит основные и вторичные клавиши мастера, как показано на следующем скриншоте:

![выход приложения csharp](./media/certificate-based-authentication/csharp-application-output.png)

Как и в предыдущем разделе, можно просмотреть журнал активности вашей учетной записи Azure Cosmos, чтобы проверить, что событие запроса на получение ключей инициировано приложением "sampleApp". 


## <a name="next-steps"></a>Дальнейшие действия

* [Защита ключей Azure Cosmos с помощью Azure Key Vault](access-secrets-from-keyvault.md)

* [Базовый упор на безопасность для Azure Cosmos DB](security-baseline.md)
