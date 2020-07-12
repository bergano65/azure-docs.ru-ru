---
title: Мониторинг и диагностика в Azure Service Fabric приложения сети
description: Сведения мониторинге и диагностике приложений в Сетке Service Fabric в Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: e940f0cf0d1547b317cd9e7bd15ac5486d5e70b2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248413"
---
# <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика
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
> Для получения имени реплики можно использовать команду az mesh service-replica. Имена реплик увеличиваются в целых числах от 0.

Далее приводится пример просмотра журналов из контейнера VotingWeb.Code из приложения для голосования.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Метрики контейнеров 

Среда сетки предоставляет несколько метрик, которые указывают, как выполняются контейнеры. Следующие метрики доступны через портал Azure и CLI Azure Monitor:

| Метрика | Описание | Units|
|----|----|----|
| CpuUtilization | Актуалкпу/Аллокатедкпу в процентах | % |
| MemoryUtilization | Актуалмем/Аллокатедмем в процентах | % |
| AllocatedCpu | ЦП выделено в соответствии с Azure Resource Manager шаблоном | Миллиардах |
| AllocatedMemory | Память, выделенная в соответствии с шаблоном Azure Resource Manager | МБ |
| ActualCpu | Использование ЦП | Миллиардах |
| ActualMemory | Использование памяти | МБ |
| ContainerStatus | 0 — недопустимо: состояние контейнера неизвестно <br> 1-Ожидание: контейнер запланирован на запуск <br> 2 — Запуск: контейнер находится в процессе запуска <br> 3 — запущено: контейнер успешно запущен <br> 4 — остановка — контейнер останавливается <br> 5 — остановлено: контейнер успешно остановлен | Недоступно |
| ApplicationStatus | 0 — неизвестно: состояние не может быть извлечено <br> 1 — готово: приложение успешно запущено <br> 2. обновление: выполняется обновление <br> 3. Создание: приложение создается <br> 4 — Удаление: приложение удаляется <br> 5 — ошибка: не удалось развернуть приложение | Недоступно |
| ServiceStatus | 0 — недопустимо: служба в настоящее время не имеет состояния работоспособности <br> 1-ОК: служба работоспособна  <br> 2 — предупреждение. возможно, возникло что-то неправильное исследование <br> 3 — ошибка. возникли проблемы, требующие исследования <br> 4 — неизвестно. состояние не может быть извлечено | Недоступно |
| ServiceReplicaStatus | 0 — недопустимо: Реплика в настоящее время не имеет состояния работоспособности <br> 1-ОК: служба работоспособна  <br> 2 — предупреждение. возможно, возникло что-то неправильное исследование <br> 3 — ошибка. возникли проблемы, требующие исследования <br> 4 — неизвестно. состояние не может быть извлечено | Недоступно | 
| RestartCount | Число перезапусков контейнера | Недоступно |

> [!NOTE]
> Значения Сервицестатус и Сервицерепликастатус совпадают со значением [HealthState](/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) в Service Fabric. 

Каждая метрика доступна в разных измерениях, что позволяет просматривать статистические выражения на разных уровнях. Текущий список измерений выглядит следующим образом:

* ApplicationName
* ServiceName
* сервицерепликанаме
* кодепаккаженаме

> [!NOTE]
> Измерение Кодепаккаженаме недоступно для приложений Linux. 

Каждое измерение соответствует различным компонентам [модели приложения Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Полный список команд доступен в документации по [Azure Monitor CLI](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , но мы включили несколько полезных примеров ниже. 

В каждом примере идентификатор ресурса соответствует этому шаблону.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Использование ЦП контейнерами в приложении

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Использование памяти для каждой реплики службы
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Перезапускается для каждого контейнера в течение одного часа 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Средняя загрузка ЦП между службами с именем "VotingWeb" в течение одного часа
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Обозреватель метрик

Обозреватель метрик — это колонка на портале, с помощью которой можно визуализировать все метрики для приложения сетки. Эта колонка доступна на странице приложения на портале и в колонке Azure Monitor, второй из которых можно использовать для просмотра метрик для всех ресурсов Azure, поддерживающих Azure Monitor. 

![Обозреватель метрик](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)
* Чтобы узнать больше о командах метрик Azure Monitor, ознакомьтесь с [документацией по Azure Monitor CLI](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
