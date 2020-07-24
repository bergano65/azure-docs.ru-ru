---
title: Развертывание диаграмм Helm с помощью Гитопс в кластере Kubernetes с поддержкой ARC (Предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Использование GitOps и Helm для настройки кластера с поддержкой Azure Arc (предварительная версия)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: f6a30dd66ccf476da0293bdebf9054b6781a6bf6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049939"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Развертывание диаграмм Helm с помощью Гитопс в кластере Kubernetes с поддержкой ARC (Предварительная версия)

Средство упаковки с открытым кодом Helm помогает устанавливать приложения Kubernetes и управлять их жизненным циклом. Как и диспетчеры пакетов Linux, например, APT и Yum, Helm используется для управления чартами Kubernetes, которые представляют собой пакеты предварительно настроенных ресурсов Kubernetes.

В этой статье показано, как настроить и применить Helm в кластере Kubernetes с поддержкой Azure Arc.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть подключенный кластер Kubernetes с поддержкой Azure Arc. Если вам нужно создать подключенный кластер, см. это [краткое руководство](./connect-cluster.md).

Для начала задайте переменные среды, которые будут использоваться в примерах в этом руководстве. Для подключенного кластера нужны имена группы ресурсов и кластера.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Проверка поддержки Arc в кластере

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Выходные данные:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Общие сведения об использовании Гитопс и Helm с поддержкой ARC в Azure Kubernetes

 Оператор Helm предоставляет расширение для Flux, которое автоматизирует выпуски Helm Chart. Выпуск Chart описывается через настраиваемый ресурс Kubernetes с именем HelmRelease. Flux синхронизирует эти ресурсы из репозитория Git в кластер, а оператор Helm гарантирует, что чарты Helm будут выпускаться в соответствии с описанием в этих ресурсах.

 Ниже приведен пример структуры репозитория Git, которую мы будем использовать в этом руководстве:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── vote-app.yaml
```

В репозитории Git у нас есть два каталога, один из которых содержит Helm диаграмму, а другой — файл конфигурации выпусков. В `releases` каталоге `vote-app.yaml` содержит конфигурацию хелмрелеасе, показанную ниже:

```bash
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: vote-app
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-vote
  values:
    frontendServiceName: arc-k8s-demo-vote-front
```

Конфигурация выпусков Helm содержит следующие поля:

- `metadata.name` является обязательным и должно соответствовать соглашению об именовании для Kubernetes;
- `metadata.namespace` является необязательным и определяет место создания выпуска;
- `spec.releaseName` является необязательным и если не указано, определяется как $namespace-$name;
- `spec.chart.path` обозначает каталог, содержащий чарт, с учетом корня репозитория;
- `spec.values` содержат пользовательские настройки для значений параметров самого чарта по умолчанию.

Параметры, указанные в файле HelmRelease spec.values, переопределяют параметры, заданные в файле values.yaml из источника чарта.

Дополнительные сведения о HelmRelease см. в официальной [документации по оператору Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Создание конфигурации

Используя расширение Azure CLI для `k8sconfiguration`, свяжите подключенный кластер с примером репозитория Git. Мы присвоим этой конфигурации имя `azure-voting-app` и развернем оператор Flux в пространстве имен `arc-k8s-demo`.

```bash
az k8sconfiguration create --name azure-voting-app \
  --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME \
  --operator-instance-name flux --operator-namespace arc-k8s-demo \
  --operator-params='--git-readonly --git-path=releases' \
  --enable-helm-operator --helm-operator-version='0.6.0' \
  --helm-operator-params='--set helm.versions=v3' \
  --repository-url https://github.com/Azure/arc-helm-demo.git  \
  --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Параметры конфигурации

Чтобы настроить создание конфигурации, [узнайте о дополнительных параметрах, которые можно использовать](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Проверка конфигурации

С помощью Azure CLI убедитесь в успешном создании `sourceControlConfiguration`.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

`sourceControlConfiguration`Ресурс обновляется с учетом состояния соответствия, сообщений и отладочной информации.

**Выходные данные:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-voting-app",
  "name": "azure-voting-app",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Проверка приложения

Выполните следующую команду и перейдите к [localhost: 3000](http://localhost:3000) в браузере, чтобы убедиться, что приложение запущено.

```bash
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo-vote-front 3000:80
```

## <a name="next-steps"></a>Дальнейшие действия

- [Использование Политики Azure для управления конфигураций кластера](./use-azure-policy.md)
