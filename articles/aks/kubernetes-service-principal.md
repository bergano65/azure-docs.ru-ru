---
title: Субъект-службы для служб Azure Kubernetes (AKS)
description: Создание субъект-службы Azure Active Directory для кластера в службе Azure Kubernetes (AKS) и управление ей
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mlearned
ms.openlocfilehash: e24d930ec82ea92a040efeed3056a10917ce2b2a
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263904"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Субъекты-службы со службой Azure Kubernetes

Для взаимодействия с API Azure кластеру AKS требуется [субъект-служба Azure Active Directory (AD)][aad-service-principal]. Субъект-служба необходима для динамического создания и управления другими ресурсами Azure, например балансировщиком нагрузки Azure или реестром контейнеров (ACR).

В этой статье показано как создать и использовать субъект-службу для кластеров AKS.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы создать субъект-службу в Azure AD, вы должны иметь права на регистрацию приложения в клиенте Azure AD и назначение приложению роли в подписке Azure. Если у вас нет необходимых разрешений, может потребоваться попросить администратора Azure AD или администратора подписки предоставить их или предварительно создать субъект-службу для использования с кластером AKS.

Если вы используете субъект-службу из другого клиента Azure AD, существуют дополнительные рекомендации относительно разрешений, доступных при развертывании кластера. Возможно, у вас нет необходимых разрешений для чтения и записи данных каталога. Дополнительные сведения см [. в разделе что такое разрешения пользователя по умолчанию в Azure Active Directory?][azure-ad-permissions]

Также требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Автоматическое создание и использование субъект-службы

При создании кластера AKS в портал Azure или с помощью команды [AZ AKS Create][az-aks-create] Azure может автоматически создать субъект-службу.

В следующем примере Azure CLI субъект-служба не указана. В этом сценарии субъект-службу в кластере AKS создает Azure CLI. Для успешного завершения операции в учетной записи Azure должны быть соответствующие права на создание субъект-службы.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Создание субъект-службы вручную

Чтобы вручную создать субъект-службу с Azure CLI, используйте команду [AZ AD SP Create для for-RBAC][az-ad-sp-create] . В следующем примере параметр `--skip-assignment` запрещает присваивание любых дополнительных назначений по умолчанию.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Результат будет похож на следующий пример. Запишите свои `appId` и `password`. Эти значения используются при создании кластера AKS в следующем разделе.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Указание субъект-службы для кластера AKS

Чтобы использовать существующий субъект-службу при создании кластера AKS с помощью команды [AZ AKS Create][az-aks-create] , используйте параметры `--service-principal` и `--client-secret`, чтобы указать `appId` и `password` из выходных данных команды [AZ AD SP Create-for-RBAC][az-ad-sp-create] :

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

Субъект-службу для кластера AKS можно использовать для доступа к другим ресурсам. Например, если вы хотите развернуть кластер AKS в существующей подсети виртуальной сети Azure или подключиться к реестру контейнеров Azure (запись контроля доступа), необходимо делегировать доступ к этим ресурсам субъекту-службе.

Чтобы делегировать разрешения, создайте назначение роли с помощью команды [AZ Role назначение Create][az-role-assignment-create] . Назначьте `appId` для определенной области, например для группы ресурсов или ресурса виртуальной сети. Затем роль определяет разрешения субъекта-службы по отношению к ресурсу, как показано в следующем примере:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Для `--scope` ресурса нужно указать полный идентификатор ресурса, например */subscriptions/\<GUID\>/resourceGroups/myResourceGroup* или */subscriptions/\<GUID\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

В следующих разделах описываются распространенные делегирования, которые вам могут потребоваться.

### <a name="azure-container-registry"></a>Реестр контейнеров Azure

Если вы используете реестр контейнеров Azure (запись контроля доступа) в качестве хранилища образов контейнеров, вам необходимо предоставить разрешения на чтение и извлечение образов для субъекта-службы в кластере AKS. В настоящее время рекомендуемой конфигурацией является использование команды [AZ AKS Create][az-aks-create] или [AZ AKS Update] [AZ-AKS-Update] для интеграции с реестром и назначения соответствующей роли для субъекта-службы. Подробные инструкции см. в статье [Аутентификация в реестре контейнеров Azure из службы Kubernetes Azure][aks-to-acr].

