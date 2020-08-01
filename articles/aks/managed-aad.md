---
title: Использование Azure AD в службе Kubernetes Azure
description: Узнайте, как использовать Azure AD в службе Kubernetes Azure (AKS).
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 896986775f0132ef08b17bdfefc00e5e06cf3d9f
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448133"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Интеграция Azure Active Directory с управляемым AKS

Интеграция Azure AD с AKS разработана для упрощения процесса интеграции Azure AD, где пользователям ранее требовалось было создавать клиентское приложение, серверное приложение и требовалось клиент Azure AD для предоставления разрешений на чтение каталога. В новой версии поставщик ресурсов AKS управляет клиентскими и серверными приложениями.

## <a name="azure-ad-authentication-overview"></a>Обзор аутентификации Azure AD

Администраторы кластера могут настроить Kubernetes управления доступом на основе ролей (RBAC) на основе удостоверения пользователя или членства в группе каталогов. Кластеры AKS проходят аутентификацию Azure AD с помощью OpenID Connect. OpenID Connect представляет собой уровень идентификации на основе протокола OAuth 2.0. Дополнительные сведения о OpenID Connect Connect см. в [документации по Open ID Connect][open-id-connect].

Дополнительные сведения о потоке интеграции AAD см. в [документации по основным понятиям интеграции Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="region-availability"></a>Доступность по регионам

Интеграция Azure Active Directory с управляемыми AKSми доступна в общедоступных регионах, где [поддерживается AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

* В настоящее время Azure для государственных организаций не поддерживается.
* В настоящее время в Azure Китая 21Vianet не поддерживается.

## <a name="limitations"></a>Ограничения 

* Невозможно отключить управляемую AKS интеграцию Azure AD
* кластеры, не поддерживающие RBAC, не поддерживаются для интеграции с AKS, управляемой AAD
* Изменение клиента Azure AD, связанного с интеграцией AAD, управляемой AKS, не поддерживается

> [!IMPORTANT]
> Функции предварительной версии AKS доступны на уровне самообслуживания. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично охвачены службой поддержки клиентов. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения доступны в следующих статьях поддержки. 
> - [Политики поддержки AKS](support-policies.md) 
> - [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="prerequisites"></a>Предварительные требования

* Azure CLI версии 2.9.0 или более поздней.
* Kubectl с минимальной версией [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

> [!Important]
> Необходимо использовать Kubectl с минимальной версией 1,18

Чтобы установить kubectl, выполните следующие команды:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
```

Используйте [эти инструкции](https://kubernetes.io/docs/tasks/tools/install-kubectl/) для других операционных систем.

```azurecli-interactive 
az feature register --name AAD-V2 --namespace Microsoft.ContainerService    
``` 

Состояние **Registered** (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list): 

```azurecli-interactive 
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"    
``` 

Когда отобразится правильный статус, обновите регистрацию поставщика ресурсов `Microsoft.ContainerService` с помощью команды [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register):    

```azurecli-interactive 
az provider register --namespace Microsoft.ContainerService 
``` 


## <a name="before-you-begin"></a>Подготовка к работе

Для кластера требуется группа Azure AD. Эта группа необходима в качестве группы администраторов кластера для предоставления разрешений администратора кластера. Вы можете использовать существующую группу Azure AD или создать новую. Запишите идентификатор объекта вашей группы Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Чтобы создать новую группу Azure AD для администраторов кластера, используйте следующую команду:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Создание кластера AKS с включенной службой Azure AD

Создайте кластер AKS, используя следующие команды интерфейса командной строки.

Создайте группу ресурсов Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Создание кластера AKS и включение административного доступа для вашей группы Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Успешное создание кластера Azure AD, управляемого AKS, содержит следующий раздел в тексте ответа.
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

После создания кластера можно приступить к его созданию.

## <a name="access-an-azure-ad-enabled-cluster"></a>Доступ к кластеру с поддержкой Azure AD

Для выполнения следующих действий потребуется встроенная роль [пользователя кластера службы Kubernetes Azure](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

Получите учетные данные пользователя для доступа к кластеру:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Следуйте инструкциям по входу.

Используйте команду kubectl Get Nodes, чтобы просмотреть узлы в кластере:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Настройте [Управление доступом на основе ролей Azure (Azure RBAC)](./azure-ad-rbac.md) , чтобы настроить дополнительные группы безопасности для кластеров.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Устранение проблем с доступом к Azure AD

> [!Important]
> Описанные ниже действия предназначены для обхода обычной проверки подлинности группы Azure AD. Используйте их только в экстренной ситуации.

Если вы постоянно блокируете доступ к действующей группе Azure AD с доступом к кластеру, вы по-прежнему можете получить учетные данные администратора для прямого доступа к кластеру.

Чтобы выполнить эти действия, необходимо иметь доступ к встроенной роли [администратора кластера службы Kubernetes Azure](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Обновление до управляемой AKS интеграции Azure AD

Если кластер использует устаревшую интеграцию Azure AD, вы можете выполнить обновление до управляемой AKS интеграции Azure AD.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Успешная миграция кластера Azure AD, управляемого AKS, содержит следующий раздел в тексте ответа.

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Если вы хотите получить доступ к кластеру, выполните действия, описанные [здесь][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Неинтерактивный вход с помощью кубелогин

Существуют некоторые неинтерактивные сценарии, например конвейеры непрерывной интеграции, которые в настоящее время недоступны в kubectl. Вы можете использовать [`kubelogin`](https://github.com/Azure/kubelogin) для доступа к кластеру с неинтерактивным входом субъекта-службы.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об [интеграции с Azure RBAC для авторизации Kubernetes][azure-rbac-integration]
* Сведения об [интеграции Azure AD с KUBERNETES RBAC][azure-ad-rbac].
* Используйте [кубелогин](https://github.com/Azure/kubelogin) для доступа к функциям для проверки подлинности Azure, которые недоступны в kubectl.
* Узнайте больше о [концепциях идентификации AKS и Kubernetes][aks-concepts-identity].
* Используйте [шаблоны Azure Resource Manager (ARM)][aks-arm-template] для создания кластеров с поддержкой Azure AD, управляемых AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
