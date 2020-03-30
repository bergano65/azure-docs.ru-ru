---
title: Рекомендации по приложению и кластеру Azure Service Fabric
description: Рекомендации по управлению кластерами, приложениями и службами с помощью Службы Azure Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551783"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Рекомендации по приложению и кластеру Azure Service Fabric

В этой статье содержатся ссылки на лучшие практики управления приложениями и кластерами Службы обслуживания Azure. Мы настоятельно рекомендуем вам внедрить эти методы для оптимизации надежности производственной среды. Используйте один из [шаблонов кластера Service Fabric,](https://github.com/Azure-Samples/service-fabric-cluster-templates) чтобы начать проектирование производственного решения или обновить существующий шаблон, чтобы включить эти методы.

## <a name="security"></a>Безопасность

* [Рекомендации по безопасности](service-fabric-best-practices-security.md)

## <a name="networking"></a>Сети

* [Рекомендации по работе в сети](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Планирование вычислений и масштабирования

* [Рекомендации по масштабированию вычислений](service-fabric-best-practices-capacity-scaling.md)
* [Планирование ресурсов компьютера](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Инфраструктура как код

* [Рекомендации по реализации инфраструктуры как кода](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика

* [Рекомендации по мониторингу и диагностике кластеров](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Проектирование приложений

* [Рекомендации по разработке приложений](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Контрольный список

После внедрения практик, предложенных в предыдущих разделах, убедитесь, что вы интегрировали все лучшие практики в контрольный список производственной готовности:
* [Контрольный список производственной готовности службы Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание кластера Azure Service Fabric в локальной или облачной средах](service-fabric-cluster-creation-for-windows-server.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Linux](service-fabric-cluster-creation-via-portal.md).
* Troubleshoot Сервис Ткань: [Устранение неполадок руководства](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)