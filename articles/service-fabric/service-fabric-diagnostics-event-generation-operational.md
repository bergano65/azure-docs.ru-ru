---
title: Список событий Azure Service Fabric
description: Полный список событий, предоставляемых Azure Service Fabric для мониторинга кластеров.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258542"
---
# <a name="list-of-service-fabric-events"></a>Список событий Service Fabric 

Service Fabric предоставляет основной набор событий кластера, чтобы сообщать о состоянии кластеров в виде [событий Service Fabric](service-fabric-diagnostics-events.md). Они основаны на действиях, выполненных Service Fabric на узлах и в кластерах, или решениях по управлению, принятых владельцем или оператором кластера. К этим событиям можно получить доступ путем настройки несколькими способами, включая настройку [журналов Azure Monitor с кластером](service-fabric-diagnostics-oms-setup.md)или запрос [eventStore.](service-fabric-diagnostics-eventstore.md) На компьютерах Windows эти события направляются в EventLog, поэтому события Service Fabric можно просматривать в средстве "Просмотр событий". 

Ниже приведены некоторые характеристики таких событий.
* Каждое событие привязывается к определенной сущности в кластере (например, к приложению, службе, узлу, реплике).
* Каждое событие содержит набор общих полей: EventInstanceId, EventName и Категория.
* Все события содержат поля, которые привязывают их к сущности, связанной с ними. Например, у события ApplicationCreated есть поля, которые указывают имя созданного приложения.
* События структурируются таким образом, чтобы их можно было использовать в разнообразных средствах для дальнейшего анализа. Кроме того, соответствующие данные события определяются как отдельные свойства, а не как длинная строка. 
* События, записанные в Service Fabric разными подсистемами, идентифицируются по показателю "Источник (задача)" (ниже). Дополнительные сведения об этих подсистемах см. в статьях [Архитектура Service Fabric](service-fabric-architecture.md) и [Общие сведения о терминологии Service Fabric](service-fabric-technical-overview.md).

Ниже приведен список этих событий Service Fabric, упорядоченных по сущности.

## <a name="cluster-events"></a>События кластера

**События обновления кластера**

Дополнительные сведения об обновлении кластера можно просмотреть [здесь](service-fabric-cluster-upgrade-windows-server.md).

| EventId | name | Категория | Описание |Источник (задача) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Обновление | Начато обновление кластера | CM | Informational |
| 29628 | ClusterUpgradeCompleted | Обновление | Обновление кластера завершено | CM | Informational | 
| 29629 | ClusterUpgradeRollbackStarted | Обновление | Начат откат обновления кластера  | CM | Предупреждение | 
| 29630 | ClusterUpgradeRollbackCompleted | Обновление | Откат обновления кластера завершен | CM | Предупреждение | 
| 29631 | ClusterUpgradeDomainCompleted | Обновление | Домен завершил обновление во время обновления кластера | CM | Informational | 

## <a name="node-events"></a>События узла

**События жизненного цикла узла** 

| EventId | name | Категория | Описание |Источник (задача) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Завершена деактивация узла | FM | Informational | 
| 18603 | NodeUp | StateTransition | Кластер обнаружил, что узел запущен | FM | Informational | 
| 18604 | NodeDown | StateTransition | Кластер обнаружил, что узел завершил работу. При перезапуске узла отобразится событие с именем NodeDown, за которым следует событие NodeUp |  FM | Error | 
| 18605 | NodeAddedToCluster | StateTransition |  В кластер добавлен новый узел, и Service Fabric может развертывать в него приложения | FM | Informational | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Узел удален с кластера. Service Fabric больше не будет развертывать приложения в этот узел | FM | Informational | 
| 18607 | NodeDeactivateStarted | StateTransition |  Начата деактивация узла | FM | Informational | 
| 25621 | NodeOpenSucceeded | StateTransition |  Узел успешно запущен | FabricNode | Informational | 
| 25622 | NodeOpenFailed | StateTransition |  Не удалось запустить узел и присоединить его к кольцу | FabricNode | Error | 
| 25624 | NodeClosed | StateTransition |  Работа узла успешно завершена | FabricNode | Informational | 
| 25626 | NodeAborted | StateTransition |  Узел некорректно завершил работу | FabricNode | Error | 

## <a name="application-events"></a>События приложения

