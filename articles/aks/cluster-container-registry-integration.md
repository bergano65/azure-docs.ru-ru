---
title: Интеграция реестра контейнеров Azure с помощью службы Kubernetes Azure
description: Узнайте, как интегрировать службу Kubernetes Azure (AKS) с реестром контейнеров Azure (запись контроля доступа).
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: 1f07581be8fc416f8aae5eec1460ca3d33bda8f9
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114234"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Предварительная версия — проверка подлинности с помощью реестра контейнеров Azure из службы Kubernetes Azure

При использовании реестра контейнеров Azure (ACR) со Службой Azure Kubernetes (AKS) необходимо установить механизм аутентификации. В этой статье описаны рекомендуемые конфигурации для аутентификации между этими двумя службами Azure.

Вы можете настроить AKS для интеграции записей контроля доступа в нескольких простых командах с Azure CLI.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения об отсутствии см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS](support-policies.md)
> * [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="before-you-begin"></a>Перед началом работы

Необходимо следующее:

* Роль **владельца** или **администратора учетной записи Azure** в подписке **Azure**
* Кроме того, требуется Azure CLI версии 2.0.70 или более поздней, а также расширение AKS-Preview 0.4.8
* На клиенте должен быть [установлен DOCKER](https://docs.docker.com/install/) , и необходим доступ к концентратору [DOCKER](https://hub.docker.com/) .

## <a name="install-latest-aks-cli-preview-extension"></a>Установка последнего расширения AKS CLI Preview

Требуется расширение **AKS-Preview 0.4.8** или более поздняя версия.

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Создание нового кластера AKS с интеграцией записей контроля доступа

Вы можете настроить интеграцию AKS и записей контроля доступа во время первоначального создания кластера AKS.  Чтобы разрешить кластеру AKS взаимодействовать с записью контроля доступа, используется **субъект-служба** Azure Active Directory. Следующая команда CLI создает запись контроля доступа в указанной группе ресурсов и настраивает соответствующую роль **акрпулл** для субъекта-службы. Если *имя записи контроля доступа* не существует в указанной группе ресурсов, автоматически создается имя `aks<resource-group>acr` записи контроля доступа по умолчанию.  Укажите допустимые значения для параметров ниже.  Параметры в квадратных скобках являются необязательными.
```azurecli
az login
az aks create -n myAKSCluster -g myResourceGroup --enable-acr [--acr <acr-name-or-resource-id>]
```
\* * Идентификатор ресурса записи контроля доступа имеет следующий формат: 

/Subscriptions/< Subscription-d >/resourceGroups/< ресурс-Group-name >/Провидерс/Микрософт.контаинеррегистри/регистриес/<name> 
  
Выполнение этого шага может занять несколько минут.

## <a name="create-acr-integration-for-existing-aks-clusters"></a>Создание интеграции записей контроля доступа для существующих кластеров AKS

Интегрируйте существующую запись контроля доступа с существующими кластерами AKS, указав допустимые значения для записей **контроля учетных** записей с именем или записи **контроля доступа (ИД ресурса** ), как показано ниже.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acr-resource-id>
```

## <a name="log-in-to-your-acr"></a>Вход в запись контроля доступа

Используйте следующую команду для входа в запись контроля доступа.  <acrname> Замените параметр именем записи контроля доступа.  Например, значение по умолчанию — **aks < > записи контроля доступа для группы ресурсов**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Извлечение образа из DOCKER HUB и отправка его в запись контроля доступа

Вытяните образ из DOCKER Hub, пометьте образ и отправьте его в запись контроля доступа.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>Обновление состояния и проверка модулей Pod

Чтобы проверить развертывание, выполните следующие действия.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Просмотрите файл YAML и измените свойство Image, заменив значение на имя сервера входа, образ и тег записи контроля доступа.

```
$ cat acr-nginx.yaml

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

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
