---
title: Подключение кластера Kubernetes с поддержкой дуги Azure (Предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Подключение кластера Kubernetes с поддержкой дуги Azure с помощью дуги Azure
keywords: Kubernetes, Arc, Azure, K8s, контейнеры
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 876bc15a3f4db1d12afec37c69656c431e5e6773
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652519"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Подключение кластера Kubernetes с поддержкой дуги Azure (Предварительная версия)

В этой статье описывается подключение существующего кластера Kubernetes к службе "Дуга Azure". Концептуальные инструкции по подключению кластеров см. в [статье об архитектуре агента Kubernetes, включенной в дугу Azure](./conceptual-agent-architecture.md).

## <a name="before-you-begin"></a>Перед началом

Убедитесь, что вы подготовили следующие необходимые компоненты:

* Работающий кластер Kubernetes. Если у вас нет кластера Kubernetes, вы можете создать тестовый кластер с помощью одного из следующих руководств:
  * Создание кластера Kubernetes с помощью [Kubernetes в DOCKER (Kind)](https://kind.sigs.k8s.io/).
  * Создайте кластер Kubernetes с помощью DOCKER для [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) или [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* Kubeconfig-файл для доступа к кластеру и роли администратора кластера в кластере для развертывания с помощью Arc с включенными агентами Kubernetes.
* Разрешения "чтение" и "запись" для пользователя или субъекта-службы, используемые с `az login` `az connectedk8s connect` командами и для `Microsoft.Kubernetes/connectedclusters` типа ресурса. Роль "кластер Kubernetes с подключением ARC в Azure" имеет эти разрешения и может использоваться для назначений ролей пользователя или субъекта-службы.
* Helm 3 для адаптации кластера с помощью `connectedk8s` расширения. [Установите последнюю версию Helm 3](https://helm.sh/docs/intro/install) для удовлетворения этого требования.
* Azure CLI версии 2.15 + для установки расширений CLI Kubernetes для службы "Дуга Azure". [Установите Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) или обновите последнюю версию.
* В службе "Дуга Azure" включены расширения CLI Kubernetes:
  
  * Установите `connectedk8s` расширение, помогающее подключить кластеры Kubernetes к Azure:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Установите расширение `k8sconfiguration`:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Если вы хотите обновить эти расширения позже, выполните следующие команды:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Поддерживаемые регионы

* Восточная часть США
* Западная Европа

## <a name="network-requirements"></a>Требования к сети

Для работы агентов Azure Arc требуются следующие протоколы, порты и исходящие URL-адреса:

* TCP через порт 443: `https://:443`
* TCP через порт 9418: `git://:9418`

| Конечная точка (DNS)                                                                                               | Описание                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Требуется для подключения агента к Azure и регистрации кластера.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Конечная точка плоскости данных для агента, чтобы отправить сведения о состоянии и получить информацию о конфигурации.                                      |
| `https://login.microsoftonline.com`                                                                            | Требуется для выборки и обновления маркеров Azure Resource Manager.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Требуется для извлечения образов контейнеров для агентов Arc Azure.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Требуется для запроса назначенных системой сертификатов Управляемое удостоверение службы (MSI).                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Регистрация двух поставщиков для Kubernetes с включенной службой "Дуга Azure"

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Регистрация — это асинхронный процесс, который может занять около 10 минут. Процесс регистрации можно контролировать, выполняя следующие команды:

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

Далее мы подключим наш кластер Kubernetes к Azure, используя `az connectedk8s connect` :

1. Проверьте подключение к кластеру Kubernetes одним из следующих способов:
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. Разверните агенты Arc Azure для Kubernetes, используя Helm 3 в `azure-arc` пространстве имен:

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
  "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
  "agentVersion": null,
  "connectivityStatus": "Connecting",
  "distribution": "gke",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "infrastructure": "gcp",
  "kubernetesVersion": null,
  "lastConnectivityTime": null,
  "location": "eastus",
  "managedIdentityCertificateExpirationTime": null,
  "name": "AzureArcTest1",
  "offering": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalCoreCount": null,
  "totalNodeCount": null,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Проверка подключенного кластера

Чтобы получить список подключенных кластеров, используйте следующую команду:

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

Этот ресурс также можно просмотреть на [портал Azure](https://portal.azure.com/).
1. Откройте портал в браузере.
1. Перейдите к группе ресурсов и ресурсу Kubernetes с включенной службой "Дуга Azure" на основе имен ресурсов и имен групп ресурсов, которые использовались ранее в `az connectedk8s connect` команде.  
> [!NOTE]
> После подключения кластера потребуется от 5 до 10 минут для метаданных кластера (версия кластера, версия агента, число узлов и т. д.) на странице обзора ресурса Kubernetes в службе "Дуга Azure", включенной в портал Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Подключение с использованием исходящего прокси-сервера

Если кластер находится за исходящим прокси-сервером, Azure CLI и агенты Kubernetes, включенные в службу "Дуга Azure", должны маршрутизировать свои запросы через исходящий прокси-сервер:

1. Проверьте версию расширения, `connectedk8s` установленного на компьютере:

    ```console
    az -v
    ```

    `connectedk8s`Для настройки агентов с исходящим прокси-сервером требуется расширение 0.2.5 +. Если на компьютере установлена версия 0.2.3 или более старая, выполните инструкции по [обновлению](#before-you-begin) , чтобы получить последнюю версию расширения на компьютере.

2. Задайте переменные среды, необходимые для Azure CLI для использования исходящего прокси-сервера:

    * Если вы используете bash, выполните следующую команду с соответствующими значениями:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Если вы используете PowerShell, выполните следующую команду с соответствующими значениями:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

3. Выполните команду Connect с указанными параметрами прокси-сервера:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Указание `excludedCIDR` в разделе `--proxy-skip-range` важно для того, чтобы обеспечить передачу данных в кластере для агентов не разбивается.
> * Несмотря на то, что `--proxy-http` `--proxy-https` `--proxy-skip-range` для большинства сред исходящих прокси-серверов ожидается, и, `--proxy-cert` требуется только в том случае, если доверенные сертификаты от прокси-сервера необходимо внедрить в хранилище доверенных сертификатов модулей Pod агента.
> * Приведенная выше спецификация прокси-сервера в настоящее время применяется только для агентов Arc Azure, а не для модулей Flux, используемых в `sourceControlConfiguration` . Группа Kubernetes с поддержкой дуги Azure активно работает над этой функцией.

## <a name="azure-arc-agents-for-kubernetes"></a>Агенты Arc Azure для Kubernetes

Kubernetes с поддержкой Azure Arc развертывает в пространстве имен `azure-arc` ряд операторов. Для просмотра этих развертываний и модулей Pod можно использовать:

```console
kubectl -n azure-arc get deployments,pods
```

**Выходные данные:**

```console
NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
deployment.apps/cluster-metadata-operator     1/1             1        1      16h
deployment.apps/clusteridentityoperator       1/1             1        1      16h
deployment.apps/config-agent                  1/1             1        1      16h
deployment.apps/controller-manager            1/1             1        1      16h
deployment.apps/flux-logs-agent               1/1             1        1      16h
deployment.apps/metrics-agent                 1/1             1        1      16h
deployment.apps/resource-sync-agent           1/1             1        1      16h

NAME                                           READY    STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Убедитесь, что все модули Pod находятся в `Running` состоянии.

## <a name="delete-a-connected-cluster"></a>Удаление подключенного кластера

Ресурс `Microsoft.Kubernetes/connectedcluster` можно удалить с помощью Azure CLI или портала Azure.


* **Удаление с помощью Azure CLI**: выполните следующую команду Azure CLI, чтобы инициировать удаление ресурса Kubernetes с включенной службой "Дуга Azure".
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Эта команда удаляет `Microsoft.Kubernetes/connectedCluster` ресурс и все связанные `sourcecontrolconfiguration` с ним ресурсы в Azure. Azure CLI использует `helm uninstall` для удаления агентов, выполняющихся в кластере.

* **Удаление портал Azure**: Удаление ресурса Kubernetes для службы "Дуга Azure" на портал Azure удаление `Microsoft.Kubernetes/connectedcluster` ресурса и всех связанных `sourcecontrolconfiguration` с ним ресурсов в Azure, но *не* удаление агентов, выполняющихся в кластере. 

  Чтобы удалить агенты, запущенные в кластере, выполните следующую команду:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Дальнейшие действия

* [Использование GitOps в подключенном кластере](./use-gitops-connected-cluster.md)
* [Использование Политики Azure для управления конфигурацией кластера](./use-azure-policy.md)
