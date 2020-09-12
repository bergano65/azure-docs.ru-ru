---
title: Включение драйверов интерфейса хранилища контейнера (CSI) в службе Kubernetes Azure (AKS)
description: Узнайте, как включить драйверы интерфейса хранилища контейнера (CSI) для диска Azure и службы файлов Azure в кластере Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: bd5706d20496e1ff00843f761443d183cf7fcae3
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422074"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Включение драйверов интерфейса хранилища контейнеров (CSI) для дисков Azure и службы файлов Azure в службе Kubernetes Azure (AKS) (Предварительная версия)

Интерфейс хранилища контейнера (CSI) является стандартом для предоставления произвольных блоков и систем хранения файлов в контейнерные рабочие нагрузки в Kubernetes. Принимая и используя CSI, служба Kubernetes Azure (AKS) может писать, развертывать и выполнять итерацию подключаемых модулей, предоставляя новые или улучшающие существующие системы хранения в Kubernetes без необходимости касаться основного кода Kubernetes и ожидания циклов выпуска.

Поддержка драйвера хранилища CSI в AKS позволяет использовать встроенные возможности.
- [*Диски Azure*](azure-disk-csi.md) — можно использовать для *создания ресурса Kubernetes* . Диски могут использовать хранилище Azure класса Premium, на основе высокопроизводительных твердотельных накопителей или хранилища Azure уровня "Стандартный", поддерживающие обычные жесткие диски или стандартные твердотельные накопители. Для большинства производственных рабочих нагрузок и рабочих нагрузок разработки следует использовать службу хранилища ценовой категории "Премиум". Диски Azure монтируются как *реадвритеонце*, поэтому доступны только для одного Pod. Для томов хранилища, к которым несколько модулей памяти могут одновременно получить доступ, используйте службу файлов Azure.
- Службы [*файлов Azure*](azure-files-csi.md) можно использовать для подключения общего ресурса SMB 3,0, поддерживаемого учетной записью хранения Azure, к модулям Pod. Служба файлов обеспечивает обмен данными между несколькими узлами и элементами pod. Файлы могут использовать хранилище Azure уровня "Стандартный", поддерживающее обычные жесткие диски или хранилище Azure класса Premium, с помощью высокопроизводительных твердотельных накопителей.

> [!IMPORTANT]
> Начиная с версии 1,21 Kubernetes, Kubernetes будет использовать только драйверы CSI и по умолчанию. Это будущее поддержки хранилища в Kubernetes.
>
> *"Драйверы в дереве"* относятся к текущим драйверам хранилища, которые являются частью основного кода kubernetes, и новыми ДРАЙВЕРами CSI, которые являются подключаемыми модулями.

## <a name="limitations"></a>Ограничения

- Этот компонент можно задать только во время создания кластера.
- Минимальная kubernetes дополнительная версия, поддерживающая драйверы CSI, — v 1.17.
- Во время предварительной версии класс хранения по умолчанию по-прежнему будет иметь тот [же класс хранения в дереве](concepts-storage.md#storage-classes). После того как эта функция общедоступна, класс хранения по умолчанию будет содержать `managed-csi` класс хранения, а классы хранения в дереве будут удалены.
- На первом этапе предварительной версии поддерживается только Azure CLI.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Регистрация `EnableAzureDiskFileCSIDriver` функции предварительной версии

Чтобы создать кластер AKS, который может использовать драйверы CSI для дисков Azure и файлов Azure, необходимо включить `EnableAzureDiskFileCSIDriver` флаг компонента в подписке.

Зарегистрируйте `EnableAzureDiskFileCSIDriver` флаг функции с помощью команды [AZ Feature Register][az-feature-register] , как показано в следующем примере:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Проверьте состояние регистрации с помощью команды [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft.ContainerService* с помощью команды [az provider register][az-provider-register].

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки предварительной версии AKS

Чтобы создать кластер AKS или пул узлов, которые могут использовать драйверы хранилища CSI, необходимо установить последнее расширение CLI для *AKS-Preview* . Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] или установите все доступные обновления с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Создание нового кластера, который может использовать драйверы хранилища CSI

Создайте новый кластер, который может использовать драйверы хранилища CSI для дисков Azure и файлов Azure, используя следующие команды интерфейса командной строки. Используйте `--aks-custom-headers` флаг, чтобы задать `EnableAzureDiskFileCSIDriver` функцию.

Создайте группу ресурсов Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Создайте кластер AKS с поддержкой драйверов хранилища CSI.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Если вы хотите создать кластеры с драйверами хранилища в виде дерева вместо драйверов хранилища CSI, это можно сделать, опустив пользовательский `--aks-custom-headers` параметр.


Проверьте, сколько томов на диске Azure можно подключить к этому узлу, выполнив следующую команду:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Дальнейшие действия

- Использование диска CSI для дисков Azure см. в статье [Использование диска Azure с драйверами CSI](azure-disk-csi.md).
- Использование диска CSI для файлов Azure см. в статье [использование файлов Azure с драйверами CSI](azure-files-csi.md).
- Дополнительные сведения о рекомендациях по хранению см. в статье рекомендации [по хранению и резервному копированию в службе Azure Kubernetes Service (AKS)][operator-best-practices-storage] .

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register