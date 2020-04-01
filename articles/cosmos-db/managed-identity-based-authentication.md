---
title: Как использовать назначенную системой управляемую идентификацию для доступа к данным Azure Cosmos DB
description: Узнайте, как настроить систему Azure AD, назначенную управляемой имитацией, чтобы получить доступ к ключам из Azure Cosmos DB. msi, управляемая идентификация службы, aad, активный каталог azure, идентификация
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80417237"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Как использовать назначенную системой управляемую идентификацию для доступа к данным Azure Cosmos DB

В этой статье вы наставите **надежное агностик вращения ключей для** доступа к клавишам Azure Cosmos DB, используя [управляемые идентификаторы.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) Пример в этой статье использует функцию Azure. Тем не менее, вы можете достичь этого решения с помощью любой службы, которая поддерживает управляемые идентификаторы. 

Вы узнаете, как создать функцию Azure, которая может получить доступ к DB Azure Cosmos без необходимости копирования любых dB-ключей Azure Cosmos. Функция будет просыпаться каждую минуту и записывать текущую температуру аквариума аквариума аквариума. Чтобы узнать, как настроить срабатывающую функцию Azure, [см. функцию «Создание» в Azure, которая запускается статьей таймер.](../azure-functions/functions-create-scheduled-function.md)

Для упрощения сценария очистка старых температурных документов обрабатывается уже настроенной настройкой [Time To Live.](./time-to-live.md) 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Назначить назначенное системой управляемое удостоверение функциональности функции Azure

На этом этапе вы назначите управляемое удостоверение, назначенное системой, функции Azure.

1. На [портале Azure](https://portal.azure.com/)откройте панель **функции Azure** и перейдите к приложению функции. 

1. Откройте **платформу с** > вкладкой**Identity:** 

   ![Вкладка идентификации](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. На вкладке **Identity** **включите** статус **системы идентификации.** Не забудьте выбрать **Сохранить**, и подтвердить, что вы хотите включить систему идентификатора. В конце панель **системы идентификационная** должна выглядеть следующим образом:  

   ![Система идентичности включен](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>Предоставить управляемый доступ к учетной записи Azure Cosmos

На этом этапе вы назначите роль управляемой изначаной системе функции Azure. Azure Cosmos DB имеет несколько встроенных ролей, которые можно присвоить управляемому итору. Для этого решения вы будете использовать следующие две роли:

|Встроенные роли  |Описание  |
|---------|---------|
|[Участник учетной записи DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Может управлять учетными записями Azure Cosmos DB Позволяет получить клавиши чтения/записи. |
|[Читатель учетной записи Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Позволяет считывать данные учетных записей Azure Cosmos DB. Позволяет получить клавиши чтения. |

> [!IMPORTANT]
> Поддержка RBAC в Azure Cosmos DB применима только к управлению операциями самолета. Операции плоскости данных обеспечиваются с помощью ключевых ключей или токенов ресурсов. Чтобы узнать больше, смотрите Безопасный доступ к статье [данных.](secure-access-to-data.md)

> [!TIP] 
> При назначении ролей назначайте только необходимый доступ. Если для службы требуется только чтение данных, то присвоите управляемое удостоверение роли **читателя учетной записи Cosmos DB.** Для получения дополнительной информации о важности [lower exposure of privileged accounts](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) доступа к наименее привилегированным привилегиям см.

Для сценария вы прочитаете температуру, а затем запишите эти данные в контейнер в Azure Cosmos DB. Поскольку необходимо написать данные, вы будете использовать роль **вкладчика accountDB DocumentDB.** 

1. Войдите на портал Azure и перейдите в учетную запись Azure Cosmos DB. Откройте **панель управления доступом (IAM),** а затем вкладку **«Назначение ролей»:**

   ![IAM Пане](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Выберите кнопку **«Добавить кнопку»,** а затем **добавьте ролевые задания.**

1. Панель **добавления ролей** открывается вправо:

   ![Добавление роли](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Роль** - Выберите **вкладчика учетной записи DocumentDB**
   * **Назначить доступ к** - В разделе «Выберите **систему», назначенном управляемым подразделом,** выберите **функциональное приложение.**
   * **Выберите** - панель будет заполнена всеми функциональными приложениями, в вашей подписке, которые имеют **идентификационную систему управляемой системы.** В нашем случае я выбираю приложение функции **SummaryService:** 

      ![Выбрать назначение](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. После того, как личность приложения функции **выбрана,** нажмите Сохранить .

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Программное доступ к клавишам Azure Cosmos DB от функции Azure

Теперь у нас есть приложение функции, которая имеет системно-назначенное управляемое удостоверение личности. Этой идентификации дается роль **вкладчика учетной записи DocumentDB** в разрешениях Azure Cosmos DB. Следующий код приложения функции получит клавиши Azure Cosmos DB, создаст объект CosmosClient, получит температуру, а затем сохранит это в Cosmos DB.

В этом примере используется [API ключей списка](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) для доступа к клавишам учетной записи Azure Cosmos DB.

> [!IMPORTANT] 
> Если вы хотите назначить роль [ **читателя учетной записи Cosmos DB,** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) вам нужно будет использовать прочитанном только [Api List Keys.](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys) Это будет только заполнить только читать только ключи.

API ключей списка `DatabaseAccountListKeysResult` возвращает объект. Этот тип не определен в библиотеках C-класса. Следующий код показывает реализацию этого класса:  

```csharp 
namespace SummarizationService 
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

Для получения назначенного системы токенов управляемого удостоверения вы будете использовать библиотеку [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) Чтобы узнать другие способы получения маркера и более подробной информации о библиотеке, `Microsoft.Azure.Service.AppAuthentication` смотрите статью [«Служба аутентификации службы».](../key-vault/service-to-service-authentication.md)

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
    public static class TemperatureMonitor
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

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
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
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Теперь вы готовы [к развертыванию функции Azure.](../azure-functions/functions-create-first-function-vs-code.md)

## <a name="next-steps"></a>Следующие шаги

* [Проверка подлинности на основе сертификатов с помощью Azure Cosmos DB и Active Directory](certificate-based-authentication.md)
* [Защита ключей Azure Cosmos с помощью Azure Key Vault](access-secrets-from-keyvault.md)
* [Базовый упор на безопасность для Azure Cosmos DB](security-baseline.md)
