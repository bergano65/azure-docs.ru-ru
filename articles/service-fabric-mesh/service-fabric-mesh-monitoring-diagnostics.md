---
title: Мониторинг и диагностика в Azure Service Fabric приложения сети
description: Сведения мониторинге и диагностике приложений в Сетке Service Fabric в Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02de8ea5dd5c53192d2b8c7beba8bc36143beac6
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627000"
---
# <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика

> [!IMPORTANT]
> Предварительная версия сетки Service Fabric Azure была снята с учета. Новые развертывания больше не будут разрешены через интерфейс API Service Fabricной сетки. Поддержка существующих развертываний будет продолжена 28 апреля 2021 г.
> 
> Дополнительные сведения см. в статье о прекращении использования [предварительной версии сети Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Сетка Azure Service Fabric — это полностью управляемая служба, которая позволяет разработчикам развертывать приложения для микрослужб без управления виртуальными машинами, хранилищем или сетями. В мониторинге и диагностике для Сетки Service Fabric выделяют три основных типа диагностических данных.

- Журналы приложений определяются как журналы из контейнерных приложений в зависимости от способа инструментирования приложения (например, журналы Docker).
- События платформы — это события из платформы Сетки, относящиеся к операции контейнера, такой как активация, деактивация и завершение работы контейнера.
- Метрики контейнера — это метрики производительности и использования ресурсов для контейнеров (статистика Docker).

В этой статье рассматриваются варианты мониторинга и диагностики, доступные для последней предварительной версии.

## <a name="application-logs"></a>Журналы приложений

Журналы Docker можно просматривать из развернутых контейнеров или из каждого отдельного контейнера. В модели приложения Сетки Service Fabric каждый контейнер представляет пакет кода в приложении. Чтобы просмотреть связанные журналы с пакетом кода, выполните следующую команду:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Для получения имени реплики можно использовать команду az mesh service-replica. Имена реплик увеличиваются в целых числах от 0.

Далее приводится пример просмотра журналов из контейнера VotingWeb.Code из приложения для голосования.

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Метрики контейнеров 

Среда сетки предоставляет несколько метрик, которые указывают, как выполняются контейнеры. Следующие метрики доступны через портал Azure и CLI Azure Monitor:

| Метрика | Описание | единиц(ы)|
|----|----|----|
| CpuUtilization | Актуалкпу/Аллокатедкпу в процентах | % |
| MemoryUtilization | Актуалмем/Аллокатедмем в процентах | % |
| AllocatedCpu | ЦП выделено в соответствии с Azure Resource Manager шаблоном | Миллиардах |
| AllocatedMemory | Память, выделенная в соответствии с шаблоном Azure Resource Manager | МБ |
| ActualCpu | Использование ЦП | Миллиардах |
| ActualMemory | Использование памяти | МБ |
| ContainerStatus | 0 — недопустимо: состояние контейнера неизвестно <br> 1-Ожидание: контейнер запланирован на запуск <br> 2 — Запуск: контейнер находится в процессе запуска <br> 3 — запущено: контейнер успешно запущен <br> 4 — остановка — контейнер останавливается <br> 5 — остановлено: контейнер успешно остановлен | н/д |
| ApplicationStatus | 0 — неизвестно: состояние не может быть извлечено <br> 1 — готово: приложение успешно запущено <br> 2. обновление: выполняется обновление <br> 3. Создание: приложение создается <br> 4 — Удаление: приложение удаляется <br> 5 — ошибка: не удалось развернуть приложение | н/д |
| ServiceStatus | 0 — недопустимо: служба в настоящее время не имеет состояния работоспособности <br> 1-ОК: служба работоспособна  <br> 2 — предупреждение. возможно, возникло что-то неправильное исследование <br> 3 — ошибка. возникли проблемы, требующие исследования <br> 4 — неизвестно. состояние не может быть извлечено | н/д |
| ServiceReplicaStatus | 0 — недопустимо: Реплика в настоящее время не имеет состояния работоспособности <br> 1-ОК: служба работоспособна  <br> 2 — предупреждение. возможно, возникло что-то неправильное исследование <br> 3 — ошибка. возникли проблемы, требующие исследования <br> 4 — неизвестно. состояние не может быть извлечено | н/д | 
| RestartCount | Число перезапусков контейнера | н/д |

> [!NOTE]
> Значения Сервицестатус и Сервицерепликастатус совпадают со значением [HealthState](/dotnet/api/system.fabric.health.healthstate) в Service Fabric.

Каждая метрика доступна в разных измерениях, что позволяет просматривать статистические выражения на разных уровнях. Текущий список измерений выглядит следующим образом:

* ApplicationName
* ServiceName
* сервицерепликанаме
* кодепаккаженаме

> [!NOTE]
> Измерение Кодепаккаженаме недоступно для приложений Linux. 

Каждое измерение соответствует различным компонентам [модели приложения Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Полный список команд доступен в документации по [Azure Monitor CLI](/cli/azure/monitor/metrics#az-monitor-metrics-list) , но мы включили несколько полезных примеров ниже. 

В каждом примере идентификатор ресурса соответствует этому шаблону.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Использование ЦП контейнерами в приложении

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Использование памяти для каждой реплики службы
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Перезапускается для каждого контейнера в течение одного часа 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Средняя загрузка ЦП между службами с именем "VotingWeb" в течение одного часа
```azurecli
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
* Чтобы узнать больше о командах метрик Azure Monitor, ознакомьтесь с [документацией по Azure Monitor CLI](/cli/azure/monitor/metrics#az-monitor-metrics-list).
