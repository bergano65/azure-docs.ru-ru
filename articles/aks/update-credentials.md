---
title: Сброс учетных данных для кластера Службы Azure Kubernetes (AKS)
description: Дополнительные сведения о том, как обновить или сбросить учетные данные участника службы для кластера в Службе Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 5aac941133296d2040d5dd670155b80f5807e1e9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614130"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Обновление или выполнение циклического сдвига учетных данных для субъект-службы в Службе Azure Kubernetes (AKS)

По умолчанию кластеры AKS создаются вместе с субъектом-службой, срок действия которого составляет один год. Когда будет истекать срок действия, вы можете сбросить учетные данные, чтобы продлить срок действия субъекта-службы на дополнительный период времени. Вы также можете обновить или сменить учетные данные как часть определенной политики безопасности. В этой статье подробно описано, как обновить эти учетные данные для кластера AKS.

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.65 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду  `az --version`. Если требуется выполнить установку или обновление, см. в разделе [установить Azure CLI][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Выбор создания или обновления субъект-службы

Если необходимо обновить учетные данные для кластера AKS, вы можете выполнить следующие действия:

* обновить учетные данные для существующей субъект-службы, которая используется в кластере, или
* создать субъект-службу и обновить кластер для использования этих новых учетных данных.

Если вы хотите создать субъект-службу, а затем обновить кластер AKS, пропустите оставшуюся часть действий, описанных в этом разделе, и продолжите [создание субъект-службы](#create-a-service-principal). Если необходимо обновить учетные данные для существующей субъект-службы, используемой кластером AKS, выполните действия, описанные в этом разделе.

### <a name="get-the-service-principal-id"></a>Получение идентификатора субъект-службы

Чтобы обновить учетные данные для существующего субъекта-службы, получить идентификатор субъекта-службы кластера с помощью [az aks show][az-aks-show] команды. В следующем примере возвращается идентификатор для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*. Идентификатор субъекта-службы задается как переменная с именем *SP_ID* для использования в дополнительную команду.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Обновление учетных данных субъект-службы

С набором переменных, который содержит идентификатор субъекта-службы, сбросьте учетные данные, с помощью [сбросить учетные данные az ad sp][az-ad-sp-credential-reset]. Следующий пример позволяет платформе Azure создать новый безопасный секрет для субъект-службы. Этот новый безопасный секрет также хранится в виде переменной.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Теперь перейдите к разделу [Обновление кластера AKS с помощью новых учетных данных](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Если вы решили обновить существующие учетные данные субъект-службы в предыдущем разделе, пропустите этот шаг. Продолжайте [обновлять кластер AKS с помощью новых учетных данных](#update-aks-cluster-with-new-credentials).

Чтобы создать субъект-службу и затем обновить кластер AKS, чтобы использовать эти новые учетные данные, используйте [az ad sp create-for-rbac][az-ad-sp-create] команды. В следующем примере параметр `--skip-assignment` запрещает присваивание любых дополнительных назначений по умолчанию.

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

Теперь определите переменные для службы основного идентификатора и секрета клиента на основе выходных данных собственного [az ad sp create-for-rbac][az-ad-sp-create] команды, как показано в следующем примере. *SP_ID* — это ваш *appId*, а *SP_SECRET* — это ваш *пароль*.

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Обновление кластера AKS с помощью новых учетных данных

Независимо от того, является ли вы решили создать субъект-службу или обновить учетные данные для существующего субъекта-службы, теперь обновление кластера AKS с помощью новых учетных данных с помощью [az aks-учетные данные обновления][az-aks-update-credentials] команды. Используются переменные для *--service-principal* и *--client-secret*.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Обновление учетных данных субъект-службы в AKS займет всего несколько минут.

## <a name="next-steps"></a>Следующие шаги

В этой статье была обновлена субъект-служба для самого кластера AKS. Дополнительные сведения о том, как управление удостоверениями для рабочих нагрузок в рамках кластера, см. в разделе [советы и рекомендации для проверки подлинности и авторизации в AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
