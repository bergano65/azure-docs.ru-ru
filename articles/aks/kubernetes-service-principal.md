---
title: Субъект-службы для служб Azure Kubernetes (AKS)
description: Создание субъект-службы Azure Active Directory для кластера в службе Azure Kubernetes (AKS) и управление ей
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: 2bc0579d3dd60d66a23a29dabff7e43ca8dfee76
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435401"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Субъекты-службы со службой Azure Kubernetes

Для взаимодействия с API-интерфейсами Azure кластеру AKS требуется [субъект-служба Azure Active Directory][aad-service-principal]. Субъект-служба необходима для динамического создания и управления другими ресурсами Azure, например балансировщиком нагрузки Azure или реестром контейнеров (ACR).

В этой статье показано как создать и использовать субъект-службу для кластеров AKS.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы создать субъект-службу в Azure AD, вы должны иметь права на регистрацию приложения в клиенте Azure AD и назначение приложению роли в подписке Azure. Если у вас нет необходимых разрешений, может потребоваться попросить администратора Azure AD или администратора подписки предоставить их или предварительно создать субъект-службу для использования с кластером AKS.

Кроме того, нужно установить и настроить Azure CLI 2.0.46 или более поздней версии. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Автоматическое создание и использование субъект-службы

При создании кластера AKS на портале Azure или с помощью команды [az aks create][az-aks-create] Azure может автоматически создать субъект-службу.

В следующем примере Azure CLI субъект-служба не указана. В этом сценарии субъект-службу в кластере AKS создает Azure CLI. Для успешного завершения операции в учетной записи Azure должны быть соответствующие права на создание субъект-службы.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="manually-create-a-service-principal"></a>Создание субъект-службы вручную

Чтобы вручную создать субъект-службу с помощью Azure CLI, используйте команду [az ad sp create-for-rbac][az-ad-sp-create]. В следующем примере параметр `--skip-assignment` запрещает присваивание любых дополнительных назначений по умолчанию.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Результат будет похож на следующий пример. Запишите свои `appId` и `password`. Эти значения используются при создании кластера AKS в следующем разделе.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2018-09-25-21-10-19",
  "name": "http://azure-cli-2018-09-25-21-10-19",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Указание субъект-службы для кластера AKS

Чтобы использовать существующую субъект-службу при создании кластера AKS с помощью команды [az aks create][az-aks-create], используйте параметры `--service-principal` и `--client-secret`, указывающие `appId` и `password` из выходных данных команды [az ad sp create-for-rbac][az-ad-sp-create].

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

При развертывании кластера AKS с помощью портала Azure на странице *Проверка подлинности* диалогового окна **Create Kubernetes cluster** (Создание кластера Kubernetes) выберите **Настроить субъект-службу**. Выберите **Использовать существующую**и укажите следующие значения:

- **Service principal client ID** (Идентификатор клиента для субъект-службы) — это *appId*;
- **Service principal client secret** (Секрет клиента субъект-службы) — это значение *password*.

![Изображение перехода к приложению Azure для голосования](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Делегирование прав доступа другим ресурсам Azure

Субъект-службу для кластера AKS можно использовать для доступа к другим ресурсам. Например, если вы хотите использовать расширенное сетевое взаимодействие для подключения к существующей виртуальной сети или Реестру контейнеров Azure (ACR), необходимо делегировать доступ субъекту-службе.

Чтобы делегировать разрешения, создайте назначение роли, используя команду [az role assignment create][az-role-assignment-create]. Вы назначаете `appId` определенной области, например группе ресурсов или ресурсу виртуальной сети. Затем роль определяет разрешения субъекта-службы по отношению к ресурсу, как показано в следующем примере:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Для `--scope` ресурса нужно указать полный идентификатор ресурса, например */subscriptions/\<GUID\>/resourceGroups/myResourceGroup* или */subscriptions/\<GUID\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

В следующих разделах описываются распространенные делегирования, которые вам могут потребоваться.

### <a name="azure-container-registry"></a>Реестр контейнеров Azure

Если вы используете Реестр контейнеров Azure (ACR) как хранилище образов контейнера, необходимо предоставить кластеру AKS разрешения на чтение и извлечение образов. Субъекту-службе кластера AKS необходимо делегировать роль *Читатель* в реестре. Подробные инструкции см. в разделе [Предоставление AKS доступа к ACR][aks-to-acr].

### <a name="networking"></a>Сеть

Вы можете использовать расширенное сетевое взаимодействие, где виртуальная сеть и подсеть или общедоступные IP-адреса находятся в другой группе ресурсов. Назначьте одно разрешение из следующего набора разрешений роли:

- Создайте [пользовательскую роль][rbac-custom-role] и определите следующие разрешения роли:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
- Или назначьте встроенную роль [Участник сетей][rbac-network-contributor] в подсети виртуальной сети.

### <a name="storage"></a>Хранилище

Вам может потребоваться доступ к существующим дисковым ресурсам в другой группе ресурсов. Назначьте одно разрешение из следующего набора разрешений роли:

- Создайте [пользовательскую роль][rbac-custom-role] и определите следующие разрешения роли:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Или назначьте встроенную роль [Участник учетных записей хранения][rbac-storage-contributor] для группы ресурсов.

### <a name="azure-container-instances"></a>Экземпляры контейнеров Azure

Если вы используете интеграцию Virtual Kubelet и AKS и решили запустить службу "Экземпляры контейнеров Azure" (ACI) в группе ресурсов, отличной от группы для кластера AKS, предоставьте субъекту-службе AKS разрешения *Участник* на доступ к группе ресурсов ACI.

## <a name="additional-considerations"></a>Дополнительные замечания

При использовании AKS и субъект-служб Azure AD учитывайте приведенные ниже аспекты.

- Субъект-служба для Kubernetes входит в конфигурацию кластера. Тем не менее не используйте идентификатор для развертывания кластера.
- Все субъекты-службы связаны с определенными приложениями Azure AD. Субъект-служба для кластера Kubernetes может быть связана с любым допустимым именем приложения Azure AD (например *https://www.contoso.org/example*). URL-адрес приложения не обязательно должен быть реальной конечной точкой.
- При указании **идентификатора клиента** субъект-службы используйте значение `appId`.
- На главной виртуальной машине и виртуальной машине узла в кластере Kubernetes учетные данные субъект-службы хранятся в файле `/etc/kubernetes/azure.json`.
- При использовании команды [az aks create][az-aks-create] для автоматического создания субъект-службы учетные данные субъект-службы записываются в файл `~/.azure/aksServicePrincipal.json` на компьютере, с которого выполняется команда.
- При удалении кластера AKS, созданного с помощью команды [az aks create][az-aks-create], автоматически созданная субъект-служба не удаляется.
    - Чтобы удалить субъект-службу, сначала нужно получить идентификатор для службы участника с помощью команды [az ad app list][az-ad-app-list]. Следующий пример выполняет запрос к кластеру с именем *myAKSCluster*, а затем удаляет идентификатор приложения с помощью команды [az ad app delete][az-ad-app-delete]. Замените эти имена своими значениями:

        ```azurecli
        az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
        az ad app delete --id <appId>
        ```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о субъект-службах Azure Active Directory см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory][service-principal].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: ../container-registry/container-registry-auth-aks.md?toc=%2fazure%2faks%2ftoc.json#grant-aks-access-to-acr
