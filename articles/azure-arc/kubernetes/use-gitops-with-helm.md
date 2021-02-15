---
title: Развертывание диаграмм Helm с помощью Гитопс в кластере Kubernetes с поддержкой ARC (Предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Использование Гитопс с Helm для конфигурации кластера с поддержкой Arc Azure (Предварительная версия)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: 883eb9c152bdc8a7c0e60e999cf9decf47fb80ec
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377930"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Развертывание диаграмм Helm с помощью Гитопс в кластере Kubernetes с поддержкой ARC (Предварительная версия)

Средство упаковки с открытым кодом Helm помогает устанавливать приложения Kubernetes и управлять их жизненным циклом. Как и для диспетчеров пакетов Linux, таких как APT и YUM, Helm используется для управления диаграммами Kubernetes, которые представляют собой пакеты предварительно настроенных ресурсов Kubernetes.

В этой статье показано, как настроить и применить Helm в кластере Kubernetes с поддержкой Azure Arc.

## <a name="before-you-begin"></a>Перед началом

Убедитесь, что у вас уже есть подключенный кластер Kubernetes с включенной службой "Дуга Azure". Если вам нужен подключенный кластер, ознакомьтесь с кратким руководством по подключению [кластера Azure ARC с поддержкой Kubernetes](./connect-cluster.md).

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Общие сведения об использовании Гитопс и Helm с поддержкой ARC в Azure Kubernetes

 Оператор Helm предоставляет расширение для Flux, которое автоматизирует выпуски Helm Chart. Выпуск Helm Chart описывается с помощью настраиваемого ресурса Kubernetes с именем Хелмрелеасе. Flux синхронизирует эти ресурсы из Git с кластером, а оператор Helm обеспечивает выпуск Helm диаграмм, указанных в ресурсах.

 [Пример репозитория](https://github.com/Azure/arc-helm-demo) , используемый в этой статье, структурирован следующим образом.

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

В репозитории Git у нас есть два каталога: один содержит Helm диаграмму, а другой — файл конфигурации выпусков. В `releases` каталоге `app.yaml` содержит конфигурацию хелмрелеасе, показанную ниже:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Конфигурация выпусков Helm содержит следующие поля:

| Поле | Описание |
| ------------- | ------------- | 
| `metadata.name` | Обязательное поле. Необходимо соблюдать соглашения об именовании Kubernetes. |
| `metadata.namespace` | Дополнительное поле. Определяет место создания выпуска. |
| `spec.releaseName` | Дополнительное поле. Если не указано имя выпуска, будет использоваться значение `$namespace-$name` . |
| `spec.chart.path` | Каталог, содержащий диаграмму, заданный относительно корня репозитория. |
| `spec.values` | Пользовательские настройки значений параметров по умолчанию из самой диаграммы. |

Параметры, заданные в Хелмрелеасе, `spec.values` будут переопределять параметры, заданные в `values.yaml` источнике диаграммы.

Дополнительные сведения о Хелмрелеасе см. в официальной [документации по оператору Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Создание конфигурации

Используя расширение Azure CLI для `k8sconfiguration` , свяжите подключенный кластер с примером репозитория Git. Присвойте этой конфигурации имя `azure-arc-sample` и разверните оператор Flux в `arc-k8s-demo` пространстве имен.

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Параметры конфигурации

Чтобы настроить создание конфигурации, [Ознакомьтесь с дополнительными параметрами, которые вы можете использовать](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Проверка конфигурации

С помощью Azure CLI убедитесь, что объект `sourceControlConfiguration` был успешно создан.

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
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
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
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

Выполните следующую команду и перейдите в `localhost:8080` браузере, чтобы убедиться, что приложение запущено.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Дальнейшие действия

- [Использование Политики Azure для управления конфигураций кластера](./use-azure-policy.md)
