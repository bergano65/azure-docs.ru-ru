---
title: Как отключить мониторинг гибридного кластера Kubernetes | Документация Майкрософт
description: В этой статье описывается, как можно отключить мониторинг гибридного кластера Kubernetes с помощью Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 86a774737d5269d77c4053ad61ab870b13288aa7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84885854"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Как отключить мониторинг гибридного кластера

После включения мониторинга кластера Kubernetes можно отключить мониторинг кластера с Azure Monitor для контейнеров, если вы решите, что вы больше не хотите его отслеживать. В этой статье показано, как это сделать для следующих сред:

- Подсистема AKS в Azure и Azure Stack
- OpenShift версии 4 и выше
- Kubernetes с поддержкой Azure Arc (предварительная версия)

## <a name="how-to-stop-monitoring-using-helm"></a>Как прерывать мониторинг с помощью Helm

Следующие действия применимы к следующим средам:

- Подсистема AKS в Azure и Azure Stack
- OpenShift версии 4 и выше

1. Чтобы сначала найти Azure Monitor для контейнеров Helm, установленных в кластере, выполните следующую команду Helm.

    ```
    helm list
    ```

    Результат должен выглядеть так:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Азмон-Containers-Release-1* представляет выпуск Helm chart для Azure Monitor для контейнеров.

2. Чтобы удалить выпуск диаграммы, выполните следующую команду Helm.

    `helm delete <releaseName>`

    Пример.

    `helm delete azmon-containers-release-1`

    Выпуск будет удален из кластера. Проверить можно, выполнив `helm list` команду:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Изменение конфигурации может занять несколько минут. Так как Helm отслеживает выпуски даже после их удаления, можно выполнить аудит журнала кластера и даже отменить удаление выпуска с помощью `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Как отключить мониторинг на Kubernetes с поддержкой Arc

### <a name="using-powershell"></a>Использование PowerShell

1. Скачайте и сохраните скрипт в локальной папке, которая настраивает кластер с помощью надстройки мониторинга, используя следующие команды:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Настройте `$azureArcClusterResourceId` переменную, задав соответствующие значения для `subscriptionId` и указав `resourceGroupName` идентификатор ресурса `clusterName` кластера Kubernetes с поддержкой дуги Azure.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Настройте `$kubeContext` переменную с помощью **KUBE-context** в кластере, выполнив команду `kubectl config get-contexts` . Если вы хотите использовать текущий контекст, задайте для него значение `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Выполните следующую команду, чтобы прерывать наблюдение за кластером.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

### <a name="using-bash"></a>Использование bash

1. Скачайте и сохраните скрипт в локальной папке, которая настраивает кластер с помощью надстройки мониторинга, используя следующие команды:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Настройте `azureArcClusterResourceId` переменную, задав соответствующие значения для `subscriptionId` и указав `resourceGroupName` идентификатор ресурса `clusterName` кластера Kubernetes с поддержкой дуги Azure.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Настройте `kubeContext` переменную с помощью **KUBE-context** в кластере, выполнив команду `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Чтобы отключить мониторинг кластера, в зависимости от сценария развертывания существуют различные команды.

    Выполните следующую команду, чтобы отключить мониторинг кластера с помощью текущего контекста.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Выполните следующую команду, чтобы отключить мониторинг кластера, указав контекст.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

## <a name="next-steps"></a>Дальнейшие шаги

Если Рабочая область Log Analytics создана только для поддержки мониторинга кластера и больше не нужна, ее необходимо удалить вручную. Если вы не знакомы с удалением рабочей области, см. статью [удаление log Analytics рабочей области Azure](../../log-analytics/log-analytics-manage-del-workspace.md).
