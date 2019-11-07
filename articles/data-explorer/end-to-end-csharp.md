---
title: Прием сквозного большого двоичного объекта в Azure обозреватель данных с помощьюC#
description: Из этой статьи вы узнаете, как получать большие двоичные объекты в Azure обозреватель данных с помощью комплексного примера, в C#котором используется.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: e22621083a44555cb3eda615c610f673cd841ec1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581837"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-c"></a>Прием сквозного большого двоичного объекта в Azure обозреватель данных с помощьюC#

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Data Explorer — это быстрая и масштабируемая служба для изучения данных журналов и телеметрии. В этой статье приводится полный пример приема данных из хранилища BLOB-объектов Azure в обозреватель данных Azure. 

Вы узнаете, как программным способом создать группу ресурсов, учетную запись хранения и контейнер, концентратор событий и кластер Azure обозреватель данных и базу данных. Вы также узнаете, как программно настроить обозреватель данных Azure для приема данных из новой учетной записи хранения.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

## <a name="install-c-nuget"></a>Установка C# NuGet

* Установите [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [Microsoft. Azure. Management. ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
* Установите [Microsoft. Azure. Management. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/).
* Установите [Microsoft. Azure. Storage. BLOB](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/).
* Установите [Microsoft. RESTful. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) для проверки подлинности.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Примеры кода 

В следующем примере кода вы получите пошаговый процесс, который приводит к приему данных в Azure обозреватель данных. 

Сначала создайте группу ресурсов. Вы также создадите ресурсы Azure, такие как учетная запись хранения и контейнер, концентратор событий и кластер Azure обозреватель данных и база данных. Затем вы создадите подписку службы "Сетка событий Azure" вместе с сопоставлением таблиц и столбцов в базе данных Azure обозреватель данных. Наконец, создайте подключение к данным, чтобы настроить обозреватель данных Azure для приема данных из новой учетной записи хранения. 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//Path to the Azure Resource Manager template JSON from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, and database by using an Azure Resource Manager template.");
var parameters = $"{{\"eventHubNamespaceName\":{{\"value\":\"{eventHubNamespaceName}\"}},\"eventHubName\":{{\"value\":\"{eventHubName}\"}},\"storageAccountName\":{{\"value\":\"{storageAccountName}\"}},\"containerName\":{{\"value\":\"{storageContainerName}\"}},\"kustoClusterName\":{{\"value\":\"{kustoClusterName}\"}},\"kustoDatabaseName\":{{\"value\":\"{kustoDatabaseName}\"}}}}";
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **Параметр** | **Описание поля** |
|---|---|---|
| tenantId | Идентификатор клиента. Он также называется ИДЕНТИФИКАТОРом каталога.|
| subscriptionId | Идентификатор подписки, используемый для создания ресурсов.|
| clientid | Идентификатор клиента приложения, которое может получать доступ к ресурсам в клиенте.|
| clientSecret | Секрет клиента приложения, которое может получить доступ к ресурсам в клиенте. |

## <a name="test-the-code-example"></a>Тестирование примера кода

1. Отправьте файл в учетную запись хранения.

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**Параметр** | **Описание поля**|
    |---|---|---|
    | storageConnectionString | Строка подключения для программно созданной учетной записи хранения.|

2. Выполните тестовый запрос в обозреватель данных Azure.

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить группу ресурсов и очистить ресурсы, используйте следующую команду:

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>Дальнейшие действия

*  Дополнительные сведения о других способах создания кластера и базы данных см. в статье [Создание кластера и базы данных Azure обозреватель данных](create-cluster-database-csharp.md).
* Дополнительные сведения о методах приема см. в статье прием [данных в Azure обозреватель данных](ingest-data-overview.md).
* Дополнительные сведения о веб-приложении см. в разделе [Краткое руководство. запрос данных в пользовательском веб-интерфейсе Azure обозреватель данных](web-query-data.md).
* [Написание запросов](write-queries.md) с помощью языка запросов Kusto.