---
title: Автомасштабирование стручков AKS с метриками шлюза приложений Azure
description: В этой статье содержатся инструкции по масштабу пакетов бэкэнда AKS с помощью метрик Портала приложений и метческого адаптера Azure Kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239451"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Автомасштабирование стручков AKS с помощью метрик ипой ных атримы приложений (бета)

По мере увеличения входящего трафика становится важным масштабировать сяобирание приложений в зависимости от спроса.

В следующем уроке мы объясним, как `AvgRequestCountPerHealthyHost` вы можете использовать метрику прикладного шлюза для расширения приложения. `AvgRequestCountPerHealthyHost`измеряет средние запросы, отправленные в определенный пул бэкэнда, и комбинацию параметров бэкэнда HTTP.

Мы будем использовать следующие два компонента:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- Мы будем использовать метрический адаптер для разоблачения метрик Application Gateway через метрический сервер. Метрический адаптер Azure Kubernetes — это проект с открытым исходным кодом в Azure, аналогичный контроллеру входа в шлюз приложений. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- Мы будем использовать HPA для использования метрик Application Gateway и таргетинга развертывания для масштабирования.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Настройка метеоада Azure Kubernetes

1. Сначала мы создадим директор службы Azure `Monitoring Reader` AAD и назначим ему доступ по группе ресурсов Application Gateway. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Теперь мы разместим с помощью [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) основного сервиса AAD, созданного выше.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Мы создадим `ExternalMetric` ресурс `appgw-request-count-metric`с именем . Этот ресурс будет инструктировать метрический адаптер для разоблачения `AvgRequestCountPerHealthyHost` метрики для `myApplicationGateway` ресурса в `myResourceGroup` группе ресурсов. Вы можете `filter` использовать поле для целевой конкретной пулбэк и бэкэнд арены HTTP настройки в приложении шлюз.

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

Теперь вы можете сделать запрос на метрический сервер, чтобы увидеть, если наша новая метрика становится подвержены:
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Использование новой метрики для расширения развертывания

Как только мы `appgw-request-count-metric` сможем разоблачить через метрический сервер, мы готовы использовать [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) для расширения нашего целевого развертывания.

В следующем примере мы нацелимся на развертывание `aspnet`выборки. Мы будем масштабировать `appgw-request-count-metric` стручки, когда > 200 `10` за стручка до максимума стручков.

Замените имя целевого развертывания и примените следующую конфигурацию автоматического масштаба:
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

Проверьте настройку с помощью инструмента нагрузоза, такого как скамейка apache:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Дальнейшие действия
- [**Проблема Ingress Контроллер вопросы:**](ingress-controller-troubleshoot.md)Устранение любых проблем с контроллером ingress.