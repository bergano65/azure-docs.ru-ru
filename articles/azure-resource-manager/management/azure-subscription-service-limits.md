---
title: Ограничения и квоты подписки Azure
description: В этой статье приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, квот и границ. Эта статья содержит информацию о том, как увеличить лимиты вместе с максимальными значениями.
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 4200784e3018ec80d9fbbbc0d3680b6b0e6d2cb3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656216"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Подписка Azure, границы, квоты и ограничения службы

В этом документе указаны некоторые из наиболее распространенных ограничений Microsoft Azure, которые иногда называются квотами.

Чтобы узнать больше о ценах на Azure, смотрите [обзор цен Azure.](https://azure.microsoft.com/pricing/) Там вы можете оценить свои расходы с помощью [калькулятора цен.](https://azure.microsoft.com/pricing/calculator/) Вы также можете перейти на страницу ценовых данных для конкретной службы, например, [Windows VMs.](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) Советы по управлению затратами приведены в статье [Предотвращение непредвиденных расходов с помощью функции выставления счетов и управления затратами в Azure](../../billing/billing-getting-started.md).

## <a name="managing-limits"></a>Управление лимитами

> [!NOTE]
> Некоторые службы имеют регулируемые ограничения.
>
> Если служба не имеет регулируемых ограничений, следующие таблицы используют **ограничение**заголовка. В этих случаях значение по умолчанию и максимальные лимиты одинаковы.
>
> Когда лимит может быть скорректирован, таблицы включают **лимит по умолчанию** и заголовки **максимального лимита.** Лимит может быть повышен выше предела по умолчанию, но не выше максимального предела.
>
> Если вы хотите повысить лимит или квоту выше лимита по умолчанию, [откройте онлайн-запрос поддержки клиентов бесплатно.](../templates/error-resource-quota.md)

[Бесплатная пробная подписка](https://azure.microsoft.com/offers/ms-azr-0044p) не имеет права на ограничение или увеличение квот. При наличии [бесплатной пробной версии подписки](https://azure.microsoft.com/offers/ms-azr-0044p) ее можно обновить до подписки [с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/). Для получения дополнительной информации смотрите [обновление подписки На бесплатную пробную версию Azure по подписке Pay-As-You-Go](../../billing/billing-upgrade-azure-subscription.md) и [бесплатной пробной подписке.](https://azure.microsoft.com/free/free-account-faq)

Некоторые ограничения управляются на региональном уровне.

В качестве примера используем квоты виртуальных процессоров. Чтобы запросить увеличение квоты с поддержкой vCPUs, необходимо решить, сколько vCPUs вы хотите использовать в каких регионах. Затем вы делаете конкретный запрос для квот группы ресурсов Azure vCPU для сумм и областей, которые вы хотите. Если вам нужно использовать 30 vCPUs в Западной Европе для запуска приложения там, вы специально запросить 30 vCPUs в Западной Европе. Ваша квота vCPU не увеличивается ни в одном другом регионе - только в Западной Европе есть квота 30-VCPU.

В результате решите, какими должны быть квоты группы ресурсов Azure для рабочей нагрузки в каком-либо одном регионе. Затем запросите ту сумму в каждом регионе, в который требуется развернуться. Для получения помощи в определении текущих [Resolve errors for resource quotas](../templates/error-resource-quota.md)квот для определенных регионов см.

## <a name="general-limits"></a>Общие лимиты

Для ограничений имен ресурсов см. [правила именования и ограничения для ресурсов Azure.](resource-name-rules.md)

Сведения об ограничениях на операции записи и чтения API Resource Manager см. в разделе [Регулирование запросов Resource Manager](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Ограничения группы управления

Следующие ограничения распространяются на [управленческие группы.](../../governance/management-groups/overview.md)

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Ограничения подписки

Следующие ограничения применяются при использовании ресурсов Azure Manager и групп ресурсов Azure.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Ограничения группы ресурсов

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Ограничения Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Ограничения управления API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Ограничения службы приложений

Следующие ограничения Службы приложения включают ограничения для веб-приложений, мобильных приложений, приложений API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Ограничения автоматизации

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Ограничения кэша Azure для Redis

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Ограничения облачных служб Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Ограничения когнитивного поиска Azure

Ценовые категории определяют емкость и ограничения службы поиска. Существуют следующие категории:

* **Бесплатный** мультитенантный сервис, совместно с другими абонентами Azure, предназначен для оценки и небольших проектов развития.
* **Базовый** предоставляет выделенные вычислительные ресурсы для небольших рабочих нагрузок в рабочей среде, поддерживая до трех реплик для обработки запросов с высоким уровнем доступности.
* **Стандартный**, который включает в себя S1, S2, S3 и S3 High Density, предназначен для больших производственных нагрузок. Несколько уровней существуют в пределах стандартного уровня, так что вы можете выбрать конфигурацию ресурса, которая наилучшим образом соответствует вашему профилю рабочей нагрузки.

**Ограничения на одну подписку**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Ограничения на одну службу поиска**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Чтобы узнать больше об ограничениях на более детальном уровне, таких как размер документа, [Service limits in Azure Cognitive Search](../../search/search-limits-quotas-capacity.md)запросы в секунду, ключи, запросы и ответы, см.

## <a name="azure-cognitive-services-limits"></a>Ограничения когнитивных служб Azure

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Ограничения Azure Cosmos DB

Для ограничений Azure Cosmos DB [см.](../../cosmos-db/concepts-limits.md)

## <a name="azure-data-explorer-limits"></a>Ограничения исследователя данных Azure

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>База данных Azure для MySQL

Дополнительные сведения об ограничениях базы данных Azure для MySQL см. в статье [Ограничения базы данных Azure для MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>База данных Azure для PostgreSQL

Сведения об ограничениях базы данных Azure для PostgreSQL см. в статье [Ограничения базы данных Azure для PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Ограничения функций Azure

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Ограничения службы Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Ограничения машинного обучения Azure

Последние значения квот Azure Machine Learning Compute можно найти на [странице квоты Машинного обучения Azure](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Ограничения службы "Карты Azure"

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Ограничения Azure Monitor

### <a name="alerts"></a>видны узлы

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Группы действий

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Запросы журнала и язык

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Рабочие области Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Ограничения политики Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Ограничения службы СигналR Azure

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Ограничения резервного копирования

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Ограничения пакета

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Классические ограничения модели развертывания

Если вместо модели развертывания azure Resource Manager используется классическая модель развертывания, применяются следующие ограничения.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Ограничения для экземпляров контейнеров Azure

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Ограничения реестра контейнера

В следующей таблице подробно описаны особенности и ограничения [уровней обслуживания](../../container-registry/container-registry-skus.md)Basic, Standard и Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Ограничения сети доставки контента

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Ограничения фабрики данных

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Ограничения Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Ограничения Data Lake Store

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Ограничения дообмена данными

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Ограничения Database Migration Service

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Ограничения сетки событий

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Ограничения Центров событий

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Ограничения диспетчера идентификации

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>Центральные границы IoT
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Пределы для Центра Интернета вещей

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Ограничения службы подготовки устройств для Центра Интернета вещей

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Ограничения хранилища ключей

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Ограничения служб мультимедиа

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Службы мультимедиа версии 2 (прежняя версия)

Для ограничений, характерных для Медиа Услуги v2 (наследие), см [Media Services v2 (наследие)](https://docs.microsoft.com/azure/media-services/previous/media-services-quotas-and-limitations)

## <a name="mobile-services-limits"></a>Ограничения мобильных служб

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Ограничения многофакторной аутентификации

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Ограничения сети

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Ограничения ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Виртуальные лимиты WAN

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Ограничения шлюза приложений

Сведения в следующей таблице относится к номерам SKU версии 1, версии 2, ценовой категории "Стандартный" и WAF, если не указано иное.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Пределы наблюдателя за сетями

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Ограничения для Приватного канала

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Ограничения диспетчера трафика

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Ограничения Лазурного бастиона

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Ограничения Azure DNS

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Ограничения брандмауэра Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Ограничения службы Azure Front Door Service

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Ограничения концентратов уведомлений

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Ограничения управления доступа на основе ролей

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Ограничения служебной шины

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Ограничения Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Ограничения базы данных SQL

Для ограничений базы данных S'L [см.](../../sql-database/sql-database-vcore-resource-limits-single-databases.md) [SQL Database resource limits for elastic pools and pooled databases](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md) [SQL Database resource limits for managed instances](../../sql-database/sql-database-managed-instance-resource-limits.md)

## <a name="sql-data-warehouse-limits"></a>Ограничения хранилища данных SQL

Для ограничений хранилища данных [SQL Data Warehouse resource limits](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)s'L см.

## <a name="storage-limits"></a>Ограничения хранилища

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Для получения дополнительной информации об ограничениях для стандартных учетных записей хранения [см.](../../storage/common/scalability-targets-standard-account.md)

### <a name="storage-resource-provider-limits"></a>Ограничения поставщика ресурсов хранилища

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Ограничения хранилища BLOB-объектов Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Ограничения службы "Файлы Azure"

Для получения дополнительной информации об [Azure Files scalability and performance targets](../../storage/files/storage-files-scale-targets.md)ограничениях файлов Azure см.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Ограничения службы "Синхронизация файлов Azure"

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Ограничения для службы Azure "Хранилище очередей"

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Ограничения для службы Azure "Хранилище таблиц"

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Ограничения для дисков виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Для получения дополнительной [информации см.](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="managed-virtual-machine-disks"></a>Управляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Неуправляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Ограничения системы StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Ограничения Stream Analytics

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Ограничения виртуальных машин

### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Ограничения виртуальных машин — диспетчер ресурсов Azure

Следующие ограничения применяются при использовании ресурсов Azure Manager и групп ресурсов Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Общие лимиты галереи изображений

Существуют ограничения на развертывание ресурсов с использованием общих изображений:

- 100 общих галерей изображений, за подписку, в регионе
- 1000 определений изображений, за подписку, в регионе
- 10 000 версий изображений, за подписку, на регион

## <a name="virtual-machine-scale-sets-limits"></a>Масштаб виртуальной машины устанавливает ограничения

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>См. также

* [Понимание пределов и увеличений Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Размеры виртуальных машин и облачных сервисов для Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Размеры облачных служб Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Правила и ограничения именования для ресурсов Azure](resource-name-rules.md)
