---
title: Интеграция реестра контейнеров Azure с помощью службы Kubernetes Azure
description: Узнайте, как интегрировать службу Kubernetes Azure (AKS) с реестром контейнеров Azure (запись контроля доступа).
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: ba52cac4ebe923b7217550ed90948d908d8daf7f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73900667"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Аутентификация с помощью реестра контейнеров Azure из Службы Azure Kubernetes

При использовании реестра контейнеров Azure (ACR) со Службой Azure Kubernetes (AKS) необходимо установить механизм аутентификации. В этой статье приведены примеры настройки проверки подлинности между этими двумя службами Azure.

Вы можете настроить AKS для интеграции записей контроля доступа в нескольких простых командах с Azure CLI.

## <a name="before-you-begin"></a>Перед началом работы

Для этих примеров требуется:

* Роль **владельца** или **администратора учетной записи Azure** в **подписке Azure**
* Azure CLI версии 2.0.73 или более поздней

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Создание нового кластера AKS с интеграцией записей контроля доступа

Вы можете настроить интеграцию AKS и записей контроля доступа во время первоначального создания кластера AKS.  Чтобы разрешить кластеру AKS взаимодействовать с записью контроля доступа, используется **субъект-служба** Azure Active Directory. Следующая команда CLI позволяет авторизовать существующую запись контроля доступа в подписке и настроить соответствующую роль **акрпулл** для субъекта-службы. Укажите допустимые значения для параметров ниже. 
```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR

```
Кроме того, можно указать имя записи контроля доступа с помощью идентификатора ресурса записей контроля доступа, имеющего следующий формат:

/Subscriptions/\<Subscription — ID\>/resourceGroups/\<Resource-Group-Name\>/Провидерс/Микрософт.контаинеррегистри/регистриес/\<Name\> 
 
```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Выполнение этого шага может занять несколько минут.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Настройка интеграции записей контроля доступа для существующих кластеров AKS

Интегрируйте существующую запись контроля доступа с существующими кластерами AKS, указав допустимые значения для записей **контроля учетных** записей с именем или записи **контроля доступа (ИД ресурса** ), как показано ниже.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
или
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Вы также можете удалить интеграцию между записью контроля доступа и кластером AKS, выполнив следующие
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
или
```
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Работа с записью контроля доступа & AKS

### <a name="import-an-image-into-your-acr"></a>Импорт изображения в запись контроля доступа

Импортируйте образ из DOCKER Hub в запись контроля доступа, выполнив следующую команду:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Развертывание примера образа из записи контроля доступа в AKS

Убедитесь, что у вас есть правильные учетные данные AKS

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Создайте файл с именем записи **контроля доступа nginx. YAML** , который содержит следующее:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

Затем запустите это развертывание в кластере AKS:
```
kubectl apply -f acr-nginx.yaml
```

Вы можете отслеживать развертывание, выполнив:
```
kubectl get pods
```
Необходимо иметь два работающих модуля.
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
