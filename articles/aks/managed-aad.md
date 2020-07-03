---
title: Использование Azure AD в службе Kubernetes Azure
description: Узнайте, как использовать Azure AD в службе Kubernetes Azure (AKS).
services: container-service
manager: gwallace
ms.topic: article
ms.date: 05/11/2020
ms.openlocfilehash: 67f5f707ad2971551e3c9623dd5c07ad880afcf2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211150"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Интеграция Azure AD в службу Kubernetes Azure (Предварительная версия)

> [!Note]
> На существующие кластеры AKS с интеграцией AAD (Azure Active Directory) не влияет новый интерфейс AAD, управляемый AKS.

Интеграция Azure AD с AKS, управляемой с помощью AAD, разработана для упрощения процесса интеграции Azure AD, где пользователям ранее требовалось было создавать клиентское приложение, серверное приложение и требовалось предоставить клиенту Azure AD разрешения на чтение каталога. В новой версии поставщик ресурсов AKS управляет клиентскими и серверными приложениями.

## <a name="limitations"></a>Ограничения

* В настоящее время вы не можете обновить существующий кластер AKS AAD в новом интерфейсе AAD, управляемом AKS.

> [!IMPORTANT]
> Функции предварительной версии AKS доступны на уровне самообслуживания. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично охвачены службой поддержки клиентов. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения см. в следующих статьях поддержки:
>
> - [Политики поддержки AKS](support-policies.md)
> - [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="before-you-begin"></a>Перед началом

> [!Important]
> Необходимо использовать Kubectl с минимальной версией 1,18

Необходимо установить следующие ресурсы:

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

Чтобы установить kubectl, используйте следующую команду:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Используйте [эти инструкции](https://kubernetes.io/docs/tasks/tools/install-kubectl/) для других операционных систем.

> [!CAUTION]
> После регистрации функции в подписке в данный момент отмена регистрации этой функции будет невозможна. При включении некоторых функций предварительной версии значения по умолчанию могут использоваться для всех кластеров AKS, созданных позже в подписке. Не включайте предварительные версии функций в производственных подписках. Вместо этого используйте отдельную подписку для тестирования функций предварительной версии и сбора отзывов.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Чтобы состояние отображалось как **зарегистрированное**, может потребоваться несколько минут. Проверить состояние регистрации можно с помощью команды [AZ Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) .

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Когда состояние отображается как зарегистрированное, обновите регистрацию `Microsoft.ContainerService` поставщика ресурсов с помощью команды [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Создание кластера AKS с включенной службой Azure AD

Теперь можно создать кластер AKS с помощью следующих команд интерфейса командной строки.

Сначала создайте группу ресурсов Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Затем создайте кластер AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Приведенная выше команда создает кластер AKS с тремя узлами, но пользователь, создавший кластер, по умолчанию не является членом группы, имеющей доступ к этому кластеру. Этот пользователь должен создать группу Azure AD, добавить себя в качестве члена группы, а затем обновить кластер, как показано ниже. Следуйте инструкциям [здесь](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Создав группу и добавив в нее участников (и других), вы можете обновить кластер с помощью группы Azure AD, выполнив следующую команду:

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Кроме того, если сначала создать группу и добавить участников, можно включить группу Azure AD во время создания с помощью следующей команды:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Успешное создание кластера AAD, управляемого AKS, содержит следующий раздел в тексте ответа.
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

## <a name="access-an-azure-ad-enabled-cluster"></a>Доступ к кластеру с поддержкой Azure AD
Чтобы получить учетные данные администратора для доступа к кластеру, выполните следующие действия.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Теперь используйте команду kubectl Get Nodes для просмотра узлов в кластере:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Чтобы получить учетные данные пользователя для доступа к кластеру, выполните следующие действия.
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Следуйте инструкциям по входу.

Вы получаете: **вам нужно войти на сервер (не санкционированный)**

Пользователь получает сообщение об ошибке, поскольку пользователь не входит в группу, имеющую доступ к кластеру.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об [управлении доступом на основе ролей в Azure AD][azure-ad-rbac].
* Используйте [кубелогин](https://github.com/Azure/kubelogin) для доступа к функциям для проверки подлинности Azure, которые недоступны в kubectl.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
