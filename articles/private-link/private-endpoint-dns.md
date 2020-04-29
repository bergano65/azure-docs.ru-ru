---
title: Конфигурация DNS для частной конечной точки Azure
description: Сведения о конфигурации DNS для частной конечной точки Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209032"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Конфигурация DNS для частной конечной точки Azure


При подключении к ресурсу частной связи с использованием полного доменного имени (FQDN) в строке подключения необходимо правильно настроить параметры DNS, чтобы разрешить доступ к выделенному частному IP-адресу. Возможно, в существующих службах Azure уже есть конфигурация DNS, используемая при подключении через общедоступную конечную точку. Это необходимо переопределить для подключения с помощью частной конечной точки. 
 
Сетевой интерфейс, связанный с частной конечной точкой, содержит полный набор сведений, необходимых для настройки DNS, включая полное доменное имя и частные IP-адреса, выделенные для данного ресурса частной ссылки. 
 
Для настройки параметров DNS для частных конечных точек можно использовать следующие параметры. 
- **Используйте файл узла (рекомендуется только для тестирования)**. Для переопределения DNS можно использовать файл узла на виртуальной машине.  
- **Используйте частную зону DNS**. Для переопределения разрешения DNS для заданной закрытой конечной точки можно использовать [частные зоны DNS](../dns/private-dns-privatednszone.md) . Частная зона DNS может быть связана с вашей виртуальной сетью для разрешения конкретных доменов.
- **Используйте пользовательский DNS-сервер**. Можно использовать собственный DNS-сервер для переопределения разрешения DNS для данного ресурса закрытой ссылки. Если [DNS-сервер](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) размещен в виртуальной сети, можно создать правило пересылки DNS для использования частной зоны DNS, чтобы упростить настройку для всех ресурсов частной связи.
 
> [!IMPORTANT]
> Не рекомендуется переопределять зону, которая активно используется для разрешения общедоступных конечных точек. Подключения к ресурсам не смогут правильно разрешаться без пересылки DNS в общедоступную службу DNS. Чтобы избежать проблем, создайте другое доменное имя или выполните предлагаемое имя для каждой службы ниже. 

## <a name="azure-services-dns-zone-configuration"></a>Настройка зоны DNS служб Azure
Службы Azure создают каноническое имя DNS-записи (CNAME) на общедоступном DNS-сервере, чтобы перенаправить разрешение в предложенные имена частных доменов. Вы сможете переопределить разрешение с помощью частного IP-адреса закрытых конечных точек. 
 
Приложениям не нужно изменять URL-адрес подключения. При попытке разрешить использование общедоступной службы DNS DNS-сервер теперь будет разрешаться в частные конечные точки. Этот процесс не влияет на существующие приложения. 

Для служб Azure используйте Рекомендуемые имена зон, как описано в следующей таблице.

