---
title: Налаживание мониторинга графического процессора с помощью Azure Monitor для контейнеров Документы Майкрософт
description: В этой статье описывается, как можно настроить мониторинг кластеров Kubernetes с помощью узлов NVIDIA и AMD GPU с помощью Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373314"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Настройка мониторинга графического процессора с помощью Azure Monitor для контейнеров

Начиная с агента версии *ciprod03022019*, Azure монитор для контейнеров интегрированного агента в настоящее время поддерживает мониторинг GPU (графические единицы обработки) использования на GPU-знать Kubernetes кластерных узлов, а также контролировать стручки / контейнеры запроса и использования ресурсов GPU.

## <a name="supported-gpu-vendors"></a>Поддерживаемые поставщики графического процессора

Azure Monitor для контейнеров поддерживает мониторинг кластеров графического процессора от следующих поставщиков графического процессора:

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor для контейнеров автоматически начинает мониторинг использования графического процессора на узлах, а графический процессор запрашивает стручки и рабочие нагрузки, собирая следующие метрики с интервалом 60 сек и храня их в таблице **InsightMetrics:**

|Имя метрики |Метрическое измерение (теги) |Описание |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Процент времени за прошедший период выборки (60 секунд), в течение которого Графический процессор был занят/активно обрабатывает контейнер. Обязанности цикла число между 1 и 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, контейнер |Каждый контейнер может указать ограничения как один или несколько графических процессоров. Невозможно запросить или ограничить часть графического процессора. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, контейнер |Каждый контейнер может запросить один или несколько графических процессоров. Невозможно запросить или ограничить часть графического процессора.|
|контейнерGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Количество памяти GPU в байтах, доступных для использования для конкретного контейнера. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Количество памяти GPU в байтах, используемых определенным контейнером. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Количество графических процессоров в узлах, которые могут быть использованы Kubernetes. |
|nodeGpuЕмкость |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Общее количество графических процессоров в узлах. |

## <a name="gpu-performance-charts"></a>Диаграммы производительности GPU 

Azure Monitor для контейнеров включает предварительно настроенные диаграммы для метрик, перечисленных ранее в таблице в качестве рабочей книги графического процессора для каждого кластера. Вы можете найти gPU рабочий журнал **узла GPU** непосредственно из кластера AKS, выбрав **учебники** из левой панели, и из **списка выпадающих** книг просмотра в Insight.

## <a name="next-steps"></a>Дальнейшие действия

- [См. Используйте графические процессоры для рабочих нагрузок в службе Azure Kubernetes](../../aks/gpu-cluster.md) Service (AKS), чтобы узнать, как развернуть кластер AKS, включающий узлы с поддержкой графического процессора.

- Узнайте больше о [GPU Оптимизированные VM SKUs в Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Просмотрите [поддержку GPU в Kubernetes,](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) чтобы узнать больше об экспериментальной поддержке Kubernetes для управления графическими процессорами в одном или нескольких узлах в кластере.