**События жизненного цикла приложения**

| EventId | name | Категория | Описание |Источник (задача) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | Создано новое приложение | CM | Informational | 
| 29625 | ApplicationDeleted | LifeCycle | Удалено имеющееся приложение | CM | Informational | 
| 23083 | ApplicationProcessExited | LifeCycle | Прекращен процесс в приложении | Размещение | Informational | 

**События обновления приложения**

Дополнительные сведения об обновлениях приложения можно просмотреть [здесь](service-fabric-application-upgrade.md).

| EventId | name | Категория | Описание |Источник (задача) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Обновление | Начато обновление приложения | CM | Informational | 
| 29622 | ApplicationUpgradeCompleted | Обновление | Завершено обновление приложения | CM | Informational | 
| 29623 | ApplicationUpgradeRollbackStarted | Обновление | Начат откат обновления приложения |CM | Предупреждение | 
| 29624 | ApplicationUpgradeRollbackCompleted | Обновление | Завершен откат обновления приложения | CM | Предупреждение | 
| 29626 | ApplicationUpgradeDomainCompleted | Обновление | Домен завершил обновление во время обновления приложения | CM | Informational | 

## <a name="service-events"></a>События службы

**События жизненного цикла службы**

| EventId | name | Категория | Описание |Источник (задача) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | Создана служба | FM | Informational | 
| 18658 | ServiceDeleted | LifeCycle | Удалена имеющаяся служба | FM | Informational | 

## <a name="partition-events"></a>События секции

**События перемещения секции**

| EventId | name | Категория | Описание |Источник (задача) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | Завершена перенастройка разделов | RA | Informational | 

## <a name="replica-events"></a>События реплики

**Реплика событий жизненного цикла**

| EventId | name | Категория | Описание |Источник (задача) | Level |
| --- | --- | ---| --- | --- | --- |
| 61701 | НадежныйСловарьОткрыт | LifeCycle | Надежный словарь открылся | Распределенныйсловарь | Informational |
| 61702 | НадежныйСловарьЗакрытый | LifeCycle | Надежный словарь закрылся | Распределенныйсловарь | Informational |
| 61703 | НадежныйСловарьCheckpointВосстановлено | LifeCycle | Надежный словарь восстановил свой контрольно-пропускной пункт | Распределенныйсловарь | Informational |
| 61704 | НадежныйСловарьCheckpointFilesSent | LifeCycle | Реплика отправила надежные файлы контрольно-пропускных пунктов словаря | Распределенныйсловарь | Informational |
| 61705 | НадежныйСловарьCheckpointFilesReceived | LifeCycle | Реплика получила надежные файлы контрольно-пропускного пункта словаря | Распределенныйсловарь | Informational |
| 61963 | НадежныйКюлиОткрытОткрыт | LifeCycle | Надежная очередь открылась | РаспределенныйКи | Informational |
| 61964 | НадежныйОчередьЗакрыто | LifeCycle | Надежная очередь закрыта | РаспределенныйКи | Informational |
| 61965 | НадежныйКликCheckpointВосстановлен | LifeCycle | Надежная очередь восстановила контрольно-пропускной пункт | РаспределенныйКи | Informational |
| 61966 | НадежныйКликCheckpointFilesSent | LifeCycle | Реплика отправила файлы контрольно-пропускных пунктов надежной очереди | РаспределенныйКи | Informational |
| 63647 | НадежныйКликCheckpointFilesПолучен | LifeCycle | Реплика получила надежные файлы контрольно-пропускного пункта очереди | РаспределенныйКи | Informational |
| 63648 | НадежныйКонтовактиКВКЕОткрыто | LifeCycle | Надежная одновременные очереди открылась | НадежныйКонтовкиКиклик | Informational |
| 63649 | НадежноеКонтовкеЗакрытоЕ | LifeCycle | Надежная одновременные очереди закрыта | НадежныйКонтовкиКиклик | Informational |
| 63650 | НадежныйConcurrentКонКиcheckpointCheckpointВосстановлено | LifeCycle | Надежная одновременные очереди восстановила свою контрольно-пропускную точку | НадежныйКонтовкиКиклик | Informational |
| 61687 | TStoreОшибка | Failure | Надежная коллекция получила неожиданную ошибку | TStore | Error |
| 63831 | PrimaryFullCopyИсты | LifeCycle | Первичная реплика инициировала полную копию | TReplicator | Informational |
| 63832 | PrimaryPartialCopyИсты | LifeCycle | Первичная реплика инициировала частичную копию | TReplicator | Informational |
| 16831 | BuildIdleReplicaStarted | LifeCycle | Первичная реплика начала строительство простаивающих реплик | Репликация | Informational |
| 16832 | BuildIdleReplicaЗавершено | LifeCycle | Первичная реплика завершила работу в простоях здания | Репликация | Informational |
| 16833 | BuildIdleReplicaFailed | LifeCycle | Первичная реплика не удалось здание простоя реплики | Репликация | Предупреждение |
| 16834 | ПервичнаяРепликаКИПолный | Health | Очередь репликации первичной репликации полна | Репликация | Предупреждение |
| 16835 | ПервичнаяРепликаКиПредупреждение | Health | Очередь репликации первичной репликации почти заполнена | Репликация | Предупреждение |
| 16836 | ПервичнаяРепликаОчередьПредупреждениеСмеймы | Health | Очередь репликации первичной репликации в порядке | Репликация | Informational |
| 16837 | ВторичноеРепликацияКИПолный | Health | Очередь репликации вторичной реплики полна | Репликация | Предупреждение |
| 16838 | ВторичноеРепликацияПредупреждение | Health | Очередь репликации вторичной реплики почти заполнена | Репликация | Предупреждение |
| 16839 | ВторичноеРепликацияСравнениПредупреждениеСмечено | Health | Очередь репликации вторичной реплики в порядке | Репликация | Informational |
| 16840 | PrimaryFaultedSlowSecondary | Health | Первичная реплика имеет неисправность медленной вторичной реплики | Репликация | Предупреждение |
| 16841 | РепликаторНеис | Health | Реплика имеет неисправность | Репликация | Предупреждение |

