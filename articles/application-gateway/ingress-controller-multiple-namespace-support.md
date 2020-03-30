---
title: Включить несколько поддержки пространства имен для контроллера входа в приложение шлюза
description: В этой статье содержится информация о том, как включить несколько служб имен в кластере Kubernetes с контроллером входа в приложение.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279927"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Включить несколько поддержки Namespace в кластере AKS с контроллером входа в шлюз приложений

## <a name="motivation"></a>Причины для использования
Kubernetes [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) позволяют разделить кластер Kubernetes и выделить их в подгруппы более крупной группы. Эти подгруппы могут затем развертывать и управлять инфраструктурой с более тонким управлением ресурсами, безопасностью, конфигурацией и т.д. Kubernetes позволяет независимо определять один или несколько входных ресурсов в каждом пространстве имен.

По версии 0.7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) может глотать события из нескольких областей имен и наблюдать за ней. Если администратор AKS решит использовать [Шлюз Приложения](https://azure.microsoft.com/services/application-gateway/) в качестве входа, все пространства имен будут использовать один и тот же экземпляр шлюза приложения. Единая установка Ingress Controller будет отслеживать доступные пространства имен и настраивать шлюз приложения, с ним он связан.

Версия 0.7 AGIC будет продолжать `default` исключительно наблюдать за пространством имен, если она явно не будет изменена на одно или несколько различных областей имен в конфигурации Helm (см. раздел ниже).

## <a name="enable-multiple-namespace-support"></a>Включение поддержки нескольких пространств имен
Для включения нескольких служб ы поддержки пространства имен:
1. изменить [файл руля-конфигурации](#sample-helm-config-file) одним из следующих способов:
   - полностью `watchNamespace` удалить ключ с [рулем-конфигурация](#sample-helm-config-file) - AGIC будет наблюдать за всеми пространствами имен
   - установлен `watchNamespace` на пустую строку - AGIC будет наблюдать все пространства имен
   - добавить несколько именных пространств, разделенных запятой (`watchNamespace: default,secondNamespace`) - AGIC будет наблюдать эти пространства имен исключительно
2. Применить изменения шаблона Helm с:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

После развертывания с возможностью наблюдения нескольких пространствах имен AGIC будет:
  - список всхотивания ресурсов из всех доступных областей имен
  - фильтр для вхотливания ресурсов, аннотированных с`kubernetes.io/ingress.class: azure/application-gateway`
  - составить комбинированный [config Шлюза приложения](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - применить конфигурацию к связанного шлюза приложения через [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Противоречивые конфигурации
Несколько [ресурсов, вводных](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) в пространстве имен, могут поручить AGIC создать противоречивые конфигурации для единого шлюза приложения. (Два входа, утверждая, например, один и тот же домен.)

В верхней части иерархии - **слушатели** (IP-адрес, порт и хост) и **правила разгрома** (связывающий слушатель, пул бэкэнда и настройки HTTP) могут быть созданы и разделены несколькими пространствами имен/входами.

С другой стороны - пути, пулы бэкэнда, настройки HTTP и сертификаты TLS могут быть созданы только одним пространством имен, и дубликаты будут удалены.

Например, рассмотрим следующие дублирующие `staging` ресурсы входа, определенные пространства имен, и `production` для: `www.contoso.com`
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

Несмотря на два ресурса, требующие трафика, `www.contoso.com` которые должны быть направлены в соответствующие пространства имен Kubernetes, только один бэкэнд может обслуживать трафик. AGIC создаст конфигурацию на основе "первый пришел, первый служил" для одного из ресурсов. Если два ресурса- и то же время создаются, то приоритет будет иметь тот, который ранее был в алфавите. Из приведенного выше примера мы сможем `production` создавать только настройки для входа. Портал приложений будет настроен со следующими ресурсами:

  - Прослушивателя:`fl-www.contoso.com-80`
  - Правило реуктора:`rr-www.contoso.com-80`
  - Бэкэнд бассейн:`pool-production-contoso-web-service-80-bp-80`
  - Настройки HTTP:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Зонд здоровья:`pb-production-contoso-web-service-80-websocket-ingress`

Обратите внимание, что, за исключением *правила слушателя* и *правила разгрома,* созданные ресурсы Application Gateway включают имя пространства имен (),`production`для которого они были созданы.

Если два входных ресурса будут введены в кластер AKS в разные моменты времени, то AGIC, скорее всего, `namespace-B` `namespace-A`окажется в сценарии, при котором он перенастраивает шлюз приложения и перенаправляет трафик с к .

Например, если `staging` вы добавили сначала, AGIC направит шлюз приложения для маршрутизании трафика в пул бэкэнда. На более позднем `production` этапе, вводя вход, приведет к тому, что AGIC `production` перезапрограммирует application Gateway, который начнет трафик на бэкэнд пул.

## <a name="restrict-access-to-namespaces"></a>Ограничить доступ к пространствам имен
По умолчанию AGIC настраивает шлюз приложения на основе аннотированного Ingress в любом пространстве имен. Если вы хотите ограничить это поведение у вас есть следующие варианты:
  - ограничить пространства имен, явно определив пространства имен `watchNamespace` AGIC должны наблюдать через ключ YAML в [рулем-конфигурации.yaml](#sample-helm-config-file)
  - использовать [Role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) для ограничения AGIC определенными пространствами имен

## <a name="sample-helm-config-file"></a>Файл конфигурации «Пример Хелм»
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

