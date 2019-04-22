---
title: Хранилище событий Azure Service Fabric | Документация Майкрософт
description: Дополнительные сведения о службе EventStore Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 36d01a9e6e55ae54377ba3f983f779dbc692c49a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681528"
---
# <a name="eventstore-service-overview"></a>Общие сведения о службе EventStore

>[!NOTE]
>Начиная с версии Service Fabric 6.4 интерфейсы API EventStore доступны только для кластеров Windows, работающих в Azure. Мы работаем над переносом этих функциональных возможностей в Linux, а также в изолированные кластеры.

## <a name="overview"></a>Обзор

Введенная в версии 6.2, служба EventStore является вариантом мониторинга в Service Fabric. EventStore предоставляет способ для оценки состояния кластера или рабочих нагрузок в определенный момент времени. EventStore — это служба Service Fabric с отслеживанием состояния, сохраняющая события из кластера. Событие предоставляется через Service Fabric Explorer, REST и API. EventStore отправляет запросы непосредственно к кластеру, чтобы получить данные диагностики по любой сущности в кластере. Их следует использовать, чтобы:

* Диагностировать проблемы при разработке, тестировании или во время использования конвейера для мониторинга.
* Проверить, правильно ли обрабатываются действия управления, предпринимаемые в кластере.
* Получить моментальный снимок взаимодействия Service Fabric с конкретной сущностью.

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Полный список событий, доступных в EventStore, см. в [этом](service-fabric-diagnostics-event-generation-operational.md) разделе.

>[!NOTE]
>В версии Service Fabric 6.2 интерфейсы API EventStore находятся на этапе предварительной версии для кластеров Windows, работающих только в Azure. Мы работаем над переносом этих функциональных возможностей в Linux, а также в изолированные кластеры.

Службу EventStore можно запрашивать для событий, доступных для каждой сущности и типа сущности в кластере. Это означает, что вы можете запрашивать события на следующих уровнях:
* Кластер. События, относящиеся к кластеру (например, обновление кластера).
* Узлы. Все события уровня узла.
* Узел. События, относящиеся к одному узлу, которые идентифицируются на основе `nodeName`.
* Приложения. Все события уровня приложения.
* Приложение. События для одного приложения, идентифицируемые на основе `applicationId`.
* Службы. События из всех служб в кластерах.
* Служба. События из определенной службы, идентифицируемые на основе `serviceId`.
* Разделы. События из всех разделов.
* Раздел. События из определенного раздела, идентифицируемые на основе `partitionId`.
* Реплики разделов. События из всех реплик или экземпляров в определенной секции, идентифицируемые на основе `partitionId`.
* Реплика раздела. События из конкретной реплики или экземпляра, идентифицируемые на основе `replicaId` или `partitionId`.

Дополнительные сведения об API см. в [справочнике по API EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

Служба EventStore также имеет возможность корреляции событий в кластере. Просмотрев события, записанные в то же время из различных объектов, которые могут влиять друг на друга, служба EventStore может связать эти события, что позволит определить причины возникновения действий в кластере. Например, если одно из приложений становится неработоспособным без принудительных изменений, EventStore будет также рассматривать другие события, предоставляемые платформой, и сопоставлять их с событием `Error` или `Warning`. Это позволяет быстрее обнаружить сбой и выполнить анализ первопричин.

## <a name="enable-eventstore-on-your-cluster"></a>Включение в кластере EventStore

### <a name="local-cluster"></a>Локальный кластер

В файл [fabricSettings.json в своем кластере](service-fabric-cluster-fabric-settings.md) добавьте EventStoreService в качестве дополнительной функции и выполните обновление кластера.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster"></a>Кластер Azure

В шаблоне Azure Resource Manager кластера, можно включить службу EventStore, выполняя [обновление конфигурации кластера](service-fabric-cluster-config-upgrade-azure.md) и добавив следующий код, можно использовать ограничения размещения для размещения реплики EventStore Служба на конкретных NodeType например NodeType, предназначенный для системных служб. Раздел `upgradeDescription` настраивает обновление конфигурации, чтобы активировать перезапуск узлов. Раздел можно удалить в другом обновлении.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              }
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Дальнейшие действия
* Сведения о начале работы с API EventStore см. в статье [Выполнение запросов к интерфейсам API EventStore для получения событий кластера](service-fabric-diagnostics-eventstore-query.md).
* Дополнительные сведения о списке событий, предоставляемых EventStore, см. в статье [о событиях Service Fabric](service-fabric-diagnostics-event-generation-operational.md).
* Общие сведения о мониторинге и диагностике в Service Fabric см. в [этой статье](service-fabric-diagnostics-overview.md).
* Полный список вызовов API можно просмотреть в [справочнике по REST API EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).
* Дополнительные сведения о мониторинге кластера см. в [этой статье](service-fabric-diagnostics-event-generation-infra.md).