---
title: Список событий Azure Service Fabric | Документация Майкрософт
description: Полный список событий, предоставляемых Azure Service Fabric для мониторинга кластеров.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde0464985f756132c60453c4e79ffefd4a1dd2c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "65788600"
---
# <a name="list-of-service-fabric-events"></a>Список событий Service Fabric 

Service Fabric предоставляет основной набор событий кластера, чтобы сообщать о состоянии кластеров в виде [событий Service Fabric](service-fabric-diagnostics-events.md). Они основаны на действиях, выполненных Service Fabric на узлах и в кластерах, или решениях по управлению, принятых владельцем или оператором кластера. Эти события могут быть доступны, настроив в ряд способов, включая настройку [журналы Azure Monitor с вашим кластером](service-fabric-diagnostics-oms-setup.md), или запрос [EventStore](service-fabric-diagnostics-eventstore.md). На компьютерах Windows эти события направляются в EventLog, поэтому события Service Fabric можно просматривать в средстве "Просмотр событий". 

Ниже приведены некоторые характеристики таких событий.
* Каждое событие привязывается к определенной сущности в кластере (например, к приложению, службе, узлу, реплике).
* Каждое событие содержит набор стандартных полей: EventInstanceId, EventName и Category.
* Все события содержат поля, которые привязывают их к сущности, связанной с ними. Например, у события ApplicationCreated есть поля, которые указывают имя созданного приложения.
* События структурируются таким образом, чтобы их можно было использовать в разнообразных средствах для дальнейшего анализа. Кроме того, соответствующие данные события определяются как отдельные свойства, а не как длинная строка. 
* События, записанные в Service Fabric разными подсистемами, идентифицируются по показателю "Источник (задача)" (ниже). Дополнительные сведения об этих подсистемах см. в статьях [Архитектура Service Fabric](service-fabric-architecture.md) и [Общие сведения о терминологии Service Fabric](service-fabric-technical-overview.md).

Ниже приведен список этих событий Service Fabric, упорядоченных по сущности.

## <a name="cluster-events"></a>События кластера

**События обновления кластера**

Дополнительные сведения об обновлении кластера можно просмотреть [здесь](service-fabric-cluster-upgrade-windows-server.md).

| EventId | Name | Категория | Описание |Источник (задача) | Уровень | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Обновление | Начато обновление кластера | CM | Информация |
| 29628 | ClusterUpgradeCompleted | Обновление | Обновление кластера завершено | CM | Информация | 
| 29629 | ClusterUpgradeRollbackStarted | Обновление | Начат откат обновления кластера  | CM | Предупреждение | 
| 29630 | ClusterUpgradeRollbackCompleted | Обновление | Откат обновления кластера завершен | CM | Предупреждение | 
| 29631 | ClusterUpgradeDomainCompleted | Обновление | Домен завершил обновление во время обновления кластера | CM | Информация | 

## <a name="node-events"></a>События узла

**События жизненного цикла узла** 

| EventId | ИМЯ | Категория | Описание |Источник (задача) | Уровень |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Завершена деактивация узла | FM | Информация | 
| 18603 | NodeUp | StateTransition | Кластер обнаружил, что узел запущен | FM | Информация | 
| 18604 | NodeDown | StateTransition | Кластер обнаружил, что узел завершил работу. При перезапуске узла отобразится событие с именем NodeDown, за которым следует событие NodeUp |  FM | Ошибка | 
| 18605 | NodeAddedToCluster | StateTransition |  В кластер добавлен новый узел, и Service Fabric может развертывать в него приложения | FM | Информация | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Узел удален с кластера. Service Fabric больше не будет развертывать приложения в этот узел | FM | Информация | 
| 18607 | NodeDeactivateStarted | StateTransition |  Начата деактивация узла | FM | Информация | 
| 25621 | NodeOpenSucceeded | StateTransition |  Узел успешно запущен | FabricNode | Информация | 
| 25622 | NodeOpenFailed | StateTransition |  Не удалось запустить узел и присоединить его к кольцу | FabricNode | Ошибка | 
| 25624 | NodeClosed | StateTransition |  Работа узла успешно завершена | FabricNode | Информация | 
| 25626 | NodeAborted | StateTransition |  Узел некорректно завершил работу | FabricNode | Ошибка | 

## <a name="application-events"></a>События приложения

