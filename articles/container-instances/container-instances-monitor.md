---
title: Мониторинг контейнеров в службе "Экземпляры контейнеров Azure"
description: Как отслеживать потребление контейнерами вычислительных ресурсов, таких как ЦП и память, в службе "Экземпляры контейнеров Azure".
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 4c1208d8cbc795e53128df0ccf93e79dc427abad
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325854"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Мониторинг ресурсов контейнеров в службе "Экземпляры контейнеров Azure"

[Azure Monitor][azure-monitoring] предоставляет информацию о том, какие вычислительные ресурсы используют экземпляры контейнеров. Эти данные об использовании ресурсов помогут определить оптимальные настройки ресурса для конкретных групп контейнеров. Azure Monitor также предоставляет метрики для отслеживания сетевой активности в экземплярах контейнеров.

В этом документе описывается, как происходит сбор метрик Azure Monitor для экземпляров контейнеров с помощью портала Azure и Azure CLI.

> [!IMPORTANT]
> Метрики Azure Monitor в Экземплярах контейнеров Azure сейчас доступны в предварительной версии с некоторыми [ограничениями](#preview-limitations). Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="preview-limitations"></a>Ограничения предварительной версии

Сейчас метрики Azure Monitor доступны только для контейнеров Linux.

## <a name="available-metrics"></a>Доступные метрики

Azure Monitor предоставляет следующие [метрики для Экземпляров контейнеров Azure][supported-metrics]. Эти метрики доступны как для отдельных контейнеров, так и для групп контейнеров.

* **Использование ЦП** — измеряется в **миллиядрах**. Одно миллиядро равно 0,001 используемого ядра ЦП. Таким образом, 500 миллиядер соответствует загрузке одного ядра на 50 %. Вычисляется в виде **среднего использования** всех ядер.

* **Использование памяти** — вычисляется как **среднее число байт**.

* **Получено байт по сети в секунду** и **Передано байт по сети в секунду** — вычисляется как **среднее число байт в секунду**. 

## <a name="get-metrics---azure-portal"></a>Получение метрик на портале Azure

При создании группы контейнеров в Azure Monitor данные отображаются на портале Azure. Чтобы просмотреть метрики для группы контейнеров, перейдите к странице **Обзор** для группы контейнеров. Здесь вы можете видеть предварительно созданные диаграммы для каждой из доступных метрик.

![Сдвоенные диаграммы][dual-chart]

В группе из нескольких контейнеров используйте функцию [измерения][monitor-dimension], чтобы получить метрики по каждому контейнеру. Чтобы создать диаграмму с метриками по отдельному контейнеру, выполните следующие действия:

1. На странице **Обзор** выберите одну из диаграмм метрик, таких как **ЦП**. 
1. Нажмите кнопку **Применить разделение**, а затем выберите **Имя контейнера**.

![Измерение][dimension]

## <a name="get-metrics---azure-cli"></a>Получение метрик через Azure CLI

Метрики для экземпляров контейнеров можно также получить с помощью Azure CLI. Для начала получите идентификатор группы контейнеров, используя приведенную ниже команду. Замените `<resource-group>` именем нужной группы ресурсов, а `<container-group>` — именем группы контейнеров.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Следующая команда возвращает метрики потребления **ЦП**:

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Измените значение параметра `--metric` в команде для получения других [поддерживаемых метрик][supported-metrics]. Например, используйте следующую команду для возвращения метрики потребления **памяти**. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

В группе с несколькими контейнерами можно добавить измерение `containerName`, чтобы получить метрики по отдельным контейнерам.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Дополнительная информация

Подробные сведения о мониторинге в Azure вы найдете [в этой статье][azure-monitoring].

Из этой статьи вы узнаете, как создать [оповещения о метриках][metric-alert] для получения уведомлений, когда значение метрики для Экземпляров контейнеров Azure достигает порогового значения.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
