---
title: Использование ключа, управляемого клиентом, для шифрования дисков Azure в службе Kubernetes Azure (AKS)
description: Применяйте собственные ключи (BYOK), чтобы зашифровать диски AKS OS и данных.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/09/2020
ms.author: mlearned
ms.openlocfilehash: 3efb7a6005d862b15beec0f979b67a701a26ba74
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903968"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Перенесите собственные ключи (BYOK) с помощью дисков Azure в службе Kubernetes Azure (AKS)

Служба хранилища Azure шифрует все данные в неактивных учетных записях хранения. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного контроля над ключами шифрования можно предоставить [ключи, управляемые клиентом][customer-managed-keys] , для шифрования дисков ОС и данных для кластеров AKS.

> [!NOTE]
> Поддерживаются кластеры AKS на основе Linux и Windows.

## <a name="before-you-begin"></a>Перед началом работы

* В этой статье предполагается, что вы создаете *новый кластер AKS*.  Кроме того, для хранения ключей шифрования необходимо использовать или создать экземпляр Azure Key Vault.

* Необходимо включить обратимое удаление и очистку для *Azure Key Vault* при использовании Key Vault для шифрования управляемых дисков.

* Требуется Azure CLI версии 2.0.79 или более поздней, а также расширение AKS-Preview 0.4.26

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения об отсутствии см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS](support-policies.md)
> * [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Установка последнего расширения AKS CLI Preview

Для использования управляемых клиентом ключей требуется расширение CLI *AKS-Preview* версии 0.4.26 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance-to-store-your-keys"></a>Создание экземпляра Azure Key Vault для хранения ключей

При необходимости можно использовать портал Azure для [настройки ключей, управляемых клиентом, с помощью Azure Key Vault][byok-azure-portal]

Создайте новую *группу ресурсов*, а затем создайте новый экземпляр *Key Vault* и включите защиту с обратимым удалением и очисткой.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations

# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup-l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Создание экземпляра Дискенкриптионсет

Для выполнения следующих действий потребуется *ключ* , хранящийся в Azure Key Vault.  Сохраните существующий ключ в созданной Key Vault или [Создайте ключ][key-vault-generate] .
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup--source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-resource-access-to-the-key-vault"></a>Предоставление ресурсу Дискенкриптионсет доступа к хранилищу ключей

Используйте Дискенкриптионсет и группы ресурсов, созданные на предыдущих шагах, и предоставьте ресурсу Дискенкриптионсет доступ к Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup--query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup--object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk-with-a-customer-manged-key"></a>Создание нового кластера AKS и шифрование диска операционной системы с помощью управляемого клиентом ключа

Создайте новую группу ресурсов и кластер AKS, а затем используйте ключ для шифрования диска операционной системы.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n $diskEncryptionSetName -g ssecmktesting --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup-l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionsetid diskEncryptionId
```

## <a name="add-a-node-pool-to-an-existing-aks-cluster-and-encrypt-the-os-disk-with-a-customer-managed-key"></a>Добавление пула узлов в существующий кластер AKS и шифрование диска ОС с помощью управляемого клиентом ключа

По умолчанию новые нодепулс не используют зашифрованные диски.  Вы можете добавить новый пул узлов в существующий кластер и зашифровать диск операционной системы с помощью собственного ключа, выполнив следующую команду.

```azurecli-interactive
# Add a nodepool to an existing cluster with BYOK encryption
nodepool add –-cluster-name myAKSCluster -n myNodePoolName -g myResourceGroup --node-osdisk-diskencryptionsetid diskEncryptionId  
```

## <a name="encrypt-your-aks-cluster-data-disk-with-a-customer-managed-key"></a>Шифрование диска данных кластера AKS с помощью управляемого клиентом ключа

Вы также можете зашифровать диски данных AKS с помощью собственных ключей.  Замените myResourceGroup и Мидискенкриптионсетнаме на реальные значения и примените YAML.

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Развертывание примера образа из записи контроля доступа в AKS

Убедитесь, что у вас есть правильные учетные данные AKS

Создайте файл с именем **byok-Azure-Disk. YAML** , содержащий следующие сведения.  Замените myResourceGroup и Мидискенкрптионсетнаме своими значениями.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{subs-id}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Затем запустите это развертывание в кластере AKS:
```azurecli-interactive
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Ограничения

* Шифрование дисков ОС, поддерживаемое с Kubernetes версии 1,17 и выше   
* Доступно только в регионах, где поддерживается BYOK
* Сейчас это только для новых кластеров AKS, существующие кластеры не могут быть обновлены
* Требуется кластер AKS, использующий масштабируемые наборы виртуальных машин, без поддержки групп доступности виртуальных машин


## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с рекомендациями [по безопасности кластера AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