**События жизненного цикла приложения**

| EventId | Name | Категория | Описание |Источник (задача) | Уровень | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | Создано новое приложение | CM | Информация | 
| 29625 | ApplicationDeleted | LifeCycle | Удалено имеющееся приложение | CM | Информация | 
| 23083 | ApplicationProcessExited | LifeCycle | Прекращен процесс в приложении | Hosting | Информация | 

**События обновления приложения**

Дополнительные сведения об обновлениях приложения можно просмотреть [здесь](service-fabric-application-upgrade.md).

| EventId | Name | Категория | Описание |Источник (задача) | Уровень | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Обновление | Начато обновление приложения | CM | Информация | 
| 29622 | ApplicationUpgradeCompleted | Обновление | Завершено обновление приложения | CM | Информация | 
| 29623 | ApplicationUpgradeRollbackStarted | Обновление | Начат откат обновления приложения |CM | Предупреждение | 
| 29624 | ApplicationUpgradeRollbackCompleted | Обновление | Завершен откат обновления приложения | CM | Предупреждение | 
| 29626 | ApplicationUpgradeDomainCompleted | Обновление | Домен завершил обновление во время обновления приложения | CM | Информация | 

## <a name="service-events"></a>События службы

**События жизненного цикла службы**

| EventId | ИМЯ | Категория | Описание |Источник (задача) | Уровень | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | Создана служба | FM | Информация | 
| 18658 | ServiceDeleted | LifeCycle | Удалена имеющаяся служба | FM | Информация | 

## <a name="partition-events"></a>События секции

**События перемещения секции**

| EventId | Name | Категория | Описание |Источник (задача) | Уровень | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | Завершена перенастройка разделов | RA | Информация | 

## <a name="replica-events"></a>События реплики

**События жизненного цикла реплики**

| EventId | Name | Категория | Описание |Источник (задача) | Уровень |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | LifeCycle | Открылся надежного словаря | DistributedDictionary | Информация |
| 61702 | ReliableDictionaryClosed | LifeCycle | Закрыл надежного словаря | DistributedDictionary | Информация |
| 61703 | ReliableDictionaryCheckpointRecovered | LifeCycle | Надежный словарь был восстановлен его контрольной точки | DistributedDictionary | Информация |
| 61704 | ReliableDictionaryCheckpointFilesSent | LifeCycle | Реплика отправил файлы контрольных точек надежного словаря | DistributedDictionary | Информация |
| 61705 | ReliableDictionaryCheckpointFilesReceived | LifeCycle | Реплика получил файлы контрольных точек надежного словаря | DistributedDictionary | Информация |
| 61963 | ReliableQueueOpened | LifeCycle | Надежная очередь открыт | DistributedQueue | Информация |
| 61964 | ReliableQueueClosed | LifeCycle | Надежная очередь закрыл | DistributedQueue | Информация |
| 61965 | ReliableQueueCheckpointRecovered | LifeCycle | Надежная очередь была восстановлена его контрольной точки | DistributedQueue | Информация |
| 61966 | ReliableQueueCheckpointFilesSent | LifeCycle | Реплика отправил файлы контрольных точек надежная очередь | DistributedQueue | Информация |
| 63647 | ReliableQueueCheckpointFilesReceived | LifeCycle | Реплика получил надежная очередь файлы контрольных точек | DistributedQueue | Информация |
| 63648 | ReliableConcurrentQueueOpened | LifeCycle | Надежная параллельная очередь открыт | ReliableConcurrentQueue | Информация |
| 63649 | ReliableConcurrentQueueClosed | LifeCycle | Надежная параллельная очередь закрыл | ReliableConcurrentQueue | Информация |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | LifeCycle | Надежная параллельная очередь была восстановлена его контрольной точки | ReliableConcurrentQueue | Информация |
| 61687 | TStoreError | Сбой | Надежные коллекции получил непредвиденная ошибка | TStore | Ошибка |
| 63831 | PrimaryFullCopyInitiated | LifeCycle | Первичная реплика запустил полного копирования | TReplicator | Информация |
| 63832 | PrimaryPartialCopyInitiated | LifeCycle | Первичная реплика запустил частичной копии | TReplicator | Информация |
| 16831 | BuildIdleReplicaStarted | LifeCycle | Первичная реплика начал строить неактивной реплики | Репликация | Информация |
| 16832 | BuildIdleReplicaCompleted | LifeCycle | Первичная реплика завершит создание неактивной реплики | Репликация | Информация |
| 16833 | BuildIdleReplicaFailed | LifeCycle | Первичной реплике происходит сбой создания неактивной реплики | Репликация | Предупреждение |
| 16834 | PrimaryReplicationQueueFull | Health | Первичная реплика очередь репликации заполнена | Репликация | Предупреждение |
| 16835 | PrimaryReplicationQueueWarning | Health | Очередь репликации первичной реплики, приближается к полной | Репликация | Предупреждение |
| 16836 | PrimaryReplicationQueueWarningMitigated | Health | Допустимо первичная реплика очереди репликации | Репликация | Информация |
| 16837 | SecondaryReplicationQueueFull | Health | Вторичная реплика очередь репликации заполнена | Репликация | Предупреждение |
| 16838 | SecondaryReplicationQueueWarning | Health | Очередь репликации вторичная реплика, приближается к полной | Репликация | Предупреждение |
| 16839 | SecondaryReplicationQueueWarningMitigated | Health | Допустимо очереди репликации вторичной реплики | Репликация | Информация |
| 16840 | PrimaryFaultedSlowSecondary | Health | Первичную реплику сбойного медленно вторичной реплики | Репликация | Предупреждение |
| 16841 | ReplicatorFaulted | Health | Произошел сбой реплики | Репликация | Предупреждение |

