---
title: Включить шифрование дисков для кластеров Azure Service Fabric Windows | Документация Майкрософт
description: В этой статье описывается, как включить шифрование дисков в узлах кластера Service Fabric в Azure с помощью Azure Resource Manager и Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66119162"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Включение шифрования дисков в узлах кластера Service Fabric на платформе Windows 
> [!div class="op_single_selector"]
> * [Шифрование дисков для Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Шифрование дисков для Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Выполните следующие действия, чтобы включить шифрование дисков в узлах кластера Service Fabric для Windows. Вам нужно будет сделать это для каждого типа узлов или масштабируемых наборов виртуальных машин. Для шифрования узлов мы будем использовать возможности шифрования дисков Azure для масштабируемых наборов виртуальных машин.

В этом руководстве описываются следующие процедуры.

* Основные понятия, которые необходимо учитывать, чтобы включить шифрование дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Windows.
* Предварительные требования, которые необходимо выполнить перед включением шифрования дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Windows.
* Шаги, которые необходимо выполнить, чтобы включить шифрование дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия
* **Автоматическая регистрация**. Для использования предварительной версии службы шифрования дисков масштабируемого набора виртуальных машин требуется автоматическая регистрация.
* Вы можете самостоятельно зарегистрировать свою подписку, выполнив следующие шаги: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Подождите около 10 минут, пока состояние не изменится на "Зарегистрировано". Вы можете проверить состояние, выполнив следующую команду: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault**. Создайте новое хранилище ключей в тех же подписке и регионе, что и масштабируемый набор, и настройте политику доступа EnabledForDiskEncryption в хранилище ключей с помощью командлета PowerShell. Вы также можете установить политику, используя пользовательский интерфейс Key Vault на портале Azure: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Установите последнюю версию [Azure CLI](/cli/azure/install-azure-cli) , который содержит новые команды шифрования.
* Установите последнюю версию [пакета Azure SDK из выпуска Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Ниже приведены командлеты ADE масштабируемого набора виртуальных машин для включения ([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) шифрования, получения ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) состояния шифрования и удаления ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) шифрования в экземпляре масштабируемого набора.

| Команда | Version |  `Source`  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 или более поздней версии | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 или более поздней версии | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 или более поздней версии | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 или более поздней версии | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 или более поздней версии | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 или более поздней версии | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Поддерживаемые сценарии для шифрования дисков
* Шифрование масштабируемого набора виртуальных машин поддерживается только для масштабируемых наборов, созданных с помощью управляемых дисков, и не поддерживается для масштабируемых наборов на основе собственных (или неуправляемых) дисков.
* Шифрование к масштабируемым наборам виртуальных машин поддерживается для томов операционной системы и данных для масштабируемого набора виртуальных машин Windows. Отключение шифрования поддерживается для томов операционной системы и данных в масштабируемых наборах Windows.
* Масштабируемый набор виртуальных машин пересоздания образа виртуальной Машины и операции обновления не поддерживаются в текущей предварительной версии.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Создание нового кластера и включение шифрования дисков

Используйте следующие команды для создания кластера, а также включить шифрование дисков, с помощью шаблона Azure Resource Manager и самозаверяющий сертификат.

### <a name="sign-in-to-azure"></a>Вход в Azure 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Использование собственного пользовательского шаблона 

Если необходимо создать пользовательский шаблон в соответствии с потребностями, настоятельно рекомендуется начать с одного из шаблонов, доступных в [примерах шаблонов Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). [Настройте шаблон кластера][customize-your-cluster-template], следуя инструкциям и учитывая пояснения в разделе ниже.

Если у вас уже есть пользовательский шаблон, дважды проверьте, что все три параметра, связанные с сертификатом, в шаблоне и файле параметров имеют указанные ниже имена, а значения равны нулю.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```azurecli

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Развертывание приложения в кластере Service Fabric на платформе Windows
Выполните шаги и рекомендации по [развертыванию приложения в кластере](service-fabric-deploy-remove-applications.md).


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Включите шифрование диска для масштабируемого набора виртуальных машин кластера Service Fabric, созданного выше.
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Проверьте, включено ли шифрование дисков для масштабируемых наборов виртуальных машин Windows.
Получите состояние всего масштабируемого набора виртуальных машин или любого экземпляра в масштабируемом наборе. Ознакомьтесь с командами ниже.
Кроме того пользователь может войти в виртуальную Машину в масштабируемом наборе и убедитесь, что диски шифруются

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Отключение шифрования дисков для масштабируемого набора виртуальных машин кластера Service Fabric 
Отключение шифрования дисков распространяется на масштабируемый набор, а не на экземпляр. 

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Дальнейшие действия
Теперь у вас есть безопасный кластер, в котором можно включить или отключить шифрование дисков для масштабируемого набора виртуальных машин кластера Service Fabric. [Enable Disk encryption for service fabric Linux cluster nodes](service-fabric-enable-azure-disk-encryption-linux.md) (Включение шифрования дисков в кластерах Service Fabric на платформе Linux) 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
