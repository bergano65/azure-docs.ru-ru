---
title: Использование GitOps и Helm для настройки кластера с поддержкой Azure Arc (предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Использование GitOps и Helm для настройки кластера с поддержкой Azure Arc (предварительная версия)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: 9cd1169c7a622da0e4be3900f94dc31fc99e762d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83662542"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>Использование GitOps и Helm для настройки кластера с поддержкой Azure Arc (предварительная версия)

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

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>Сведения об использовании Helm для Kubernetes с поддержкой Azure Arc

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
    └── prod
        └── azure-vote-app.yaml
```

В репозитории Git есть два каталога, один из которых содержит чарт Helm, а другой — конфигурацию выпусков. В каталоге `releases/prod` есть файл `azure-vote-app.yaml` с представленной ниже конфигурацией HelmRelease:

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

Конфигурация выпусков Helm содержит следующие поля:

- `metadata.name` является обязательным и должно соответствовать соглашению об именовании для Kubernetes;
- `metadata.namespace` является необязательным и определяет место создания выпуска;
- `spec.releaseName` является необязательным и если не указано, определяется как $namespace-$name;
- `spec.chart.path` обозначает каталог, содержащий чарт, с учетом корня репозитория;
- `spec.values` содержат пользовательские настройки для значений параметров самого чарта по умолчанию.

Параметры, указанные в файле HelmRelease spec.values, переопределяют параметры, заданные в файле values.yaml из источника чарта.

Дополнительные сведения о HelmRelease см. в официальной [документации по оператору Helm](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html).

## <a name="create-a-configuration"></a>Создание конфигурации

Используя расширение Azure CLI для `k8sconfiguration`, свяжите подключенный кластер с примером репозитория Git. Мы присвоим этой конфигурации имя `azure-voting-app` и развернем оператор Flux в пространстве имен `prod`.

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Параметры конфигурации

Чтобы настроить создание конфигурации, [узнайте о дополнительных параметрах, которые можно использовать](./use-gitops-connected-cluster.md#additional-parameters).


## <a name="validate-the-configuration"></a>Проверка конфигурации

С помощью Azure CLI убедитесь в успешном создании `sourceControlConfiguration`.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Обратите внимание, что в ресурс `sourceControlConfiguration` сохраняются состояние соответствия, сообщения и отладочная информация.

**Выходные данные:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Compliant",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Проверка приложения

Теперь мы убедимся, что приложение запущено и работает, получив IP-адрес службы.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Выходные данные:**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Найдите внешний IP-адрес в приведенных выше выходных данных и откройте его в браузере.

## <a name="next-steps"></a>Дальнейшие действия

- [Использование Политики Azure для управления конфигураций кластера](./use-azure-policy.md)