## <a name="container-events"></a>События контейнера

**События жизненного цикла контейнера** 

| EventId | Name | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Контейнер запущен | Hosting | Информация | 1 |
| 23075 | ContainerDeactivated | Контейнер остановлен | Hosting | Информация | 1 |
| 23082 | ContainerExited | Работа контейнера завершена. Проверьте на наличие параметра UnexpectedTermination | Hosting | Информация | 1 |

## <a name="health-reports"></a>Отчеты о работоспособности

[Модель работоспособности Service Fabric](service-fabric-health-introduction.md) предоставляет широкие, гибкие и увеличенные возможности оценки работоспособности и создания отчетов. Начиная с Service Fabric версии 6.2 данные о работоспособности записываются как события платформы для предоставления записей журнала о работоспособности. Чтобы объем событий работоспособности не был слишком высоким, записываются только следующие события Service Fabric:

* Все отчеты о состоянии работоспособности `Error` или `Warning`.
* Отчеты о состоянии работоспособности `Ok` во время переходов.
* Истечение срока действия события работоспособности `Error` или `Warning`. Таким образом можно определять период, в течение которого сущность была неработоспособна.

**События отчета о работоспособности кластера**

| EventId | ИМЯ | Описание |Источник (задача) | Уровень | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Доступен новый отчет о работоспособности кластера | HM | Информация | 1 |
| 54437 | ClusterHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности кластера | HM | Информация | 1 |

**События отчета о работоспособности узла**

| EventId | Name | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Доступен новый отчет о работоспособности узла | HM | Информация | 1 |
| 54432 | NodeHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности узла | HM | Информация | 1 |

**События отчета о работоспособности приложения**

| EventId | Name | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Создан отчет о работоспособности приложения. Он создается для приложений, которые не были развернуты | HM | Информация | 1 |
| 54426 | DeployedApplicationNewHealthReport | Создан отчет о работоспособности развернутого приложения | HM | Информация | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Создан отчет о работоспособности развернутой службы | HM | Информация | 1 |
| 54434 | ApplicationHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности приложения | HM | Информация | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности развернутого приложения | HM | Информация | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности развернутой службы | HM | Информация | 1 |

**События отчета о работоспособности службы**

| EventId | Name | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Создан отчет о работоспособности службы | HM | Информация | 1 |
| 54433 | ServiceHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности службы | HM | Информация | 1 |

**События отчета о работоспособности секции**

| EventId | Name | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Создан отчет о работоспособности раздела | HM | Информация | 1 |
| 54431 | PartitionHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности раздела | HM | Информация | 1 |

**События отчета о работоспособности реплики**

| EventId | Name | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Создан отчет о работоспособности реплики с отслеживанием состояния | HM | Информация | 1 |
| 54430 | StatelessInstanceNewHealthReport | Создан отчет о работоспособности экземпляра без отслеживания состояния | HM | Информация | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности реплики с отслеживанием состояния | HM | Информация | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности реплики без учета состояния | HM | Информация | 1 |

