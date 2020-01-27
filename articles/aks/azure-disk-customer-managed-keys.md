---
title: Использование ключа, управляемого клиентом, для шифрования дисков Azure в службе Kubernetes Azure (AKS)
description: Применяйте собственные ключи (BYOK), чтобы зашифровать диски AKS OS и данных.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 23a5dbf2333ca86c2d51d54bf983b00a71936eec
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547954"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Перенесите собственные ключи (BYOK) с помощью дисков Azure в службе Kubernetes Azure (AKS)

Служба хранилища Azure шифрует все данные в неактивных учетных записях хранения. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного контроля над ключами шифрования можно предоставить [ключи, управляемые клиентом][customer-managed-keys] , для шифрования дисков ОС и данных для кластеров AKS.

> [!NOTE]
> BYOK Linux и кластеры AKS на базе Windows доступны в [регионах Azure][supported-regions] , поддерживающих шифрование на стороне сервера для управляемых дисков Azure.

## <a name="before-you-begin"></a>Перед началом работы

* В этой статье предполагается, что вы создаете *новый кластер AKS*.

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

## <a name="create-an-azure-key-vault-instance"></a>Создание экземпляра Azure Key Vault

Используйте экземпляр Azure Key Vault для хранения ключей.  При необходимости можно использовать портал Azure для [настройки ключей, управляемых клиентом, с помощью Azure Key Vault][byok-azure-portal]

Создайте новую *группу ресурсов*, а затем создайте новый экземпляр *Key Vault* и включите защиту с обратимым удалением и очисткой.  Убедитесь, что для каждой команды используются одни и те же имена регионов и групп ресурсов.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>Создание экземпляра Дискенкриптионсет

Замените *микэйваултнаме* именем хранилища ключей.  Кроме того, потребуется *ключ* , хранящийся в Azure Key Vault, чтобы выполнить следующие действия.  Сохраните существующий ключ в Key Vault, созданном на предыдущих шагах, или [Создайте новый ключ][key-vault-generate] и замените *микэйнаме* ниже именем своего ключа.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Предоставление Дискенкриптионсет доступа к хранилищу ключей

Используйте Дискенкриптионсет и группы ресурсов, созданные на предыдущих шагах, и предоставьте ресурсу Дискенкриптионсет доступ к Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Создание нового кластера AKS и шифрование диска ОС

Создайте **новую группу ресурсов** и кластер AKS, а затем используйте ключ для шифрования диска операционной системы. Ключи, управляемые клиентом, поддерживаются только в Kubernetes версиях более 1,17. 

> [!IMPORTANT]
> Убедитесь, что вы создали новую группу ресурсов для кластера AKS.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

При добавлении новых пулов узлов в кластер, созданный выше, для шифрования диска операционной системы используется управляемый клиентом ключ, указанный во время создания.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Шифрование диска данных кластера AKS

Вы также можете зашифровать диски данных AKS с помощью собственных ключей.

> [!IMPORTANT]
> Убедитесь, что у вас есть правильные учетные данные AKS. Субъекту-службе потребуется доступ участника к группе ресурсов, в которой развернута дискенкриптионсет. В противном случае возникнет ошибка, предлагающая, что у субъекта-службы нет разрешений.

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

Создайте файл с именем **byok-Azure-Disk. YAML** , содержащий следующие сведения.  Замените Мязуресубскриптионид, myResourceGroup и Мидискенкрптионсетнаме своими значениями и примените YAML.  Обязательно используйте группу ресурсов, в которой развернута Дискенкриптионсет.  Если вы используете Azure Cloud Shell, этот файл можно создать с помощью VI или Nano, как при работе в виртуальной или физической системе:

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
Затем запустите это развертывание в кластере AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Ограничения

* BYOK сейчас доступно только в общедоступной версии и в некоторых [регионах Azure][supported-regions] .
* Шифрование дисков ОС, поддерживаемое с Kubernetes версии 1,17 и выше   
* Доступно только в регионах, где поддерживается BYOK
* Шифрование с помощью управляемых клиентом ключей в настоящее время предназначено только для новых кластеров AKS, обновление существующих кластеров невозможно
* Требуется кластер AKS, использующий масштабируемые наборы виртуальных машин, без поддержки групп доступности виртуальных машин


## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с рекомендациями [по безопасности кластера AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
