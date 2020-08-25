---
title: Устранение распространенных проблем Kubernetes с поддержкой Azure Arc (предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Устранение распространенных проблем с кластерами Kubernetes с поддержкой Arc.
keywords: Kubernetes, Arc, Azure, контейнеры
ms.openlocfilehash: 404516778255409d56dd5c3a7d1fd96711cc981f
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723679"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Устранение неполадок Kubernetes с поддержкой Azure Arc (предварительная версия)

В этом документе содержатся некоторые распространенные сценарии устранения неполадок с подключением, разрешениями и агентами.

## <a name="general-troubleshooting"></a>Общие действия по устранению неполадок

### <a name="azure-cli-set-up"></a>Настройка Azure CLI
Перед использованием команд az connectedk8s или az k8sconfiguration убедитесь, что az настроен для работы с правильной подпиской Azure.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Агенты azure-arc
Все агенты для Kubernetes с поддержкой Azure Arc развертываются в виде объектов pod в пространстве имен `azure-arc`. В обычных условиях должны выполняться все объекты pod, и они должны проходить проверку работоспособности.

Сначала проверьте выпуск Azure Arc Helm.

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Если выпуск Helm не найден или отсутствует, попробуйте подключить кластер еще раз.

Если выпуск Helm имеется и `STATUS: deployed`, определите состояние агентов с помощью `kubectl`:

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Для всех объектов pod значение параметра `STATUS` должно отображаться как `Running`, а значение параметра `READY` — либо `3/3`, либо `2/2`. Получите журналы и описания объектов pod, которые возвращают `Error` или `CrashLoopBackOff`. Если какая-либо из этих модулей повреждена, `Pending` это может быть вызвано нехваткой ресурсов на узлах кластера. При увеличении [масштаба кластера](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#resizing-a-cluster) эти модули могут переходить в `Running` состояние.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Подключение кластеров Kubernetes к службе "Дуга Azure"

Для подключения кластеров к Azure требуется доступ к подписке Azure и доступ `cluster-admin` к целевому кластеру. Если кластер недоступен или у него недостаточно разрешений, подключение завершится сбоем.

### <a name="insufficient-cluster-permissions"></a>Недостаточно разрешений для кластера

Если предоставленный файл kubeconfig не имеет достаточных разрешений для установки агентов Azure Arc, команда Azure CLI вернет ошибку при попытке вызова API Kubernetes.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Владелец кластера должен использовать пользователя Kubernetes с разрешениями администратора кластера.

### <a name="installation-timeouts"></a>Время ожидания установки

Для установки агента Azure Arc требуется запустить набор контейнеров в целевом кластере. Если для работы с кластером используется медленное подключение к Интернету, извлечение образа контейнера может занять больше времени, чем время ожидания Azure CLI.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

## <a name="configuration-management"></a>Управление конфигурацией

### <a name="general"></a>Общие сведения
Чтобы устранить проблемы с конфигурацией системы управления версиями, выполните команды az с параметром --debug.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Создание конфигурации системы управления версиями
Роль участника для ресурса Microsoft.Kubernetes/connectedCluster является обязательным условием. Ее достаточно для создания ресурса Microsoft.KubernetesConfiguration/sourceControlConfiguration.

### <a name="configuration-remains-pending"></a>Конфигурация остается `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Наблюдение

Azure Monitor для контейнеров требуется, чтобы его демон был запущен в привилегированном режиме. Чтобы успешно настроить канонический Kubernetes кластер для мониторинга, выполните следующую команду:

```console
juju config kubernetes-worker allow-privileged=true
```