|Тип ресурса частной ссылки   |Подресурс  |Имя зоны  |
|---------|---------|---------|
|База данных SQL (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure синапсе Analytics (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        | privatelink.database.windows.net |
|Учетная запись хранения (Microsoft. Storage/storageAccounts)    |  Большой двоичный объект (BLOB-объект, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Учетная запись хранения (Microsoft. Storage/storageAccounts)    |    Таблица (таблица, table_secondary)      |   privatelink.table.core.windows.net       |
|Учетная запись хранения (Microsoft. Storage/storageAccounts)    |    Очередь (очередь, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Учетная запись хранения (Microsoft. Storage/storageAccounts)   |    Файл (файл, file_secondary)      |    privatelink.file.core.windows.net      |
|Учетная запись хранения (Microsoft. Storage/storageAccounts)     |  Интернет (веб, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake файловой системы Gen2 (Microsoft. Storage/storageAccounts)  |  Data Lake файловой системы Gen2 (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. Азурекосмосдб/databaseAccounts)|Таблица|privatelink.table.cosmos.azure.com|
|База данных Azure для PostgreSQL — один сервер (Microsoft. Дбфорпостгрескл/Servers)|постгресклсервер|privatelink.postgres.database.azure.com|
|База данных Azure для MySQL (Microsoft. Дбформискл/Servers)|mysqlServer|privatelink.mysql.database.azure.com|
|База данных Azure для MariaDB (Microsoft. Дбформариадб/Servers)|мариадбсервер|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft. KeyVault/Vault)|Хранилище|privatelink.vaultcore.azure.net|
|Azure Kubernetes Service — API Kubernetes (Microsoft. ContainerService/Манажедклустерс)    | манажедклустер | {GUID}. привателинк. {Region}. azmk8s. IO|
|Поиск Azure (Microsoft. Search/Сеарчсервицес)|сеарчсервице|privatelink.search.windows.net|   
|Реестр контейнеров Azure (Microsoft. ContainerRegistry/реестров) | реестр | privatelink.azurecr.io |
|Настройка приложений Azure (Microsoft. Аппконфигуратион/Конфигуратионсторес)| configurationStore | privatelink.azconfig.io|
|Azure Backup (Microsoft. RecoveryServices/Vault)| Хранилище |привателинк. {Region}. Backup. WindowsAzure. com|
|Концентратор событий Azure (Microsoft. EventHub/пространства имен)| namespace |privatelink.servicebus.windows.net|
|Служебная шина Azure (Microsoft. ServiceBus/пространства имен) | namespace |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft. Relay/Namespaces) | namespace |privatelink.servicebus.windows.net|
|Сетка событий Azure (Microsoft. EventGrid/темы)     | Раздел | главе. {Region}. привателинк. eventgrid. Azure. NET|
|Сетка событий Azure (Microsoft. EventGrid/Domains) | домен | поддомен. {Region}. привателинк. eventgrid. Azure. NET |
|Веб-приложения Azure (Microsoft. Web/Sites)    | site | privatelink.azurewebsites.net |
|Машинное обучение Azure (Microsoft. Мачинелеарнингсервицес/workspaces)    | Рабочая область | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>Сценарии конфигурации DNS

Полное доменное имя служб разрешает общедоступный IP-адрес, поэтому необходимо изменить конфигурацию DNS для разрешения частного IP-адреса частной конечной точки.

DNS является критически важным компонентом, обеспечивающим правильную работу приложения, устраняя при этом правильный IP-адрес частной конечной точки.

В зависимости от ваших предпочтений для встроенного разрешения DNS доступны следующие сценарии.

- [Рабочие нагрузки виртуальных сетей без пользовательского DNS-сервера](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Рабочие нагрузки виртуальных сетей без пользовательского DNS-сервера

Эта конфигурация подходит для рабочих нагрузок виртуальных сетей без пользовательского DNS-сервера. В этом сценарии клиент запрашивает IP-адрес частной конечной точки для предоставленного Azure DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS будет отвечать за разрешение DNS для частных зон DNS.


 > [!NOTE]
> В этом сценарии используется база данных SQL Azure, рекомендуемая Частная зона DNSной зоной. Для других служб можно настроить модель, используя следующие справочные сведения о [конфигурации зоны DNS служб Azure](#azure-services-dns-zone-configuration).

Для правильной настройки необходимы следующие ресурсы:

- Виртуальная сеть клиента

- Частная зона DNS зоны [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md) с [типом записи A](../dns/dns-zones-records.md#record-types)

- Сведения о частной конечной точке (имя записи FQDN и частный IP-адрес)

На следующей схеме показана последовательность разрешения DNS из рабочих нагрузок виртуальной сети с использованием частной зоны DNS.

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="одна виртуальная сеть и предоставленная Azure DNS":::

Эту модель можно расширить на несколько одноранговых виртуальных сетей, связанных с одной частной конечной точкой. Это можно сделать, [добавив новые ссылки на виртуальную сеть](../dns/private-dns-virtual-network-links.md) в частную зону DNS для всех одноранговых виртуальных сетей.

 > [!IMPORTANT]
>  Для этой конфигурации требуется одна частная зона DNS. при создании нескольких зон с одинаковым именем для разных виртуальных сетей потребуется выполнить операции вручную для слияния записей DNS.

В этом сценарии существует топология сети [hub &](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) с периферийными сетями, которые совместно используют общую конечную точку, а все периферийные виртуальные сети связаны с той же частной зоной DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="концентраторы и резервные службы, предоставляемые Azure":::


## <a name="next-steps"></a>Дальнейшие шаги
- [Сведения о частных конечных точках](private-endpoint-overview.md)
