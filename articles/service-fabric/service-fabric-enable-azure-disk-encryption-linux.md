---
title: Включить шифрование дисков для кластеров Azure Service Fabric для Linux | Документация Майкрософт
description: В этой статье описывается, как включить шифрование дисков для узлов кластера Azure Service Fabric в Linux с помощью Azure Resource Manager и хранилища ключей Azure.
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
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258764"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Включить шифрование дисков для узлов кластера Azure Service Fabric в Linux 
> [!div class="op_single_selector"]
> * [Шифрование дисков для Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Шифрование дисков для Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

В этом руководстве вы узнаете, как включить шифрование дисков на узлах кластера Azure Service Fabric в Linux. Вам потребуется выполнить следующие действия для каждого из типов узлов и масштабируемые наборы виртуальных машин. Для шифрования узлов, мы будем использовать функцию шифрования диска Azure в наборах масштабирования виртуальных машин.

В этом руководстве описываются следующие темы:

* Основные понятия, которые следует учитывать при задает включения шифрования диска в масштабируемом виртуальных машин кластера Service Fabric в Linux.
* Действия, чтобы следовать перед включением шифрования дисков в Service Fabric узлы кластера на Linux.
* Действия, чтобы следовать, чтобы включить шифрование дисков на узлах кластера Service Fabric в Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

 **Самостоятельной регистрации**

Обзор шифрования диска для масштабируемого набора виртуальных машин требуется самостоятельной регистрации. Выполните следующие действия.

1. Выполните следующую команду: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Подождите около 10 минут, пока не находится в состоянии *зарегистрированные*. Вы можете проверить состояние, выполнив следующую команду:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Хранилище ключей Azure**

1. Создайте хранилище ключей в тех же подписке и регионе, что и масштабируемый набор. Затем выберите **EnabledForDiskEncryption** политику для хранилища ключей доступа с помощью командлета PowerShell. Можно также задать политику с помощью пользовательского интерфейса хранилища ключей на портале Azure, выполнив следующую команду:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Установите последнюю версию [Azure CLI](/cli/azure/install-azure-cli), который содержит новые команды шифрования.

3. Установите последнюю версию [пакета SDK для Azure с помощью Azure PowerShell](https://github.com/Azure/azure-powershell/releases) выпуска. Ниже приведены масштабируемого набора виртуальных машин командлеты шифрования дисков Azure, чтобы включить ([задать](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) шифрования, получения ([получить](/powershell/module/az.compute/get-azvmssvmdiskencryption)) состояние шифрования, а также удалите ([отключить](/powershell/module/az.compute/disable-azvmssdiskencryption)) экземпляр набора шифрования на шкале.


| Команда | Version |  source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 или более поздней версии | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 или более поздней версии | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 или более поздней версии | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 или более поздней версии | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 или более поздней версии | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 или более поздней версии | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Поддерживаемые сценарии для шифрования дисков
* Шифрование для масштабируемых наборов виртуальных машин поддерживается только для масштабируемых наборов, созданных с помощью управляемых дисков. Оно не поддерживается для масштабируемых наборов на основе собственных (или неуправляемых) дисков.
* При отключении шифрования и шифрования поддерживаются для томов операционной системы и данных в масштабируемых наборах виртуальных машин в Linux. 
* Операции пересоздания образа и обновления виртуальной машины (VM), для масштабируемых наборов виртуальных машин не поддерживаются в текущей предварительной версии.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Создать новый кластер и включить шифрование дисков

Используйте следующие команды для создания кластера и включить шифрование дисков с помощью шаблона Azure Resource Manager и самозаверяющий сертификат.

### <a name="sign-in-to-azure"></a>Вход в Azure  

Войдите в систему с помощью следующих команд:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Использование собственного пользовательского шаблона 

Если вам нужно создать пользовательский шаблон, мы настоятельно рекомендуем использовать один из шаблонов на [примеры шаблонов для создания кластера Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) страницы. 

Если у вас уже есть пользовательский шаблон, дважды проверьте, что все три связанные с сертификатом параметра в шаблоне и файле параметров имеют следующие имена. Также убедитесь, что значения являются null следующим образом:

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

Так как для масштабируемых наборов виртуальных машин в Linux поддерживается только шифрование дисков данных, необходимо добавить диск данных с помощью шаблона Resource Manager. Обновите шаблон для подготовки диска данных следующим образом:

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


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Вот эквивалентные команды интерфейса командной строки. Измените значения в инструкциях объявления на соответствующие значения. Интерфейс командной строки поддерживает все параметры, поддерживаемые предыдущей команды PowerShell.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Подключить диск данных к экземпляру Linux
Прежде чем продолжить с шифрованием на масштабируемый набор виртуальных машин, убедитесь, что правильно подключен диск добавленных данных. Войдите виртуальной Машине кластера Linux и выполните **LSBLK** команды. Выходные данные должны представлять этот диск данных, добавленных в **точки подключения** столбца.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Развертывание приложения в кластере Service Fabric в Linux
Чтобы развернуть приложение в кластере, выполните действия и инструкции см. на [краткое руководство: Развертывание контейнеров Linux в Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Включить шифрование дисков для масштабируемых наборов виртуальных машин, созданную ранее
Чтобы включить шифрование дисков для масштабирования виртуальных машин задает созданный просмотрите предыдущие шаги, выполните следующие команды:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Проверить, если включено шифрование диска для виртуальной машины масштабируемого набора Linux
Чтобы получить состояние всей масштабируемый набор виртуальных машин или любого экземпляра в масштабируемом наборе, выполните следующие команды.
Кроме того, можно войти в кластер Linux виртуальной Машины и запустить **LSBLK** команды. Выходные данные должны представлять диск данных, добавленных в **точки подключения** столбца и **тип** столбца следует прочитать *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Отключить шифрование дисков виртуальной машины масштабируемого набора в кластере Service Fabric
Отключите шифрование дисков виртуальной машины масштабируемого набора, выполнив следующие команды. Обратите внимание на то, что отключение шифрования диска применяется ко всей виртуальной машины масштабируемого набора и не каждого отдельного экземпляра.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Дальнейшие действия
На этом этапе необходимо иметь защищенного кластера и знаете, как включить и отключить шифрование дисков для узлов кластера Service Fabric и масштабируемые наборы виртуальных машин. Одинаковые рекомендации на узлах кластера Service Fabric в Linux см. в разделе [шифрования диска для Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
