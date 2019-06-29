---
title: Запускать службы Azure Kubernetes
titleSuffix: Text Analytics - Azure Cognitive Services
description: Развертывание контейнеров analytics текста с учетом образа анализа тональности в службы Azure Kubernetes и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 4d5e1da01be531550915a38bed17dd8e57be907a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454953"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Развертывание контейнера анализ тональности для службы Azure Kubernetes (AKS)

Дополнительные сведения о развертывании Cognitive Services [текстовая аналитика](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) контейнера с помощью образа анализ тональности для службы Azure Kubernetes (AKS). Эта процедура служит отличным примером создания ресурса анализа текста, создания связанное изображение анализ тональности, а также для выполнения этой оркестрацией двух из браузера. С помощью контейнеров можно сместить внимание разработчиков от управления инфраструктурой, чтобы вместо этого сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>Технические условия

Для выполнения этой процедуры необходимо установить и запустить несколько средств локально. Не используйте Azure CloudShell.

* Используйте подписку Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
* Текстовый редактор, например: [Visual Studio Code](https://code.visualstudio.com/download).
* Установка [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Установка [интерфейс командной строки Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
* Ресурс Azure с правильной ценовой категорией. Не все ценовые категории поддерживают этот контейнер.
    * Ресурс **Анализ текста** с ценовой категорией F0 или "Стандартный".
    * Ресурс **Cognitive Services** с ценовой категорией S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>Развертывание контейнера текста аналитики в кластере AKS

1. Откройте Azure CLI и войдите в Azure

    ```azurecli
    az login
    ```

1. Войдите в кластер AKS (Замените `your-cluster-name` и `your-resource-group` с соответствующими значениями)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    После выполнения этой команды, он выводит сообщение, аналогичное приведенному ниже:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Если у вас есть несколько подписок, доступные для учетной записи Azure и `az aks get-credentials` команда возвращает ошибку, распространенной проблемой является то, что вы используете неправильную подписку. Просто задать контекст в сеансе Azure CLI для использования той же подписке, который вы создали ресурсы с и повторите попытку.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Откройте текстовый редактор по выбору, (в этом примере используется __Visual Studio Code__):

    ```azurecli
    code .
    ```

1. В текстовом редакторе создайте файл с именем _sentiment.yaml_ и вставьте в него следующий yaml-ФАЙЛ:

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
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: "https://westus2.api.cognitive.microsoft.com/"
            - name: apikey
              value: "16c12e3419f54ba49a3222177cef781d"
     
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

1. Сохраните файл и закройте текстовый редактор.
1. Выполнение Kubernetes `apply` с _sentiment.yaml_ целевым объектом:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    После команды успешно применил конфигурацию развертывания, сообщение, аналогичное приведенному ниже приведен выходных данных.

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Убедитесь, что была развернута POD:

    ```console
    kubectl get pods
    ```

    При этом будет выведено состояние выполнения POD:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Убедитесь, что служба доступна и получите IP-адрес:

    ```console
    kubectl get services
    ```

    При этом будет выведено состояние выполнения _тональности_ службы в POD:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../../cognitive-services-container-support.md)
* Используйте [текстовая аналитика подключенной службы](../vs-text-connected-service.md)