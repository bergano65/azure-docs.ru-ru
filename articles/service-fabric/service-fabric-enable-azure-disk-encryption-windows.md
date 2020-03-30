---
title: Включить шифрование дисков для кластеров Windows
description: В этой статье описывается, как включить шифрование дисков для кластерных узлов Azure Service Fabric с помощью Azure Key Vault в azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251817"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Включить шифрование дисков для кластерных узлов Azure Service Fabric в Windows 
> [!div class="op_single_selector"]
> * [Шифрование дисков для Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Шифрование дисков для Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

В этом уроке вы узнаете, как включить шифрование дисков на кластерных узлах Service Fabric в Windows. Вам нужно будет следовать этим шагам для каждого из типов узлов и виртуальных наборов масштабов машины. Для шифрования узлов мы используем возможность шифрования azure Disk в наборах виртуальных машин.

Руководство охватывает следующие темы:

* Ключевые концепции, о чем следует знать при включении шифрования диска в кластерных виртуальных наборах масштабов машин Service Fabric в Windows.
* Шаги, которым следует следовать, прежде чем включить шифрование диска в кластерных узлах Service Fabric в Windows.
* Следует следовать мерам по шифрованию диска на кластерных узлах Service Fabric в Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

**Саморегистрация** 

Предварительный просмотр шифрования диска для набора виртуальной шкалы машины требует саморегистрации. Выполните указанные ниже действия. 

1. Во-первых, запустите следующую команду:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Подождите около 10 минут, пока статус читает *Зарегистрировано*. Проверить состояние можно, запустив следующую команду: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Убежище ключей Azure** 

1. Создайте хранилище ключей в той же подписке и регионе, что и набор масштабов, а затем выберите политику доступа **EnabledForDiskEncryption** на хранилище ключей, используя свой cmdlet PowerShell. Вы также можете настроить политику, используя ui Key Vault на портале Azure со следующей командой:
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
* Шифрование поддерживается для ОС и объемов данных в виртуальных наборах масштабов машин в Windows. Отключите шифрование также поддерживается для ОС и объемов данных для виртуальных наборов масштаба машины в Windows.
* Виртуальный переизбыток и обновление операций для виртуальных наборов масштабов машин не поддерживаются в текущем предварительном просмотре.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Создание нового кластера и включение шифрования диска

Используйте следующие команды для создания кластера и включения шифрования дисков с использованием шаблона Azure Resource Manager и самозаверяющего сертификата.

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

Если вам необходимо создать пользовательский шаблон в соответствии с вашими потребностями, мы настоятельно рекомендуем начать с одного из шаблонов, которые доступны на странице [шаблонов создания шаблонов создания кластеров Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Чтобы настроить раздел [шаблона кластера,][customize-your-cluster-template] см.

Если у вас уже есть пользовательский шаблон, проверьте, что все три параметра, связанных с сертификатом в шаблоне и файле параметра, названы следующим образом и что значения являются нулевыми следующим образом:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Развертывание приложения в кластер Service Fabric в Windows
Чтобы развернуть приложение в кластере, выполните инструкции и рекомендации в [развертывании и удалите приложения с помощью PowerShell.](service-fabric-deploy-remove-applications.md)


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Проверка включения шифрования диска для виртуального масштаба машины, установленного в Windows
Получите статус всего набора виртуальной шкалы машины или любого экземпляра в масштабе, установленном при запуске следующих команд.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Кроме того, вы можете войти в набор виртуальной шкалы машины и убедиться, что диски зашифрованы.

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
На этом этапе вы должны иметь безопасный кластер и знать, как включить и отключить шифрование диска для кластерных узлов Service Fabric и виртуальных наборов масштабов машин. Для получения аналогичных рекомендаций по кластерным узлам Service Fabric в Linux [см.](service-fabric-enable-azure-disk-encryption-linux.md)

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
