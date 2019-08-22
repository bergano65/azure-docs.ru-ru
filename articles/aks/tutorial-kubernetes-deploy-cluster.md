---
title: Руководство по Kubernetes в Azure. Развертывание кластера
description: В этом руководстве по Службе Azure Kubernetes (AKS) вы создаете кластер AKS и используете kubectl для подключения к главному узлу Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: e0dea5fcc7860e8653d542653e279010f8ede200
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898856"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Руководство по Развертывание кластера службы Azure Kubernetes (AKS)

Kubernetes предоставляет распределенную платформу для контейнерных приложений. AKS позволяет быстро создать готовый производственный кластер Kubernetes. В этом руководстве (третья часть из восьми) кластер Kubernetes развертывается в AKS. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание субъекта-службы для взаимодействия ресурсов.
> * Развертывание кластера AKS.
> * Установка интерфейса командной строки Kubernetes (kubectl).
> * Настройка kubectl для подключения к кластеру AKS.

В дополнительных руководствах приложение Azure для голосования развертывается в кластере, масштабируется и обновляется.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах образ контейнера был создан и передан в экземпляр реестра контейнеров Azure. Если вы не выполнили эти действия, вы можете начать с раздела руководства 1 [Создание образов контейнеров][aks-tutorial-prepare-app].

Для выполнения задач из этого руководства требуется Azure CLI 2.0.53 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Для взаимодействия с API-интерфейсами Azure кластеру AKS требуется субъект-служба Azure Active Directory. Этот субъект-служба может быть автоматически создан с помощью интерфейса командной строки или портала Azure либо вы можете сами предварительно создать этот компонент и назначить дополнительные разрешения. В этом руководстве вы создадите субъект-службу, предоставите доступ к экземпляру ACR Azure, созданному в предыдущем руководстве, а затем создадите кластер AKS.

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac][]. Параметр `--skip-assignment` ограничивает назначение дополнительных разрешений. По умолчанию этот субъект-служба действителен в течение одного года.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Запишите значения параметров *appId* и *password*. Эти значения используются в приведенных далее действиях.

## <a name="configure-acr-authentication"></a>Настройка проверки подлинности ACR

Для доступа к образам, хранящимся в ACR, субъекту-службе AKS необходимо предоставить соответствующие права для извлечения образов из ACR.

Сначала получите идентификатор ресурса ACR с помощью команды [az acr show][]. Измените имя реестра `<acrName>` на имя реестра своего экземпляра ACR, а имя группы ресурсов — на имя той группы ресурсов, в которой находится экземпляр ACR.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Чтобы предоставить кластеру AKS соответствующие права доступа для извлечения образов, хранящихся в ACR, назначьте роль `AcrPull` с помощью команды [az role assignment create][]. Замените `<appId`> и `<acrId>` на значения, собранных на предыдущих двух шагах.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Создание кластера Kubernetes

Кластеры AKS могут использовать управление доступом на основе ролей (RBAC) Kubernetes. Эти элементы управления позволяют определять доступ к ресурсам на основе ролей, назначенных пользователям. Разрешения объединяются, если пользователю назначено несколько ролей. Разрешения могут охватывать одно пространство имен или весь кластер. По умолчанию Azure CLI автоматически включает RBAC при создании кластера AKS.

Создайте кластер AKS с помощью команды [az aks create][]. В следующем примере в группе ресурсов *myResourceGroup* создается кластер с именем *myAKSCluster*. Эта группа ресурсов была создана в [предыдущем руководстве][aks-tutorial-prepare-acr]. Укажите собственные значения `<appId>` и `<password>` из предыдущего шага, на котором был создан субъект-служба.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Через несколько минут развертывание завершится, и отобразятся сведения о развертывании AKS в формате JSON.

## <a name="install-the-kubernetes-cli"></a>Установка интерфейса командной строки Kubernetes

Для подключения к кластеру Kubernetes с локального компьютера используйте средство [kubectl][kubectl] (клиент командной строки Kubernetes).

Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Его также можно установить локально с помощью команды [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Подключение к кластеру с помощью kubectl

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][]. В следующем примере возвращаются учетные данные для кластера AKS с именем *myAKSCluster* в группе ресурсов *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Чтобы проверить подключение к кластеру, выполните команду [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули кластер Kubernetes в AKS и настроили `kubectl` для подключения к нему. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание субъекта-службы для взаимодействия ресурсов.
> * Развертывание кластера AKS.
> * Установка интерфейса командной строки Kubernetes (kubectl).
> * Настройка kubectl для подключения к кластеру AKS.

Перейдите к следующему руководству, чтобы узнать, как развертывать приложения в кластер.

> [!div class="nextstepaction"]
> [Запуск приложений в Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create;
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
