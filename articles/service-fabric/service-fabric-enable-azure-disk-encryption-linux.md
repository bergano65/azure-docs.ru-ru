---
title: Включить шифрование дисков для кластеров Linux
description: В этой статье описывается, как включить шифрование дисков для кластерных узлов Azure Service Fabric в Linux с помощью azure Resource Manager и Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252811"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Включить шифрование дисков для кластерных узлов Azure Service Fabric в Linux 
> [!div class="op_single_selector"]
> * [Шифрование дисков для Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Шифрование дисков для Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

В этом уроке вы узнаете, как включить шифрование дисков на кластерных узлах Azure Service Fabric в Linux. Вам нужно будет следовать этим шагам для каждого из типов узлов и виртуальных наборов масштабов машины. Для шифрования узлов мы используем возможность шифрования azure Disk в наборах виртуальных машин.

Руководство охватывает следующие темы:

* Ключевые концепции, о которой следует знать при включении шифрования диска в кластере SinainFabric virtual machine scale sets в Linux.
* Шаги, которым следует следовать, прежде чем включить шифрование диска в кластерных узлах Service Fabric в Linux.
* Шаги, которым следует следовать, чтобы включить шифрование диска на кластерных узлах Service Fabric в Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

 **Саморегистрация**

Предварительный просмотр шифрования диска для набора виртуальной шкалы машины требует саморегистрации. Выполните указанные ниже действия.

1. Выполните следующую команду: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Подождите около 10 минут, пока статус читает *Зарегистрировано*. Проверить состояние можно, запустив следующую команду:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Убежище ключей Azure**

1. Создайте хранилище ключей в тех же подписке и регионе, что и масштабируемый набор. Затем выберите политику доступа **EnabledForDiskEncryption** на хранилище ключей, используя свой смдлет PowerShell. Вы также можете настроить политику, используя ui Key Vault на портале Azure со следующей командой:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Установите последнюю версию [Azure CLI,](/cli/azure/install-azure-cli)которая имеет новые команды шифрования.

3. Установите последнюю версию [SDK Azure из релиза Azure PowerShell.](https://github.com/Azure/azure-powershell/releases) Ниже приведены виртуальные машины масштаб набора Azure Disk шифрования cmdlets для включения[(набор)](/powershell/module/az.compute/set-azvmssdiskencryptionextension)шифрования, получить ([получить](/powershell/module/az.compute/get-azvmssvmdiskencryption)) статус шифрования, и удалить ([отключить](/powershell/module/az.compute/disable-azvmssdiskencryption)) шифрования на шкале набора экземпляра.


| Команда | Версия |  Источник  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryption   | 1.0.0 или позже | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 или позже | Az.Compute |
| Отключить-AzVmssDiskШифрование   | 1.0.0 или позже | Az.Compute |
| Get-AzVmssDiskШифрование   | 1.0.0 или позже | Az.Compute |
| Get-AzVmssVMDiskШифрование   | 1.0.0 или позже | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 или позже | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Поддерживаемые сценарии для шифрования дисков
* Шифрование для виртуальных наборов масштабов машины поддерживается только для наборов масштабов, созданных с управляемыми дисками. Оно не поддерживается для масштабируемых наборов на основе собственных (или неуправляемых) дисков.
* Как шифрование, так и отключить шифрование поддерживаются для ОС и объемов данных в наборах виртуальных машин в Linux. 
* Виртуальный машинный (VM) переизбыток и обновление операций для виртуальных наборов масштабов машины не поддерживается в текущем предварительном просмотре.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Создание нового кластера и включение шифрования диска

Используйте следующие команды для создания кластера и включения шифрования диска с помощью шаблона Azure Resource Manager и сертификата, подписанного самостоятельно.

### <a name="sign-in-to-azure"></a>Вход в Azure  

Вопиющий со следующими командами:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Использование собственного пользовательского шаблона 

Если вам необходимо создать пользовательский шаблон, мы настоятельно рекомендуем использовать один из шаблонов на странице [шаблонов создания шаблонов создания кластеров Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 

Если у вас уже есть пользовательский шаблон, проверьте, что все три параметра, связанных с сертификатом, в шаблоне и файле параметра названы следующим образом. Также убедитесь, что значения являются нулевыми следующим образом:

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

Поскольку только шифрование диска данных поддерживается для виртуальных наборов масштабов машин в Linux, необходимо добавить диск данных с помощью шаблона Resource Manager. Обновление шаблона для предоставления диска данных следующим образом:

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

Вот эквивалентная команда CLI. Измените значения в деклараторных заявлениях на соответствующие значения. CLI поддерживает все другие параметры, которые поддерживает предыдущая команда PowerShell.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Установите диск данных в экземпляр Linux
Прежде чем продолжить шифрование в виртуальном наборе масштабов машины, убедитесь, что добавленный диск данных правильно установлен. Вопием в кластере Linux VM и запустите команду **LSBLK.** На выходе должен отображаться добавленный диск данных в столбце **Маунт-Пойнт.**


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Развертывание приложения в кластер Service Fabric в Linux
Чтобы развернуть приложение в кластере, выполните последующие действия и рекомендации в [компании «Быстрый старт: Развертывание контейнеров Linux](service-fabric-quickstart-containers-linux.md)в Service Fabric».


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Включить шифрование диска для виртуальных наборов масштабов машины, созданных ранее
Для включения шифрования диска для набора виртуальной машины, созданных на предыдущих этапах, запустите следующие команды:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Проверка, включено ли шифрование диска для виртуального масштаба машины, установленного в Linux
Чтобы получить статус всего набора виртуальной шкалы машины или любого экземпляра в наборе масштабов, запустите следующие команды.
Кроме того, вы можете войти в кластер Linux VM и запустить команду **LSBLK.** На выходе должен отображаться добавленный диск данных в столбце **Маунт-Пойнт,** а столбец **Типа** должен читать *Crypt.*

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Отключить шифрование диска для виртуальной шкалы машины, установленной в кластере Service Fabric
Отключите шифрование диска для виртуальной шкалы машины, установленной при запуске следующих команд. Обратите внимание, что отключение шифрования диска относится ко всему набору виртуальной шкалы машин, а не к отдельному экземпляру.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Дальнейшие действия
На этом этапе вы должны иметь безопасный кластер и знать, как включить и отключить шифрование диска для кластерных узлов Service Fabric и виртуальных наборов масштабов машин. Для получения аналогичных рекомендаций по кластерным узлам Service Fabric в Linux [см.](service-fabric-enable-azure-disk-encryption-windows.md) 
