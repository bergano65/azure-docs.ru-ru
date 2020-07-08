---
title: Подключение кластера Kubernetes с поддержкой Azure Arc (предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Подключение к Azure Arc кластера Kubernetes, который поддерживает Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, контейнеры
ms.custom: references_regions
ms.openlocfilehash: ec77609e5ee30cd3451c52635e530eb7153bc9a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341387"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Подключение кластера Kubernetes с поддержкой Azure Arc (предварительная версия)

Подключите кластер Kubernetes к Azure Arc.

## <a name="before-you-begin"></a>Перед началом

Убедитесь, что выполняются следующие требования:

* Работающий кластер Kubernetes. Если у вас нет кластера Kubernetes, вы можете создать тестовый кластер с помощью одного из следующих руководств:
  * Создание кластера Kubernetes с помощью [Kubernetes в DOCKER (Kind)](https://kind.sigs.k8s.io/)
  * Создание кластера Kubernetes с помощью DOCKER для [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) или [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
* Вам потребуется файл kubeconfig для доступа к кластеру и роли администратора кластера в кластере для развертывания с помощью Arc с включенными агентами Kubernetes.
* Пользователь или субъект-служба, которые будут указаны в командах `az login` и `az connectedk8s connect`, имеют разрешения на чтение и запись для типа ресурса Microsoft.Kubernetes/connectedclusters. Для назначения ролей пользователю или субъекту-службе для подключения через Azure CLI можно применить роль "Azure Arc for Kubernetes Onboarding", у которой есть все нужные разрешения.
* Helm 3 требуется для адаптации кластера с помощью расширения connectedk8s. [Установите последнюю версию Helm 3](https://helm.sh/docs/intro/install) для удовлетворения этого требования.
* Azure CLI версии 2.3 + требуется для установки расширений CLI с включенной службой ARC для Kubernetes. [Установите Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) или обновите последнюю версию, чтобы убедиться в наличии Azure CLI версии 2.3 и выше.
* Установите расширения CLI Kubernetes с поддержкой ARC:
  
  Установите расширение `connectedk8s`, которое поможет подключить кластеры Kubernetes к Azure.
  
  ```console
  az extension add --name connectedk8s
  ```
  
  Установите расширение `k8sconfiguration`:
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  Если вы хотите обновить эти расширения позже, выполните следующие команды:
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Поддерживаемые регионы

* Восточная часть США
* Западная Европа

## <a name="network-requirements"></a>Требования к сети

Для работы агентов Azure Arc требуются следующие протоколы, порты и исходящие URL-адреса.

* TCP на порту 443 (`https://:443`);
* TCP на порту 9418 (`git://:9418`).

| Конечная точка (DNS)                                                                                               | Описание                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Требуется для подключения агента к Azure и регистрации кластера                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Конечная точка плоскости данных, через которую агент будет отправлять сведения о состоянии и извлекать сведения о конфигурации.                                      |
| `https://docker.io`                                                                                            | Требуется для извлечения образов контейнеров.                                                                                         |
| `https://github.com`, git://github.com                                                                         | Примеры репозиториев GitOps размещаются на сайте GitHub. Агенту конфигурации требуется подключение к конечной точке Git, которую вы укажете в настройках. |
| `https://login.microsoftonline.com`                                                                            | Требуется для извлечения и обновления маркеров Azure Resource Manager.                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Требуется агентам Azure Arc для извлечения образов контейнеров.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Регистрация двух поставщиков для Kubernetes с поддержкой Azure Arc:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Регистрация — это асинхронный процесс. На ее завершение может уйти около 10 минут. Процесс регистрации можно контролировать, выполняя следующие команды:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группу ресурсов мы будем использовать для хранения метаданных кластера.

Для начала создайте группу ресурсов, где будет размещен ресурс подключенного кластера.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Выходные данные:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Подключение кластера

Теперь подключите кластер Kubernetes к Azure. Рабочий процесс для `az connectedk8s connect` выглядит следующим образом:

1. Проверьте возможность подключения к кластеру Kubernetes через `KUBECONFIG`, `~/.kube/config` или `--kube-config`.
1. Разверните агенты Azure Arc для Kubernetes с помощью Helm 3 в пространстве имен `azure-arc`.

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Выходные данные:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Проверка подключенного кластера

Получите список подключенных кластеров:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Выходные данные:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Этот ресурс также можно просмотреть на [портал Azure](https://portal.azure.com/). После открытия портала в браузере перейдите к группе ресурсов и ресурсу Kubernetes с включенной службой "Дуга Azure" на основе имени ресурса и входных данных имени группы ресурсов, которые использовались ранее в `az connectedk8s connect` команде.

Kubernetes с поддержкой Azure Arc развертывает в пространстве имен `azure-arc` ряд операторов. Эти развертывания и объекты pod можно просмотреть здесь:

```console
kubectl -n azure-arc get deployments,pods
```

**Выходные данные:**

```console
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

## <a name="azure-arc-agents-for-kubernetes"></a>Агенты Azure Arc для Kubernetes

Kubernetes с поддержкой Azure Arc состоит из нескольких агентов (операторов), которые выполняются в кластере, развернутом в пространстве имен `azure-arc`.

* `deployment.apps/config-agent` наблюдает за подключенным кластером и ищет ресурсы конфигурации системы управления версиями, примененные к этому кластеру, а также обновляет состояние соответствия.
* `deployment.apps/controller-manager` является оператором для операторов, то есть управляет взаимодействием между компонентами Azure Arc.
* `deployment.apps/metrics-agent` собирает метрики от других агентов Azure Arc, чтобы обеспечить оптимальную производительность этих агентов.
* `deployment.apps/cluster-metadata-operator` собирает метаданные кластера: версию кластера, число узлов и версию агента Azure Arc.
* `deployment.apps/resource-sync-agent` синхронизирует упомянутые выше метаданные кластера с Azure.
* `deployment.apps/clusteridentityoperator`: Служба "Дуга Azure" Kubernetes сейчас поддерживает назначенное системой удостоверение. клустеридентитйоператор хранит сертификат управляемого удостоверения службы (MSI), используемый другими агентами для взаимодействия с Azure.
* `deployment.apps/flux-logs-agent` собирает журналы из операторов Flux, развернутых в составе конфигурации системы управления версиями.

## <a name="delete-a-connected-cluster"></a>Удаление подключенного кластера

Ресурс `Microsoft.Kubernetes/connectedcluster` можно удалить с помощью Azure CLI или портала Azure.


* **Удаление с помощью Azure CLI**: можно использовать следующую команду Azure CLI для инициации удаления ресурса Kubernetes с включенной службой "Дуга Azure".
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Это приведет к удалению `Microsoft.Kubernetes/connectedCluster` ресурса и всех связанных `sourcecontrolconfiguration` с ним ресурсов в Azure. Azure CLI использует удаление Helm для удаления агентов, выполняющихся в кластере.

* **Удаление на портал Azure**: Удаление ресурса Kubernetes в службе "Дуга Azure" на портал Azure удаление `Microsoft.Kubernetes/connectedcluster` ресурса и всех связанных `sourcecontrolconfiguration` ресурсов в Azure, но не удаление агентов, выполняющихся в кластере. Чтобы удалить агенты, запущенные в кластере, выполните следующую команду.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Дальнейшие действия

* [Использование GitOps в подключенном кластере](./use-gitops-connected-cluster.md)
* [Использование Политики Azure для управления конфигурацией кластера](./use-azure-policy.md)
