---
title: Устранение неполадок в контроллере входа в приложение
description: В этой статье приводится документация о том, как устранить общие вопросы и/или проблемы с контроллером входа в приложение.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795513"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Устранение проблем наобщиваемых вопросов или проблем с контроллером Ingress

[Azure Cloud Shell](https://shell.azure.com/) — это самый удобный способ устранения неполадок при установке AKS и AGIC. Запустите оболочку с [shell.azure.com](https://shell.azure.com/) или нажав на ссылку:

[![Встраиваемый запуск](https://shell.azure.com/images/launchcloudshell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Тест с помощью простого приложения Kubernetes

Приведенные ниже шаги предполагают:
  - У вас есть кластер AKS с включенным расширенным сетью
  - AGIC установлен на кластере AKS
  - Вы уже hav шлюз приложения на VNET совместно с вашим кластером AKS

Чтобы убедиться, что установка Приложения Gateway и AKS AGIC настроена правильно, разместите простейшую из возможных приложений:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Копируйте и вставьте все строки сразу из приведенного выше сценария в [облачную оболочку Azure.](https://shell.azure.com/) Пожалуйста, убедитесь, что вся команда скопирована - начиная с `cat` и включая последнюю `EOF`.

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

После успешного развертывания приложения над кластером AKS появится новый Pod, Service и Ingress.

Получить список стручков с `kubectl get pods -o wide` [облачной оболочки](https://shell.azure.com/): .
Мы ожидаем, что будет создан аст под именем 'Test-agic-app-pod'. Он будет иметь IP-адрес. Этот адрес должен быть в пределах VNET шлюза приложения, который используется с AKS.

![Стручки](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Получить список услуг: `kubectl get services -o wide`. Мы ожидаем увидеть службу под названием "тест-агика-приложение-сервис".

![Стручки](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Получить список входов: `kubectl get ingress`. Мы ожидаем, что был создан ресурс Ingress под названием 'Test-agic-app-ingress'. Ресурс будет иметь название хозяина "test.agic.contoso.com".

![Стручки](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Один из стручков будет AGIC. `kubectl get pods`покажет список стручков, один из которых начнется с "неприязни". Получите все журналы `kubectl logs <name-of-ingress-controller-pod>` этого стручка, чтобы убедиться, что у нас было успешное развертывание. Успешное развертывание добавило бы в журнал следующие строки:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Кроме того, из [Cloud Shell](https://shell.azure.com/) мы можем получить только `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`строки, указывающие на успешную конфигурацию Gateway application с , где `<ingress-azure....>` должно быть точное название стручка AGIC.

Приложение Шлюза будет иметь следующую конфигурацию применяется:

- Слушатель: ![слушатель](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Правило реуктора: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Бэкэнд бассейн:
  - Там будет один IP-адрес в бэкэнд адрес пула, и он будет `kubectl get pods -o wide` 
 ![соответствовать IP-адрес Pod мы наблюдали ранее с backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Наконец, мы `cURL` можем использовать команду из [облачной оболочки](https://shell.azure.com/) для создания подключения HTTP к недавно развернутому приложению:

1. Использование `kubectl get ingress` для получения публичного IP-адреса прикладного шлюза
2. Используйте `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`.

![Стручки](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Результат указывает `HTTP/1.1 200 OK` на то, что система Application Gateway - AKS и AGIC работает в штатном состоянии, как и ожидалось.


## <a name="inspect-kubernetes-installation"></a>Инспектирование Кубернетес Установка

### <a name="pods-services-ingress"></a>Стручки, Услуги, Ingress
Приложение Gateway Ingress Controller (AGIC) непрерывно отслеживает следующие ресурсы Kubernetes: [Развертывание](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) или [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Служба](https://kubernetes.io/docs/concepts/services-networking/service/), [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Для того, чтобы AGIC функционировала так, как ожидалось, должны быть следующие:
  1. AKS должен иметь один или несколько **здоровых стручков.**
     Проверить это из `kubectl get pods -o wide --show-labels` [облачной оболочки](https://shell.azure.com/) с `apsnetapp`Если у вас есть pod с , ваш выход может выглядеть следующим образом:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Один или несколько **услуг**, ссылаясь на `selector` стручки выше через соответствующие этикетки.
     Проверить это из [облачной оболочки](https://shell.azure.com/) с`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress**, аннотированный с `kubernetes.io/ingress.class: azure/application-gateway`, ссылаясь на службу выше Проверить это от cloud [Shell](https://shell.azure.com/) с`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Просмотр аннотаций входа выше: `kubectl get ingress aspnetapp -o yaml` `aspnetapp` (замените имя вашего входа)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     Всходный ресурс должен быть `kubernetes.io/ingress.class: azure/application-gateway`аннотирован.
 

### <a name="verify-observed-namespace"></a>Проверить наблюдаемое пространство имен

* Получите существующие пространства имен в кластере Kubernetes. В каком пространстве имен работает ваше приложение? Является ли AGIC смотреть, что пространство имен? Обратитесь к документации [поддержки нескольких имен](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) о том, как правильно настроить наблюдаемые пространства имен.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* Под AGIC должен находиться в пространстве `NAMESPACE`имен (см. колонку). `default` Здоровый стручок будет иметь `Running` в колонке. `STATUS` Там должно быть по крайней мере один AGIC стручка.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Если стручок AGIC`STATUS` не является здоровым (колонка из команды выше, не `Running`является):
  - получить журналы, чтобы понять, почему:`kubectl logs <pod-name>`
  - для предыдущего экземпляра стручка:`kubectl logs <pod-name> --previous`
  - описать стручок, чтобы получить больше контекста:`kubectl describe pod <pod-name>`


* Есть ли у вас kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) и [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) ресурсы?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Является ли ваша [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) аннотируется с: ? `kubernetes.io/ingress.class: azure/application-gateway` AGIC будет следить только за ресурсами Kubernetes Ingress, которые имеют эту аннотацию.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC излучает события Kubernetes для определенных критических ошибок. Вы можете просмотреть следующие:
  - в вашем терминале через`kubectl get events --sort-by=.metadata.creationTimestamp`
  - в вашем браузере с помощью [веб-интерфейса Kubernetes (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Уровни ведения журнала

AGIC имеет 3 уровня регистрации. Уровень 1 является по умолчанию и показывает минимальное количество строк журнала.
Уровень 5, с другой стороны, будет отображать все журналы, в том числе дезинфицированных содержание конфигурации применяется к ARM.

Сообщество Kubernetes установило 9 уровней лесозаготовок для инструмента [kubectl.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) В этом репозитории мы используя 3 из них, с аналогичной семантикой:


| Уровень детализации | Описание |
|-----------|-------------|
|  1        | Уровень журнала по умолчанию; показывает детали запуска, предупреждения и ошибки |
|  3        | Расширенная информация о событиях и изменениях; списки созданных объектов |
|  5        | Логи маршализированных объектов; показывает, дезинфицированные JSON конфигурации применяется к ARM |


Уровни многословности регулируются `verbosityLevel` через переменную в файле [руля-конфигурации.](#sample-helm-config-file) Увеличьте уровень многословности, чтобы `5` получить jSON конфигурации отправлены в [ARM:](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
  - добавить `verbosityLevel: 5` на линии сам по себе в [руля-config.yaml](#sample-helm-config-file) и повторно установить
  - получить журналы с`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Файл конфигурации «Пример Хелм»
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

