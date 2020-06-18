---
title: Как подключить Azure Arc к Azure Monitor для контейнеров (предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Как подключить Azure Arc к Azure Monitor для контейнеров
keywords: Kubernetes, Arc, Azure, K8s, контейнеры
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680748"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Как подключить Azure Monitor для контейнеров к Arc (предварительная версия)

Подключите [контейнеры с поддержкой Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) к кластерам Kubernetes с поддержкой Azure Arc.

## <a name="before-you-begin"></a>Перед началом

* [Версии Kubernetes](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Дистрибутивы Linux для узлов кластера (главного и рабочего) — Ubuntu (LTS-версии 18.04 и 16.04)
* Минимальные разрешения для роли RBAC "Участник" в подписке Azure кластера Kubernetes с поддержкой Azure Arc
* Полный идентификатор ресурса Azure кластера Kubernetes с поддержкой Azure Arc
* Контекст Kubeconfig кластера Kubernetes
* Агент мониторинга требует, чтобы cAdvisor в Kubelet выполнялся на защищенном порте 10250 на незащищенном порте 10255 на всех узлах для извлечения показателей производительности.   
* Рекомендуется настроить в качестве порта Kubelet cAdvisor защищенный порт 10250.
* Агент мониторинга требует, чтобы следующие исходящие порты и домены отправляли данные мониторинга на сервер Azure Monitor (если заблокировано прокси-сервером/брандмауэром).
    -  *.ods.opinsights.azure.com 443
    -  *.oms.opinsights.azure.com 443
    -  *.blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Переход

### <a name="using-helm-chart"></a>С использованием диаграммы HELM

### <a name="option-1-using-powershell--script"></a>Вариант 1. С использованием сценария PowerShell

1. Скачайте сценарий подключения

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Установите [ядро PowerShell](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) на компьютер разработки, чтобы выполнить сценарий подключения PowerShell.

3. Вход в Azure

    ```console
    az login --use-device-code
    ```

4. Выполните сценарий ниже со своим кластером Azure Arc K8s ResourceId и контекстом кластера Kubernetes

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>Вариант 2. С использованием сценария Bash

> **Подсказка.** В сценарии используются функции bash 4, так что убедитесь, что ваш bash находится в актуальном состоянии. Уточнить текущую версию можно в `bash --version`.

1. Скачайте сценарий подключения

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Выполните сценарий ниже со своим кластером Azure Arc K8s ResourceId и контекстом кластера Kubernetes

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Настройка сбора данных коллекции

По умолчанию агент не собирает журналы stdout и stderr контейнеров в пространстве имен kube-system.
См. информацию о настройке агента с нужными параметрами сбора данных в разделе https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config.

## <a name="configure-scraping-of-prometheus-metrics"></a>Настройка очистки метрик Prometheus

Azure Monitor для контейнеров удаляет метрики Prometheus и ввод данных на серверы Azure Monitor.
См. инструкции по настройке очистки Prometheus в разделе https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration.

## <a name="user-interface"></a>Пользовательский интерфейс

Для просмотра кластера подключения перейдите в раздел https://aka.ms/azmon-containers-azurearc.

## <a name="disable-monitoring"></a>Отключение мониторинга

Если по какой-либо причине требуется отключить мониторинг, можно просто удалить диаграмму HELM Azure Monitor для контейнеров, чтобы остановить сбор и ввод данных мониторинга на сервер Azure Monitor для контейнеров.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Использование политики Azure для управления конфигурацией кластера](./use-azure-policy.md)

