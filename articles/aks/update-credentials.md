---
title: Сброс учетных данных для кластера Службы Azure Kubernetes (AKS)
description: Дополнительные сведения о том, как обновить или сбросить учетные данные участника службы для кластера в Службе Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: bda0ab50b829fa2e6d58e73b51e3a0a0f6c9e2af
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432922"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Обновление или выполнение циклического сдвига учетных данных для субъект-службы в Службе Azure Kubernetes (AKS)

По умолчанию кластеры AKS создаются вместе с субъектом-службой, срок действия которого составляет один год. Когда будет истекать срок действия, вы можете сбросить учетные данные, чтобы продлить срок действия субъекта-службы на дополнительный период времени. Вы также можете обновить или сменить учетные данные как часть определенной политики безопасности. В этой статье подробно описано, как обновить эти учетные данные для кластера AKS.

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.65 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Выбор создания или обновления субъект-службы

Если необходимо обновить учетные данные для кластера AKS, вы можете выполнить следующие действия:

* обновить учетные данные для существующей субъект-службы, которая используется в кластере, или
* создать субъект-службу и обновить кластер для использования этих новых учетных данных.

### <a name="update-existing-service-principal-expiration"></a>Обновление существующего срока действия субъекта-службы

Чтобы обновить учетные данные для существующего субъекта-службы, получите идентификатор субъекта-службы для кластера с помощью команды [AZ AKS показывать][az-aks-show] . В следующем примере возвращается идентификатор для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*. ИДЕНТИФИКАТОР субъекта-службы задается в виде переменной с именем *SP_ID* для использования в дополнительной команде.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

При использовании набора переменных, содержащего идентификатор субъекта-службы, теперь необходимо сбросить учетные данные с помощью команды [AZ AD SP Credential rereset][az-ad-sp-credential-reset]. Следующий пример позволяет платформе Azure создать новый безопасный секрет для субъект-службы. Этот новый безопасный секрет также хранится в виде переменной.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Теперь перейдите к разделу [Обновление кластера AKS с помощью новых учетных данных](#update-aks-cluster-with-new-credentials). Этот шаг необходим для отражения изменений субъекта-службы в кластере AKS.

### <a name="create-a-new-service-principal"></a>Создание нового субъекта-службы

Если вы решили обновить существующие учетные данные субъект-службы в предыдущем разделе, пропустите этот шаг. Продолжайте [обновлять кластер AKS с помощью новых учетных данных](#update-aks-cluster-with-new-credentials).

Чтобы создать субъект-службу, а затем обновить кластер AKS для использования этих новых учетных данных, используйте команду [AZ AD SP Create-for-RBAC][az-ad-sp-create] . В следующем примере параметр `--skip-assignment` запрещает присваивание любых дополнительных назначений по умолчанию.

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

Теперь определите переменные для идентификатора субъекта-службы и секрета клиента, используя выходные данные собственной команды [AZ AD SP Create-for-RBAC][az-ad-sp-create] , как показано в следующем примере. *SP_ID* — это ваш *appId*, а *SP_SECRET* — это ваш *пароль*.

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Теперь перейдите к разделу [Обновление кластера AKS с помощью новых учетных данных](#update-aks-cluster-with-new-credentials). Этот шаг необходим для отражения изменений субъекта-службы в кластере AKS.

## <a name="update-aks-cluster-with-new-credentials"></a>Обновление кластера AKS с помощью новых учетных данных

Независимо от того, выбрано ли обновление учетных данных для существующего субъекта-службы или создание субъекта-службы, вы теперь можете обновить кластер AKS с помощью новых учетных данных, выполнив команду [AZ AKS Update-Credentials][az-aks-update-credentials] . Используются переменные для *--service-principal* и *--client-secret*.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Обновление учетных данных субъект-службы в AKS займет всего несколько минут.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье была обновлена субъект-служба для самого кластера AKS. Дополнительные сведения об управлении удостоверениями для рабочих нагрузок в кластере см. в разделе рекомендации [по проверке подлинности и авторизации в AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
