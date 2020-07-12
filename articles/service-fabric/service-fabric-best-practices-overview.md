---
title: Рекомендации по приложению и кластеру Azure Service Fabric
description: Рекомендации и вопросы проектирования для управления кластерами, приложениями и службами с помощью Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 86a02fd489ca0eec61b798db7136f963277f6c82
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261087"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Рекомендации по приложению и кластеру Azure Service Fabric

В этой статье приведены ссылки на рекомендации по управлению приложениями и кластерами Azure Service Fabric. Мы настоятельно рекомендуем реализовать эти методы для оптимизации надежности рабочей среды. Используйте один из [шаблонов кластера Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) , чтобы начать разработку рабочего решения, или обновите существующий шаблон, чтобы включить эти рекомендации.

## <a name="security"></a>Безопасность

* [Рекомендации по обеспечению безопасности](service-fabric-best-practices-security.md)

## <a name="networking"></a>Сеть

* [Рекомендации по работе в сети](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Планирование вычислений и масштабирования

* [Рекомендации по масштабированию вычислений](service-fabric-best-practices-capacity-scaling.md)
* [Планирование ресурсов компьютера](./service-fabric-cluster-capacity.md)

## <a name="infrastructure-as-code"></a>Инфраструктура как код

* [Рекомендации по реализации инфраструктуры как кода](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика

* [Рекомендации по мониторингу и диагностике кластеров](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Проектирование приложений

* [Рекомендации по проектированию приложений](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Контрольный список

После реализации рекомендаций, предлагаемых в предыдущих разделах, убедитесь, что вы реализовали все рекомендации в контрольном списке готовности к производству:
* [Контрольный список готовности к производству Azure Service Fabric](./service-fabric-production-readiness-checklist.md)

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание кластера Azure Service Fabric в локальной или облачной средах](service-fabric-cluster-creation-for-windows-server.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Linux](service-fabric-cluster-creation-via-portal.md).
* Устранение неполадок Service Fabric: [руководства по устранению неполадок](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
