---
title: Конфигурация DNS частной конечной точки Azure
description: Сведения о конфигурации DNS частной конечной точки Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: bb1f4b5e37cecc33cef115f26c44ad6375c7e327
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734384"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Конфигурация DNS частной конечной точки Azure


Если вы подключаетесь к ресурсу Приватного канала, используя полное доменное имя (FQDN) в строке подключения, важно правильно настроить параметры DNS, чтобы разрешить доступ к выделенному частному IP-адресу. Возможно, существующие службы Microsoft Azure уже используют конфигурацию DNS для подключения через общедоступную конечную точку. Эту конфигурацию необходимо переопределить для подключения с помощью частной конечной точки. 
 
Сетевой интерфейс, связанный с частной конечной точкой, содержит полный набор сведений для настройки DNS, включая полное доменное имя и частные IP-адреса, выделенные для определенного ресурса Приватного канала. 
 
Чтобы настраивать параметры DNS для частных конечных точек, можно использовать следующие варианты. 
- **Использовать файл узла (рекомендуется только для тестирования).** Для переопределения DNS можно использовать файл узла на виртуальной машине.  
- **Использовать частную зону DNS**. Чтобы переопределить разрешение DNS для определенной частной конечной точки, можно использовать [частные зоны DNS](../dns/private-dns-privatednszone.md). Частная зона DNS может быть связана с вашей виртуальной сетью для разрешения конкретных доменов.
- **Использовать DNS-сервер пересылки (необязательно).** Чтобы переопределить разрешение DNS для определенного ресурса Приватного канала, можно использовать DNS-сервер пересылки. Если [DNS-сервер](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) размещен в виртуальной сети, можно создать правило переадресации DNS, чтобы использовать частную зону DNS для упрощения настройки для всех ресурсов частной связи.

> [!IMPORTANT]
> Не рекомендуется переопределять зону, которая активно используется для разрешения открытых конечных точек. Подключения к ресурсам не смогут правильно разрешаться без переадресации DNS в общедоступную службу DNS. Чтобы избежать проблем, создайте другое доменное имя или следуйте предлагаемому имени для каждой службы ниже. 



## <a name="azure-services-dns-zone-configuration"></a>Настройка зоны DNS служб Azure
Службы Azure создадут каноническое имя DNS-записи (CNAME) в общедоступной службе DNS, чтобы перенаправить разрешение в предложенное имя частного домена. Разрешение можно переопределить с помощью частного IP-адреса частных конечных точек. 
 
Приложениям не нужно изменять URL-адрес подключения. При попытке разрешения с использованием общедоступной службы DNS сервер доменных имен теперь будет выполнять разрешение в частные конечные точки. Этот процесс не влияет на существующие приложения. 

> [!IMPORTANT]
> Частные сети уже используют частную зону DNS для заданного типа. может подключаться только к общедоступным ресурсам, если у них нет подключений к частным конечным точкам. в противном случае для завершения последовательности разрешения DNS требуется соответствующая конфигурация DNS в частной зоне DNS. 

Для служб Azure используйте рекомендуемые имена зон, описанные в следующей таблице.

