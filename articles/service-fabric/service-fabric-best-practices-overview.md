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
ms.date: 04/24/2019
ms.author: pepogors
ms.openlocfilehash: 051d6b1129724ce4e8a67bde4e56ebe61cd832f3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231373"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Рекомендации по приложению и кластеру Azure Service Fabric

Управление приложениями Azure Service Fabric и кластеров успешно, существуют операции, которые мы настоятельно рекомендуем выполнить для оптимизации для обеспечения надежности рабочей среды; можно выполнять операции, определенные в этом документе и выберите один из наших [кластера Service Fabric Azure примеры шаблонов](https://github.com/Azure-Samples/service-fabric-cluster-templates) приступить к проектированию решения производства или изменить существующий шаблон для включения этих рекомендаций.

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

После завершения работы со всеми вышеперечисленными разделами убедитесь, что вы интегрировали все рекомендации в контрольном списке готовности рабочей среды:
* [Контрольный список готовности рабочей среды Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md)
* Устранение неполадок: [Руководство по устранению неполадок Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)