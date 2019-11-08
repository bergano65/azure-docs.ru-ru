---
title: Включение поддержки нескольких пространств имен для контроллера входящего трафика шлюза приложений
description: В этой статье содержатся сведения о том, как включить поддержку нескольких пространств имен в кластере Kubernetes с входящим контроллером шлюза приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795561"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Включение поддержки нескольких пространств имен в кластере AKS с помощью контроллера входящего трафика шлюза приложений

## <a name="motivation"></a>Справоч
[Пространства имен](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes позволяют секционировать кластер Kubernetes и распределять его между подгруппами более крупной команды. Затем эти подкоманды могут развертывать инфраструктуру и управлять ею с помощью более мелких средств управления ресурсами, безопасностью, конфигурацией и т. д. Kubernetes позволяет определять один или несколько входящих ресурсов отдельно в каждом пространстве имен.

Начиная с версии 0,7 [шлюз приложений Azure Kubernetes ингрессконтроллер](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (агик) может принимать события из нескольких пространств имен и наблюдать за ними. Если администратор AKS решил использовать [шлюз приложений](https://azure.microsoft.com/services/application-gateway/) в качестве входящего, все пространства имен будут использовать один и тот же экземпляр шлюза приложений. При отдельной установке контроллера входящего трафика будут отслеживаться доступные пространства имен, а также будет настроен шлюз приложений, с которым он связан.

Версия 0,7 АГИК будет продолжать монопольно наблюдать за `default` пространством имен, если только это не будет явно изменено на одно или несколько пространств имен в конфигурации Helm (см. раздел ниже).

## <a name="enable-multiple-namespace-support"></a>Включить поддержку нескольких пространств имен
Чтобы включить поддержку нескольких пространств имен:
1. Измените файл [Helm-config. YAML](#sample-helm-config-file) одним из следующих способов.
   - Удалите `watchNamespace` ключ целиком из [Helm-config. YAML](#sample-helm-config-file) -агик будет наблюдать за всеми пространствами имен
   - Задайте для `watchNamespace` пустую строку — АГИК будет следить за всеми пространствами имен.
   - Добавьте несколько пространств имен, разделенных запятыми (`watchNamespace: default,secondNamespace`). АГИК будет наблюдать только за этими пространствами имен
2. применить изменения шаблона Helm с помощью: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

После развертывания с возможностью наблюдения за несколькими пространствами имен АГИК будет:
  - Вывод списка входящих ресурсов из всех доступных пространств имен
  - фильтровать по входящим ресурсам, помеченным `kubernetes.io/ingress.class: azure/application-gateway`
  - Создание общей [конфигурации шлюза приложений](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - Применение конфигурации к связанному шлюзу приложений через [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Конфликтующие конфигурации
Несколько [ресурсов](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) , входящих в состав пространства имен, могут дать указание агик создавать конфликтующие конфигурации для одного шлюза приложений. (Два передает заявляют один и тот же домен для экземпляра.)

В начале иерархии- **прослушиватели** (IP-адрес, порт и узел) и **правила маршрутизации** (прослушиватель привязки, серверный пул и параметры HTTP) могут быть созданы и совместно использоваться несколькими пространствами имен или передает.

С другой стороны, пути, серверные пулы, параметры HTTP и TLS-сертификаты могут быть созданы только одним пространством имен, а дубликаты будут удалены.

Например, рассмотрим следующие дублирующиеся входные ресурсы, заданные `staging` и `production` для `www.contoso.com`:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Несмотря на то, что два входящих ресурса требуют трафика для `www.contoso.com` направляться в соответствующие пространства имен Kubernetes, только одна серверная часть может обслуживать трафик. АГИК будет создавать конфигурацию для одного из ресурсов на основе первых поступающих. Если одновременно создаются два передает ресурсов, приоритет будет иметь один из них, описанный ранее в алфавите. Из приведенного выше примера можно будет только создавать параметры для `production` входящий. Шлюз приложений будет настроен со следующими ресурсами:

  - Прослушиватель: `fl-www.contoso.com-80`
  - Правило маршрутизации: `rr-www.contoso.com-80`
  - Внутренний пул: `pool-production-contoso-web-service-80-bp-80`
  - Параметры HTTP: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Проба работоспособности: `pb-production-contoso-web-service-80-websocket-ingress`

Обратите внимание, что за исключением правила *прослушивателя* и *маршрутизации*, созданные ресурсы шлюза приложений включают имя пространства имен (`production`), для которого они были созданы.

Если два входящих ресурса введены в кластер AKS в разные моменты времени, скорее всего, АГИК будет в ситуации, когда он перестраивает шлюз приложений и перенаправляет трафик с `namespace-B` на `namespace-A`.

Например, если вы добавили `staging` сначала, АГИК настроит шлюз приложений для маршрутизации трафика в промежуточный внутренний пул. На более позднем этапе, посвященном выполнению `production`, приведет к перепрограммированию шлюза приложений АГИК, который начнет маршрутизацию трафика в внутренний пул `production`.

## <a name="restrict-access-to-namespaces"></a>Ограничение доступа к пространствам имен
По умолчанию АГИК будет настраивать шлюз приложений на основе пометки входных данных в любом пространстве имен. Если вы хотите ограничить это поведение, вы можете использовать следующие параметры:
  - Ограничьте пространство имен, явно определив пространства имен АГИК, с помощью ключа YAML `watchNamespace` в [Helm-config. YAML](#sample-helm-config-file)
  - Используйте [Role/ролебиндинг](https://docs.microsoft.com/azure/aks/azure-ad-rbac) , чтобы ограничить агик конкретными пространствами имен

## <a name="sample-helm-config-file"></a>Пример файла конфигурации Helm
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

