---
title: Конфигурация DNS для частной конечной точки Azure
description: Сведения о конфигурации DNS для частной конечной точки Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7db02546b562f1b542080efdbda8968940655e95
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121303"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Конфигурация DNS для частной конечной точки Azure


При подключении к ресурсу частной связи с использованием полного доменного имени (FQDN) в строке подключения необходимо правильно настроить параметры DNS, чтобы разрешить доступ к выделенному частному IP-адресу. Возможно, в существующих службах Azure уже есть конфигурация DNS, используемая при подключении через общедоступную конечную точку. Эту конфигурацию необходимо переопределить для подключения с помощью частной конечной точки. 
 
Сетевой интерфейс, связанный с частной конечной точкой, содержит полный набор сведений, необходимых для настройки DNS, включая полное доменное имя и частные IP-адреса, выделенные для данного ресурса частной ссылки. 
 
Для настройки параметров DNS для частных конечных точек можно использовать следующие параметры. 
- **Используйте файл узла (рекомендуется только для тестирования)**. Для переопределения DNS можно использовать файл узла на виртуальной машине.  
- **Используйте частную зону DNS**. Для переопределения разрешения DNS для заданной закрытой конечной точки можно использовать [частные зоны DNS](../dns/private-dns-privatednszone.md) . Частная зона DNS может быть связана с вашей виртуальной сетью для разрешения конкретных доменов.
- **Используйте сервер пересылки DNS (необязательно)**. Вы можете использовать сервер пересылки DNS для переопределения разрешения DNS для данного ресурса частной ссылки. Если [DNS-сервер](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) размещен в виртуальной сети, можно создать правило пересылки DNS для использования частной зоны DNS, чтобы упростить настройку для всех ресурсов частной связи.
 
> [!IMPORTANT]
> Не рекомендуется переопределять зону, которая активно используется для разрешения общедоступных конечных точек. Подключения к ресурсам не смогут правильно разрешаться без пересылки DNS в общедоступную службу DNS. Чтобы избежать проблем, создайте другое доменное имя или выполните предлагаемое имя для каждой службы ниже. 

## <a name="azure-services-dns-zone-configuration"></a>Настройка зоны DNS служб Azure
Службы Azure будут создавать каноническое имя DNS-записи (CNAME) на общедоступном DNS-сервере, чтобы перенаправить разрешение в предложенное имя частного домена. Разрешение можно переопределить с помощью частного IP-адреса закрытых конечных точек. 
 
Приложениям не нужно изменять URL-адрес подключения. При попытке разрешить использование общедоступной службы DNS DNS-сервер теперь будет разрешаться в частные конечные точки. Этот процесс не влияет на существующие приложения. 

Для служб Azure используйте Рекомендуемые имена зон, как описано в следующей таблице.

