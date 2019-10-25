---
title: Создание подключения к данным в сетке событий для Azure обозреватель данных с помощью Python
description: Из этой статьи вы узнаете, как создать подключение к данным в сетке событий для Azure обозреватель данных с помощью Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 11ae18714d1806aac1cc4f55ce6511fc12498440
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800439"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Создание подключения к данным в сетке событий для Azure обозреватель данных с помощью Python

> [!div class="op_single_selector"]
> * [Microsoft Azure](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure обозреватель данных обеспечивает прием (загрузку данных) из концентраторов событий, центров Интернета вещей и больших двоичных объектов, записанных в контейнеры больших двоичных объектов. В этой статье вы создадите подключение к данным в сетке событий для Azure обозреватель данных с помощью Python.

## <a name="prerequisites"></a>Технические условия

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

* Создание [кластера и базы данных](create-cluster-database-csharp.md)

* Создание [сопоставления таблиц и столбцов](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Задание [политик базы данных и таблиц](database-table-policies-csharp.md) (необязательно)

* Создайте [учетную запись хранения с подпиской службы "Сетка событий](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)".

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Добавление подключения к данным в сетке событий

В следующем примере показано, как добавить подключение к данным в сетке событий программным способом. См. раздел [Создание подключения к данным в сетке событий в Azure обозреватель данных](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) для добавления подключения к данным сетки событий с помощью портал Azure.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenant_id | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscription_id | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор подписки, используемый для создания ресурсов.|
| client_id | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента приложения, которое может получать доступ к ресурсам в клиенте.|
| client_secret | *кскскскскскскскскскскскскскс* | Секрет клиента приложения, которое может получить доступ к ресурсам в клиенте. |
| resource_group_name | *testrg* | Имя группы ресурсов, содержащей кластер.|
| cluster_name | *mykustocluster* | Имя кластера.|
| database_name | *mykustodatabase* | Имя целевой базы данных в кластере.|
| data_connection_name | *мевенсубконнект* | Требуемое имя подключения к данным.|
| table_name | *стормевентс* | Имя целевой таблицы в целевой базе данных.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Имя сопоставления столбцов, связанного с целевой таблицей.|
| data_format | *-* | Формат данных сообщения.|
| event_hub_resource_id | *Идентификатор ресурса* | Идентификатор ресурса концентратора событий, в котором сетка событий настроена для отправки событий. |
| storage_account_resource_id | *Идентификатор ресурса* | Идентификатор ресурса учетной записи хранения, в которой хранятся данные для приема. |
| consumer_group | *$Default* | Группа потребителей концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения к данным.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]