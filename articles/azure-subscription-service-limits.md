---
title: Ограничения и квоты подписки Azure
description: В этой статье приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, квот и границ. Эта статья содержит сведения о том, как увеличить лимиты и максимальные значения.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 04/19/2019
ms.author: byvinyal
ms.openlocfilehash: 8d7fa7bbb501675ee38ccc9183e981ce7bb714cf
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150353"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Подписка Azure, границы, квоты и ограничения службы
В этом документе указаны некоторые из наиболее распространенных ограничений Microsoft Azure, которые иногда называются квотами. Этот документ на текущий момент охватывает не все службы Azure. Со временем список будет расширен и обновлен, чтобы охватить больше служб.

Дополнительные сведения о ценах на Azure, см. в разделе [Azure, общие сведения о ценах](https://azure.microsoft.com/pricing/). Там вы сможете оценить затраты с помощью [калькулятором цен](https://azure.microsoft.com/pricing/calculator/). Вы также можете перейти к странице с ценами для конкретной службы, например, [виртуальных машин Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Советы по управлению затратами приведены в статье [Предотвращение непредвиденных расходов с помощью функции выставления счетов и управления затратами в Azure](billing/billing-getting-started.md).

> [!NOTE]
> Если вы хотите вызвать ограничение или квоту выше ограничение по умолчанию, [откройте запрос в службу поддержки бесплатно](azure-resource-manager/resource-manager-quota-errors.md). Ограничения не могут быть увеличены выше максимальное значение, показанное в следующих таблицах. Если столбец не максимальный предел, ресурс не имеет настраиваемого ограничения.
>
> [Бесплатная пробная версия подписки](https://azure.microsoft.com/offers/ms-azr-0044p) не предусмотрена возможность увеличения ограничения и квоты. При наличии [бесплатной пробной версии подписки](https://azure.microsoft.com/offers/ms-azr-0044p) ее можно обновить до подписки [с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/). Дополнительные сведения см. в разделе [обновление бесплатной пробной версии Azure подписки на подписку с оплатой по мере использования](billing/billing-upgrade-azure-subscription.md) и [бесплатную пробную версию подписки часто задаваемые вопросы о](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Ограничения и диспетчер ресурсов Azure
Теперь можно объединить несколько ресурсов Azure в одной группы ресурсов Azure. При использовании групп ресурсов ограничения, которые были глобальными, становятся управляемыми на региональном уровне с помощью Azure Resource Manager. Дополнительные сведения о группах ресурсов Azure см. в разделе [обзоре Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

В следующем списке ограничений новая таблица отражает все различия ограничений при использовании Azure Resource Manager. Например, имеется **ограничения подписки** таблицы и **ограничения подписки — Azure Resource Manager** таблицы. Если ограничение применяется в обоих случаях, он отображается только в первой таблице. Если не указано иное, ограничения глобальны во всех областях.

> [!NOTE]
> Квоты для ресурсов в группах ресурсов Azure доступны для каждого региона по подписке, а не каждой подписки как квоты управления службами. В качестве примера используем квоты виртуальных процессоров. Чтобы запросить увеличение квоты с поддержкой виртуальных процессоров, необходимо решить, сколько виртуальных процессоров, будет использоваться в каких регионах. Затем отправить запрос на квоты виртуальных процессоров группы ресурсов Azure для суммы и областей, которые должны. Если необходимо использовать 30 виртуальных процессоров в Западной Европе для запуска приложения, специально запрашивать 30 виртуальных процессоров в Западной Европе. В любом другом регионе — не увеличить квоту на виртуальные ЦП, только Западной Европе имеет квоты 30 виртуальных процессоров.
> <!-- -->
> Таким образом решите, каким вашей квоты группы ресурсов Azure необходимо для вашей рабочей нагрузки в любой из одного региона. Затем запросите эти ресурсы в каждом регионе, в которую вы хотите развернуть. Помощь в определении текущих квот для конкретных регионов см. в разделе [Устранение неполадок в развертывании](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Ограничения определенных служб
* [Active Directory](#active-directory-limits)
* [Управление API](#api-management-limits)
* [Служба приложений](#app-service-limits)
* [Шлюз приложений](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Автоматизация](#automation-limits)
* [Кэш Azure для Redis](#azure-cache-for-redis-limits)
* [Облачные службы Azure](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [База данных Azure для MySQL](#azure-database-for-mysql)
* [База данных Azure для PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Брандмауэр Azure](#azure-firewall-limits)
* [Служба Azure Kubernetes (AKS)](#azure-kubernetes-service-limits)
* [служба "Карты Azure"](#azure-maps-limits);
* [Azure Monitor](#monitor-limits)
* [Политика Azure](#azure-policy-limits)
* [Поиск Azure;](#azure-search-limits)
* [Служба Azure SignalR](#azure-signalr-service-limits)
* [Архивация](#backup-limits)
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
* [Входной двери службы](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [Центр Интернета вещей](#iot-hub-limits)
* [Служба подготовки устройств для Центра Интернета вещей](#iot-hub-device-provisioning-service-limits)
* [хранилище ключей;](#key-vault-limits)
* [Балансировщик нагрузки](#load-balancer)
* [Служба Log Analytics](#log-analytics-limits)
* [Службы мультимедиа](#media-services-limits)
* [Мобильные службы](#mobile-services-limits)
* [Многофакторная идентификация](#multi-factor-authentication-limits)
* [Сеть](#networking-limits)
* [Наблюдатель за сетями](#network-watcher-limits)
* [Центры уведомлений](#notification-hubs-limits)
* [Общедоступный IP-адрес](#publicip-address)
* [Группа ресурсов](#resource-group-limits)
* [Контроль доступа на основе ролей](#role-based-access-control-limits)
* [Планировщик](#scheduler-limits)
* [Служебная шина](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [База данных SQL](#sql-database-limits)
* [Хранилище данных SQL](#sql-data-warehouse-limits)
* [Хранилище](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Анализ потока](#stream-analytics-limits)
* [Подписка](#subscription-limits)
* [Диспетчер трафика](#traffic-manager-limits)
* [Виртуальные машины](#virtual-machines-limits)
* [Масштабируемые наборы виртуальных машин](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Ограничения подписки
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Ограничения подписки — управление службами Azure (Классическая модель развертывания)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Ограничения подписки — Azure Resource Manager
При использовании Azure Resource Manager и групп ресурсов Azure, применяются следующие ограничения. Не указаны ограничения, которые не были изменены с помощью Azure Resource Manager. См. в предыдущей таблице эти ограничения.

Сведения об ограничениях на операции записи и чтения API Resource Manager см. в разделе [Регулирование запросов Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Ограничения группы ресурсов
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин
#### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Ограничения виртуальных машин — диспетчер ресурсов Azure
При использовании Azure Resource Manager и групп ресурсов Azure, применяются следующие ограничения. Не указаны ограничения, которые не были изменены с помощью Azure Resource Manager. См. в предыдущей таблице эти ограничения.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Общие ограничения коллекции образов

Существуют ограничения на одну подписку, для развертывания ресурсов с помощью Shared галерей изображений:
- 10 коллекций общих образов на одну подписку на один регион;
- 200 определений образов на одну подписку на один регион;
- 2000 версий образов на одну подписку на один регион.

### <a name="virtual-machine-scale-sets-limits"></a>Ограничения наборов масштабирования виртуальных машин
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Ограничения для экземпляров контейнеров Azure
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Ограничения реестра контейнера
В следующей таблице приведены сведения о возможностях и ограничениях [уровней служб](./container-registry/container-registry-skus.md) "Базовый", "Стандартный" и "Премиум".

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Ограничения службы Kubernetes Azure
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Ограничения сети
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Ограничения ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Ограничения шлюза приложений

Сведения в следующей таблице относится к номерам SKU версии 1, версии 2, ценовой категории "Стандартный" и WAF, если не указано иное.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Пределы наблюдателя за сетями
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Ограничения диспетчера трафика
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Ограничения Azure DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Ограничения брандмауэра Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Ограничения службы Azure Front Door Service
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Ограничения хранилища
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Дополнительные сведения об ограничениях учетных записей хранения см. в разделе [целевые показатели масштабируемости и производительности службы хранилища Azure](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Ограничения поставщика ресурсов хранилища 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Ограничения хранилища BLOB-объектов Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Ограничения службы "Файлы Azure"
Дополнительные сведения об ограничениях службы файлов Azure см. в разделе [целевые показатели масштабируемости и производительности службы файлов Azure](storage/files/storage-files-scale-targets.md).

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

Дополнительные сведения см. в разделе [размеры виртуальных машин](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Управляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Неуправляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Ограничения облачных служб Azure
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Ограничения службы приложений
Следующие ограничения Службы приложения включают ограничения для веб-приложений, мобильных приложений, приложений API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Ограничения планировщика
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Ограничения пакета
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Ограничения служб BizTalk
В следующей таблице показаны ограничения для служб BizTalk Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Ограничения Azure Cosmos DB
Azure Cosmos DB — это глобальная база данных, пропускную способность и хранилище которой можно масштабировать в соответствии с требованиями приложения. Если у вас есть вопросы по масштабированию Azure Cosmos DB, отправьте сообщение по адресу askcosmosdb@microsoft.com.

### <a name="azure-database-for-mysql"></a>База данных Azure для MySQL
Дополнительные сведения об ограничениях базы данных Azure для MySQL см. в статье [Ограничения базы данных Azure для MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>База данных Azure для PostgreSQL
Сведения об ограничениях базы данных Azure для PostgreSQL см. в статье [Ограничения базы данных Azure для PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Поиск Azure ограничивает
Ценовые категории определяют емкость и ограничения службы поиска. Существуют следующие категории:

* **Бесплатный** мультитенантная служба, совместно с другими подписчиками Azure и предназначен для оценки и разработки небольших проектов.
* **Базовый** предоставляет выделенные вычислительные ресурсы для небольших рабочих нагрузок в рабочей среде, поддерживая до трех реплик для обработки запросов с высоким уровнем доступности.
* **Стандартный**, включающее S1, S2 и S3 и S3 высокой плотности, предназначен для более ресурсоемкие рабочие нагрузки. Существует несколько уровней в рамках уровня "стандартный", в котором можно выбрать конфигурацию ресурсов, которая лучше всего подходит для вашего профиля рабочей нагрузки.

**Ограничения на одну подписку**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Ограничения на одну службу поиска**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Дополнительные сведения об ограничениях, включая размер документов, количество запросов в секунду, ключи, запросы и ответы, см. в статье [Ограничения поиска Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Ограничения служб мультимедиа
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Ограничивает сети доставки содержимого
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Ограничения мобильных служб
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Пределы монитора
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hubs-limits"></a>Ограничения концентраторов уведомлений
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

### <a name="event-grid-limits"></a>Ограничения сетки событий
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Ограничения службы "Карты Azure"
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Ограничения политики Azure
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Ограничения системы StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Ограничения Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Ограничения резервного копирования
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Ограничивает служба Azure SignalR
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Ограничения Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Ограничения Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Ограничения управления API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Ограничения кэша Azure для Redis
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Ограничения хранилища ключей
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Ограничивает многофакторной проверки подлинности
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Ограничения автоматизации
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Ограничения Identity Manager
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Ограничения управления доступа на основе ролей
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Ограничения базы данных SQL
Ограничения базы данных SQL, см. в разделе [ограничения ресурсов базы данных SQL для отдельных баз данных](sql-database/sql-database-vcore-resource-limits-single-databases.md), [ограничения ресурсов базы данных SQL для эластичных пулов и базы данных в составе](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), и [ограничения ресурсов базы данных SQL для управляемых экземпляров](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Ограничения хранилища данных SQL
Ограничения хранилища данных SQL, см. в разделе [ограничения ресурсов хранилища данных SQL](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>См. также
- [Ознакомьтесь с Azure ограничениями и увеличения](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Размеры виртуальных машин и облачных служб Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Размеры для облачных служб Azure](cloud-services/cloud-services-sizes-specs.md)
