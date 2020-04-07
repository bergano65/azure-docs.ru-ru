---
title: Что такое частная конечная точка Azure?
description: Узнайте о частной точке зрения Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: b8f55b0693b7ffdf10516cfb19b67ccc5a94dc5a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742906"
---
# <a name="what-is-azure-private-endpoint"></a>Что такое частная конечная точка Azure?

Частная конечная точка Azure — это сетевой интерфейс, который защищенно и надежно подключается к службе через Приватный канал Azure. Private Endpoint использует частный IP-адрес из вашего VNet, эффективно вневедя службу в ваш VNet. Услуга может быть сервисом Azure, таким как Azure Storage, Azure Cosmos DB, S'L и т.д. или вашей собственной [службой private Link.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Частные свойства конечных точек 
 Частная конечная точка определяет следующие свойства: 


|Свойство  |Описание |
|---------|---------|
|name    |    Уникальное имя в группе ресурсов.      |
|Подсеть    |  Подсеть для развертывания и распределения частных IP-адресов из виртуальной сети. Для требований к подсети смотрите раздел Ограничения в этой статье.         |
|Ресурс частной ссылки    |   Частный ресурс ссылки для подключения с использованием идентификатора ресурсов или псевдонима из списка доступных типов. Для всего трафика, отправленного на этот ресурс, будет создан уникальный идентификатор сети.       |
|Целевой субресурс   |      Субресурс для подключения. Каждый тип ресурса частной ссылки имеет различные варианты для выбора на основе предпочтений.    |
|Метод утверждения подключения    |  Автоматический или ручной. На основе разрешений на управление доступом на основе ролей (RBAC) ваша частная конечная точка может быть утверждена автоматически. Если вы попытаетесь подключиться к частному ресурсу ссылки без RBAC, используйте ручной метод, чтобы позволить владельцу ресурса утвердить соединение.        |
|Сообщение запроса     |  Можно указать сообщение для запрашиваемых подключений, которое должно быть утверждено вручную. Это сообщение может быть использовано для идентификации конкретного запроса.        |
|Состояние подключения   |   Свойство только для чтения, которое определяет, активна ли частная конечная точка. Для отправки трафика можно использовать только частные конечные точки в утвержденном состоянии. Дополнительные состояния доступны: <br>-**Утверждено**: Подключение было автоматически или вручную одобрено и готово к использованию.</br><br>-**Ожидание**: Соединение было создано вручную и находится на утверждении владельцем частного ресурса ссылки.</br><br>-**Отклонено**: Соединение было отклонено владельцем частного ресурса ссылки.</br><br>-**Отключено:** Соединение было удалено владельцем частного ресурса ссылки. Частная конечная точка становится информативной и должна быть удалена для очистки. </br>|

