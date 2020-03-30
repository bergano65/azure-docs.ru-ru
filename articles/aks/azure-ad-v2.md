---
title: Использование Azure AD в службе Azure Kubernetes
description: Узнайте, как использовать Azure AD в службе Azure Kubernetes (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 430df504c677b005f5ff5e7fdd9346aed3e168af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294459"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Интеграция Azure AD в службу Azure Kubernetes (Предварительный просмотр)

> [!Note]
> Существующие кластеры AKS v1 с интеграцией AD не зависят от нового опыта AKS v2.

Интеграция Azure AD с AKS v2 предназначена для упрощения интеграции Azure AD с помощью AKS v1, где пользователи должны были создать клиентское приложение, серверное приложение и потребовали от арендатора Azure AD предоставить разрешения на чтение каталогов. В новой версии поставщик ресурсов AKS управляет приложениями для клиента и сервера.

## <a name="limitations"></a>Ограничения

* В настоящее время нельзя обновить существующий кластер Azure AD с поддержкой AKS v1 до опыта v2.

> [!IMPORTANT]
> Функции предварительного просмотра AKS доступны на основе самообслуживания. Предварительные просмотры предоставляются "как есть" и "по мере возможности", и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные просмотры AKS частично покрываются поддержкой клиентов на основе наилучших усилий. Таким образом, эти функции не предназначены для использования в производстве. Для получения дополнительной информации смотрите следующие статьи поддержки:
>
> - [Политики поддержки AKS](support-policies.md)
> - [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="before-you-begin"></a>Перед началом

Необходимо установить следующие ресурсы:

- Azure CLI, версия 2.2.0 или позже
- Расширение aks-preview 0.4.38
- Кубектль с минимальной версией [1.18 бета-версии](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Для установки/обновления расширения aks-preview или позже используйте следующие команды Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Для установки kubectl используйте следующие
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/darwin/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

Используйте [эти инструкции](https://kubernetes.io/docs/tasks/tools/install-kubectl/) для других операционных систем.

> [!CAUTION]
> После регистрации функции по подписке вы не можете в настоящее время отменить регистрацию этой функции. При вменить некоторые функции предварительного просмотра по умолчанию могут использоваться для всех кластеров AKS, созданных впоследствии в подписке. Не включайте функции предварительного просмотра в производственных подписках. Вместо этого используйте отдельную подписку для тестирования функций предварительного просмотра и сбора отзывов.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Это может занять несколько минут для статуса, чтобы показать, как **зарегистрировано**. Проверить статус регистрации можно с помощью команды [списка функций az:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Когда статус отображается зарегистрированным, освежите регистрацию поставщика `Microsoft.ContainerService` ресурсов с помощью команды [регистра аз-провайдера:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Создание кластера AKS с включенным Azure AD

Теперь можно создать кластер AKS, используя следующие команды CLI.

Во-первых, создайте группу ресурсов Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Затем создайте кластер AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Вышеупомянутая команда создает кластер AKS с тремя узлами, но пользователь, создавший кластер по умолчанию, не является членом группы, которая имеет доступ к этому кластеру. Пользователю необходимо создать группу Azure AD, добавить себя в качестве члена группы, а затем обновить кластер, как показано ниже. Следуйте инструкциям [здесь](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

После создания группы и добавления себя (и других) в качестве участника можно обновить кластер с группой Azure AD, используя следующую команду

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
Кроме того, если сначала создать группу и добавить участников, можно включить группу Azure AD во время создания следующей команды,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

Успешное создание кластера Azure AD v2 имеет следующий раздел в органе реагирования
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Кластер создается в течение нескольких минут.

## <a name="access-an-azure-ad-enabled-cluster"></a>Доступ к кластеру Azure AD с поддержкой Azure
Чтобы получить учетные данные от аминиста для доступа к кластеру:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Теперь используйте команду kubectl для просмотра узлов в кластере:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Чтобы получить учетные данные пользователей для доступа к кластеру:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Следуйте инструкциям, чтобы войти.

Вы получаете: **Вы должны быть зарегистрированы на сервер (несанкционированный)**

Пользователь выше получает ошибку, потому что пользователь не является частью группы, которая имеет доступ к кластеру.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [элементах управления доступом на основе роли Azure AD.][azure-ad-rbac]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md