---
title: Использование управляемых удостоверений в службе Kubernetes Azure
description: Узнайте, как использовать управляемые удостоверения в службе Kubernetes Azure (AKS).
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 20e255958cbd90aaddf060e42d7627c1e1ebec88
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371466"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Использование управляемых удостоверений в службе Kubernetes Azure

В настоящее время кластер Azure Kubernetes Service (AKS) (в частности, поставщик облачных служб Kubernetes) требует удостоверения для создания дополнительных ресурсов, таких как подсистемы балансировки нагрузки и управляемые диски в Azure. Это может быть *управляемое удостоверение* или субъект- *Служба*. При использовании [субъекта-службы](kubernetes-service-principal.md)необходимо предоставить один или AKS от вашего имени. Если вы используете управляемое удостоверение, оно будет создано автоматически AKS. Кластеры, использующие субъекты-службы, в конечном итоге достигают состояния, в котором необходимо обновить субъект-службу, чтобы обеспечить работу кластера. Управление субъектами-службами повышает сложность, поэтому вместо этого проще использовать управляемые удостоверения. Те же требования к разрешениям применяются и к субъектам-службам, и к управляемым удостоверениям.

*Управляемые удостоверения* по сути являются оболочкой для субъектов-служб и упрощают их управление. Поворот учетных данных для MI происходит автоматически каждые 46 дней в соответствии с Azure Active Directory по умолчанию. AKS использует управляемые системой и назначенные пользователем типы удостоверений. В настоящее время эти удостоверения неизменяемы. Дополнительные сведения см. в статье об [управляемых удостоверениях для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Перед началом

Необходимо установить следующий ресурс:

- Azure CLI версии 2.8.0 или более поздней.

## <a name="limitations"></a>Ограничения

* Кластеры AKS с управляемыми удостоверениями могут быть включены только во время создания кластера.
* Существующие кластеры AKS нельзя перенести в управляемые удостоверения.
* Во время операций **обновления** кластера управляемое удостоверение временно недоступно.
* Клиенты, перемещая и миграция управляемых удостоверений, поддерживают кластеры.
* Если кластер `aad-pod-identity` включен, модули NMI, управляемые узлами, изменяют узлы iptables для перехвата вызовов к конечной точке метаданных экземпляра Azure. Такая конфигурация означает, что любой запрос, сделанный в конечной точке метаданных, перехватывается функцией NMI, даже если Pod не используется `aad-pod-identity` . Азуреподидентитексцептион CRD можно настроить так, чтобы сообщать `aad-pod-identity` о том, что любые запросы к конечной точке метаданных, созданные из Pod, совпадающие с метками, определенными в CRD, должны быть прокси-серверами без обработки в NMI. Системные модули с `kubernetes.azure.com/managedby: aks` меткой в пространстве имен _KUBE-System_ должны быть исключены в `aad-pod-identity` с помощью настройки азуреподидентитексцептион CRD. Дополнительные сведения см. [в разделе Отключение AAD-Pod-Identity для конкретного Pod или приложения](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Чтобы настроить исключение, установите [YAML-исключение MIC](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Сводка по управляемым удостоверениям

AKS использует несколько управляемых удостоверений для встроенных служб и надстроек.

| Идентификация                       | Имя    | Вариант использования | Разрешения по умолчанию | Приведите свое удостоверение
|----------------------------|-----------|----------|
| Уровень управления | не видно | Используется AKS для управляемых сетевых ресурсов, включая подсистемы балансировки нагрузки и управляемые общедоступные IP-адреса AKS. | Роль участника для группы ресурсов узла | Preview (Предварительный просмотр)
| kubelet | Имя кластера AKS — ажентпул | Проверка подлинности с помощью реестра контейнеров Azure (запись контроля доступа) | НД (для kubernetes v 1,15 доллара +) | Сейчас не поддерживается
| Дополнительно | азуренпм | Удостоверение не требуется | Н/Д | Нет
| Дополнительно | Мониторинг сети Азурекни | Удостоверение не требуется | Н/Д | Нет
| Дополнительно | азуреполици (привратник) | Удостоверение не требуется | Н/Д | Нет
| Дополнительно | азуреполици | Удостоверение не требуется | Н/Д | Нет
| Дополнительно | Calico | Удостоверение не требуется | Н/Д | Нет
| Дополнительно | Панель мониторинга | Удостоверение не требуется | Н/Д | Нет
| Дополнительно | хттпаппликатионраутинг | Управляет необходимыми сетевыми ресурсами | Роль читателя для группы ресурсов Node, роль участника для зоны DNS | Нет
| Дополнительно | Шлюз приложений входящего трафика | Управляет необходимыми сетевыми ресурсами| Роль участника для группы ресурсов узла | Нет
| Дополнительно | omsagent | Используется для отправки метрик AKS в Azure Monitor | Роль издателя метрик мониторинга | Нет
| Дополнительно | Virtual-Node (АЦиконнектор) | Управляет необходимыми сетевыми ресурсами для службы "экземпляры контейнеров Azure" (ACI) | Роль участника для группы ресурсов узла | Нет
| Проект OSS | AAD-Pod-Identity | Позволяет приложениям безопасно получать доступ к облачным ресурсам с помощью Azure Active Directory (AAD) | Н/Д | Действия по предоставлению разрешения в https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Создание кластера AKS с управляемыми удостоверениями

Теперь можно создать кластер AKS с управляемыми удостоверениями с помощью следующих команд интерфейса командной строки.

Сначала создайте группу ресурсов Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Затем создайте кластер AKS:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Успешное создание кластера с помощью управляемых удостоверений содержит следующие сведения о профиле субъекта-службы:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Используйте следующую команду, чтобы запросить ObjectID управляемого удостоверения плоскости управления:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Результат должен выглядеть следующим образом:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

После создания кластера можно развернуть рабочие нагрузки приложений в новом кластере и взаимодействовать с ним так же, как и с кластерами AKS на основе субъектов-служб.

> [!NOTE]
> Для создания и использования собственной виртуальной сети, статического IP-адреса или подключенного диска Azure, где ресурсы выходят за пределы группы ресурсов рабочего узла, используйте PrincipalID управляемого удостоверения, назначенного системой кластера, для выполнения назначения ролей. Дополнительные сведения о назначении ролей см. в статье [Делегирование доступа к другим ресурсам Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Предоставление разрешений на управляемое удостоверение кластера, используемое поставщиком облачных служб Azure, может занять 60 минут для заполнения.

Наконец, получите учетные данные для доступа к кластеру:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>Приведите собственную плоскость управления (Предварительная версия)
Настраиваемое удостоверение плоскости управления позволяет предоставлять доступ к существующему удостоверению до создания кластера. Это позволяет выполнять такие сценарии, как использование настраиваемой виртуальной сети или Аутбаундтипе UDR с управляемым удостоверением.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Нужно установить следующие ресурсы:
- Azure CLI версии 2.9.0 или более поздней.
- Расширение AKS-Preview 0.4.57

Ограничения для работы с собственной плоскостью управления (Предварительная версия):
* В настоящее время Azure для государственных организаций не поддерживается.
* В настоящее время в Azure Китая 21Vianet не поддерживается.

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

Состояние **Registered** (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true):

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

Когда отобразится правильный статус, обновите регистрацию поставщика ресурсов `Microsoft.ContainerService` с помощью команды [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true):

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Если у вас еще нет управляемого удостоверения, вы можете создать его, например, с помощью команды [AZ Identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Результат должен выглядеть следующим образом:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Если управляемое удостоверение является частью подписки, можно выполнить запрос с помощью [команды AZ Identity CLI][az-identity-list] .  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Теперь для создания кластера с существующим удостоверением можно использовать следующую команду:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Успешное создание кластера с помощью управляемых удостоверений содержит следующие сведения о профиле Userassignedidentities класса:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>Дальнейшие шаги
* Используйте [шаблоны Azure Resource Manager (ARM) ][aks-arm-template] для создания кластеров с поддержкой управляемого удостоверения.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true
