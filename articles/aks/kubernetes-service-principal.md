---
title: Субъект-службы для служб Azure Kubernetes (AKS)
description: Создание субъект-службы Azure Active Directory для кластера в службе Azure Kubernetes (AKS) и управление ей
services: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 523f08ddbf22e175af5b0604b04d4a2460ffd634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259426"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Субъекты-службы со службой Azure Kubernetes

Для взаимодействия с API-интерфейсами Azure кластеру AKS требуется [субъект-служба Azure Active Directory][aad-service-principal]. Субъект-служба необходима для динамического создания и управления другими ресурсами Azure, например балансировщиком нагрузки Azure или реестром контейнеров (ACR).

В этой статье показано как создать и использовать субъект-службу для кластеров AKS.

## <a name="before-you-begin"></a>Перед началом

Чтобы создать субъект-службу в Azure AD, вы должны иметь права на регистрацию приложения в клиенте Azure AD и назначение приложению роли в подписке Azure. Если у вас нет необходимых разрешений, может потребоваться попросить администратора Azure AD или администратора подписки предоставить их или предварительно создать субъект-службу для использования с кластером AKS.

Если вы используете принцип службы от другого арендатора Azure AD, при развертывании кластера возникают дополнительные соображения, доступные для разрешений. Возможно, у вас нет соответствующих разрешений на чтение и написание информации каталога. Для получения дополнительной [информации смотрите, что такое пользовательские разрешения по умолчанию в active Directory Azure?][azure-ad-permissions]

Вам также нужна версия Azure CLI 2.0.59 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Автоматическое создание и использование субъект-службы

При создании кластера AKS на портале Azure или с помощью команды [az aks create][az-aks-create] Azure может автоматически создать субъект-службу.

В следующем примере Azure CLI субъект-служба не указана. В этом сценарии субъект-службу в кластере AKS создает Azure CLI. Для успешного завершения операции в учетной записи Azure должны быть соответствующие права на создание субъект-службы.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Создание субъект-службы вручную

Чтобы вручную создать субъект-службу с помощью Azure CLI, используйте команду [az ad sp create-for-rbac][az-ad-sp-create]. В следующем примере параметр `--skip-assignment` запрещает присваивание любых дополнительных назначений по умолчанию.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

Результат будет похож на следующий пример. Запишите свои `appId` и `password`. Эти значения используются при создании кластера AKS в следующем разделе.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
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

> [!NOTE]
> Если вы используете существующий директор службы с индивидуальным секретом, убедитесь, что секрет не превышает 190 байтов.

При развертывании кластера AKS с помощью портала Azure на странице *Проверка подлинности* диалогового окна **Create Kubernetes cluster** (Создание кластера Kubernetes) выберите **Настроить субъект-службу**. Выберите **Использовать существующую**и укажите следующие значения:

- **Service principal client ID** (Идентификатор клиента для субъект-службы) — это *appId*;
- **Service principal client secret** (Секрет клиента субъект-службы) — это значение *password*.

