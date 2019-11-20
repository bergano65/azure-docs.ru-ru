---
title: Ограничения и квоты подписки Azure
description: В этой статье приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, квот и границ. Эта статья содержит сведения о том, как увеличить пределы и максимальное значение.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: azure-policy
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: b6ce20196b6ffdcd6699611998260dc981cc4f44
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185043"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Подписка Azure, границы, квоты и ограничения службы
В этом документе указаны некоторые из наиболее распространенных ограничений Microsoft Azure, которые иногда называются квотами. Этот документ на текущий момент охватывает не все службы Azure. Со временем список будет развернут и обновлен, чтобы охватить больше служб.

Дополнительные сведения о ценах Azure см. в статье [Общие сведения о ценах на Azure](https://azure.microsoft.com/pricing/). Здесь вы можете оценить затраты с помощью [калькулятора цен](https://azure.microsoft.com/pricing/calculator/). Вы также можете посетить страницу сведений о ценах для конкретной службы, например [виртуальных машин Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Советы по управлению затратами приведены в статье [Предотвращение непредвиденных расходов с помощью функции выставления счетов и управления затратами в Azure](billing/billing-getting-started.md).

> [!NOTE]
> Если вы хотите увеличить лимит или квоту выше предельного значения по умолчанию, отправьте [запрос в службу поддержки клиентов через Интернет](azure-resource-manager/resource-manager-quota-errors.md)бесплатно. Ограничения не могут быть вызваны над максимальным предельным значением, показанным в следующих таблицах. Если столбец максимального ограничения отсутствует, ресурс не имеет регулируемых ограничений.
>
> [Бесплатные пробные подписки](https://azure.microsoft.com/offers/ms-azr-0044p) не подходят для увеличения лимита или квоты. При наличии [бесплатной пробной версии подписки](https://azure.microsoft.com/offers/ms-azr-0044p) ее можно обновить до подписки [с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/). Дополнительные сведения см. в статье [Обновление бесплатной пробной подписки Azure до подписки с оплатой по мере использования](billing/billing-upgrade-azure-subscription.md) и [бесплатной пробной подписки](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Ограничения и Azure Resource Manager
Теперь можно объединить несколько ресурсов Azure в одну группу ресурсов Azure. При использовании групп ресурсов ограничения, которые однажды были глобальными, становятся управляемыми на региональном уровне с Azure Resource Manager. Дополнительные сведения о группах ресурсов Azure см. в разделе [Azure Resource Manager обзор](azure-resource-manager/resource-group-overview.md).

В следующем списке ограничений в новой таблице отражены все различия в ограничениях при использовании Azure Resource Manager. Например, таблица **ограничивается подпиской** на подписку, а **Azure Resource Manager** таблицей. Если ограничение применяется к обоим сценариям, оно отображается только в первой таблице. Если не указано иное, ограничения глобальны во всех областях.

> [!NOTE]
> Квоты для ресурсов в группах ресурсов Azure доступны для каждого региона, а не для подписки, так как квоты на управление службами. В качестве примера используем квоты виртуальных процессоров. Чтобы запросить увеличение квоты с поддержкой виртуальных ЦП, необходимо решить, сколько виртуальных ЦП вы хотите использовать в разных регионах. Затем вы выполните Специальный запрос квоты виртуальных ЦП для группы ресурсов Azure для нужных вам объемов и регионов. Если вам нужно использовать 30 виртуальных ЦП в Западной Европе для запуска приложения, вы запрашиваете 30 виртуальных ЦП в Западной Европе. Квота виртуальных ЦП не увеличивается в любом другом регионе. в Западной Европе есть 30-виртуальных цпная квота.
> <!-- -->
> В результате определите, какие квоты группы ресурсов Azure должны быть для вашей рабочей нагрузки в одном регионе. Затем запросите эту сумму в каждом регионе, в котором требуется выполнить развертывание. Сведения о том, как определить текущие квоты для конкретных регионов, см. в разделе [Устранение неполадок развертывания](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Ограничения определенных служб
* [Active Directory](#active-directory-limits)
* [Управление API](#api-management-limits)
* [Служба приложений](#app-service-limits)
* [Шлюз приложений](#application-gateway-limits)
* [Служба автоматизации](#automation-limits)
* [Кэш Azure для Redis](#azure-cache-for-redis-limits)
* [Облачные службы Azure](#azure-cloud-services-limits)
* [Когнитивный поиск Azure](#azure-cognitive-search-limits)
* [Azure Cognitive Services](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [База данных Azure для MySQL](#azure-database-for-mysql)
* [База данных Azure для PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Брандмауэр Azure](#azure-firewall-limits)
* [Функции Azure](#functions-limits)
* [Служба Azure Kubernetes (AKS)](#azure-kubernetes-service-limits)
* [машинное обучение Azure](#azure-machine-learning-limits)
* [служба "Карты Azure"](#azure-maps-limits);
* [Azure Monitor](#azure-monitor-limits)
* [Политика Azure](#azure-policy-limits)
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
* [Служба "Передняя дверь"](#azure-front-door-service-limits)
* [Диспетчер удостоверений](#identity-manager-limits)
* [Центр Интернета вещей](#iot-hub-limits)
* [Служба подготовки устройств для Центра Интернета вещей](#iot-hub-device-provisioning-service-limits)
* [хранилище ключей;](#key-vault-limits)
* [Службы мультимедиа](#media-services-limits)
* [Мобильные службы](#mobile-services-limits)
* [Многофакторная идентификация](#multi-factor-authentication-limits)
* [Сеть](#networking-limits)
  * [Шлюз приложений](#application-gateway-limits)
  * [Azure бастиона](#azure-bastion-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Брандмауэр Azure](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Балансировщик нагрузки](#load-balancer)
  * [Наблюдатель за сетями](#network-watcher-limits)
  * [Общедоступный IP-адрес](#publicip-address)
  * [Приватный канал](#private-link-limits)
  * [Диспетчер трафика](#traffic-manager-limits)
  * [Виртуальная сеть](#networking-limits)
  * [Виртуальная глобальная сеть](#virtual-wan-limits)
* [Центры уведомлений](#notification-hubs-limits)
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
* [Виртуальные машины](#virtual-machines-limits)
* [Масштабируемые наборы виртуальных машин](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Ограничения подписки
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Ограничения подписки — Управление службами Azure (классическая модель развертывания)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Ограничения подписки — Azure Resource Manager
При использовании Azure Resource Manager и групп ресурсов Azure действуют следующие ограничения. Ограничения, которые не были изменены Azure Resource Manager, отсутствуют в списке. Эти ограничения приведены в предыдущей таблице.

Сведения об ограничениях на операции записи и чтения API Resource Manager см. в разделе [Регулирование запросов Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Ограничения группы ресурсов
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин
#### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Ограничения виртуальных машин — диспетчер ресурсов Azure
При использовании Azure Resource Manager и групп ресурсов Azure действуют следующие ограничения.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Ограничения общей коллекции образов

Существуют ограничения на подписку для развертывания ресурсов с помощью общих коллекций образов.
- 100 коллекций общих образов на подписку для каждого региона
- 1 000 определений образов для каждой подписки на регион
- 10 000. версии образов на подписку на регион

### <a name="virtual-machine-scale-sets-limits"></a>Ограничения для масштабируемых наборов виртуальных машин
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Ограничения для экземпляров контейнеров Azure
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Ограничения реестра контейнера
В следующей таблице приведены сведения о возможностях и ограничениях [уровней служб](./container-registry/container-registry-skus.md) "Базовый", "Стандартный" и "Премиум".

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Ограничения службы Kubernetes Azure
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-limits"></a>Ограничения Машинное обучение Azure
Последние значения квоты на Машинное обучение Azure вычислений можно найти на [странице квота машинное обучение Azure](../articles/machine-learning/service/how-to-manage-quotas.md) .

### <a name="networking-limits"></a>Ограничения сети
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Ограничения ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="virtual-wan-limits"></a>Ограничения виртуальной глобальной сети
[!INCLUDE [virtual-wan-limits](../includes/virtual-wan-limits.md)]

#### <a name="application-gateway-limits"></a>Ограничения шлюза приложений

Сведения в следующей таблице относится к номерам SKU версии 1, версии 2, ценовой категории "Стандартный" и WAF, если не указано иное.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Пределы наблюдателя за сетями
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Ограничения для частных ссылок
[!INCLUDE [private-link-limits](../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Ограничения диспетчера трафика
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-bastion-limits"></a>Ограничения Azure бастиона
[!INCLUDE [Azure Bastion limits](../includes/bastion-limits.md)]

#### <a name="azure-dns-limits"></a>Ограничения Azure DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Ограничения брандмауэра Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Ограничения службы Azure Front Door Service
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Ограничения хранилища
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Дополнительные сведения об ограничениях учетной записи хранения см. в статье [целевые показатели масштабируемости и производительности службы хранилища Azure](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Ограничения поставщика ресурсов хранилища

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Ограничения хранилища BLOB-объектов Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Ограничения службы "Файлы Azure"
Дополнительные сведения об ограничениях для файлов Azure см. в статье [целевые показатели масштабируемости и производительности службы файлов Azure](storage/files/storage-files-scale-targets.md).

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

Дополнительные сведения см. в статье [размеры виртуальных машин](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Управляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Неуправляемые диски виртуальной машины

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Ограничения облачных служб Azure
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Ограничения Cognitive Services Azure
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>Ограничения службы приложений
Следующие ограничения Службы приложения включают ограничения для веб-приложений, мобильных приложений, приложений API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Ограничения функций
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Ограничения планировщика
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Ограничения пакета
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Ограничения служб BizTalk
В следующей таблице показаны ограничения для служб BizTalk Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Ограничения Azure Cosmos DB
Ограничения Azure Cosmos DB см. [в разделе ограничения в Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>База данных Azure для MySQL
Дополнительные сведения об ограничениях базы данных Azure для MySQL см. в статье [Ограничения базы данных Azure для MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>База данных Azure для PostgreSQL
Сведения об ограничениях базы данных Azure для PostgreSQL см. в статье [Ограничения базы данных Azure для PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-cognitive-search-limits"></a>Ограничения Когнитивный поиск Azure
Ценовые категории определяют емкость и ограничения службы поиска. Существуют следующие категории:

* **Бесплатная** многоклиентская служба, совместно используемая с другими подписчиками Azure, предназначена для оценки и небольших проектов разработки.
* **Базовый** предоставляет выделенные вычислительные ресурсы для небольших рабочих нагрузок в рабочей среде, поддерживая до трех реплик для обработки запросов с высоким уровнем доступности.
* **Стандартная**, включающая высокую плотность S1, S2, S3 и S3, предназначена для больших рабочих нагрузок. На уровне Standard существует несколько уровней, чтобы можно было выбрать конфигурацию ресурсов, которая лучше соответствует профилю рабочей нагрузки.

**Ограничения на одну подписку**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Ограничения на одну службу поиска**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Дополнительные сведения об ограничениях на более детализированном уровне, таких как размер документа, количество запросов в секунду, ключи, запросы и ответы, см. [в статье ограничения служб в Azure когнитивный Поиск](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Ограничения служб мультимедиа
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Ограничения сети доставки содержимого
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Ограничения мобильных служб
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Ограничения Azure Monitor

#### <a name="alerts"></a>Оповещения

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Группы действий

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Ведение журнала запросов и языка

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Рабочие области Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




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

### <a name="backup-limits"></a>Ограничения резервного копирования
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Ограничения службы Azure SignalR
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Ограничения Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Ограничения управления API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Ограничения кэша Azure для Redis
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Ограничения хранилища ключей
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Ограничения многофакторной проверки подлинности
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Ограничения автоматизации
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Ограничения диспетчера удостоверений
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Ограничения управления доступа на основе ролей
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Ограничения базы данных SQL
Ограничения для базы данных SQL см. в разделе [ограничения ресурсов базы данных SQL для отдельных баз данных](sql-database/sql-database-vcore-resource-limits-single-databases.md), [ограничения ресурсов базы данных SQL для эластичных пулов и баз данных](sql-database/sql-database-vcore-resource-limits-elastic-pools.md)в составе пула, а [ограничения ресурсов базы данных SQL для управляемых экземпляров](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Ограничения хранилища данных SQL
Ограничения для хранилища данных SQL см. в статье [ограничения ресурсов хранилища данных](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)SQL.

## <a name="see-also"></a>См. также
- [Общие сведения об ограничениях и росте Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Размеры виртуальных машин и облачных служб для Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Размеры для облачных служб Azure](cloud-services/cloud-services-sizes-specs.md)