### <a name="networking"></a>Сеть

Вы можете использовать расширенное сетевое взаимодействие, где виртуальная сеть и подсеть или общедоступные IP-адреса находятся в другой группе ресурсов. Назначьте одно разрешение из следующего набора разрешений роли:

- Создайте [пользовательскую роль][rbac-custom-role] и определите следующие разрешения роли.
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft. Network/virtualNetworks/подсети/запись*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- Или назначьте встроенную роль " [участник сети][rbac-network-contributor] " в подсети в виртуальной сети.

### <a name="storage"></a>Хранилище

Вам может потребоваться доступ к существующим дисковым ресурсам в другой группе ресурсов. Назначьте одно разрешение из следующего набора разрешений роли:

- Создайте [пользовательскую роль][rbac-custom-role] и определите следующие разрешения роли.
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Или назначьте встроенную роль [участника учетной записи хранения][rbac-storage-contributor] в группе ресурсов.

### <a name="azure-container-instances"></a>Экземпляры контейнеров Azure

Если вы используете интеграцию Virtual Kubelet и AKS и решили запустить службу "Экземпляры контейнеров Azure" (ACI) в группе ресурсов, отличной от группы для кластера AKS, предоставьте субъекту-службе AKS разрешения *Участник* на доступ к группе ресурсов ACI.

## <a name="additional-considerations"></a>Дополнительные замечания

При использовании AKS и субъект-служб Azure AD учитывайте приведенные ниже аспекты.

- Субъект-служба для Kubernetes входит в конфигурацию кластера. Тем не менее не используйте идентификатор для развертывания кластера.
- По умолчанию учетные данные субъекта-службы действительны в течение одного года. Вы можете [Обновить или повернуть учетные данные субъекта-службы][update-credentials] в любое время.
- Все субъекты-службы связаны с определенными приложениями Azure AD. Субъект-служба для кластера Kubernetes может быть связана с любым допустимым именем приложения Azure AD (например *https://www.contoso.org/example* ). URL-адрес приложения не обязательно должен быть реальной конечной точкой.
- При указании **идентификатора клиента** субъект-службы используйте значение `appId`.
- На виртуальных машинах узла агента в кластере Kubernetes учетные данные субъекта-службы хранятся в файле @no__t – 0.
- При автоматическом создании субъекта-службы с помощью команды [AZ AKS Create][az-aks-create] учетные данные субъекта-службы записываются в файл @no__t – 1 на компьютере, используемом для выполнения команды.
- При удалении кластера AKS, созданного с помощью команды [AZ AKS Create][az-aks-create], автоматически созданный субъект-служба не удаляется.
    - Чтобы удалить субъект-службу, выполните запрос к кластеру *сервицепринЦипалпрофиле. ClientID* и удалите его с помощью команды [AZ AD App Delete][az-ad-app-delete]. Замените имена группы ресурсов и кластера собственными значениями.

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Устранение неполадок

Учетные данные субъекта-службы для кластера AKS кэшируются Azure CLI. Если срок действия этих учетных данных истек, возникнут ошибки при развертывании кластеров AKS. Следующее сообщение об ошибке при выполнении команды [AZ AKS Create][az-aks-create] может указывать на проблему с кэшированными учетными данными субъекта-службы:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Проверьте возраст файла учетных данных с помощью следующей команды:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Срок действия учетных данных субъекта-службы по умолчанию составляет один год. Если файл *акссервицепринЦипал. JSON* старше одного года, удалите файл и повторите попытку развертывания кластера AKS.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure Active Directory субъектах-службах см. в разделе [объекты приложения и субъекта-службы][service-principal].

Сведения об обновлении учетных данных см. в [статье обновление или смена учетных данных субъекта-службы в AKS][update-credentials].

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
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
