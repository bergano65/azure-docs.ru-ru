---
title: Мониторинг и диагностика приложений Сетки Service Fabric в Azure | Документы Майкрософт
description: Сведения мониторинге и диагностике приложений в Сетке Service Fabric в Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939819"
---
# <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика.
Сетка Azure Service Fabric — это полностью управляемая служба, которая позволяет разработчикам развертывать приложения для микрослужб без управления виртуальными машинами, хранилищем или сетями. В мониторинге и диагностике для Сетки Service Fabric выделяют три основных типа диагностических данных.

- Журналы приложений определяются как журналы из контейнерных приложений в зависимости от способа инструментирования приложения (например, журналы Docker).
- События платформы — это события из платформы Сетки, относящиеся к операции контейнера, такой как активация, деактивация и завершение работы контейнера.
- Метрики контейнера — это метрики производительности и использования ресурсов для контейнеров (статистика Docker).

В этой статье рассматриваются варианты мониторинга и диагностики, доступные для последней предварительной версии.

## <a name="application-logs"></a>Журналы приложений

Журналы Docker можно просматривать из развернутых контейнеров или из каждого отдельного контейнера. В модели приложения Сетки Service Fabric каждый контейнер представляет пакет кода в приложении. Чтобы просмотреть связанные журналы с пакетом кода, выполните следующую команду:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Для получения имени реплики можно использовать команду az mesh service-replica. Имена реплики увеличивая целых чисел от 0.

Далее приводится пример просмотра журналов из контейнера VotingWeb.Code из приложения для голосования.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Метрики контейнера 

Среде сетки предоставляет небольшое число метрик, указывающее, как выполняется контейнеров. Доступны следующие метрики через Azure портала и Azure отслеживать интерфейса командной строки:

| Метрика | Описание | Units|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu в процентах | % |
| MemoryUtilization | ActualMem/AllocatedMem в процентах | % |
| AllocatedCpu | ЦП, выделенных в соответствии с шаблона Azure Resource Manager | Миллиардах |
| AllocatedMemory | Память, выделенную в соответствии с шаблона Azure Resource Manager | МБ |
| ActualCpu | Загрузка ЦП. | Миллиардах |
| ActualMemory | Использование памяти | МБ |
| Состояние_контейнера | 0 — недопустимое: Состояние контейнера неизвестно <br> 1 - ожидает обработки: Контейнер запланирован к запуску <br> 2 - начала: Контейнер находится в процессе запуска <br> 3 - started: Контейнер успешно запущена <br> 4 - остановка: Выполняется остановка контейнера <br> 5 - остановлено: Контейнер успешно остановлена | Н/Д |
| ApplicationStatus | 0 — неизвестно: Не удается найти состояние <br> 1 - все готово: Приложение выполняется успешно <br> 2 — обновление: Установить обновление выполняется <br> 3 - Создание: Создается приложение <br> 4 — удаление: Приложение удаляется. <br> 5 - не удалось: Не удалось развернуть приложение | Н/Д |
| ServiceStatus | 0 — недопустимое: В настоящее время служба не имеет состояния работоспособности <br> 1 - ОК: Служба находится в работоспособном состоянии  <br> 2 - предупреждение. Возможно, проблемы, требующие исследования <br> 3 - ошибка: Что-то не так, что требуется исследование <br> 4 — неизвестно: Не удается найти состояние | Н/Д |
| ServiceReplicaStatus | 0 — недопустимое: Реплика не имеет состояния работоспособности <br> 1 - ОК: Служба находится в работоспособном состоянии  <br> 2 - предупреждение. Возможно, проблемы, требующие исследования <br> 3 - ошибка: Что-то не так, что требуется исследование <br> 4 — неизвестно: Не удается найти состояние | Н/Д | 
| RestartCount | Количество перезагрузок контейнера | Н/Д |

> [!NOTE]
> Значения ServiceStatus и ServiceReplicaStatus совпадают [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) в Service Fabric. 

Каждой метрики можно найти в различных измерений, чтобы можно было видеть статистические выражения на разных уровнях. Текущий список измерений, следующим образом:

* ApplicationName
* ServiceName
* ServiceReplicaName
* Имя_пакета_кода

> [!NOTE]
> Измерение Имя_пакета_кода недоступен для приложений Linux. 

Каждое измерение соответствует различные компоненты [модель приложения Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Монитор Azure CLI

Полный список команд, доступных в [документация CLI для Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , но мы добавили несколько полезных примеров ниже 

В каждом примере идентификатор ресурса соответствует данному шаблону

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Использование ЦП контейнеров в приложение

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Использование памяти для каждой реплики службы
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Перезагрузка для каждого контейнера в окне 1 час 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Средний объем использования ЦП на службы с именем «VotingWeb» в окне 1 час
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Обозреватель метрик

Обозреватель метрик является колонка на портале, в котором вы можете визуализировать все метрики для приложения сетки. В этой колонке отображается на странице приложения на портале и колонку Azure monitor, последний из которых можно использовать для просмотра метрик для всех ресурсов Azure, которые поддерживают Azure Monitor. 

![Обозреватель метрик](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)
* Дополнительные сведения о командах метрик Azure Monitor, ознакомьтесь с [документация CLI для Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