| Тип ресурса частной ссылки/Подресурс |Имя Частной зоны DNS | Общедоступные серверы переадресации зоны DNS |
|---|---|---|
| Служба автоматизации Azure/(Microsoft. Automation/automationAccounts)/веб-перехватчик, Дскандхибридворкер | privatelink.azure-automation.net | azure-automation.net |
| База данных SQL Azure (Microsoft.SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.SQL/servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Учетная запись хранения (Microsoft.Storage/storageAccounts)/Большой двоичный объект (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Учетная запись хранения (Microsoft.Storage/storageAccounts)/Таблица (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Учетная запись хранения (Microsoft.Storage/storageAccounts)/Очередь (queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Учетная запись хранения (Microsoft.Storage/storageAccounts)/Файл (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Учетная запись хранения (Microsoft.Storage/storageAccounts)/Сеть (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Файловая система Azure Data Lake 2-го поколения (Microsoft.Storage/storageAccounts)/Файловая система Data Lake 2-го поколения (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Таблица | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| База данных Azure для PostgreSQL — одиночный сервер (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| База данных Azure для MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| База данных Azure для MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / vault | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service — API Kubernetes (Microsoft. ContainerService/Манажедклустерс)/Управление | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Поиск Azure (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Реестр контейнеров Azure (Microsoft.ContainerRegistry/registries) / реестр | privatelink.azurecr.io | azurecr.io |
| Конфигурация приложений Azure (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft.RecoveryServices/vaults) / хранилище | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Центры событий Azure (Microsoft.EventHub/namespaces)/Пространство имен | privatelink.servicebus.windows.net | servicebus.windows.net. |
| Служебная шина Azure (Microsoft.ServiceBus/namespaces) / пространство имен | privatelink.servicebus.windows.net | servicebus.windows.net. |
| Центр Интернета вещей Azure (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net | azure-devices.net |
| Azure Relay (Microsoft.Relay/namespaces) / пространство имен | privatelink.servicebus.windows.net | servicebus.windows.net. |
| Сетка событий Azure (Microsoft.EventGrid/topics) / тема | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Сетка событий Azure (Microsoft.EventGrid/domains) / домен | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Веб-приложения Azure (Microsoft. Web/Sites) и сайты | privatelink.azurewebsites.net | azurewebsites.net; |
| Машинное обучение Azure (Microsoft.MachineLearningServices/workspaces)/Рабочая область | privatelink.api.azureml.ms | api.azureml.ms |
| Центр Интернета вещей (Microsoft. Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| SignalR (Microsoft. Сигналрсервице/SignalR)/SignalR | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. Insights/Привателинкскопес)/азуремонитор | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft. CognitiveServices/Accounts)/Account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Синхронизация файлов Azure (Microsoft. StorageSync/Сторажесинксервицес)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |
| Фабрика данных Azure (Microsoft. данные и фабрики)/факт |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Фабрика данных Azure (Microsoft. данные и фабрики)/портал |  privatelink.azure.com  |  azure.com  |

 
## <a name="dns-configuration-scenarios"></a>Сценарии настройки DNS

Полное доменное имя служб автоматически разрешается в общедоступный IP-адрес. Чтобы выполнить разрешение в частный IP-адрес частной конечной точки, необходимо соответствующим образом изменить конфигурацию DNS.

DNS является критически важным компонентом, обеспечивающим правильную работу приложения за счет правильного разрешения IP-адреса частной конечной точки.

Доступны следующие встроенные сценарии, связанные с разрешением DNS:

- [Рабочие нагрузки виртуальных сетей без пользовательского DNS-сервера](#virtual-network-workloads-without-custom-dns-server)
- [Локальные рабочие нагрузки с использованием DNS-сервера переадресации](#on-premises-workloads-using-a-dns-forwarder)
- [Рабочие нагрузки виртуальных сетей и локальные рабочие нагрузки с использованием DNS-сервера пересылки](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> [DNS-прокси-сервер брандмауэра Azure](../firewall/dns-settings.md#dns-proxy) можно использовать в качестве DNS-сервера пересылки для [локальных рабочих нагрузок](#on-premises-workloads-using-a-dns-forwarder) и [рабочих нагрузок виртуальной сети с помощью сервера пересылки DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder).

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Рабочие нагрузки виртуальных сетей без пользовательского DNS-сервера

Эта конфигурация подходит для рабочих нагрузок виртуальных сетей без пользовательского DNS-сервера. В этом сценарии клиент запрашивает IP-адрес частной конечной точки для предоставленной Azure службы DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS будет отвечать за разрешение DNS для частных зон DNS.

> [!NOTE]
> В этом сценарии используется частная зона DNS, рекомендуемая Базой данных SQL Azure. Для других служб можно настроить эту модель, используя следующий ресурс: [Настройка зоны DNS служб Azure](#azure-services-dns-zone-configuration).

Для правильной настройки необходимы следующие ресурсы:

- виртуальная сеть клиента;

- Частная зона DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) с [записью типа A](../dns/dns-zones-records.md#record-types);

- сведения о частной конечной точке (имя записи FQDN и частный IP-адрес).

На следующем снимке экрана показана последовательность разрешения DNS из рабочих нагрузок виртуальной сети с использованием частной зоны DNS.

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Одна виртуальная сеть и DNS, предоставляемые Azure":::

Эту модель можно расширить на несколько одноранговых виртуальных сетей, связанных с одной частной конечной точкой. Это можно сделать, [добавляя новые ссылки на виртуальные сети](../dns/private-dns-virtual-network-links.md) в частную зону DNS для всех одноранговых виртуальных сетей.

> [!IMPORTANT]
> Для этой конфигурации требуется одна частная зона DNS. При создании нескольких зон с одинаковым именем для разных виртуальных сетей потребуется выполнить операции объединения записей DNS вручную.

> [!IMPORTANT]
> Если вы используете частную конечную точку в модели звезды из другой подписки, повторно используйте ту же частную зону DNS в концентраторе.

В этом сценарии используется [звездообразная](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) топология сети с периферийными сетями, которые совместно используют общую частную конечную точку. Все периферийные виртуальные сети связаны с одной частной зоной DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Модель звезда с DNS, предоставляемыми Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Локальные рабочие нагрузки с использованием DNS-сервера переадресации

Чтобы локальные рабочие нагрузки разрешали полное доменное имя частной конечной точки в частный IP-адрес, необходимо использовать DNS-сервер пересылки, чтобы обеспечить разрешение [общедоступной зоны DNS](#azure-services-dns-zone-configuration) службы Azure в Azure.

Следующий сценарий подходит для локальной сети с DNS-сервером пересылки в Azure, который отвечает за разрешение всех запросов DNS с помощью серверной пересылки в предоставленную Azure службу DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> В этом сценарии используется частная зона DNS, рекомендуемая Базой данных SQL Azure. Для других служб можно настроить эту модель, используя следующий ресурс: [Настройка зоны DNS служб Azure](#azure-services-dns-zone-configuration).

Для правильной настройки необходимы следующие ресурсы:

- Локальная сеть
- Виртуальная сеть [, подключенная к локальной среде](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-сервер переадресации, развернутый в Azure; 
- Частная зона DNS зоны [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md) с [типом записи A](../dns/dns-zones-records.md#record-types)
- сведения о частной конечной точке (имя записи FQDN и частный IP-адрес).

На следующей схеме показана последовательность разрешения DNS из локальной сети, в которой используется DNS-сервер пересылки, развернутый в Azure, где разрешение выполняется с помощью частной зоны DNS, [связанной с виртуальной сетью](../dns/private-dns-virtual-network-links.md).

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Локальная среда с использованием Azure DNS":::

Эту конфигурацию можно расширить для локальной сети, в которой уже есть решение DNS. Локальное решение DNS необходимо настроить для пересылки трафика DNS на Azure DNS с помощью сервера [условной пересылки](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) , который ссылается на сервер DNS, развернутый в Azure.

> [!NOTE]
> В этом сценарии используется частная зона DNS, рекомендуемая Базой данных SQL Azure. Для других служб можно настроить модель, используя следующую ссылку: [Конфигурация зоны DNS служб Azure](#azure-services-dns-zone-configuration) .

Для правильной настройки необходимы следующие ресурсы:

- локальная сеть с настраиваемым решением DNS; 
- Виртуальная сеть [, подключенная к локальной среде](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-сервер переадресации, развернутый в Azure;
- Частная зона DNS зоны [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)  с [типом записи A](../dns/dns-zones-records.md#record-types)
- сведения о частной конечной точке (имя записи FQDN и частный IP-адрес).

На следующей схеме показана последовательность разрешения DNS из локальной сети, которая условно перенаправляет трафик DNS в Azure, где разрешение выполняется с помощью частной зоны DNS, [связанной с виртуальной сетью](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> Условную переадресацию следует выполнять для рекомендуемого [сервера пересылки общедоступной зоны DNS](#azure-services-dns-zone-configuration). Пример: `database.windows.net` вместо **привателинк**. Database.Windows.NET.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Локальная переадресация в Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Рабочие нагрузки виртуальных сетей и локальные рабочие нагрузки с использованием DNS-сервера пересылки

Общий подход для рабочих нагрузок, которым требуется доступ к частной конечной точке из виртуальных и локальных сетей, включает использование общего DNS-сервера пересылки, который обеспечивает разрешение для [общедоступной зоны DNS](#azure-services-dns-zone-configuration) службы Azure, развернутой в Azure.

Приведенный ниже сценарий подходит для локальной сети, в которой используется DNS-сервер пересылки в Azure, и виртуальных сетей, которым требуется доступ к частной конечной точке, расположенной в общей сети концентратора.  

Этот DNS-сервер пересылки отвечает за разрешение всех запросов DNS с помощью серверной пересылки в предоставленную Azure службу DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Для этой конфигурации требуется одна частная зона DNS. Все клиентские подключения из локальных и [пиринговых виртуальных сетей](../virtual-network/virtual-network-peering-overview.md) также должны использовать одну частную зону DNS.

> [!NOTE]
> В этом сценарии используется частная зона DNS, рекомендуемая Базой данных SQL Azure. Для других служб можно настроить эту модель, используя следующий ресурс: [Настройка зоны DNS служб Azure](#azure-services-dns-zone-configuration).

Для правильной настройки необходимы следующие ресурсы:

- Локальная сеть
- Виртуальная сеть [, подключенная к локальной среде](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- [пиринговая виртуальная сеть;](../virtual-network/virtual-network-peering-overview.md) 
- DNS-сервер переадресации, развернутый в Azure;
- Частная зона DNS зоны [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)  с [типом записи A](../dns/dns-zones-records.md#record-types)
- сведения о частной конечной точке (имя записи FQDN и частный IP-адрес).

На следующей схеме показана последовательность разрешения DNS из локальной и виртуальной сети, в которой используется DNS-сервер пересылки, развернутый в Azure, где разрешение осуществляется с помощью частной зоны DNS, [связанной с виртуальной сетью](../dns/private-dns-virtual-network-links.md).

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Гибридный сценарий":::

## <a name="next-steps"></a>Дальнейшие действия
- [Узнайте больше о частных конечных точках](private-endpoint-overview.md)
