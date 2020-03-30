---
title: Мониторинг и диагностика в приложениях Azure Service Fabric Mesh
description: Сведения мониторинге и диагностике приложений в Сетке Service Fabric в Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498143"
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
> Для получения имени реплики можно использовать команду az mesh service-replica. Реплики имена приравнявания integers от 0.

Далее приводится пример просмотра журналов из контейнера VotingWeb.Code из приложения для голосования.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Метели контейнеров 

Среда сетки предоставляет несколько метрик, указывающих на выполнение контейнеров. Следующие метрики доступны на портале Azure и CLI монитора Azure:

| Метрика | Описание | Units|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu в процентах | % |
| ПамятьУтилизация | ActualMem/AllocatedMem в процентах | % |
| ВыделеноCpu | Cpu, выделенный в соответствии с шаблоном менеджера ресурсов Azure | Милликорс |
| ВыделеннаяПамять | Память, выделенная в соответствии с шаблоном управления ресурсами Azure | МБ |
| ActualCpu | Загрузка ЦП. | Милликорс |
| Фактическаяпамять | Использование памяти | МБ |
| КонтейнерСтатус | 0 - Недействительным: Статус контейнера неизвестен <br> 1 - Ожидание: Контейнер запланирован на запуск <br> 2 - Начало: Контейнер находится в процессе запуска <br> 3 - Запущенный: контейнер успешно стартовал <br> 4 - Остановка: Контейнер останавливается <br> 5 - Остановлено: контейнер успешно остановлен | Недоступно |
| ПриложениеСтатус | 0 - Неизвестный: Статус не может быть извлечен <br> 1 - Готов: приложение работает успешно <br> 2 - Обновление: Существует обновление в процессе <br> 3 - Создание: Приложение создается <br> 4 - Удаление: Приложение удаляется <br> 5 - Не удалось: приложение не удалось развернуть | Недоступно |
| СервисСтатус | 0 - Инвалид: Служба в настоящее время не имеет состояния здоровья <br> 1 - Ok: Услуга здорова  <br> 2 - Предупреждение: Там может быть что-то неправильно, требующих расследования <br> 3 - Ошибка: Существует что-то неправильно, что нуждается в расследовании <br> 4 - Неизвестный: Статус не может быть извлечен | Недоступно |
| СервисРепликАстампром | 0 - Инвалид: реплика в настоящее время не имеет состояния здоровья <br> 1 - Ok: Услуга здорова  <br> 2 - Предупреждение: Там может быть что-то неправильно, требующих расследования <br> 3 - Ошибка: Существует что-то неправильно, что нуждается в расследовании <br> 4 - Неизвестный: Статус не может быть извлечен | Недоступно | 
| ПерезапускCount | Количество перезагружаемых контейнеров | Недоступно |

> [!NOTE]
> Значения ServiceStatus и ServiceReplicaStatus такие же, как и значения [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) в сервисной ткани. 

Каждая метрика доступна на разных измерениях, так что вы можете видеть агрегаты на разных уровнях. Текущий перечень измерений:

* ApplicationName
* ServiceName
* СервисРепликНамь
* CodePackageName

> [!NOTE]
> Измерение CodePackageName недоступно для приложений Linux. 

Каждое измерение соответствует различным компонентам [модели приложения Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Полный список команд доступен в [документах Azure Monitor CLI,](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) но мы включили несколько полезных примеров ниже 

В каждом примере идентификатор ресурса следует этому шаблону

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Использование контейнеров в приложении

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Использование памяти для каждой реплики службы
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Перезагрузка для каждого контейнера в 1-часовом окне 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Среднее использование процессора между службами под названием "VotingWeb" в 1 часе окна
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Обозреватель метрик

Исследователь метрик — это лезвие портала, в котором можно визуализировать все метрики для приложения Mesh. Это лезвие доступно на странице приложения на портале и лезвии монитора Azure, последнее из которых можно использовать для просмотра метрик для всех ресурсов Azure, поддерживающих Azure Monitor. 

![Обозреватель метрик](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)
* Чтобы узнать больше об командах метрик Azure Monitor, ознакомьтесь [с документами CLI Azure Monitor.](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)
