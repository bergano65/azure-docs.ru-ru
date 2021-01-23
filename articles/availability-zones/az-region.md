---
title: Службы Azure с поддержкой Зон доступности
description: Для создания высокодоступного и отказоустойчивого приложения в Azure Зоны доступности предоставляет физически разделенные зоны, которые можно использовать для выполнения ресурсов.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: c63ea4f9cdb961ca492d5dcf22a89627864236cd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733206"
---
# <a name="azure-services-that-support-availability-zones"></a>Службы Azure с поддержкой Зон доступности

Зоны доступности — это предложение высокого уровня доступности, защищающее приложения и данные от сбоев центров обработки данных. Список существующих и предстоящих регионов, которые поддерживают Зоны доступности, см. [в статье регионы и зоны доступности в Azure](az-overview.md).  

В этом разделе перечислены службы Azure, которые поддерживают Зоны доступности. 

Службы, доступные в каждом регионе, а также предстоящие планы по доступности, можно найти в [продуктах, доступных по регионам](https://azure.microsoft.com/global-infrastructure/services/).

Все службы управления Azure спроектированы как устойчивые к сбоям на уровне региона. В спектре сбоев один или несколько сбоев зоны доступности в регионе имеют меньший радиус сбоя по сравнению со всем сбоем региона. Azure может выполнить восстановление после сбоя на уровне зоны для служб управления в пределах региона. Azure выполняет критическое обслуживание по одной зоне за раз в пределах региона, чтобы предотвратить сбои, влияющие на ресурсы клиента, развернутые в Зоны доступности в пределах региона.


![концептуальное представление региона Azure с тремя зонами](./media/az-region/azure-region-availability-zones.png)


Службы Azure, поддерживающие Зоны доступности, делятся на три категории: **зональные**, **избыточные в зонах** и **не региональные** службы. Рабочие нагрузки клиентов можно классифицировать для использования любого из этих сценариев архитектуры в соответствии с производительностью и устойчивостью приложений.

- **Зональные Services** — ресурс можно развернуть в определенной, самостоятельно выбираемой зоне доступности для достижения более строгой задержки или требований к производительности.  Отказоустойчивость — это самостоятельная репликация приложений и данных в одну или несколько зон в пределах региона.  Ресурсы можно закреплять в определенной зоне. Например, виртуальные машины, управляемые диски или стандартные IP-адреса могут быть закреплены в определенной зоне, что обеспечивает повышенную устойчивость путем использования одного или нескольких экземпляров ресурсов, распределенных между зонами.

- **Службы, избыточные** в пределах зоны — Платформа Azure реплицирует ресурсы и данные между зонами.  Корпорация Майкрософт управляет доставкой высокого уровня доступности, так как Azure автоматически реплицирует и распространяет экземпляры в пределах региона.  ZRS, например, реплицирует данные по трем зонам, чтобы сбой зоны не влиял на высокую доступность данных. 

- **Службы, не являющиеся региональными** , — это службы, не зависящие от конкретного региона Azure, что делает их устойчивыми к простоям в пределах зоны, а также по различным регионам.


Чтобы обеспечить всестороннюю непрерывность бизнес-процессов в Azure, создайте архитектуру приложений, используя сочетание зон доступности с парами регионов Azure. Можно синхронно реплицировать приложения и данные с помощью зон доступности в регионе Azure, чтобы обеспечить высокий уровень доступности, и асинхронно реплицировать их между регионами Azure, чтобы обеспечить защиту с помощью аварийного восстановления. Дополнительные сведения см. в статье [Создание решений для обеспечения высокой доступности с помощью зоны доступности](/azure/architecture/high-availability/building-solutions-for-high-availability). 


### <a name="azure-services-supporting-availability-zones"></a>Службы Azure, поддерживающие Зоны доступности

- Виртуальные машины старого поколения не перечислены ниже. Дополнительные сведения см. в статье [предыдущие поколения размеров виртуальных машин](../virtual-machines/sizes-previous-gen.md).

- Некоторые службы не являются региональными. Дополнительные сведения см. [в разделе регионы и зоны доступности в Azure](az-overview.md) . Эти службы не имеют зависимости от конкретного региона Azure, что делает их устойчивыми к простоям на уровне зоны и по всему региону.  Список нерегиональный службы можно найти в списке [продуктов, доступных по регионам](https://azure.microsoft.com/global-infrastructure/services/).



## <a name="americas"></a>Северная и Южная Америка

| **Продукты** | **Центральная часть США** | **Восточная часть США** | **Восточная часть США 2** | **Западная часть США 2** | **Центральная Канада** |
|--|--|--|--|--|--|
| **Compute** |  |  |  |  |  |
| [Среды службы приложений (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальные машины](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Контейнеры** |  |  |  |
| [Служба Azure Kubernetes (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Реестр контейнеров](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Хранилище** |  |  |  |  |  |
| [Azure Data Lake Storage 2-го поколения](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Хранилище файлов Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Хранилище BLOB-объектов](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Управляемые диски](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Сетевое взаимодействие** |  |  |  |  |  |
| [Шлюз приложений версии 2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Брандмауэр Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Стандартный IP-адрес](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальная сеть](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT виртуальной сети](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальная глобальная сеть](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN-шлюз](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Базы данных** |  |  |  |  |  |
| [Кэш Azure для Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [База данных Azure для MySQL — гибкий сервер](../mysql/flexible-server/concepts-high-availability.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Гибкий сервер Базы данных Azure для PostgreSQL](../postgresql/flexible-server/overview.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [База данных SQL Azure (уровень общего назначения)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | : heavy_check_mark: (Предварительная версия) | : heavy_check_mark: (Предварительная версия) | : heavy_check_mark: (Предварительная версия) | :x: |
| [База данных SQL Azure (уровни критически важный для бизнеса Premium &)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Аналитика** |  |  |  |  |  |
| [Центры событий](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Интеграция** |  |  |  |  |  |
| [Сетка событий](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Служебная шина](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Управление** |  |  |  |  |  |
| [Наблюдатель за сетями](../network-watcher/frequently-asked-questions.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| **Безопасность** |  |  |  |  |  |
| [Доменные службы Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Европа

| **Продукты** | **Центральная Франция** | **Северная Европа** | **южная часть Соединенного Королевства** | **Западная Европа** |
|--|--|--|--|--|
| **Compute** |  |  |  |  |
| [Среды службы приложений (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Служба Azure Kubernetes (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальные машины](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Хранилище** |  |  |  |  |
| [Azure Data Lake Storage 2-го поколения](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Хранилище файлов Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Хранилище BLOB-объектов](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Управляемые диски](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Сетевое взаимодействие** |  |  |  |  |
| [Шлюз приложений версии 2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Брандмауэр Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Стандартный IP-адрес](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальная сеть](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT виртуальной сети](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальная глобальная сеть](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN-шлюз](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Базы данных** |  |  |  |  |
| [Кэш Azure для Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [База данных Azure для MySQL — гибкий сервер](../mysql/flexible-server/concepts-high-availability.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Гибкий сервер Базы данных Azure для PostgreSQL](../postgresql/flexible-server/overview.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [База данных SQL Azure (уровень общего назначения)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | : heavy_check_mark: (Предварительная версия) | : heavy_check_mark: (Предварительная версия) | :x: | : heavy_check_mark: (Предварительная версия) |
| [База данных SQL Azure (уровни критически важный для бизнеса Premium &)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Аналитика** |  |  |  |  |
| [Центры событий](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Интеграция** |  |  |  |  |
| [Сетка событий](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Служебная шина](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Управление** |  |  |  |  |
| [Наблюдатель за сетями](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| **Безопасность** |  |  |  |  |
| [Доменные службы Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Азиатско-Тихоокеанский регион



| **Продукты** | **Восточная Япония** | **Юго-Восточная Азия** | **Восточная Австралия** |
|--|--|--|--|
| **Compute** |  |  |  |
| [Среды службы приложений (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Служба Azure Kubernetes (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальные машины](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Хранилище** |  |  |  |
| [Azure Data Lake Storage 2-го поколения](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Хранилище файлов Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Хранилище BLOB-объектов](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Управляемые диски](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Сетевое взаимодействие** |  |  |  |
| [Шлюз приложений версии 2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Брандмауэр Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Стандартный IP-адрес](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальная сеть](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT виртуальной сети](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Виртуальная глобальная сеть](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN-шлюз](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Базы данных** |  |  |  |
| [Кэш Azure для Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [База данных Azure для MySQL — гибкий сервер](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Гибкий сервер Базы данных Azure для PostgreSQL](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [База данных SQL Azure (уровень общего назначения)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | : heavy_check_mark: (Предварительная версия) | : heavy_check_mark: (Предварительная версия) | : heavy_check_mark: (Предварительная версия) |
| [База данных SQL Azure (уровни критически важный для бизнеса Premium &)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Аналитика** |  |  |  |
| [Центры событий](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Интеграция** |  |  |  |
| [Сетка событий](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Служебная шина](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Управление** |  |  |  |
| [Наблюдатель за сетями](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :x: | :x: |
| **Безопасность** |  |  |  |
| [Доменные службы Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>Предстоящие Зоны доступности 

Azure предлагает поддержку Зоны доступности в следующих регионах:
- US Gov (Вирджиния)
- Северная часть ЮАР;
- Центрально-южная часть США
- Центрально-Западная Германия

Список существующих и будущих регионов, которые поддерживают Зоны доступности, можно найти [здесь](https://azure.microsoft.com/global-infrastructure/geographies/).    

Чтобы узнать больше о поддержке Зоны доступности в этих регионах, обратитесь к специалисту по продажам или сотруднику корпорации Майкрософт.


## <a name="pricing-for-vms-in-availability-zones"></a>Цены на виртуальные машины в Зоны доступности

За виртуальные машины, развернутые в зоне доступности, не взимается дополнительная плата. Дополнительные сведения см. на [странице с ценами на пропускную способность](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Приступая к работе с зонами доступности

- [Создание виртуальной машины](../virtual-machines/windows/create-portal-availability-zone.md)
- [Добавление управляемого диска с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Создание избыточного между зонами масштабируемого набора виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Распределение нагрузки виртуальных машин по зонам с помощью службы Load Balancer уровня "Стандартный" с избыточным между зонами внешним интерфейсом](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard).
- [Распределение нагрузки виртуальных машин в пределах зоны с помощью Load Balancer уровня "Стандартный" с зональным внешним интерфейсом](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard).
- [Хранилище, избыточное между зонами](../storage/common/storage-redundancy.md)
- [Уровень общего назначения базы данных SQL](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Географическое аварийное восстановление в Центрах событий](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Географическое аварийное восстановление в служебной шине](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Создание шлюза виртуальной сети, избыточного между зонами](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Добавление области, избыточной в пределах зоны, для Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [начало работы кэша Azure для Redis Зоны доступности](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Создание экземпляра доменных служб Azure Active Directory](../active-directory-domain-services/tutorial-create-instance.md)
- [Создание кластера Azure Kubernetes Service (AKS), использующего Зоны доступности](../aks/availability-zones.md)


## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Регионы и зоны доступности в Azure](az-overview.md)