## <a name="chaos-testing-events"></a>События тестирования в условиях хаоса 

**События сеанса тестирования в условиях хаоса**

| EventId | ИМЯ | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Начат сеанс тестирования в условиях хаоса | Тестирование | Информация | 1 |
| 50023 | ChaosStopped | Завершен сеанс тестирования в условиях хаоса | Тестирование | Информация | 1 |

**События узла в условиях хаоса**

| EventId | ИМЯ | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Перезапуск узла запланирован как часть сеанса тестирования в условиях хаоса | Тестирование | Информация | 1 |
| 50087 | ChaosNodeRestartCompleted | Завершен перезапуск узла как часть сеанса тестирования в условиях хаоса | Тестирование | Информация | 1 |

**События приложения в условиях хаоса**

| EventId | ИМЯ | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Во время сеанса тестирования в условиях хаоса запланирован перезапуск пакета кода | Тестирование | Информация | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Во время сеанса тестирования в условиях хаоса завершен перезапуск пакета кода | Тестирование | Информация | 1 |

**События секции в условиях хаоса**

| EventId | ИМЯ | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Перемещение основного раздела запланировано как часть сеанса тестирования в условиях хаоса | Тестирование | Информация | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Перемещение дополнительного раздела запланировано как часть сеанса тестирования в условиях хаоса | Тестирование | Информация | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Доступен более глубокий анализ перемещения основного раздела | Тестирование | Информация | 1 |

**События реплики в условиях хаоса**

| EventId | Name | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Перезапуск реплики запланирован как часть сеанса тестирования в условиях хаоса | Тестирование | Информация | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Удаление реплики запланировано как часть сеанса тестирования в условиях хаоса | Тестирование | Информация | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Удаление реплики завершено как часть сеанса тестирования в условиях хаоса | Тестирование | Информация | 1 |

## <a name="other-events"></a>Другие события

**События корреляции**

| EventId | ИМЯ | Описание |Источник (задача) | Уровень | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Обнаружена корреляция | Тестирование | Информация | 1 |

## <a name="events-prior-to-version-62"></a>События версии ниже 6.2

Ниже приведен полный список событий, предоставляемых Service Fabric до версии 6.2.

| EventId | Name | Источник (задача) | Уровень |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Информация |
| 25621 | NodeOpenedSuccess | FabricNode | Информация |
| 25622 | NodeOpenedFailed | FabricNode | Информация |
| 25623 | NodeClosing | FabricNode | Информация |
| 25624 | NodeClosed | FabricNode | Информация |
| 25625 | NodeAborting | FabricNode | Информация |
| 25626 | NodeAborted | FabricNode | Информация |
| 29627 | ClusterUpgradeStart | CM | Информация |
| 29628 | ClusterUpgradeComplete | CM | Информация |
| 29629 | ClusterUpgradeRollback | CM | Информация |
| 29630 | ClusterUpgradeRollbackComplete | CM | Информация |
| 29631 | ClusterUpgradeDomainComplete | CM | Информация |
| 23074 | ContainerActivated | Hosting | Информация |
| 23075 | ContainerDeactivated | Hosting | Информация |
| 29620 | ApplicationCreated | CM | Информация |
| 29621 | ApplicationUpgradeStart | CM | Информация |
| 29622 | ApplicationUpgradeComplete | CM | Информация |
| 29623 | ApplicationUpgradeRollback | CM | Информация |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Информация |
| 29625 | ApplicationDeleted | CM | Информация |
| 29626 | ApplicationUpgradeDomainComplete | CM | Информация |
| 18566 | ServiceCreated | FM | Информация |
| 18567 | ServiceDeleted | FM | Информация |

## <a name="next-steps"></a>Дальнейшие действия

* Получите представление о [диагностике в Service Fabric](service-fabric-diagnostics-overview.md).
* Дополнительные сведения о службе EventStore см. в [этой статье](service-fabric-diagnostics-eventstore.md).
* Изменение конфигурации [системы диагностики Azure](service-fabric-diagnostics-event-aggregation-wad.md) для сбора дополнительных журналов
* [Настройка Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) для просмотра журналов операционного канала
