---
title: Анализ настроений Kubernetes конфигурации и развертывания шагов
titleSuffix: Azure Cognitive Services
description: Анализ настроений Kubernetes конфигурации и развертывания шагов
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b43299974034f55b57b86191b3556c3d5c2ee83b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877857"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Развертывание контейнера анализа настроений в кластер AKS

1. Откройте CLI Azure и вопием в Azure.

    ```azurecli
    az login
    ```

1. Войти на кластер AKS. `your-cluster-name` Заменить `your-resource-group` и с соответствующими значениями.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    После выполнения этой команды он сообщает сообщение, аналогичное следующему:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Если у вас есть несколько подписок, доступных для вас в учетной записи Azure и `az aks get-credentials` команда возвращается с ошибкой, распространенной проблемой является то, что вы используете неправильную подписку. Установите контекст сеанса Azure CLI, чтобы использовать ту же подписку, с помощью которого вы создали ресурсы, и повторить попытку.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Откройте текстовый редактор выбора. В этом примере используется Visual Studio Code.

    ```console
    code .
    ```

1. В текстовом редакторе создайте новый файл под названием *sentiment.yaml*и вставьте в него следующий YAML. Обязательно замените `billing/value` `apikey/value` и с вашей собственной информацией.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Сохранить файл и закрыть текстовый редактор.
1. Выполнить команду Kubernetes `apply` с *файлом sentiment.yaml* в качестве своей цели:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    После успешного применения командой конфигурации развертывания сообщение выглядит похоже на следующий вывод:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Убедитесь, что стручок был развернут:

    ```console
    kubectl get pods
    ```

    Выход для состояния работая стручка:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Убедитесь, что услуга доступна, и получите IP-адрес.

    ```console
    kubectl get services
    ```

    Выход для запущенного состояния службы *настроений* в стручке:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
