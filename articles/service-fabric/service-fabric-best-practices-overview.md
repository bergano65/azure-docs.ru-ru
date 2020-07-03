---
title: Рекомендации по приложению и кластеру Azure Service Fabric
description: Рекомендации и вопросы проектирования для управления кластерами, приложениями и службами с помощью Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551783"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Рекомендации по приложению и кластеру Azure Service Fabric

В этой статье приведены ссылки на рекомендации по управлению приложениями и кластерами Azure Service Fabric. Мы настоятельно рекомендуем реализовать эти методы для оптимизации надежности рабочей среды. Используйте один из [шаблонов кластера Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) , чтобы начать разработку рабочего решения, или обновите существующий шаблон, чтобы включить эти рекомендации.

## <a name="security"></a>Безопасность

* [Рекомендации по обеспечению безопасности](service-fabric-best-practices-security.md)

## <a name="networking"></a>Сеть

* [Рекомендации по работе в сети](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Планирование вычислений и масштабирования

* [Рекомендации по масштабированию вычислений](service-fabric-best-practices-capacity-scaling.md)
* [Планирование ресурсов компьютера](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Инфраструктура как код

* [Рекомендации по реализации инфраструктуры как кода](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика

* [Рекомендации по мониторингу и диагностике кластеров](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Проектирование приложений

* [Рекомендации по проектированию приложений](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Контрольный список

После реализации рекомендаций, предлагаемых в предыдущих разделах, убедитесь, что вы реализовали все рекомендации в контрольном списке готовности к производству:
* [Контрольный список готовности к производству Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание кластера Azure Service Fabric в локальной или облачной средах](service-fabric-cluster-creation-for-windows-server.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Linux](service-fabric-cluster-creation-via-portal.md).
* Устранение неполадок Service Fabric: [руководства по устранению неполадок](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)