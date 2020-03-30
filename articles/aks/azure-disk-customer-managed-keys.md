---
title: Используйте ключ, управляемый клиентом, для шифрования дисков Azure в службе Azure Kubernetes (AKS)
description: Принесите свои собственные ключи (BYOK) для шифрования дисков AKS OS и Data.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596510"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Принесите свои собственные ключи (BYOK) с дисками Azure в службе Azure Kubernetes (AKS)

Azure Storage шифрует все данные в учетной записи хранения в состоянии покоя. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного контроля над ключами шифрования можно предоставить [управляемые клиентом ключи][customer-managed-keys] для использования в состоянии защиты как для ОС, так и для дисков данных для кластеров AKS.

> [!NOTE]
> Кластеры AKS на базе BYOK Linux и Windows доступны в [регионах Azure,][supported-regions] которые поддерживают шифрование дисков, управляемых Azure.

## <a name="before-you-begin"></a>Перед началом

* Эта статья предполагает, что вы создаете *новый кластер AKS.*

* При использовании Key Vault для шифрования управляемых дисков необходимо включить защиту от мягкого удаления и очистки *для Azure Key Vault.*

* Вам нужна версия Azure CLI 2.0.79 или позже и расширение aks-preview 0.4.26

> [!IMPORTANT]
> Функции предварительного просмотра AKS являются отказом от самообслуживания. Предварительные просмотры предоставляются "как есть" и "по мере возможности" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Анонсы AKS частично покрываются поддержкой клиентов на основе наилучших усилий. Таким образом, эти функции не предназначены для использования в производстве. Для получения дополнительной информации, пожалуйста, ознакомьтесь со следующими статьями поддержки:
>
> * [Политики поддержки AKS](support-policies.md)
> * [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Установите последнее расширение предварительного просмотра AKS CLI

Для использования ключей, управляемых клиентом, требуется версия расширения *CLI aks-preview* 0.4.26 или выше. Установите расширение *aks-preview* Azure CLI с помощью команды [расширения az,][az-extension-add] а затем проверьте наличие доступных обновлений с помощью команды [обновления расширения аз:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Создание экземпляра Убежища ключей Azure

Используйте экземпляр Azure Key Vault для хранения ключей.  Вы можете дополнительно использовать портал Azure для [настройки ключей, управляемых клиентами, с помощью Azure Key Vault][byok-azure-portal]

Создайте новую *группу ресурсов,* затем создайте новый экземпляр *Key Vault* и включите мягкую защиту удаления и очистки.  Убедитесь, что для каждой команды используются одни и те же названия групп и ресурсов.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Создание экземпляра DiskEncryptionSet

Замените *myKeyVaultName* с именем вашего хранилища ключей.  Для выполнения следующих шагов также потребуется *ключ,* хранящийся в Хранилище ключей Azure.  Либо храните существующий ключ в ключе Vault, созданном на предыдущих шагах, либо [создайте новый ключ][key-vault-generate] и замените *myKeyName* ниже с именем ключа.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Предоставить доступ к хранилищу ключей

Используйте DiskEncryptionSet и группы ресурсов, созданные на предыдущих этапах, и предоставите ресурсу DiskEncryptionSet доступ к Хранилищу ключей Azure.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Создание нового кластера AKS и шифрование диска ОС

Создайте **новую группу ресурсов** и кластер AKS, а затем используйте ключ для шифрования диска ОС. Ключи, управляемые клиентами, поддерживаются только в версиях Kubernetes больше, чем 1.17. 

> [!IMPORTANT]
> Убедитесь, что вы создаете новую группу resoruce для вашего кластера AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

При добавлении новых пулов узлов в созданный выше кластер ключа, управляемого клиентом во время создания, используется для шифрования диска ОС.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Шифрование диска кластерных данных AKS

Вы также можете шифровать диски данных AKS с вашими собственными ключами.

> [!IMPORTANT]
> Убедитесь, что у вас есть надлежащие учетные данные AKS. Директор Службы должен иметь доступ к группе ресурсов, в которой развернут набор шифрования дисков. В противном случае вы получите ошибку, предполагающую, что у директора службы нет разрешений.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Создайте файл, называемый **byok-azure-disk.yaml,** содержащий следующую информацию.  Замените myAzureSubscriptionId, myResourceGroup и myDiskEncrptionSetName на ваши значения, и применить yaml.  Обязательно используйте группу ресурсов, в которой развернут ваш DiskEncryptionSet.  Если вы используете оболочку облака Azure, этот файл может быть создан с помощью vi или nano, как если бы работает над виртуальной или физической системой:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Затем запустите развертывание в кластере AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Ограничения

* BYOK в настоящее время доступен только в ГА и Предварительный просмотр в некоторых [регионах Azure][supported-regions]
* Шифрование диска ОС поддерживается версией Kubernetes 1.17 и выше   
* Доступно только в тех регионах, где поддерживается поддержка BYOK
* Шифрование с ключами, управляемыми клиентами, в настоящее время предназначено только для новых кластеров AKS, существующие кластеры не могут быть обновлены
* Кластер AKS с использованием виртуальных наборов шкалы машин не требуется, поддержка виртуальных наборов доступности машин


## <a name="next-steps"></a>Дальнейшие действия

Обзор [рекомендаций по безопасности кластеров AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
