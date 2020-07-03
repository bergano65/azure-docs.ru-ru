---
title: Работа с перезагрузками узла Linux с помощью куред
titleSuffix: Azure Kubernetes Service
description: Сведения об обновлении узлов Linux и их автоматической перезагрузке с помощью куред в службе Kubernetes Azure (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 955e5323769a7b9bf80413c045aaa3d55547eb02
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208080"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Применение обновлений безопасности и ядра для узлов Linux в службе Kubernetes Azure (AKS)

Для защиты кластеров обновления безопасности автоматически применяются к узлам Linux в AKS. Учитываются такие обновления, как исправления безопасности для операционной системы и обновления ядра. Чтобы завершить установку некоторых обновлений, нужно перезагрузить узел. AKS не выполняет автоматическую перезагрузку этих узлов Linux для завершения процесса обновления.

Процесс обновления узлов Windows Server в актуальном состоянии немного отличается. Узлы Windows Server не получают ежедневных обновлений. Вместо этого вы выполняете обновление AKS, которое развертывает новые узлы с помощью последнего образа и исправлений сервера базового окна. Для кластеров AKS, использующих узлы Windows Server, см. статью [Обновление пула узлов в AKS][nodepool-upgrade].

В этой статье показано, как использовать управляющую программу куред с открытым исходным кодом [(демон перезагрузки KUbernetes)][kured] для просмотра узлов Linux, требующих перезагрузки, а затем автоматически обрабатывать перепланирование запущенных модулей Pod и перезагрузки узла.

> [!NOTE]
> `Kured` создается как проект Weaveworks с открытым кодом. Поддержка для этого проекта в AKS предоставляется только по мере возможности. Дополнительную поддержку можно найти в #weaveном канале резервного времени сообщества.

## <a name="before-you-begin"></a>Подготовка к работе

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Также требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Основные сведения об обновлении узла AKS

В кластере AKS узлы Kubernetes выполняются на виртуальных машинах Azure. Эти виртуальные машины под управлением Linux создаются на основе образа Ubuntu. В операционной системе настраивается автоматическая проверка наличия обновлений, выполняющаяся каждую ночь. Все доступные обновления безопасности или ядра скачиваются и устанавливаются автоматически.

![Процесс обновления и перезагрузка узла AKS с помощью kured](media/node-updates-kured/node-reboot-process.png)

Чтобы завершить установку некоторых обновлений системы безопасности, например обновлений ядра, нужно перезагрузить узел. Узел Linux, требующий перезагрузки, создает файл с именем */Вар/рун/ребут-рекуиред*. При этом перезагрузка не выполняется автоматически.

Вы можете использовать собственные рабочие процессы и системы для обработки запросов на перезагрузку узла или оркестрировать этот процесс с помощью `kured`. `kured`В используется развертываемый набор [управляющих][DaemonSet] программ, который запускает модуль на каждом узле Linux в кластере. Эти модули Pod в наборе управляющих программ для определения существования файла */Вар/рун/ребут-рекуиред* , а затем запуска процесса перезагрузки узлов.

### <a name="node-upgrades"></a>Обновления узлов

В AKS есть специальный дополнительный процесс для *обновления* кластера. Обычно обновление подразумевает переход на новую версию Kubernetes, а не просто обновления безопасности для узла. При обновлении AKS выполняются следующие действия:

* развертывание нового узла со всеми последними обновлениями безопасности и новой версией Kubernetes;
* блокировка старого узла и остановка процессов на нем;
* назначение выполнения pod на новом узле;
* удаление старого узла.

При обработке события обновления нельзя сохранить старую версию Kubernetes. Необходимо указать более новую версию Kubernetes. Чтобы перейти на последнюю версию Kubernetes, вы можете [обновить кластер AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Развертывание kured в кластере AKS

Чтобы развернуть `kured` Демон, установите следующую официальную диаграмму куред Helm. При этом создается роль, а также роль кластера, привязки и учетная запись службы, а затем развертывается набор управляющих программ с помощью `kured`.

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

Также вы можете настроить дополнительные параметры для `kured`, например интеграцию с Prometheus или Slack. Дополнительные сведения о дополнительных параметрах конфигурации см. в разделе [Куред Helm Chart][kured-install].

## <a name="update-cluster-nodes"></a>Обновление узлов кластера

По умолчанию узлы Linux в AKS проверяют наличие обновлений каждый вечер. Если вы не хотите ждать, обновление можно запустить вручную и убедиться, что `kured` выполняется правильно. Сначала установите [подключение по SSH к одному из узлов AKS][aks-ssh]. Получив SSH-подключение к узлу Linux, проверьте наличие обновлений и примените их следующим образом.

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

После обновления вы можете проверить состояние узлов с помощью команды [kubectl get nodes][kubectl-get-nodes] с параметром `--output wide`. В представленной ниже новой версии выходных данных вы можете заметить разницу в значениях *KERNEL-VERSION* для базовых узлов. *AKS-nodepool1-28993262-0* был обновлен на предыдущем шаге и содержит версию ядра *4.15.0-1039-Azure*. Узел *AKS-nodepool1-28993262-1* , который не был обновлен, отображает версию ядра *4.15.0-1037-Azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье описано, как `kured` использовать для автоматической перезагрузки узлов Linux в рамках процесса обновления системы безопасности. Чтобы перейти на последнюю версию Kubernetes, вы можете [обновить кластер AKS][aks-upgrade].

Для кластеров AKS, использующих узлы Windows Server, см. статью [Обновление пула узлов в AKS][nodepool-upgrade].

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
