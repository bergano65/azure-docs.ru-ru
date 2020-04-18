---
title: Как использовать назначенную системой управляемую идентификацию для доступа к данным Azure Cosmos DB
description: Узнайте, как настроить систему Azure Active Directory (Azure AD), назначенную управляемой иственницы (управляемой службы) для доступа к ключам из DB Azure Cosmos.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641231"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Используйте системные управляемые идентификаторы для доступа к данным Azure Cosmos DB

В этой статье вы наберете *надежное агностическое* решение для вращения ключей для доступа к клавишам Azure Cosmos DB с помощью [управляемых идентификаторов.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) Пример в этой статье использует функции Azure, но можно использовать любую службу, которая поддерживает управляемые идентификаторы. 

Вы узнаете, как создать функциональное приложение, которое может получить доступ к данным Azure Cosmos DB без необходимости копирования любых dB-ключей Azure Cosmos. Функция приложение будет просыпаться каждую минуту и записывать текущую температуру аквариума аквариума аквариума. Чтобы узнать, как настроить приложение функции, вызванное таймером, см. [функцию «Создай» в Azure, которая запускается статьей таймер.](../azure-functions/functions-create-scheduled-function.md)

Для упрощения сценария настройка [Time To Live](./time-to-live.md) уже настроена для очистки старых температурных документов. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Назначить функционерам приложения, назначенного системой,

На этом этапе вы назначите управляемое удостоверение, назначенное системой, приложению функции.

1. На [портале Azure](https://portal.azure.com/)откройте панель **функции Azure** и перейдите в приложение функции. 

1. Откройте **платформу с** > вкладкой**Identity:** 

   ![Скриншот, показывающий функции платформы и параметры идентификации для приложения функции.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. На вкладке **Identity** **включите** **статус** идентификации системы и выберите **Сохранить.** Панель **идентификации** должна выглядеть следующим образом:  

   ![Скриншот, показывающий статус идентификации системы, установленный на On.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Предоставить доступ к вашей учетной записи Azure Cosmos

На этом этапе вы назначите роль системе, назначенной системой, управляемой идентификации. Azure Cosmos DB имеет несколько встроенных ролей, которые можно присвоить управляемому итору. Для этого решения вы будете использовать следующие две роли:

|Встроенные роли  |Описание  |
|---------|---------|
|[Участник учетной записи DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Может управлять учетными записями Azure Cosmos DB Позволяет получить клавиши чтения/записи. |
|[Читатель учетной записи Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Позволяет считывать данные учетных записей Azure Cosmos DB. Позволяет получить клавиши чтения. |

> [!IMPORTANT]
> Поддержка управления доступом на основе ролей в Azure Cosmos DB применяется только к управлению операциями самолетов. Операции плоскости данных обеспечиваются с помощью ключевых ключей или токенов ресурсов. Чтобы узнать больше, смотрите Безопасный доступ к статье [данных.](secure-access-to-data.md)

> [!TIP] 
> При присвоении ролей назначайте только необходимый доступ. Если ваша служба требует только данных для чтения, то присвоите роль читателя учетной записи **Cosmos DB** управляемой идентификации. Для получения дополнительной информации о важности доступа к наименее привилегированным привилегиям [см.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

В этом случае приложение функции будет считывать температуру аквариума, а затем записывать эти данные в контейнер в Azure Cosmos DB. Поскольку приложение функции должно записывать данные, необходимо назначить роль **вкладчика учетной записи DocumentDB.** 

1. Войдите на портал Azure и перейдите на свой аккаунт Azure Cosmos DB. Откройте панель **управления доступом (IAM),** а затем вкладку **«Назначения ролей»:**

   ![Скриншот, показывающий панель управления доступом и вкладку «Назначения ролей».](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Выберите **и добавьте** > **назначение ролей.**

1. Панель **назначения ролей Добавить** открывается вправо:

   ![Скриншот, показывающий панель назначения роли Добавить.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Роль**: Выберите **вкладчика учетной записи DocumentDB**
   * **Назначить доступ к**: Под **разделом «Выберите систему», назначенной управляемой идентификацией,** выберите **функции App.**
   * **Выберите**: Панель будет заполнена всеми функциональными приложениями в вашей подписке, которые имеют **идентификационную систему управляемой системы.** В этом случае выберите приложение функции **FishTankTemperatureService:** 

      ![Скриншот, показывающий панель назначения роли Add, заполненную примерами.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. После того как вы выбрали приложение функции, выберите **Сохранить**.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Программное доступ к клавишам Azure Cosmos DB

Теперь у нас есть функциональное приложение, которое имеет системное управляемое удостоверение с ролью **вкладчика accountDB** в разрешениях Azure Cosmos DB. Следующий код приложения функции получит клавиши Azure Cosmos DB, создаст объект CosmosClient, получит температуру аквариума, а затем сохранит это в Azure Cosmos DB.

В этом примере используется [API ключей списка](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) для доступа к клавишам учетной записи Azure Cosmos DB.

> [!IMPORTANT] 
> Если вы хотите назначить роль [читателя учетной записи Cosmos DB,](#grant-access-to-your-azure-cosmos-account) вам необходимо использовать [API-изсписка, прочитанном только по](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)клавишам. Это будет заполнить только читать только ключи.

API ключей списка `DatabaseAccountListKeysResult` возвращает объект. Этот тип не определен в библиотеках C-класса. Следующий код показывает реализацию этого класса:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

В примере также используется простой документ под названием "TemperatureRecord", который определяется следующим образом:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Для получения назначенных системой управляемых токенов вы будете использовать библиотеку [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) Чтобы узнать другие способы получения маркера `Microsoft.Azure.Service.AppAuthentication` и узнать больше информации о библиотеке, смотрите статью [аутентификации службы к службе.](../key-vault/general/service-to-service-authentication.md)


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Теперь вы готовы [развернуть приложение функции.](../azure-functions/functions-create-first-function-vs-code.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Проверка подлинности на основе сертификатов с помощью Azure Cosmos DB и активного каталога Azure](certificate-based-authentication.md)
* [Безопасные клавиши Azure Cosmos DB с помощью Azure Key Vault](access-secrets-from-keyvault.md)
* [Базовый упор на безопасность для Azure Cosmos DB](security-baseline.md)
