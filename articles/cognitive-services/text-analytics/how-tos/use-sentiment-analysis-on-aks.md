---
title: Запуск службы Kubernetes Azure — Анализ текста
titleSuffix: Azure Cognitive Services
description: Разверните Анализ текста контейнеры с образом анализа тональности в службе Kubernetes Azure и протестируйте их в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552272"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>Развертывание контейнера тональности Analysis Service в службе Kubernetes Azure

Узнайте, как развернуть контейнер [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) Cognitive Services Azure с помощью образа анализа тональности в службе Kubernetes Azure (AKS). В этой процедуре показано, как создать ресурс Анализ текста, как создать связанный образ анализа тональности и как выполнить это согласование этих двух элементов из браузера. С помощью контейнеров можно не только управлять инфраструктурой, но и сосредоточиться на разработке приложений.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этой процедуры необходимо установить и запустить несколько средств локально. Не используйте Azure Cloud Shell. Кроме этого, вам потребуются:

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
* Текстовый редактор, например [Visual Studio Code](https://code.visualstudio.com/download).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) установлен.
* Установленная [CLI Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) .
* Ресурс Azure с правильной ценовой категорией. Не все ценовые категории поддерживают этот контейнер.
    * **Анализ текста ресурс Azure** только с ценовыми категориями F0 или Standard.
    * Ресурс **Azure Cognitive Services** с ценовой категорией S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>Развертывание контейнера Анализ текста в кластере AKS

1. Откройте Azure CLI и войдите в Azure.

    ```azurecli
    az login
    ```

1. Войдите в кластер AKS. Замените `your-cluster-name` и`your-resource-group` соответствующими значениями.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    После выполнения этой команды будет выводится сообщение следующего вида:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Если в учетной записи Azure доступно несколько подписок, а `az aks get-credentials` команда возвращает ошибку, то распространенной проблемой является то, что вы используете неправильную подписку. Задайте контекст сеанса Azure CLI, чтобы использовать ту же подписку, в которой были созданы ресурсы, и повторите попытку.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Откройте текстовый редактор по желанию. В этом примере используется Visual Studio Code.

    ```azurecli
    code .
    ```

1. В текстовом редакторе создайте новый файл с именем _тональности. YAML_и вставьте в него следующий YAML. Обязательно замените `billing/value` и `apikey/value` собственными сведениями.

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
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
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
1. Выполните команду Kubernetes `apply` с параметром _тональности. YAML_ в качестве целевого объекта:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    После того как команда успешно применит конфигурацию развертывания, появится сообщение следующего вида:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Убедитесь, что модуль был развернут:

    ```console
    kubectl get pods
    ```

    Выходные данные для состояния выполнения Pod:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Убедитесь, что служба доступна, и получите IP-адрес.

    ```console
    kubectl get services
    ```

    Выходные данные для состояния выполнения службы _тональности_ в Pod:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Следующие шаги

* Использование большего числа [контейнеров Cognitive Services](../../cognitive-services-container-support.md)
* Использование [подключенной службы анализ текста](../vs-text-connected-service.md)
