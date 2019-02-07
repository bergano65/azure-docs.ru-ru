---
title: Сброс учетных данных для кластера Службы Azure Kubernetes (AKS)
description: Дополнительные сведения о том, как обновить или сбросить учетные данные участника службы для кластера в Службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/30/2019
ms.author: iainfou
ms.openlocfilehash: d880615d0d132403c935fe39e8478d7b3fc48dbe
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490079"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Обновление или выполнение циклического сдвига учетных данных для субъект-службы в Службе Azure Kubernetes (AKS)

По умолчанию кластеры AKS создаются вместе с субъектом-службой, срок действия которого составляет один год. Когда будет истекать срок действия, вы можете сбросить учетные данные, чтобы продлить срок действия субъекта-службы на дополнительный период времени. Вы также можете обновить или сменить учетные данные как часть определенной политики безопасности. В этой статье подробно описано, как обновить эти учетные данные для кластера AKS.

## <a name="before-you-begin"></a>Перед началом работы

Кроме того, вам необходимо установить и настроить службу Azure CLI версии 2.0.56 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Выбор создания или обновления субъект-службы

Если необходимо обновить учетные данные для кластера AKS, вы можете выполнить следующие действия:

* обновить учетные данные для существующей субъект-службы, которая используется в кластере, или
* создать субъект-службу и обновить кластер для использования этих новых учетных данных.

Если вы хотите создать субъект-службу, а затем обновить кластер AKS, пропустите оставшуюся часть действий, описанных в этом разделе, и продолжите [создание субъект-службы](#create-a-service-principal). Если необходимо обновить учетные данные для существующей субъект-службы, используемой кластером AKS, выполните действия, описанные в этом разделе.

### <a name="get-the-service-principal-id"></a>Получение идентификатора субъект-службы

Чтобы обновить учетные данные для существующей субъект-службы, получите идентификатор субъект-службы своего кластера с помощью команды [az aks show][az-aks-show]. В следующем примере возвращается идентификатор для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*. Идентификатор субъект-службы задается в виде переменной для использования в дополнительной команде.

```azurecli-interactive
SP_ID=$(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Обновление учетных данных субъект-службы

С помощью набора переменных, который содержит идентификатор субъект-службы, сбросьте учетные данные с помощью [учетных данных az ad sp][az-ad-sp-credential-reset]. Следующий пример позволяет платформе Azure создать новый безопасный секрет для субъект-службы. Этот новый безопасный секрет также хранится в виде переменной.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Теперь перейдите к разделу [Обновление кластера AKS с помощью новых учетных данных](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Если вы решили обновить существующие учетные данные субъект-службы в предыдущем разделе, пропустите этот шаг. Продолжайте [обновлять кластер AKS с помощью новых учетных данных](#update-aks-cluster-with-new-credentials).

Чтобы создать субъект-службу, а затем обновить кластер AKS для использования этих новых учетных данных, используйте команду [az ad sp create-for-rbac][az-ad-sp-create]. В следующем примере параметр `--skip-assignment` запрещает присваивание любых дополнительных назначений по умолчанию.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Результат будет похож на следующий пример. Запишите свои `appId` и `password`. Эти значения используются на следующем шаге.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Теперь определите переменные для идентификатора субъект-службы и секрета клиента, используя выходные данные своей собственной команды [az ad sp create-for-rbac][az-ad-sp-create], как показано в следующем примере. *SP_ID* — это ваш *appId*, а *SP_SECRET* — это ваш *пароль*.

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Обновление кластера AKS с помощью новых учетных данных

Независимо от того, решили ли вы создать или обновить учетные данные для существующей субъект-службы, теперь вы обновите кластер AKS новыми учетными данными, используя команду [az aks update-credentials][az-aks-update-credentials]. Используются переменные для *--service-principal* и *--client-secret*.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Обновление учетных данных субъект-службы в AKS займет всего несколько минут.

## <a name="next-steps"></a>Дополнительная информация

В этой статье была обновлена субъект-служба для самого кластера AKS. Дополнительные сведения о том, как управлять удостоверениями для рабочих нагрузок в кластере, см. в статье [Рекомендации по аутентификации и авторизации в службе Azure Kubernetes (AKS)][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
