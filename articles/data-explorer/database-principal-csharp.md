---
title: Добавление участников базы данных для обозреватель данных Azure с помощьюC#
description: Из этой статьи вы узнаете, как добавить субъекты базы данных для Azure обозреватель данных с C#помощью.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965038"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Добавление участников базы данных для обозреватель данных Azure с помощьюC#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Шаблон Azure Resource Manager](database-principal-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы добавите участников базы данных для Azure обозреватель данных с помощью C#.

## <a name="prerequisites"></a>Технические условия

* Если вы не установили Visual Studio 2019, вы можете скачать и использовать **бесплатную** [версию Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.
* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Создайте кластер и базу данных](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Установка C# NuGet

* Установите [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [Microsoft. RESTful. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) для проверки подлинности.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Добавление участника базы данных

В следующем примере показано, как добавить участника базы данных программным способом.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenantId | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscriptionId | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор подписки, используемый для создания ресурсов.|
| clientid | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента приложения, которое может получать доступ к ресурсам в клиенте.|
| clientSecret | *кскскскскскскскскскскскскскс* | Секрет клиента приложения, которое может получить доступ к ресурсам в клиенте. |
| имя_группы_ресурсов | *testrg* | Имя группы ресурсов, содержащей кластер.|
| clusterName | *mykustocluster* | Имя кластера.|
| databaseName | *mykustodatabase* | Имя базы данных.|
| принЦипалассигнментнаме | *databasePrincipalAssignment1* | Имя ресурса участника базы данных.|
| principalId | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор участника, который может быть адресом электронной почты пользователя, ИДЕНТИФИКАТОРом приложения или именем группы безопасности.|
| роль | *Администратор* | Роль участника базы данных, которой может быть "admin", "принимающий", "Monitor", "User", "Унрестриктедвиеверс", "Viewer".|
| тенантидфорпринЦипал | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента участника.|
| принЦипалтипе | *Приложение* | Тип участника, который может иметь значение "User", "App" или "Group"|

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Прием данных с помощью библиотеки Node в Azure Data Explorer](node-ingest-data.md)
