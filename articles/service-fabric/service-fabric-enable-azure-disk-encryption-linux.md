---
title: Включить шифрование дисков для кластеров Azure Service Fabric для Linux | Документация Майкрософт
description: В этой статье описывается, как включить шифрование дисков в масштабируемом наборе кластера Service Fabric в Azure с помощью Azure Resource Manager и Azure Key Vault.
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
ms.openlocfilehash: 3de26efb74b9349282d36beb94e8a2a269227fbf
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488315"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Включение шифрования дисков в узлах кластера Service Fabric на платформе Linux 
> [!div class="op_single_selector"]
> * [Шифрование дисков для Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Шифрование дисков для Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Выполните следующие действия, чтобы включить шифрование дисков в узлах кластера Service Fabric для Linux. Вам нужно будет сделать это для каждого типа узлов или масштабируемых наборов виртуальных машин. Для шифрования узлов мы будем использовать возможности шифрования дисков Azure для масштабируемых наборов виртуальных машин.

В этом руководстве описываются следующие процедуры.

* Основные понятия, которые необходимо учитывать, чтобы включить шифрование дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Linux.
* Предварительные требования, которые необходимо выполнить перед включением шифрования дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Linux.
* Шаги, которые необходимо выполнить, чтобы включить шифрование дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Linux.


## <a name="prerequisites"></a>Технические условия

* **Автоматическая регистрация**. Для использования предварительной версии службы шифрования дисков масштабируемого набора виртуальных машин требуется автоматическая регистрация.
* Вы можете самостоятельно зарегистрировать свою подписку, выполнив следующие шаги: 
```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Подождите около 10 минут, пока состояние не изменится на "Зарегистрировано". Вы можете проверить состояние, выполнив следующую команду: 
```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault**. Создайте Key Vault в тех же подписке и регионе, что и масштабируемый набор виртуальных машин, и настройте политику доступа EnabledForDiskEncryption в Key Vault с помощью командлета PowerShell. Вы также можете установить политику, используя пользовательский интерфейс Key Vault на портале Azure: 
```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest), в которой есть новые команды шифрования.
* Установите последнюю версию [пакета Azure SDK из выпуска Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Ниже приведены масштабируемого набора виртуальных машин командлеты шифрования дисков AZURE, чтобы включить ([задать](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) шифрования, получения ([получить](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) состояние шифрования и remove ([отключить](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) шифрования на шкале установите экземпляр. 

| Команда | Version (версия) |  Источник  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 или выше | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 или выше | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 или выше | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 или выше | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 или выше | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 или выше | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Поддерживаемые сценарии для шифрования дисков
* Шифрование масштабируемого набора виртуальных машин поддерживается только для масштабируемых наборов, созданных с помощью управляемых дисков, и не поддерживается для масштабируемых наборов на основе собственных (или неуправляемых) дисков.
* Шифрование масштабируемого набора виртуальных машин поддерживается для тома данных для масштабируемого набора виртуальных машин Linux. Шифрование дисков ОС НЕ поддерживается в текущей предварительной версии для Linux.
* Масштабируемый набор виртуальных машин пересоздания образа виртуальной Машины и операции обновления не поддерживаются в текущей предварительной версии.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Создание кластера Linux и включение шифрования дисков

Используйте следующие команды для создания кластера, а также включить шифрование дисков, с помощью шаблона Azure Resource Manager и самозаверяющий сертификат.

### <a name="sign-in-to-azure"></a>Вход в Azure  

```powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Использование собственного пользовательского шаблона 

Если необходимо создать пользовательский шаблон в соответствии с потребностями, настоятельно рекомендуется начать с одного из шаблонов, доступных в [примерах шаблонов Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) для кластера Linux. 

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

Так как для масштабируемого набора виртуальных машин Linux поддерживается только шифрование диска данных, необходимо добавить диск данных с помощью шаблона Azure Resource Manager. Обновите шаблон для подготовки диска данных, как показано ниже:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Это можно также сделать с помощью следующей команды CLI. Замените значения в инструкциях объявления на соответствующие. Интерфейс командной строки (CLI) поддерживает все параметры, которые поддерживает вышеуказанная команда PowerShell.

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

#### <a name="linux-data-disk-mounting"></a>Подключение диска данных Linux
Прежде чем продолжить с шифрованием масштабируемого набора виртуальных машин Linux, нам нужно убедить, что добавленный диск данных правильно подключен. Войдите в виртуальную Машину Linux кластера и выполните команду LSBLK. В выходных данных должно быть показано, что диск данных добавлен в столбце точки подключения.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Развертывание приложения в кластере Service Fabric на платформе Linux
Выполните шаги и рекомендации по [развертыванию приложения в кластере](service-fabric-quickstart-containers-linux.md).


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Включите шифрование диска для масштабируемого набора виртуальных машин кластера Service Fabric на платформе Linux, созданного выше.
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Проверьте, включено ли шифрование дисков для масштабируемых наборов виртуальных машин Linux.
Получите состояние всего масштабируемого набора виртуальных машин или любой виртуальной машины экземпляра в масштабируемом наборе. Ознакомьтесь с командами ниже.
Кроме того пользователь может войти в виртуальную Машину Linux кластера и выполните команду LSBLK. В выходных данных должно быть показано, что диск данных добавлен в столбце точки подключения и в качестве типа столбца добавленного диска данных указано значение Crypt.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Отключение шифрования дисков для масштабируемого набора виртуальных машин кластера Service Fabric 
Отключение шифрования дисков распространяется на масштабируемый набор, а не на экземпляр. 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Дальнейшие действия
Теперь у вас есть безопасный кластер, в котором можно включить или отключить шифрование дисков для масштабируемого набора виртуальных машин кластера Service Fabric на платформе Linux. Перейдите к [шифрованию дисков Azure для Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
