---
title: Автоматическое масштабирование модулей AKS с помощью метрик шлюза приложений Azure
description: В этой статье приведены инструкции по масштабированию модулей AKS серверной части с помощью метрик шлюза приложений и адаптера метрик Azure Kubernetes.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5e0533a44db269229b2f26fa8d2f2b4f84f4d0b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85125469"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Автоматическое масштабирование модулей AKS с помощью метрик шлюза приложений (бета-версия)

При увеличении входящего трафика становится крайне важным, чтобы масштабировать приложения в зависимости от спроса.

В следующем руководстве объясняется, как можно использовать метрику шлюза приложений `AvgRequestCountPerHealthyHost` для масштабирования приложения. `AvgRequestCountPerHealthyHost` измеряет среднее число запросов, отправленных в определенный серверный пул и сочетание параметров HTTP серверной части.

Мы будем использовать следующие два компонента:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) — Мы будем использовать адаптер метрики для предоставления метрик шлюза приложений через сервер метрик. Адаптер метрик Azure Kubernetes — это проект с открытым исходным кодом в Azure, аналогичный контроллеру входящего трафика шлюза приложений. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) — Мы будем использовать HPA для использования метрик шлюза приложений и нацелены на развертывание для масштабирования.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Настройка адаптера метрик Azure Kubernetes

1. Сначала необходимо создать субъект-службу Azure AAD и назначить ему `Monitoring Reader` доступ к группе ресурсов шлюза приложений. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Теперь мы развернемся [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) с помощью созданного выше субъекта-службы AAD.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Мы создадим `ExternalMetric` ресурс с именем `appgw-request-count-metric` . Этот ресурс попросит адаптеру метрик предоставить `AvgRequestCountPerHealthyHost` метрику для `myApplicationGateway` ресурса в `myResourceGroup` группе ресурсов. Это поле можно использовать `filter` для назначения определенного внутреннего пула и параметра HTTP серверной части в шлюзе приложений.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Теперь можно выполнить запрос к серверу метрик, чтобы узнать, предоставлена ли нам новая метрика:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Использование новой метрики для увеличения масштаба развертывания

После того как мы сможем предоставить доступ `appgw-request-count-metric` через сервер метрик, мы готовы к использованию [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) для масштабирования целевого развертывания.

В следующем примере мы будем ориентироваться на пример развертывания `aspnet` . Мы будем масштабировать модули Pod при `appgw-request-count-metric` > 200 на каждый модуль до максимального числа `10` модулей.

Замените имя целевого развертывания и примените следующую конфигурацию автоматического масштабирования:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Протестируйте настройку с помощью средства нагрузочного тестирования, например Apache Bench:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Дальнейшие действия
- [**Устранение неполадок с контроллером**](ingress-controller-troubleshoot.md)входящего трафика. Устраните неполадки с контроллером входящего трафика.