## <a name="container-events"></a>События контейнера

**События жизненного цикла контейнера** 

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Контейнер запущен | Размещение | Informational | 1 |
| 23075 | ContainerDeactivated | Контейнер остановлен | Размещение | Informational | 1 |
| 23082 | ContainerExited | Работа контейнера завершена. Проверьте на наличие параметра UnexpectedTermination | Размещение | Informational | 1 |

## <a name="health-reports"></a>Отчеты о работоспособности

[Модель работоспособности Service Fabric](service-fabric-health-introduction.md) предоставляет широкие, гибкие и увеличенные возможности оценки работоспособности и создания отчетов. Начиная с Service Fabric версии 6.2 данные о работоспособности записываются как события платформы для предоставления записей журнала о работоспособности. Чтобы объем событий работоспособности не был слишком высоким, записываются только следующие события Service Fabric:

* Все отчеты о состоянии работоспособности `Error` или `Warning`.
* Отчеты о состоянии работоспособности `Ok` во время переходов.
* Истечение срока действия события работоспособности `Error` или `Warning`. Таким образом можно определять период, в течение которого сущность была неработоспособна.

**События отчета о работоспособности кластера**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Доступен новый отчет о работоспособности кластера | HM | Informational | 1 |
| 54437 | ClusterHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности кластера | HM | Informational | 1 |

**События отчета о работоспособности узла**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Доступен новый отчет о работоспособности узла | HM | Informational | 1 |
| 54432 | NodeHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности узла | HM | Informational | 1 |

**События отчета о работоспособности приложения**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Создан отчет о работоспособности приложения. Он создается для приложений, которые не были развернуты | HM | Informational | 1 |
| 54426 | DeployedApplicationNewHealthReport | Создан отчет о работоспособности развернутого приложения | HM | Informational | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Создан отчет о работоспособности развернутой службы | HM | Informational | 1 |
| 54434 | ApplicationHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности приложения | HM | Informational | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности развернутого приложения | HM | Informational | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности развернутой службы | HM | Informational | 1 |

**События отчета о работоспособности службы**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Создан отчет о работоспособности службы | HM | Informational | 1 |
| 54433 | ServiceHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности службы | HM | Informational | 1 |

**События отчета о работоспособности секции**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Создан отчет о работоспособности раздела | HM | Informational | 1 |
| 54431 | PartitionHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности раздела | HM | Informational | 1 |