![Изображение перехода к приложению Azure для голосования](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Делегирование прав доступа другим ресурсам Azure

Субъект-службу для кластера AKS можно использовать для доступа к другим ресурсам. Например, если требуется развернуть кластер AKS в существующую виртуальную сетевую подсеть Azure или подключиться к реестру контейнеров Azure (ACR), необходимо делегировать доступ к этим ресурсам главному руководителю службы.

Чтобы делегировать разрешения, создайте назначение ролей с помощью [назначения роли аз создать][az-role-assignment-create] команду. Назначаем `appId` определенную область, например группу ресурсов или виртуальный сетевой ресурс. Затем роль определяет разрешения субъекта-службы по отношению к ресурсу, как показано в следующем примере:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Для `--scope` ресурса нужно указать полный идентификатор ресурса, например */subscriptions/\<GUID\>/resourceGroups/myResourceGroup* или */subscriptions/\<GUID\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

В следующих разделах описываются распространенные делегирования, которые вам могут потребоваться.

### <a name="azure-container-registry"></a>Реестр контейнеров Azure

Если вы используете реестр контейнеров Azure (ACR) в качестве хранилища изображений контейнеров, вам необходимо предоставить разрешения директору службы для кластера AKS для чтения и вытягивания изображений. В настоящее время рекомендуемая конфигурация заключается в использовании команды обновления [az aks][az-aks-create] или [az aks][az-aks-update] для интеграции с реестром и присвоения соответствующей роли для основного обслуживания. Подробные шаги можно узнать в [реестре контейнеров Azure от службы Azure Kubernetes.][aks-to-acr]

### <a name="networking"></a>Сети

Вы можете использовать расширенное сетевое взаимодействие, где виртуальная сеть и подсеть или общедоступные IP-адреса находятся в другой группе ресурсов. Назначьте одно разрешение из следующего набора разрешений роли:

- Создайте [пользовательскую роль][rbac-custom-role] и определите следующие разрешения роли:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- Или назначьте встроенную роль [Участник сетей][rbac-network-contributor] в подсети виртуальной сети.

### <a name="storage"></a>Хранилище

Вам может потребоваться доступ к существующим дисковым ресурсам в другой группе ресурсов. Назначьте одно разрешение из следующего набора разрешений роли:

- Создайте [пользовательскую роль][rbac-custom-role] и определите следующие разрешения роли:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Или назначьте встроенную роль [Участник учетных записей хранения][rbac-storage-contributor] для группы ресурсов.

### <a name="azure-container-instances"></a>Экземпляры контейнеров Azure

Если вы используете интеграцию Virtual Kubelet и AKS и решили запустить службу "Экземпляры контейнеров Azure" (ACI) в группе ресурсов, отличной от группы для кластера AKS, предоставьте субъекту-службе AKS разрешения *Участник* на доступ к группе ресурсов ACI.

## <a name="additional-considerations"></a>Дополнительные сведения

При использовании AKS и субъект-служб Azure AD учитывайте приведенные ниже аспекты.

- Субъект-служба для Kubernetes входит в конфигурацию кластера. Тем не менее не используйте идентификатор для развертывания кластера.
- По умолчанию основные учетные данные службы действительны в течение одного года. Вы можете [обновить или повернуть основные учетные данные службы][update-credentials] в любое время.
- Все субъекты-службы связаны с определенными приложениями Azure AD. Основной сервис для кластера Kubernetes может быть связан с любым действительным именем приложения Azure AD (например: *https://www.contoso.org/example*). URL-адрес приложения не обязательно должен быть реальной конечной точкой.
- При указании **идентификатора клиента** субъект-службы используйте значение `appId`.
- На вдовых узлов агента в кластере Kubernetes основные учетные данные службы хранятся в файле`/etc/kubernetes/azure.json`
- При использовании команды [az aks create][az-aks-create] для автоматического создания субъект-службы учетные данные субъект-службы записываются в файл `~/.azure/aksServicePrincipal.json` на компьютере, с которого выполняется команда.
- Если вы специально не передаете основной сервис в дополнительных командах `~/.azure/aksServicePrincipal.json` AKS CLI, используется принцип службы по умолчанию, расположенный по адресу.  
- Вы также можете дополнительно удалить файл aksServicePrincipal.json, и AKS создаст новый директор службы.
- При удалении кластера AKS, созданного с помощью команды [az aks create][az-aks-create], автоматически созданная субъект-служба не удаляется.
    - Чтобы удалить субъект-службу, выполните запрос для кластера *servicePrincipalProfile.clientId*, а затем удалите субъект-службу с помощью команды [az ad app delete][az-ad-app-delete]. Замените имена группы ресурсов и кластера собственными значениями.

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Устранение неполадок

Основные учетные данные службы для кластера AKS консируются Azure CLI. Если срок действия этих учетных данных истек, вы сталкиваетесь с ошибками развертывания кластеров AKS. Следующее сообщение об ошибке при [запуске az aks][az-aks-create] может указывать на проблему с основными учетными данными кэшированных служб:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Проверьте возраст файла учетных данных с помощью следующей команды:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Срок действия по умолчанию для основных учетных данных службы составляет один год. Если ваш файл *aksServicePrincipal.json* старше одного года, удалите файл и повторите попытку развернуть кластер AKS.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о принципах [Application and service principal objects][service-principal]службы Активных каталогов Azure см.

Для получения информации о том, как обновить учетные данные, [см.][update-credentials]

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
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
