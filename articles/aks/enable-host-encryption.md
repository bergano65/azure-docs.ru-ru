---
title: Включение шифрования на основе узла в службе Kubernetes Azure (AKS)
description: Узнайте, как настроить шифрование на основе узла в кластере Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 6f145750ac9158c0184ac9d4d6c303828ee5bdf4
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225362"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Шифрование на основе узла в службе Azure Kubernetes Service (AKS) (Предварительная версия)

При шифровании на основе узла данные, хранящиеся на узле виртуальной машины агента AKS, шифруются в неактивных потоках и передаются в зашифрованном виде в службу хранилища. Это означает, что временные диски шифруются при хранении с помощью ключей, управляемых платформой. Кэш ОС и дисков данных шифруется при хранении с помощью управляемых платформой ключей или ключей, управляемых клиентом, в зависимости от типа шифрования, установленного на этих дисках. По умолчанию при использовании AKS диски операционной системы и данных шифруются при хранении с помощью ключей, управляемых платформой. Это означает, что кэши для этих дисков также шифруются по умолчанию с помощью ключей, управляемых платформой.  Вы можете указать собственные управляемые ключи [, используя собственные ключи (BYOK) с дисками Azure в службе Kubernetes Azure](azure-disk-customer-managed-keys.md). Кэш для этих дисков будет также зашифрован с помощью ключа, указанного на этом шаге.


## <a name="before-you-begin"></a>Перед началом

Этот компонент можно задать только при создании кластера или во время создания пула узлов.

> [!NOTE]
> Шифрование на основе узла доступно в [регионах Azure][supported-regions] , которые поддерживают шифрование на стороне сервера для управляемых дисков Azure и только с конкретными [поддерживаемыми размерами виртуальных машин][supported-sizes].

### <a name="prerequisites"></a>Обязательные условия

- Убедитесь, что `aks-preview` установлено расширение CLI v 0.4.55 или более поздней версии.
- Убедитесь, что в `EncryptionAtHost` разделе включено установлен флаг компонента `Microsoft.Compute` .
- Убедитесь, что в `EncryptionAtHost` разделе включено установлен флаг компонента `Microsoft.ContainerService` .

### <a name="register-encryptionathost--preview-features"></a>Регистрация `EncryptionAtHost` функций предварительной версии

Чтобы создать кластер AKS, использующий шифрование на основе узла, необходимо включить `EncryptionAtHost` флаг компонента в подписке.

Зарегистрируйте `EncryptionAtHost` флаг функции с помощью команды [AZ Feature Register][az-feature-register] , как показано в следующем примере:

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"

az feature register --namespace "Microsoft.ContainerService"  --name "EncryptionAtHost"
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Состояние регистрации можно проверить с помощью команды [az feature list][az-feature-list].

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EncryptionAtHost')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию `Microsoft.ContainerService` `Microsoft.Compute` поставщиков ресурсов и с помощью команды [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.Compute

az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения об отсутствии см. в следующих статьях поддержки:
>
> - [Политики поддержки AKS](support-policies.md)
> - [Часто задаваемые вопросы о поддержке Azure](faq.md)

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Чтобы создать кластер AKS с шифрованием на основе узла, необходимо установить последнее расширение CLI *AKS-Preview* . Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] или проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ограничения

- Можно включить только для новых пулов узлов или новых кластеров.
- Можно включить только в [регионах Azure][supported-regions] , которые поддерживают шифрование управляемых дисков Azure на стороне сервера и только с конкретными [поддерживаемыми размерами виртуальных машин][supported-sizes].
- Требуется кластер AKS и пул узлов на основе масштабируемых наборов виртуальных машин (VMSS) в качестве *типа набора виртуальных машин*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Использовать шифрование на основе узла в новых кластерах (Предварительная версия)

Настройте узлы агента кластера для использования шифрования на основе узла при создании кластера. Используйте `--aks-custom-headers` флаг, чтобы задать `EnableEncryptionAtHost` заголовок.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Если вы хотите создавать кластеры без шифрования на основе узла, это можно сделать, опустив пользовательский `--aks-custom-headers` параметр.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Использовать шифрование на основе узла в существующих кластерах (Предварительная версия)

Вы можете включить шифрование на основе узла в существующих кластерах, добавив новый пул узлов в кластер. Настройте новый пул узлов для использования шифрования на основе узла с помощью `--aks-custom-headers` флага.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Если вы хотите создать новые пулы узлов без функции шифрования на основе узла, это можно сделать, опустив пользовательский `--aks-custom-headers` параметр.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [рекомендациями по безопасности кластера AKS][best-practices-security] Подробнее о [шифровании на основе узла](/azure/virtual-machines/linux/disk-encryption#host-based-encryption).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[supported-regions]: /azure/virtual-machines/linux/disk-encryption#supported-regions
[supported-sizes]: /azure/virtual-machines/linux/disk-encryption#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
