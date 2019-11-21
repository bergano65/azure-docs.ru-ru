---
title: Ограничения и квоты подписки Azure
description: В этой статье приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, квот и границ. This article includes information on how to increase limits along with maximum values.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: cost-management-billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: ed08a457ac00da7b40dc267cc1eb3b61db9df297
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224481"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Подписка Azure и лимиты службы, квоты и ограничения
В этом документе указаны некоторые из наиболее распространенных ограничений Microsoft Azure, которые иногда называются квотами. Этот документ на текущий момент охватывает не все службы Azure. Over time, the list will be expanded and updated to cover more services.

To learn more about Azure pricing, see [Azure pricing overview](https://azure.microsoft.com/pricing/). There, you can estimate your costs by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). You also can go to the pricing details page for a particular service, for example, [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Советы по управлению затратами приведены в статье [Предотвращение непредвиденных расходов с помощью функции выставления счетов и управления затратами в Azure](billing/billing-getting-started.md).

> [!NOTE]
> If you want to raise the limit or quota above the default limit, [open an online customer support request at no charge](azure-resource-manager/resource-manager-quota-errors.md). The limits can't be raised above the maximum limit value shown in the following tables. If there's no maximum limit column, the resource doesn't have adjustable limits.
>
> [Free Trial subscriptions](https://azure.microsoft.com/offers/ms-azr-0044p) aren't eligible for limit or quota increases. При наличии [бесплатной пробной версии подписки](https://azure.microsoft.com/offers/ms-azr-0044p) ее можно обновить до подписки [с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/). For more information, see [Upgrade your Azure Free Trial subscription to a Pay-As-You-Go subscription](billing/billing-upgrade-azure-subscription.md) and the [Free Trial subscription FAQ](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limits and Azure Resource Manager
It's now possible to combine multiple Azure resources into a single Azure resource group. When you use resource groups, limits that once were global become managed at a regional level with Azure Resource Manager. For more information about Azure resource groups, see [Azure Resource Manager overview](azure-resource-manager/resource-group-overview.md).

In the following list of limits, a new table reflects any differences in limits when you use Azure Resource Manager. For example, there's a **Subscription limits** table and a **Subscription limits - Azure Resource Manager** table. When a limit applies to both scenarios, it's only shown in the first table. Если не указано иное, ограничения глобальны во всех областях.

> [!NOTE]
> Quotas for resources in Azure resource groups are per-region accessible by your subscription, not per-subscription as the service management quotas are. В качестве примера используем квоты виртуальных процессоров. To request a quota increase with support for vCPUs, you must decide how many vCPUs you want to use in which regions. You then make a specific request for Azure resource group vCPU quotas for the amounts and regions that you want. If you need to use 30 vCPUs in West Europe to run your application there, you specifically request 30 vCPUs in West Europe. Your vCPU quota isn't increased in any other region--only West Europe has the 30-vCPU quota.
> <!-- -->
> As a result, decide what your Azure resource group quotas must be for your workload in any one region. Then request that amount in each region into which you want to deploy. For help in how to determine your current quotas for specific regions, see [Troubleshoot deployment issues](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Ограничения определенных служб
* [Active Directory](#active-directory-limits)
* [Управление API](#api-management-limits)
* [Служба приложений](#app-service-limits)
* [Шлюз приложений](#application-gateway-limits)
* [Служба автоматизации](#automation-limits)
* [Кэш Azure для Redis](#azure-cache-for-redis-limits)
* [Oблачныe службы Azure](#azure-cloud-services-limits)
* [Azure Cognitive Search](#azure-cognitive-search-limits)
* [Azure Cognitive Services](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [База данных Azure для MySQL](#azure-database-for-mysql)
* [База данных Azure для PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Брандмауэр Azure](#azure-firewall-limits)
* [Функции Azure](#functions-limits)
* [Служба Azure Kubernetes (AKS)](#azure-kubernetes-service-limits)
* [Машинное обучение Azure](#azure-machine-learning-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Политика Azure](#azure-policy-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Azure Backup](#backup-limits)
* [Пакетная служба](#batch-limits)
* [Службы BizTalk](#biztalk-services-limits)
* [Экземпляры контейнеров](#container-instances-limits)
* [Реестр контейнеров](#container-registry-limits)
* [Сеть доставки содержимого](#content-delivery-network-limits)
* [Фабрика данных](#data-factory-limits)
* [Аналитика озера данных](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Сетка событий](#event-grid-limits)
* [Центры событий](#event-hubs-limits)
* [Front Door Service](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [Центр Интернета вещей](#iot-hub-limits)
* [Служба подготовки устройств к добавлению в Центр Интернета вещей](#iot-hub-device-provisioning-service-limits)
* [хранилище ключей;](#key-vault-limits)
* [Службы мультимедиа](#media-services-limits)
* [Мобильные службы](#mobile-services-limits)
* [Многофакторная идентификация](#multi-factor-authentication-limits)
* [Сеть](#networking-limits)
  * [Шлюз приложений](#application-gateway-limits)
  * [Azure Bastion](#azure-bastion-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Брандмауэр Azure](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [Наблюдатель за сетями](#network-watcher-limits)
  * [Общедоступный IP-адрес](#publicip-address)
  * [Приватный канал](#private-link-limits)
  * [Диспетчер трафика](#traffic-manager-limits)
  * [Виртуальная сеть](#networking-limits)
  * [Virtual WAN](#virtual-wan-limits)
* [центры уведомлений](#notification-hubs-limits)
* [группа ресурсов](#resource-group-limits)
* [Контроль доступа на основе ролей](#role-based-access-control-limits)
* [Планировщик](#scheduler-limits)
* [Служебная шина](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [База данных SQL](#sql-database-limits)
* [Хранилище данных SQL](#sql-data-warehouse-limits)
* [Хранилище](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Анализ потока](#stream-analytics-limits)
* [подписка](#subscription-limits)
* [Виртуальные машины](#virtual-machines-limits)
* [Масштабируемые наборы виртуальных машин](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Ограничения подписки
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Subscription limits - Azure Service Management (classic deployment model)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Ограничения подписки — Azure Resource Manager
The following limits apply when you use Azure Resource Manager and Azure resource groups. Limits that haven't changed with Azure Resource Manager aren't listed. See the previous table for those limits.

Сведения об ограничениях на операции записи и чтения API Resource Manager см. в разделе [Регулирование запросов Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Resource group limits
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин
#### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Ограничения виртуальных машин — диспетчер ресурсов Azure
The following limits apply when you use Azure Resource Manager and Azure resource groups.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Shared Image Gallery limits

There are limits, per subscription, for deploying resources using Shared Image Galleries:
- 100 shared image galleries, per subscription, per region
- 1,000 image definitions, per subscription, per region
- 10,000 image versions, per subscription, per region

### <a name="virtual-machine-scale-sets-limits"></a>Virtual machine scale sets limits
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Ограничения для экземпляров контейнеров Azure
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Ограничения реестра контейнера
В следующей таблице приведены сведения о возможностях и ограничениях [уровней служб](./container-registry/container-registry-skus.md) "Базовый", "Стандартный" и "Премиум".

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes Service limits
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-limits"></a>Azure Machine Learning limits
The latest values for Azure Machine Learning Compute quotas can be found in the [Azure Machine Learning quota page](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Ограничения сети
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute limits
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="virtual-wan-limits"></a>Virtual WAN limits
[!INCLUDE [virtual-wan-limits](../includes/virtual-wan-limits.md)]

#### <a name="application-gateway-limits"></a>Ограничения шлюза приложений

Сведения в следующей таблице относится к номерам SKU версии 1, версии 2, ценовой категории "Стандартный" и WAF, если не указано иное.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Пределы наблюдателя за сетями
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Private Link limits
[!INCLUDE [private-link-limits](../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Ограничения диспетчера трафика
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-bastion-limits"></a>Azure Bastion limits
[!INCLUDE [Azure Bastion limits](../includes/bastion-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS limits
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Ограничения брандмауэра Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Ограничения службы Azure Front Door Service
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Ограничения хранилища
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

For more information on storage account limits, see [Azure Storage scalability and performance targets](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Ограничения поставщика ресурсов хранилища

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Ограничения хранилища BLOB-объектов Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Ограничения службы "Файлы Azure"
For more information on Azure Files limits, see [Azure Files scalability and performance targets](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Ограничения службы "Синхронизация файлов Azure"
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Ограничения для службы Azure "Хранилище очередей"
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Ограничения для службы Azure "Хранилище таблиц"
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Ограничения для дисков виртуальной машины
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

For more information, see [Virtual machine sizes](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Управляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Неуправляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Azure Cloud Services limits
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Azure Cognitive Services limits
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>Ограничения службы приложений
Следующие ограничения Службы приложения включают ограничения для веб-приложений, мобильных приложений, приложений API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Functions limits
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Ограничения планировщика
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Ограничения пакета
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Ограничения служб BizTalk
The following table shows the limits for Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Ограничения Azure Cosmos DB
For Azure Cosmos DB limits, see [Limits in Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>База данных Azure для MySQL
Дополнительные сведения об ограничениях базы данных Azure для MySQL см. в статье [Ограничения базы данных Azure для MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>База данных Azure для PostgreSQL
Сведения об ограничениях базы данных Azure для PostgreSQL см. в статье [Ограничения базы данных Azure для PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-cognitive-search-limits"></a>Azure Cognitive Search limits
Ценовые категории определяют емкость и ограничения службы поиска. Существуют следующие категории:

* **Free** multitenant service, shared with other Azure subscribers, is intended for evaluation and small development projects.
* **Базовый** предоставляет выделенные вычислительные ресурсы для небольших рабочих нагрузок в рабочей среде, поддерживая до трех реплик для обработки запросов с высоким уровнем доступности.
* **Standard**, which includes S1, S2, S3, and S3 High Density, is for larger production workloads. Multiple levels exist within the Standard tier so that you can choose a resource configuration that best matches your workload profile.

**Ограничения на одну подписку**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Ограничения на одну службу поиска**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

To learn more about limits on a more granular level, such as document size, queries per second, keys, requests, and responses, see [Service limits in Azure Cognitive Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Ограничения служб мультимедиа
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network limits
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Ограничения мобильных служб
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Azure Monitor limits

#### <a name="alerts"></a>Оповещения

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Группы действий

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Log queries and language

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Рабочие области Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Notification Hubs limits
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Ограничения Центров событий
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Ограничения служебной шины
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Пределы для Центра Интернета вещей
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Ограничения службы подготовки устройств для Центра Интернета вещей
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Ограничения фабрики данных
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Ограничения Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Ограничения Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Ограничения Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Ограничения Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Ограничения Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Event Grid limits
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Ограничения службы "Карты Azure"
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Ограничения политики Azure
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Ограничения системы StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Ограничения резервного копирования
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Azure SignalR Service limits
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Ограничения Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Ограничения управления API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Ограничения кэша Azure для Redis
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Ограничения хранилища ключей
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication limits
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Ограничения автоматизации
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager limits
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Ограничения управления доступа на основе ролей
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Ограничения базы данных SQL
For SQL Database limits, see [SQL Database resource limits for single databases](sql-database/sql-database-vcore-resource-limits-single-databases.md), [SQL Database resource limits for elastic pools and pooled databases](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), and [SQL Database resource limits for managed instances](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Ограничения хранилища данных SQL
For SQL Data Warehouse limits, see [SQL Data Warehouse resource limits](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Дополнительные материалы
- [Understand Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Virtual machine and cloud service sizes for Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Sizes for Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md)
