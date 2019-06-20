---
title: Рекомендации по приложению и кластеру Azure Service Fabric | Документация Майкрософт
description: Рекомендации по управлению кластерами и приложениями Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206800"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Рекомендации по приложению и кластеру Azure Service Fabric

В этой статье ссылки на лучшие решения по управлению приложениями Azure Service Fabric и кластерами. Мы настоятельно рекомендуем реализовать эти методы для обеспечения надежности рабочей среды. Используйте один из [шаблоны кластера Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) приступить к проектированию решения производства или обновить существующий шаблон для включения этих рекомендаций.

## <a name="security"></a>Безопасность

* [Рекомендации по безопасности](service-fabric-best-practices-security.md)

## <a name="networking"></a>Сеть

* [Рекомендации по работе в сети](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Планирование вычислений и масштабирования

* [Рекомендации по масштабированию вычислений](service-fabric-best-practices-capacity-scaling.md)
* [Планирование ресурсов компьютера](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Инфраструктура как код

* [Рекомендации по реализации инфраструктуры как кода](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика.

* [Рекомендации по мониторингу и диагностике кластеров](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Проектирование приложений

* [Рекомендации по разработке приложения](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Контрольный список

После реализации рекомендации, предлагаемые в предыдущих разделах, убедитесь, что Вы интегрировали все рекомендации в контрольном списке готовности рабочей:
* [Контрольный список для готовности рабочей среде Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md)
* Устранение неполадок Service Fabric: [Руководства по устранению неполадок](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)