| Тип ресурса частной ссылки/подресурс |Имя зоны Частная зона DNS | Имя зоны общедоступного DNS |
|---|---|---|---|
| База данных SQL (Microsoft. SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure синапсе Analytics (Microsoft. SQL/Servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Учетная запись хранения (Microsoft. Storage/storageAccounts)/BLOB (BLOB-объект, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Учетная запись хранения (Microsoft. Storage/storageAccounts)/таблица (таблица, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Учетная запись хранения (Microsoft. Storage/storageAccounts)/очередь (очередь, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Учетная запись хранения (Microsoft. Storage/storageAccounts)/File (File, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Учетная запись хранения (Microsoft. Storage/storageAccounts)/Web (Web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake файловая система Gen2 (Microsoft. Storage/storageAccounts)/Data Lake файловая система Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)/Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| База данных Azure для PostgreSQL — Single Server (Microsoft. Дбфорпостгрескл/Servers)/Постгресклсервер | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| База данных Azure для MySQL (Microsoft. Дбформискл/Servers)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| База данных Azure для MariaDB (Microsoft. Дбформариадб/Servers)/Мариадбсервер | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft. KeyVault/Vault)/хранилище | privatelink.vaultcore.azure.net | vault.azure.net |
| Azure Kubernetes Service — API Kubernetes (Microsoft. ContainerService/Манажедклустерс)/Манажедклустер | привателинк. {Region}. azmk8s. IO | {Region}. azmk8s. IO |
| Поиск Azure (Microsoft. Search/Сеарчсервицес)/Сеарчсервице | privatelink.search.windows.net | search.windows.net |
| Реестр контейнеров Azure (Microsoft. ContainerRegistry/реестров)/Реестр | privatelink.azurecr.io | azurecr.io |
| Конфигурация приложений Azure (Microsoft. Аппконфигуратион/Конфигуратионсторес)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft. RecoveryServices/Vault)/хранилище | привателинк. {Region}. Backup. WindowsAzure. com | {Region}. Backup. WindowsAzure. com |
| Концентратор событий Azure (Microsoft. EventHub/пространства имен)/пространство имен | privatelink.servicebus.windows.net | servicebus.windows.net. |
| Служебная шина Azure (Microsoft. ServiceBus/пространства имен)/пространство имен | privatelink.servicebus.windows.net | servicebus.windows.net. |
| Azure Relay (Microsoft. Relay/пространства имен)/пространство имен | privatelink.servicebus.windows.net | servicebus.windows.net. |
| Служба "Сетка событий Azure" (Microsoft. EventGrid/темы)/раздел | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Служба "Сетка событий Azure" (Microsoft. EventGrid/Domains)/domain | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Веб-приложения Azure (Microsoft. Web/Sites)/site | privatelink.azurewebsites.net | azurewebsites.net; |
| Машинное обучение Azure (Microsoft. Мачинелеарнингсервицес/рабочие области)/Рабочая область | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>Сценарии конфигурации DNS

Полное доменное имя служб автоматически разрешается в общедоступный IP-адрес, поэтому для разрешения на частный IP-адрес частной конечной точки необходимо соответствующим образом изменить конфигурацию DNS.

DNS является критически важным компонентом, обеспечивающим правильную работу приложения, устраняя при этом правильный IP-адрес частной конечной точки.

В зависимости от ваших предпочтений для встроенного разрешения DNS доступны следующие сценарии.

- [Рабочие нагрузки виртуальных сетей без пользовательского DNS-сервера](#virtual-network-workloads-without-custom-dns-server)
- [Локальные рабочие нагрузки с использованием DNS-сервера пересылки](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Рабочие нагрузки виртуальных сетей без пользовательского DNS-сервера

Эта конфигурация подходит для рабочих нагрузок виртуальных сетей без пользовательского DNS-сервера. В этом сценарии клиент запрашивает IP-адрес частной конечной точки для предоставленного Azure DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS будет отвечать за разрешение DNS для частных зон DNS.


> [!NOTE]
> В этом сценарии используется база данных SQL Azure, рекомендуемая Частная зона DNSной зоной. Для других служб можно настроить модель, используя следующие справочные сведения о [конфигурации зоны DNS служб Azure](#azure-services-dns-zone-configuration).

Для правильной настройки необходимы следующие ресурсы:

- Виртуальная сеть клиента

- Частная зона DNS зоны [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md) с [типом записи A](../dns/dns-zones-records.md#record-types)

- Сведения о частной конечной точке (имя записи FQDN и частный IP-адрес)

На следующей схеме показана последовательность разрешения DNS из рабочих нагрузок виртуальной сети с использованием частной зоны DNS.

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Одна виртуальная сеть и DNS, предоставляемые Azure":::

Эту модель можно расширить на несколько одноранговых виртуальных сетей, связанных с одной частной конечной точкой. Это можно сделать, [добавив новые ссылки на виртуальную сеть](../dns/private-dns-virtual-network-links.md) в частную зону DNS для всех одноранговых виртуальных сетей.

> [!IMPORTANT]
>  Для этой конфигурации требуется одна частная зона DNS. при создании нескольких зон с одинаковым именем для разных виртуальных сетей потребуется выполнить операции вручную для слияния записей DNS.

В этом сценарии существует топология сети [hub &](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) с периферийными сетями, которые совместно используют общую конечную точку, а все периферийные виртуальные сети связаны с той же частной зоной DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Концентраторы и резервные службы с DNS, предоставляемыми Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Локальные рабочие нагрузки с использованием DNS-сервера пересылки
 
Чтобы локальные рабочие нагрузки могли разрешать полное доменное имя частной конечной точки в частный IP-адрес, необходимо использовать DNS-сервер пересылки, чтобы обеспечить разрешение [общедоступной зоны DNS](#azure-services-dns-zone-configuration) службы Azure, развернутой в Azure.


Следующий сценарий подходит для локальной сети, в которой есть сервер пересылки DNS в Azure, который, в свою очередь, отвечает за разрешение всех запросов DNS с помощью серверной пересылки на уровне сервера DNS, предоставленного Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> В этом сценарии используется база данных SQL Azure, рекомендуемая Частная зона DNSной зоной.Для других служб можно настроить модель, используя следующие справочные сведения о [конфигурации зоны DNS служб Azure](#azure-services-dns-zone-configuration).

Для правильной настройки необходимы следующие ресурсы:

- Локальная сеть
- Виртуальная сеть [, подключенная к локальной среде](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-сервер пересылки, развернутый в Azure 
- Частная зона DNS зоны [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)   с [типом записи A](../dns/dns-zones-records.md#record-types)
- Сведения о частной конечной точке (имя записи FQDN и частный IP-адрес)

На следующей схеме показана последовательность разрешения DNS из локальной сети, в которой используется сервер пересылки DNS, развернутый в Azure, где разрешение выполняется с помощью частной зоны DNS, связанной с виртуальной сетью.

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Локальная среда с использованием Azure DNS":::

Эту конфигурацию можно расширить для локальной сети, в которой уже имеется решение DNS. 
Локальное решение DNS необходимо настроить для пересылки трафика DNS на Azure DNS через [Условный сервер пересылки](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) , ссылающийся на DNS-сервер перенаправления, развернутый в Azure.

> [!NOTE]
> В этом сценарии используется база данных SQL Azure, рекомендуемая Частная зона DNSной зоной.Для других служб можно настроить модель, используя следующие справочные сведения о [конфигурации зоны DNS служб Azure](#azure-services-dns-zone-configuration).

Для правильной настройки необходимы следующие ресурсы:


- Локальная сеть с настраиваемым решением DNS 
- Виртуальная сеть [, подключенная к локальной среде](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-сервер пересылки, развернутый в Azure
- Частная зона DNS зоны [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)    с [типом записи A](../dns/dns-zones-records.md#record-types)
- Сведения о частной конечной точке (имя записи FQDN и частный IP-адрес)

На следующей схеме показана последовательность разрешения DNS из локальной сети, которая условно перенаправляет трафик DNS в Azure, где разрешение выполняется с помощью частной зоны DNS, связанной с виртуальной сетью.

> [!IMPORTANT]
> Условная пересылка должна быть выполнена в [общедоступной зоне DNS](#azure-services-dns-zone-configuration)   , например:  `database.windows.net`   , вместо **привателинк**. Database.Windows.NET

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Локальная пересылка на Azure DNS":::


## <a name="next-steps"></a>Дальнейшие действия
- [Сведения о частных конечных точках](private-endpoint-overview.md)
