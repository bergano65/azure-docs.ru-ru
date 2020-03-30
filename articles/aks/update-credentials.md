---
title: Сброс учетных данных для кластера Службы Azure Kubernetes (AKS)
description: Узнайте, как обновить или сбросить учетные данные службы или учетных данных aAD-приложений для кластера Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: b7d652be3733cb130a3973909de59489047efe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475550"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Обновление или поверните учетные данные службы Azure Kubernetes (AKS)

По умолчанию кластеры AKS создаются вместе с субъектом-службой, срок действия которого составляет один год. Когда будет истекать срок действия, вы можете сбросить учетные данные, чтобы продлить срок действия субъекта-службы на дополнительный период времени. Вы также можете обновить или сменить учетные данные как часть определенной политики безопасности. В этой статье подробно описано, как обновить эти учетные данные для кластера AKS.

Возможно, вы также [интегрировали кластер AKS с Azure Active Directory][aad-integration]и использовать его в качестве поставщика аутентификации для кластера. В этом случае для кластера будет создано еще 2 идентификатора, aAD Server App и приложение клиента AAD, вы также можете сбросить эти учетные данные. 

## <a name="before-you-begin"></a>Перед началом

Вам нужна версия Azure CLI 2.0.65 или более поздняя установленная и настроенная. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Обновление или создание нового директора службы для кластера AKS

Если необходимо обновить учетные данные для кластера AKS, вы можете выполнить следующие действия:

* обновить учетные данные для существующей субъект-службы, которая используется в кластере, или
* создать субъект-службу и обновить кластер для использования этих новых учетных данных.

### <a name="reset-existing-service-principal-credential"></a>Сбросить существующие основные учетные данные службы

Чтобы обновить учетные данные для существующей субъект-службы, получите идентификатор субъект-службы своего кластера с помощью команды [az aks show][az-aks-show]. В следующем примере возвращается идентификатор для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*. Идентификатор основного обслуживания устанавливается как *переменный,* названный SP_ID для использования в дополнительной команде.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

С помощью набора переменных, который содержит идентификатор субъект-службы, сбросьте учетные данные с помощью [учетных данных az ad sp][az-ad-sp-credential-reset]. Следующий пример позволяет платформе Azure создать новый безопасный секрет для субъект-службы. Этот новый безопасный секрет также хранится в виде переменной.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Теперь продолжайте [обновлять кластер AKS с новыми учетными данными основных служб.](#update-aks-cluster-with-new-service-principal-credentials) Этот шаг необходим для того, чтобы основные изменения Службы были отражены в кластере АКС.

### <a name="create-a-new-service-principal"></a>Создание нового директора службы

Если вы решили обновить существующие учетные данные субъект-службы в предыдущем разделе, пропустите этот шаг. Продолжайте [обновлять кластер AKS с новыми учетными данными службы.](#update-aks-cluster-with-new-service-principal-credentials)

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

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Теперь продолжайте [обновлять кластер AKS с новыми учетными данными основных служб.](#update-aks-cluster-with-new-service-principal-credentials) Этот шаг необходим для того, чтобы основные изменения Службы были отражены в кластере АКС.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Обновление кластера AKS с новыми учетными данными основного обслуживания

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

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Обновление кластера AKS с новыми учетными данными приложения AAD

Вы можете создавать новые приложения AAD Server и Client, следуя [шагам интеграции AAD.][create-aad-app] Или сбросить существующие приложения AAD по тому же методу, что и [для основного сбросить службу.](#reset-existing-service-principal-credential) После этого вам просто нужно обновить учетные данные приложения AAD кластера, используя ту же команду обновлений [az aks,][az-aks-update-credentials] но с помощью переменных *--reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Дальнейшие действия

В этой статье был обновлен директор службы для самого кластера AKS и приложений интеграции AAD. Дополнительные сведения о том, как управлять удостоверениями для рабочих нагрузок в кластере, см. в статье [Рекомендации по аутентификации и авторизации в службе Azure Kubernetes (AKS)][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
