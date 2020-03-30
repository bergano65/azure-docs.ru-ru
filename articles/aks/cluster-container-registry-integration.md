---
title: Интеграция реестра контейнеров Azure с сервисом Azure Kubernetes
description: Узнайте, как интегрировать службу Azure Kubernetes (AKS) в реестр контейнеров Azure (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: f83faf05eb7099557d5b653e0b24591062c44d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368457"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Аутентификация с помощью реестра контейнеров Azure из Службы Azure Kubernetes

При использовании реестра контейнеров Azure (ACR) со Службой Azure Kubernetes (AKS) необходимо установить механизм аутентификации. В этой статье приводятся примеры настройки аутентификации между этими двумя службами Azure.

Вы можете настроить AKS для интеграции ACR в нескольких простых команд с Azure CLI.

## <a name="before-you-begin"></a>Перед началом

Для этих примеров требуются:

* Роль **владельца** или **администратора учетной записи Azure** в **подписке Azure**
* Версия Azure CLI 2.0.73 или позже

Чтобы избежать необходимости роли администратора учетной записи **владельца** или **администратора учетной записи Azure,** можно настроить основную службу вручную или использовать существующий принцип службы для проверки подлинности ACR от AKS. Для получения дополнительной информации, см [ACR аутентификации с основой службы](../container-registry/container-registry-auth-service-principal.md) или [аутентификации из Kubernetes с тайной тянуть](../container-registry/container-registry-auth-kubernetes.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Создание нового кластера AKS с интеграцией ACR

Вы можете настроить интеграцию AKS и ACR при первоначальном создании кластера AKS.  Для того чтобы кластер AKS взаимодействовал с ACR, используется **принцип службы** active Directory Azure. Следующая команда CLI позволяет авторизовать существующий ACR в подписке и настраивает соответствующую роль **ACRPull** для основного обслуживания. Поставка действительных значений для параметров ниже.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Кроме того, можно указать имя ACR с помощью идентификатора ресурса ACR, который имеет следующий формат:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Этот шаг может занять несколько минут.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Настройка интеграции ACR для существующих кластеров AKS

Интегрируйте существующий ACR с существующими кластерами AKS, предоставляя действительные значения для **acr-name** или **acr-resource-id** в качестве ниже.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

или

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Вы также можете удалить интеграцию между кластером ACR и AKS со следующими

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

или диспетчер конфигурации служб

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Работа с ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Импортируйте изображение в ACR

Импортируйте изображение из концентратора докеров в ACR, запустив следующее:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Развертывание образца изображения с ACR в AKS

Убедитесь, что у вас есть надлежащие учетные данные AKS

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Создайте файл под названием **acr-nginx.yaml,** содержащий следующее:

```yaml
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

Затем запустите развертывание в кластере AKS:

```console
kubectl apply -f acr-nginx.yaml
```

Вы можете контролировать развертывание, запустив:

```console
kubectl get pods
```

У тебя должно быть два ходовых стручка.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
