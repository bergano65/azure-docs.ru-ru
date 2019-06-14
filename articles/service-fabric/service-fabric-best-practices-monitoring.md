---
title: Рекомендации по мониторингу в Azure Service Fabric | Документация Майкрософт
description: Рекомендации по мониторингу кластеров и приложений Service Fabric.
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
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d90daaf18e5161053e00671b7667d05ec8e5db76
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60533815"
---
# <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика.

[Мониторинг и диагностика](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) критически важны для разработки, тестирования и развертывания рабочих нагрузок в любой облачной среде. Например, вы можете отслеживать использование приложений, действия, предпринимаемые платформой Service Fabric, использование ресурсов (с помощью счетчиков производительности) и общую работоспособность кластера. Эту информацию можно использовать для диагностики и устранения проблем, а также для предотвращения их возникновения в будущем.

## <a name="application-monitoring"></a>Мониторинг приложений

В процессе мониторинга приложений фиксируются особенности использования возможностей и компонентов приложения. Следите за работой приложений, чтобы перехватить проблемы, влияющие на пользователей. Ответственность за мониторинг приложения лежит на пользователях, разрабатывающих приложение и его службы, так как он привязан к бизнес-логике приложения. Мы рекомендуем настроить мониторинг приложений с помощью [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), средства мониторинга приложений Azure.

## <a name="cluster-monitoring"></a>Мониторинг кластера

Одна из целей создания Service Fabric — сделать работу приложений устойчивой в случае сбоев оборудования. Это достигается благодаря способности системных служб платформы обнаруживать проблемы с инфраструктурой и быстро выполнять отработку отказа рабочих нагрузок на другие узлы в кластере. Но что делать, если в самих системных службах есть проблемы? Или если при попытке развертывания или перемещения рабочей нагрузки нарушаются правила размещения служб? Service Fabric предоставляет средства диагностики этих и других проблем, чтобы убедиться в том, что вы получаете уведомление о взаимодействии платформы Service Fabric с вашими приложениями, службами, контейнерами и узлами.

Для кластеров Windows, рекомендуется настроить мониторинг кластера с [агент диагностики](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) и [журналы Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Для кластеров Linux журналы Azure Monitor также является рекомендуемым приложением для платформы Azure и контроль инфраструктуры. Системы диагностики для платформы Linux требуют другую конфигурацию, записанную в статье [События кластера Service Fabric под управлением Linux в системном журнале](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Мониторинг инфраструктуры

[Журналы Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) рекомендуется использовать для мониторинга событий на уровне кластера. После настройки агента Log Analytics с рабочей областью, как описано в предыдущей статье, вы сможете собирать метрики производительности, например использование ЦП, счетчики производительности .NET, такие как использование ЦП на уровне процесса, счетчики производительности Service Fabric, например число исключений из надежной службы, а также метрики контейнера, такие как использование ЦП.  Необходимо будет записывать журналы контейнера stdout и stderr, таким образом, чтобы они были доступны в журналах Azure Monitor.

## <a name="watchdogs"></a>Модули наблюдения

Как правило, модуль наблюдения является отдельной службой, которая может отслеживать работоспособность и нагрузку в службах, проверять связь с конечными точками и сообщать о непредвиденных событиях работоспособности в кластере. Он позволяет предотвратить ошибки, которые невозможно обнаружить только на основе производительности отдельной службы. Модули наблюдения также удобны для размещения кода, выполняющего корректирующие действия, не требующие участия пользователя (например, очистку файлов журнала в хранилище через определенные интервалы времени). Пример реализации службы наблюдения см. в статье [Azure Service Fabric watchdog sample](https://github.com/Azure-Samples/service-fabric-watchdog-service) (Пример службы наблюдения Azure Service Fabric).

## <a name="next-steps"></a>Дальнейшие действия

* Приступите к инструментированию приложений: [Ведение журнала приложения](service-fabric-diagnostics-event-generation-app.md).
* Выполните процедуру настройки Application Insights для приложения, следуя указаниям в разделе [Руководство. Мониторинг и диагностика приложения ASP.NET Core в Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Узнайте о мониторинге платформы и событиях, предоставляемых Service Fabric: [Мониторинг кластера](service-fabric-diagnostics-event-generation-infra.md).
* Настройка интеграции журналов Azure Monitor с помощью Service Fabric: [Настроить журналы Azure Monitor для кластера](service-fabric-diagnostics-oms-setup.md)
* Узнайте, как настроить журналы Azure Monitor для наблюдения за контейнерами: [Руководство. Мониторинг контейнеров Windows в Service Fabric с помощью Log Analytics](service-fabric-tutorial-monitoring-wincontainers.md).
* Примеры проблем и решений диагностики с помощью Service Fabric см. в описании [диагностики распространенных сценариев](service-fabric-diagnostics-common-scenarios.md).
* Ознакомьтесь с общими рекомендациями по мониторингу ресурсов Azure в статье [Мониторинг и диагностика](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).