**События отчета о работоспособности реплики**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Создан отчет о работоспособности реплики с отслеживанием состояния | HM | Informational | 1 |
| 54430 | StatelessInstanceNewHealthReport | Создан отчет о работоспособности экземпляра без отслеживания состояния | HM | Informational | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности реплики с отслеживанием состояния | HM | Informational | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Истек срок действия имеющегося отчета о работоспособности реплики без учета состояния | HM | Informational | 1 |

## <a name="chaos-testing-events"></a>События тестирования в условиях хаоса 

**События сеанса тестирования в условиях хаоса**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Начат сеанс тестирования в условиях хаоса | Тестирование | Informational | 1 |
| 50023 | ChaosStopped | Завершен сеанс тестирования в условиях хаоса | Тестирование | Informational | 1 |

**События узла в условиях хаоса**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Перезапуск узла запланирован как часть сеанса тестирования в условиях хаоса | Тестирование | Informational | 1 |
| 50087 | ChaosNodeRestartCompleted | Завершен перезапуск узла как часть сеанса тестирования в условиях хаоса | Тестирование | Informational | 1 |

**События приложения в условиях хаоса**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Во время сеанса тестирования в условиях хаоса запланирован перезапуск пакета кода | Тестирование | Informational | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Во время сеанса тестирования в условиях хаоса завершен перезапуск пакета кода | Тестирование | Informational | 1 |

**События секции в условиях хаоса**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Перемещение основного раздела запланировано как часть сеанса тестирования в условиях хаоса | Тестирование | Informational | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Перемещение дополнительного раздела запланировано как часть сеанса тестирования в условиях хаоса | Тестирование | Informational | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Доступен более глубокий анализ перемещения основного раздела | Тестирование | Informational | 1 |

**События реплики в условиях хаоса**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Перезапуск реплики запланирован как часть сеанса тестирования в условиях хаоса | Тестирование | Informational | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Удаление реплики запланировано как часть сеанса тестирования в условиях хаоса | Тестирование | Informational | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Удаление реплики завершено как часть сеанса тестирования в условиях хаоса | Тестирование | Informational | 1 |

## <a name="other-events"></a>Другие события

**События корреляции**

| EventId | name | Описание |Источник (задача) | Level | Версия |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Обнаружена корреляция | Тестирование | Informational | 1 |

## <a name="events-prior-to-version-62"></a>События версии ниже 6.2

Ниже приведен полный список событий, предоставляемых Service Fabric до версии 6.2.

| EventId | name | Источник (задача) | Level |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informational |
| 25621 | NodeOpenedSuccess | FabricNode | Informational |
| 25622 | NodeOpenedFailed | FabricNode | Informational |
| 25623 | NodeClosing | FabricNode | Informational |
| 25624 | NodeClosed | FabricNode | Informational |
| 25625 | NodeAborting | FabricNode | Informational |
| 25626 | NodeAborted | FabricNode | Informational |
| 29627 | ClusterUpgradeStart | CM | Informational |
| 29628 | ClusterUpgradeComplete | CM | Informational |
| 29629 | ClusterUpgradeRollback | CM | Informational |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informational |
| 29631 | ClusterUpgradeDomainComplete | CM | Informational |
| 23074 | ContainerActivated | Размещение | Informational |
| 23075 | ContainerDeactivated | Размещение | Informational |
| 29620 | ApplicationCreated | CM | Informational |
| 29621 | ApplicationUpgradeStart | CM | Informational |
| 29622 | ApplicationUpgradeComplete | CM | Informational |
| 29623 | ApplicationUpgradeRollback | CM | Informational |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informational |
| 29625 | ApplicationDeleted | CM | Informational |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informational |
| 18566 | ServiceCreated | FM | Informational |
| 18567 | ServiceDeleted | FM | Informational |

## <a name="next-steps"></a>Дальнейшие действия

* Получите представление о [диагностике в Service Fabric](service-fabric-diagnostics-overview.md).
* Дополнительные сведения о службе EventStore см. в [этой статье](service-fabric-diagnostics-eventstore.md).
* Изменение конфигурации [системы диагностики Azure](service-fabric-diagnostics-event-aggregation-wad.md) для сбора дополнительных журналов
* [Настройка Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) для просмотра журналов операционного канала
