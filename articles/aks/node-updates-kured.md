---
title: Ручка Linux узла перезагрузки с kured
titleSuffix: Azure Kubernetes Service
description: Узнайте, как обновить узлы Linux и автоматики перезагрузить их с помощью kured в Службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 8006baa3025ee1e794359bed854094cc9005dd14
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668384"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Применяйте обновления безопасности и ядра к узлам Linux в службе Azure Kubernetes (AKS)

Для защиты кластеров обновления безопасности автоматически применяются к узлам Linux в AKS. Учитываются такие обновления, как исправления безопасности для операционной системы и обновления ядра. Чтобы завершить установку некоторых обновлений, нужно перезагрузить узел. AKS не автоматически перезагружает эти узлы Linux для завершения процесса обновления.

Процесс обновления узлов Windows Server (в настоящее время в предварительном просмотре в AKS) немного отличается. Узлы Windows Server не получают ежедневных обновлений. Вместо этого выполняется обновление AKS, которое развертывает новые узлы с последним базовым изображением Window Server и патчами. Для кластеров AKS, которые используют узлы Windows Server, [см.][nodepool-upgrade]

В этой статье показано, как использовать kured с открытым исходным кодом [(KUbernetes REboot Daemon)][kured] для просмотра узлов Linux, требующих перезагрузки, а затем автоматически обрабатывать перепланирование ходовых стручков и процесс перезагрузки узлов.

> [!NOTE]
> `Kured` создается как проект Weaveworks с открытым кодом. Поддержка для этого проекта в AKS предоставляется только по мере возможности. Дополнительную поддержку можно найти в канале slack #weave сообщества.

## <a name="before-you-begin"></a>Подготовка к работе

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Вам также нужна версия Azure CLI 2.0.59 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Основные сведения об обновлении узла AKS

В кластере AKS узлы Kubernetes выполняются на виртуальных машинах Azure. Эти виртуальные машины под управлением Linux создаются на основе образа Ubuntu. В операционной системе настраивается автоматическая проверка наличия обновлений, выполняющаяся каждую ночь. Все доступные обновления безопасности или ядра скачиваются и устанавливаются автоматически.

![Процесс обновления и перезагрузка узла AKS с помощью kured](media/node-updates-kured/node-reboot-process.png)

Чтобы завершить установку некоторых обновлений системы безопасности, например обновлений ядра, нужно перезагрузить узел. Узла Linux, требующего перезагрузки, создает файл под названием */var/run/reboot-required.* При этом перезагрузка не выполняется автоматически.

Вы можете использовать собственные рабочие процессы и системы для обработки запросов на перезагрузку узла или оркестрировать этот процесс с помощью `kured`. С `kured`, [DaemonSet][DaemonSet] развернута, которая работает стручок на каждом узлах Linux в кластере. Эти стручки в DaemonSet наблюдают за существованием *файла/вар/запуска/перезагрузки,* а затем инициируют процесс перезагрузки узлов.

### <a name="node-upgrades"></a>Обновления узлов

В AKS есть специальный дополнительный процесс для *обновления* кластера. Обычно обновление подразумевает переход на новую версию Kubernetes, а не просто обновления безопасности для узла. При обновлении AKS выполняются следующие действия:

* развертывание нового узла со всеми последними обновлениями безопасности и новой версией Kubernetes;
* блокировка старого узла и остановка процессов на нем;
* назначение выполнения pod на новом узле;
* удаление старого узла.

При обработке события обновления нельзя сохранить старую версию Kubernetes. Необходимо указать более новую версию Kubernetes. Чтобы перейти на последнюю версию Kubernetes, вы можете [обновить кластер AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Развертывание kured в кластере AKS

Чтобы развернуть `kured` DaemonSet, установите следующую официальную диаграмму Kured Helm. Это создает роль роли и кластера, привязки и учетную запись `kured`службы, а затем развертывает DaemonSet с помощью .

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Также вы можете настроить дополнительные параметры для `kured`, например интеграцию с Prometheus или Slack. Для получения дополнительной информации о [kured Helm chart][kured-install]дополнительных параметрах конфигурации, см.

## <a name="update-cluster-nodes"></a>Обновление узлов кластера

По умолчанию узлы Linux в AKS проверяют обновления каждый вечер. Если вы не хотите ждать, обновление можно запустить вручную и убедиться, что `kured` выполняется правильно. Сначала установите [подключение по SSH к одному из узлов AKS][aks-ssh]. Если у вас есть подключение SSH к узну Linux, проверьте наличие обновлений и применяйте их следующим образом:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Если эти обновления требуют перезагрузки узла, создается файл */var/run/reboot-required*. По умолчанию `Kured` каждые 60 минут проверяет наличие узлов, которые требуют перезагрузки.

## <a name="monitor-and-review-reboot-process"></a>Мониторинг и проверка процесса перезагрузки

Если одна из реплик DaemonSet определит, что узлу требуется перезагрузка, к этому узлу применяется блокировка через API Kubernetes. Такая блокировка запрещает размещение на этом узле дополнительных групп pod. Также блокировка гарантирует, что за один раз перезагружается только один узел. После применения блокировки на этом узле постепенно завершается выполнение всех групп pod, а затем узел перезагружается.

Состояние узлов можно отслеживать с помощью команды [kubectl get nodes][kubectl-get-nodes]. В следующем примере выходных данных показан узел с состоянием *SchedulingDisabled*, который готовится к процессу перезагрузки:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

После обновления вы можете проверить состояние узлов с помощью команды [kubectl get nodes][kubectl-get-nodes] с параметром `--output wide`. В представленной ниже новой версии выходных данных вы можете заметить разницу в значениях *KERNEL-VERSION* для базовых узлов. *Aks-nodepool1-28993262-0* был обновлен на предыдущем этапе и показывает версию ядра *4.15.0-1039-azure*. Узел *aks-nodepool1-28993262-1,* который не был обновлен, показывает версию ядра *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Следующие шаги

В этой статье `kured` подробно описано, как использовать для автоматической перезагрузки узлов Linux в процессе обновления безопасности. Чтобы перейти на последнюю версию Kubernetes, вы можете [обновить кластер AKS][aks-upgrade].

Для кластеров AKS, которые используют узлы Windows Server, [см.][nodepool-upgrade]

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
