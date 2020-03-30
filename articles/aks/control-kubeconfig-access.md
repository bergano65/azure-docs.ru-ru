---
title: Ограничение доступа к kubeconfig в Службе Azure Kubernetes (AKS)
description: Узнайте, как управлять доступом к файлу конфигурации Kubernetes (kubeconfig) для администраторов и пользователей кластера.
services: container-service
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 25c710cce2855d6af985d3f46082f47573bbc101
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259556"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Чтобы определить доступ к файлу конфигурации Kubernetes в службе Azure Kubernetes (AKS), используйте элементы управления доступом на основе ролей.

Вы можете взаимодействовать с кластерами Kubernetes с помощью инструмента `kubectl`. Azure CLI позволяет без труда получить учетные данные для доступа и сведения о конфигурации, чтобы подключиться к кластерам AKS с помощью `kubectl`. Чтобы ограничить доступ к сведениям о конфигурации Kubernetes (*kubeconfig*) и ограничить разрешения, можно использовать элементы управления доступом Azure на основе ролей (RBAC).

В этой статье показано, как назначить роли RBAC, чтобы ограничить доступ к сведениям о конфигурации для кластера AKS.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

В этой статье также требуется, чтобы вы запускали версию Azure CLI 2.0.65 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli-install]

## <a name="available-cluster-roles-permissions"></a>Доступные разрешения ролей кластера

При взаимодействии с кластером AKS с помощью инструмента `kubectl` используется файл конфигурации, который определяет сведения о подключении кластера. Этот файл конфигурации обычно хранится в *q/.kube/config.* Несколько кластеров могут быть определены в этом файле *kubeconfig.* Вы можете переключаться между кластерами с помощью команды [kubectl config use-context][kubectl-config-use-context].

Команда [az aks get-credentials][az-aks-get-credentials] позволяет получить учетные данные для доступа к кластеру AKS и объединить их в файл *kubeconfig*. Доступ к этим учетным данным также можно контролировать с помощью управления доступом Azure на основе ролей (RBAC). Эти роли Azure RBAC позволяют определить, кто может получить файл *kubeconfig* и какие он получит разрешения в пределах кластера.

Ниже описаны две встроенные роли:

* **Роль администратора кластера в Службе Azure Kubernetes**  
  * Разрешение доступа к вызову API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action*. Этот вызов API [позволяет перечислить учетные данные администратора кластера][api-cluster-admin].
  * Скачивание файла *kubeconfig* для роли *clusterAdmin*.
* **Роль пользователя кластера в Службе Azure Kubernetes**
  * Разрешение доступа к вызову API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action*. Этот вызов API [позволяет перечислить учетные данные пользователя кластера][api-cluster-user].
  * Скачивание файла *kubeconfig* для роли *clusterUser*.

Эти роли RBAC могут быть применены к пользователю или группе Active Directory (AD) Azure Active.

> ! (ПРИМЕЧАНИЕ) В кластерах, которые используют Azure AD, пользователи с ролью *кластера User* имеют пустой файл *kubeconfig,* который вызывает вход в систему. После входа в систему пользователи получают доступ на основе настроек Azure AD или группы. Пользователи с ролью *кластераAdmin* имеют доступ к админ.
>
> Кластеры, не использовавазы Azure AD, используют только роль *кластераAdmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Назначать разрешения на роль пользователю или группе

Чтобы назначить одну из доступных ролей, необходимо получить идентификатор ресурсов кластера AKS и идентификатор учетной записи или группы пользователей Azure AD. Следующий пример команды:

* Получите идентификатор ресурса кластера, используя команду [шоу az aks][az-aks-show] для кластера под названием *myAKSCluster* в группе ресурсов *myResourceGroup.* При необходимости укажите имя вашей группы ресурсов и кластера.
* Используйте [шоу-шоу учетной записи az][az-account-show] и команды [пользователя ad,][az-ad-user-show] чтобы получить идентификатор пользователя.
* Наконец, назначить роль с помощью [назначения роли аз создать][az-role-assignment-create] команду.

Следующий пример присваивает учетную запись *кластера Azure Kubernetes* отдельной учетной записи пользователя:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Если требуется назначить разрешения группе Azure AD, обновите `--assignee` параметр, показанный в предыдущем примере, с идентификатором объекта для *группы,* а не для *пользователя.* Чтобы получить идентификатор объекта для группы, используйте команду [шоу-шоу ad az.][az-ad-group-show] Следующий пример получает идентификатор объекта для группы Azure AD под названием *appdev:*`az ad group show --group appdev --query objectId -o tsv`

При необходимости можно изменить предыдущее назначение на *роль пользователя кластера*.

В следующем примере выходных данных показано, что назначение ролей выполнено успешно:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Получение и проверка сведений о конфигурации

Если роли RBAC назначены, получите определение *kubeconfig* для кластера AKS с помощью команды [az aks get-credentials][az-aks-get-credentials]. В приведенном ниже примере возвращаются учетные данные *--admin*, которые будут работать правильно при наличии *роли администратора кластера*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Затем с помощью команды [kubectl config view][kubectl-config-view] можно проверить, показано ли в *context*, что применены сведения о конфигурации администратора:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Удаление разрешений ролей

Чтобы удалить назначения роли, используйте команду [az role assignment delete][az-role-assignment-delete]. Укажите идентификатор учетной записи и идентификатор ресурса кластера, полученные в предыдущих командах. Если вы назначили роль группе, а не пользователю, укажите для `--assignee` параметра соответствующий идентификатор объекта группы, а не идентификатор объекта учетной записи:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы повысить уровень безопасности при доступе к кластерам AKS, [интегрируйте аутентификацию Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
