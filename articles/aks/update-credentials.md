---
title: Сброс учетных данных для кластера Службы Azure Kubernetes (AKS)
description: Узнайте, как обновить или сбросить учетные данные субъекта-службы или приложения AAD для кластера Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 5dab9a778653d2ec6e32ddb3833ddcf6a95cae13
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096102"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Обновление или смена учетных данных для службы Azure Kubernetes (AKS)

По умолчанию кластеры AKS создаются вместе с субъектом-службой, срок действия которого составляет один год. Когда будет истекать срок действия, вы можете сбросить учетные данные, чтобы продлить срок действия субъекта-службы на дополнительный период времени. Вы также можете обновить или сменить учетные данные как часть определенной политики безопасности. В этой статье подробно описано, как обновить эти учетные данные для кластера AKS.

Вы также можете [интегрировать кластер AKS с Azure Active Directory][aad-integration]и использовать его в качестве поставщика проверки подлинности для кластера. В этом случае у вас будет еще 2 удостоверения, созданные для кластера, серверное приложение AAD и клиентское приложение AAD. Эти учетные данные также можно сбросить. 

## <a name="before-you-begin"></a>Перед началом

Требуется Azure CLI версии 2.0.65 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Обновление или создание нового субъекта-службы для кластера AKS

Если необходимо обновить учетные данные для кластера AKS, вы можете выполнить следующие действия:

* обновить учетные данные для существующей субъект-службы, которая используется в кластере, или
* создать субъект-службу и обновить кластер для использования этих новых учетных данных.

### <a name="reset-existing-service-principal-credential"></a>Сбросить существующие учетные данные субъекта-службы

Чтобы обновить учетные данные для существующего субъекта-службы, получите идентификатор субъекта-службы для кластера с помощью команды [AZ AKS показывать][az-aks-show] . В следующем примере возвращается идентификатор для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*. ИДЕНТИФИКАТОР субъекта-службы задается как переменная с именем *SP_ID* для использования в дополнительной команде.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

При использовании набора переменных, содержащего идентификатор субъекта-службы, теперь необходимо сбросить учетные данные с помощью команды [AZ AD SP Credential rereset][az-ad-sp-credential-reset]. Следующий пример позволяет платформе Azure создать новый безопасный секрет для субъект-службы. Этот новый безопасный секрет также хранится в виде переменной.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Теперь продолжайте [обновлять кластер AKS, используя новые учетные данные субъекта-службы](#update-aks-cluster-with-new-service-principal-credentials). Этот шаг необходим для отражения изменений субъекта-службы в кластере AKS.

### <a name="create-a-new-service-principal"></a>Создание нового субъекта-службы

Если вы решили обновить существующие учетные данные субъект-службы в предыдущем разделе, пропустите этот шаг. Продолжайте [обновлять кластер AKS, используя новые учетные данные субъекта-службы](#update-aks-cluster-with-new-service-principal-credentials).

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

Теперь продолжайте [обновлять кластер AKS, используя новые учетные данные субъекта-службы](#update-aks-cluster-with-new-service-principal-credentials). Этот шаг необходим для отражения изменений субъекта-службы в кластере AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Обновить кластер AKS с новыми учетными данными субъекта-службы

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

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Обновление кластера AKS с новыми учетными данными приложения AAD

Вы можете создать новые серверные и клиентские приложения AAD, следуя [инструкциям по интеграции AAD][create-aad-app]. Или сбросьте существующие приложения AAD, следуя тому [же методу, что и для сброса субъекта-службы](#reset-existing-service-principal-credential). После этого нужно просто обновить учетные данные приложения AAD кластера с помощью той же команды [AZ AKS Update-Credential][az-aks-update-credentials] , но с помощью переменных *--Reset-AAD* .

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

В этой статье субъект-служба для самого кластера AKS и приложений интеграции AAD обновлен. Дополнительные сведения об управлении удостоверениями для рабочих нагрузок в кластере см. в разделе рекомендации [по проверке подлинности и авторизации в AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
