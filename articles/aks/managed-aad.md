---
title: Использование Azure AD в службе Kubernetes Azure
description: Узнайте, как использовать Azure AD в службе Kubernetes Azure (AKS).
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: f22b79cb8a730fb9c28dd1a208ab672473218b79
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105954"
---
# <a name="aks-managed-azure-active-directory-integration-preview"></a>AKS-управляемая интеграция Azure Active Directory (Предварительная версия)

> [!NOTE]
> На существующие кластеры AKS (Azure Kubernetes Service) с интеграцией Azure Active Directory (Azure AD) не влияют новые возможности Azure AD, управляемые AKS.

Интеграция Azure AD с AKS разработана для упрощения процесса интеграции Azure AD, где пользователям ранее требовалось было создавать клиентское приложение, серверное приложение и требовалось клиент Azure AD для предоставления разрешений на чтение каталога. В новой версии поставщик ресурсов AKS управляет клиентскими и серверными приложениями.

## <a name="azure-ad-authentication-overview"></a>Обзор аутентификации Azure AD

Администраторы кластера могут настроить Kubernetes управления доступом на основе ролей (RBAC) на основе удостоверения пользователя или членства в группе каталогов. Кластеры AKS проходят аутентификацию Azure AD с помощью OpenID Connect. OpenID Connect представляет собой уровень идентификации на основе протокола OAuth 2.0. Дополнительные сведения о OpenID Connect Connect см. в [документации по Open ID Connect][open-id-connect].

Дополнительные сведения о потоке интеграции AAD см. в [документации по основным понятиям интеграции Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Ограничения

* Сейчас вы не можете обновить существующий интегрированный кластер AKS Azure AD до нового интерфейса Azure AD, управляемого AKS.

> [!IMPORTANT]
> Функции предварительной версии AKS доступны на уровне самообслуживания. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично охвачены службой поддержки клиентов. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения доступны в следующих статьях поддержки.
>
> - [Политики поддержки AKS](support-policies.md)
> - [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="before-you-begin"></a>Перед началом

* Найдите идентификатор клиента учетной записи Azure, перейдя в портал Azure и выбрав Azure Active Directory > свойства > идентификатор каталога.

> [!Important]
> Необходимо использовать Kubectl с минимальной версией 1,18

Нужно установить следующие ресурсы:

- Azure CLI версии 2.5.1 или более поздней.
- Расширение AKS-Preview 0.4.38
- Kubectl с минимальной версией [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

Чтобы установить или обновить расширение AKS-Preview или более поздней версии, используйте следующие Azure CLI команды:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Чтобы установить kubectl, выполните следующие команды:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Используйте [эти инструкции](https://kubernetes.io/docs/tasks/tools/install-kubectl/) для других операционных систем.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Состояние **Registered** (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list):

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Когда отобразится правильный статус, обновите регистрацию поставщика ресурсов `Microsoft.ContainerService` с помощью команды [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register):

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Создание кластера AKS с включенной службой Azure AD

Создайте кластер AKS, используя следующие команды интерфейса командной строки.

Создайте группу ресурсов Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Вы можете использовать существующую группу Azure AD или создать новую. Вам потребуется идентификатор объекта для группы Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Чтобы создать новую группу Azure AD для администраторов кластера, используйте следующую команду:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

Создание кластера AKS и включение административного доступа для вашей группы Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Успешное создание кластера Azure AD, управляемого AKS, содержит следующий раздел в тексте ответа.
```
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Кластер создается в течение нескольких минут.

## <a name="access-an-azure-ad-enabled-cluster"></a>Доступ к кластеру с поддержкой Azure AD

Для выполнения следующих действий потребуется встроенная роль [пользователя кластера службы Kubernetes Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) .

Получите учетные данные пользователя для доступа к кластеру:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
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
Настройте [Управление доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/aks/azure-ad-rbac) , чтобы настроить дополнительные группы безопасности для кластеров.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Устранение проблем с доступом к Azure AD

> [!Important]
> Описанные ниже действия предназначены для обхода обычной проверки подлинности группы Azure AD. Используйте их только в экстренной ситуации.

Если вы постоянно блокируете доступ к действующей группе Azure AD с доступом к кластеру, вы по-прежнему можете получить учетные данные администратора для прямого доступа к кластеру.

Чтобы выполнить эти действия, необходимо иметь доступ к встроенной роли [администратора кластера службы Kubernetes Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

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
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

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