Вот некоторые ключевые сведения о частных конечных точках: 
- Частная конечная точка обеспечивает связь между потребителями из того же VNet, регионально заглянул VNets, глобально заглянул VNets и на помещениях с помощью [VPN](https://azure.microsoft.com/services/vpn-gateway/) или [Экспресс-маршрут](https://azure.microsoft.com/services/expressroute/) и услуг, работающих на Private Link.
 
- При создании частной конечной точки также создается сетевой интерфейс для жизненного цикла ресурса. Интерфейсу присваивается частный IP-адрес из подсети, который отображается в службе private Link Service.
 
- Частная конечная точка должна быть развернута в том же регионе, что и виртуальная сеть. 
 
- Ресурс частной ссылки может быть развернут в другом регионе, чем виртуальная сеть и частная конечная точка.
 
- Несколько частных конечных точек могут быть созданы с помощью одного и того же частного ресурса ссылки. Для одной сети, используюйсобой общую конфигурацию DNS-сервера, рекомендуемая практика заключается в использовании одной частной конечной точки для данного частного ресурса ссылки, чтобы избежать дублирования записей или конфликтов в разрешении DNS. 
 
- Несколько частных конечных точек могут быть созданы на одних и тех же или разных подсетях в одной виртуальной сети. Количество частных конечных точек, которые можно создать в подписке, ограничено. Для получения подробной [информации](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)см.


 
## <a name="private-link-resource"></a>Частный ресурс ссылки 
Частный ресурс ссылки является целью назначения данной частной конечной точки. Ниже приводится список доступных частных типов ресурсов ссылки: 
 
|Имя ресурса private link  |Тип ресурса   |Подресурсы  |
|---------|---------|---------|
|**Частная ссылка службы** (Ваш собственный сервис)   |  Microsoft.Network/privateLinkServices       | empty |
|**База данных SQL Azure** | Microsoft.Sql/servers    |  Сервер Sql (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Сервер Sql (sqlServer)        | 
|**Хранилище Azure**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Таблица (таблица, table_secondary)<BR> Очередь (очередь, queue_secondary)<BR> Файл (файл, file_secondary)<BR> Веб (интернет, web_secondary)        |
|**Azure Data Lake Storage 2-го поколения**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Система файлов озера данных Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Кассандра, Гремлин, Стол|
|**База данных Azure для PostgreS-L -Одинсервер** | Microsoft.DBforPostgreSQL/servers   | postgresqlServer |
|**База данных Azure для MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**База данных Azure для MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Убежище ключей Azure** | Microsoft.KeyVault/vaults    | Хранилище |
|**Служба Azure Kubernetes - API Kubernetes** | Microsoft.ContainerService/managedClusters | управляемыйКластер |
|**Поиск Azure;** | Microsoft.Search/SearchService| поискСервис|  
|**Реестр контейнеров Azure** | Microsoft.ContainerRegistry/registries  | реестр |
|**Конфигурация приложений Azure** | Microsoft.Appconfiguration/configurationStores   | configurationStore |
|**Azure Backup** | Microsoft.RecoveryServices/vaults   | Хранилище |
|**Концентратор событий Azure** | Microsoft.EventHub/namespaces    | namespace |
|**Служебная шина Azure** | Microsoft.ServiceBus/namespaces | namespace |
|**Ретранслятор Azure** | Microsoft.Relay/namespaces | namespace |
|**Сетка событий Azure** | Microsoft.EventGrid/topics  | Раздел |
|**Сетка событий Azure** | Microsoft.EventGrid/домены | домен |
|**Лазурные WebApps** | Microsoft.Web/sites    | site |
|**Машинное обучение Azure** | Microsoft.MachineLearningServices/workspaces  | Рабочая область |
  
 
## <a name="network-security-of-private-endpoints"></a>Сетевая безопасность частных конечных точек 
При использовании частных конечных точек для служб Azure трафик обеспечивается определенным ресурсом частного соединения. Платформа выполняет элемент управления доступом для проверки сетевых соединений, достигающих только указанного частного ресурса ссылки. Для доступа к дополнительным ресурсам в той же службе Azure требуются дополнительные частные конечные точки. 
 
Можно полностью заблокировать рабочие нагрузки от доступа к конечным точкам доступа к общедоступным точкам для подключения к поддерживаемой службе Azure. Этот элемент управления обеспечивает дополнительный уровень сетевой безопасности для ваших ресурсов, предоставляя встроенную защиту эксфильтрации, которая препятствует доступу к другим ресурсам, размещенным в той же службе Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Доступ к частному ресурсу ссылки с помощью рабочего процесса утверждения 
Вы можете подключиться к частному ресурсу ссылки, используя следующие методы утверждения соединения:
- **Автоматически** одобрен, когда вы владеете или имеете разрешение на конкретный частный ресурс ссылки. Требуемое разрешение основано на типе ресурсов частной ссылки в следующем формате: Microsoft. \<Поставщик>/<resource_type>/частноеутверждение/действие
- **Ручной** запрос, когда у вас нет необходимого разрешения и хотел бы запросить доступ. Будет инициирован рабочий процесс утверждения. Частная конечная точка и последующее подключение к частной конечной точке создаются в состоянии "Ожидание". Владелец ресурса частной ссылки должен утвердить это подключение. После его утверждения частная конечная точка может нормально отправлять трафик, как показано на следующей диаграмме рабочего процесса утверждения.  

![утверждение рабочего процесса](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Владелец ресурса частной ссылки может выполнять следующие действия по приватной конечной точке соединения: 
- Просмотрите все сведения о приватных соединениях конечных точек. 
- Утвердить приватное соединение конечных точек. Соответствующая частная конечная точка будет иметь возможность отправлять трафик на частный ресурс ссылки. 
- Отклонить частное соединение конечных точек. Соответствующая частная конечная точка будет обновлена с учетом статуса.
- Удалить приватное соединение конечных точек в любом состоянии. Соответствующая частная конечная точка будет обновлена с отключенным состоянием, чтобы отразить действие, владелец частной конечных точек может только удалить ресурс на этом этапе. 
 
> [!NOTE]
> Только частная конечная точка в утвержденном состоянии может отправлять трафик на данный частный ресурс ссылки. 

### <a name="connecting-using-alias"></a>Подключение с использованием Alias
Прозвище является уникальным прозвищем, которое генерируется, когда владелец сервиса создает частную услугу ссылки за стандартным балансером нагрузки. Владелец сервиса может поделиться этим псевдонимом со своими потребителями в автономном режиме. Потребители могут запросить подключение к частной службе ссылки, используя либо ресурс URI, либо Alias. Если вы хотите подключиться с помощью Alias, необходимо создать частную конечную точку с помощью ручного метода утверждения соединения. Для использования ручного метода утверждения соединения установите параметр ручного запроса для достоверной во время частной конечных точек создания потока. Посмотрите на [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) и [АЗ сети частной точки конца создать](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) для деталей. 

## <a name="dns-configuration"></a>Настройка DNS 
При подключении к частному ресурсу ссылок, используя полностью квалифицированное доменное имя (ФЗДН) в строке подключения, важно правильно настроить настройки DNS для решения выделенного частного IP-адреса. Существующие службы Azure могут уже иметь конфигурацию DNS для использования при подключении через общедоступную конечную точку. Это должно быть переопределено для подключения с помощью частной точки конца. 
 
Сетевой интерфейс, связанный с частной конечной точкой, содержит полный набор информации, необходимой для настройки DNS, включая F-DN и частные IP-адреса, выделенные для данного частного ресурса ссылки. 
 
Вы можете использовать следующие параметры для настройки настроек DNS для частных конечных точек: 
- **Используйте файл Host (рекомендуется только для тестирования).** Вы можете использовать файл хоста на виртуальной машине, чтобы переопределить DNS.  
- **Используйте частную зону DNS.** Можно использовать частные зоны DNS для переопределения разрешения DNS для данной частной конечных точек. Частная зона DNS может быть связана с вашей виртуальной сетью для решения конкретных доменов.
- **Используйте пользовательский DNS-сервер.** Вы можете использовать свой собственный DNS-сервер для переопределения разрешения DNS для данного частного ресурса ссылки. Если ваш [DNS-сервер](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) размещается в виртуальной сети, можно создать правило переадресации DNS для использования частной зоны DNS для упрощения конфигурации для всех частных ресурсов связи.
 
> [!IMPORTANT]
> Не рекомендуется переопределять зону, которая активно используется для решения общедоступных конечных точек. Подключение к ресурсам не сможет решить правильно без переадресировки DNS в общедоступный DNS. Чтобы избежать проблем, создайте другое доменное имя или следуйте предложенному названию для каждой службы ниже. 
 
Для служб Azure используйте рекомендуемые названия зон, описанные в следующей таблице:

|Тип ресурсов Private Link   |Подресурс  |Название зоны  |
|---------|---------|---------|
|СЗЛ ДБ (Microsoft.Sql/серверы)    |  Сервер Sql (sqlServer)        |   privatelink.database.windows.net       |
|Аналитика Azure Synapse (Microsoft.Sql/серверы)    |  Сервер Sql (sqlServer)        | privatelink.database.windows.net |
|Учетная запись хранилища (Microsoft.Storage/StorageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Учетная запись хранилища (Microsoft.Storage/StorageAccounts)    |    Таблица (таблица, table_secondary)      |   privatelink.table.core.windows.net       |
|Учетная запись хранилища (Microsoft.Storage/StorageAccounts)    |    Очередь (очередь, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Учетная запись хранилища (Microsoft.Storage/StorageAccounts)   |    Файл (файл, file_secondary)      |    privatelink.file.core.windows.net      |
|Учетная запись хранилища (Microsoft.Storage/StorageAccounts)     |  Веб (интернет, web_secondary)        |    privatelink.web.core.windows.net      |
|Система файлов озера данных Gen2 (Microsoft.Storage/StorageAccounts)  |  Система файлов озера данных Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Таблица|privatelink.table.cosmos.azure.com|
|База данных Azure для PostgreS'L - Единый сервер (Microsoft.DBforPostgreS'L/серверы)|postgresqlServer|privatelink.postgres.database.azure.com|
|База данных Azure для MyS'L (Microsoft.DBforMyS'L/серверы)|mysqlServer|privatelink.mysql.database.azure.com|
|База данных Azure для MariaDB (Microsoft.DBforMariaDB/серверы)|mariadbServer|privatelink.mariadb.database.azure.com|
|Убежище ключей Azure (Microsoft.KeyVault/vaults)|Хранилище|privatelink.vaultcore.azure.net|
|Служба Azure Kubernetes - API Kubernetes (Microsoft.ContainerService/управляемыекластеры) | управляемыйКластер | (Guid).privatelink. <region>.azmk8s.io|
|Поиск azure (Microsoft.Search/SearchServices)|поискСервис|privatelink.search.windows.net|   
|Реестр контейнеров Azure (Microsoft.ContainerRegistry/регистраторы) | реестр | privatelink.azurecr.io |
|Конфигурация приложения Azure (Microsoft.Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Резервное копирование Azure (Microsoft.RecoveryServices/vaults)| Хранилище |privatelink. (регион).backup.windowsazure.com|
|Концентратор событий Azure (Microsoft.EventHub/namespaces)| namespace |privatelink.servicebus.windows.net|
|Автобус службы Azure (Microsoft.ServiceBus/namespaces) | namespace |privatelink.servicebus.windows.net|
|Реле Azure (Microsoft.Relay/namespaces) | namespace |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft.EventGrid/темы)   | Раздел | Теме. (регион).privatelink.eventgrid.azure.net|
|Azure Event Grid (Microsoft.EventGrid/домены) | домен | Домена. (регион).privatelink.eventgrid.azure.net |
|Лазурные WebApps (Microsoft.Web/сайты)    | site | privatelink.azurewebsites.net |
|Машинное обучение Azure (Microsoft.MachineLearningServices/рабочие области)   | Рабочая область | privatelink.api.azureml.ms |
 
Azure создаст каноническое имя DNS-записи (CNAME) на общедоступной DNS, чтобы перенаправить разрешение на предлагаемые доменные имена. Вы сможете переопределить разрешение с помощью частного IP-адреса ваших частных конечных точек. 
 
Приложениям не нужно менять URL-адрес соединения. При попытке решения с помощью общедоступной DNS сервер DNS теперь будет разрешаться с вашими частными конечными точками. Этот процесс не влияет на приложения. 
 
## <a name="limitations"></a>Ограничения
 
Следующая таблица содержит список известных ограничений при использовании частных конечных точек: 


|Ограничение |Описание |Меры по снижению риска  |
|---------|---------|---------|
|Правила Группы сетевой безопасности (NSG) и маршруты, определяемые пользователями, не применяются к частной точке    |NSG не поддерживается в частных конечных точках. В то время как подсети, содержащие частную конечную точку, могут иметь nsG, связанные с ней, правила не будут эффективными для трафика, обрабатываемого частной конечной точкой. Для развертывания частных конечных точек в подсети необходимо [отключить сетевые политики.](disable-private-endpoint-network-policy.md) NSG по-прежнему применяется на других рабочих нагрузках, размещенных в той же подсети. Маршруты в любой клиентской подсети будут использовать префикс /32, изменение поведения маршрутификатора по умолчанию требует аналогичного UDR  | Управление трафиком с помощью правил NSG для исходящего трафика на клиентах-источниках. Развертывание отдельных маршрутов с префиксом /32 для переопределения частных конечных маршрутов. Журналы NSG Flow и информация мониторинга исходящих соединений по-прежнему поддерживаются и могут быть использованы        |


## <a name="next-steps"></a>Дальнейшие действия
- [Create a Private Endpoint using Azure portal ](create-private-endpoint-portal.md) (Создание частной конечной точки для сервера базы данных SQL с помощью портала)
- [Создание частной конечной точки для сервера базы данных SQL с помощью портала PowerShell ](create-private-endpoint-powershell.md)
- [Создание частной конечной точки для сервера базы данных SQL с помощью портала CLI ](create-private-endpoint-cli.md)
- [Создание частной конечной точки для учетной записи хранения, используя портал ](create-private-endpoint-storage-portal.md)
- [Создание частной конечной точки для учетной записи Azure Cosmos с помощью портала](../cosmos-db/how-to-configure-private-endpoints.md)
- [Create your own Private Link service using Azure PowerShel](create-private-link-service-powershell.md) (Создание собственного Приватного канала с помощью Azure PowerShell)
- [Создайте свою собственную частную ссылку для базы данных Azure для PostgreS - Единый сервер с помощью portal](../postgresql/howto-configure-privatelink-portal.md)
- [Создайте собственную частную ссылку для базы данных Azure для PostgreS-L - Единый сервер с помощью CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Создайте свою собственную частную ссылку для базы данных Azure для MyS'L с помощью портала](../mysql/howto-configure-privatelink-portal.md)
- [Создайте собственную частную ссылку для базы данных Azure для MyS'L с помощью CLI](../mysql/howto-configure-privatelink-cli.md)
- [Создайте свою собственную частную ссылку для базы данных Azure для MariaDB с помощью portal](../mariadb/howto-configure-privatelink-portal.md)
- [Создайте свою собственную частную ссылку для базы данных Azure для MariaDB с помощью CLI](../mariadb/howto-configure-privatelink-cli.md)
