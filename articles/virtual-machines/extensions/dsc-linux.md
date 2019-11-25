---
title: Azure DSC extension for Linux
description: Установка пакетов OMI и DSC для обеспечения возможности настройки виртуальной машины Linux в Azure с помощью Desired State Configuration.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: b631a370c64522c201f1208819b5a76895d83b09
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457519"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC extension for Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) is a management platform that you can use to manage your IT and development infrastructure with configuration as code.

> [!NOTE]
> The DSC extension for Linux and the [Azure Monitor virtual machine extension for Linux](/azure/virtual-machines/extensions/oms-linux) currently present a conflict and aren't supported in a side-by-side configuration. Don't use the two solutions together on the same VM.

The DSCForLinux extension is published and supported by Microsoft. Оно устанавливает агенты OMI и DSC на виртуальных машинах Azure. The DSC extension can also do the following actions:


- Register the Linux VM to an Azure Automation account to pull configurations from the Azure Automation service (Register ExtensionAction).
- Push MOF configurations to the Linux VM (Push ExtensionAction).
- Apply meta MOF configuration to the Linux VM to configure a pull server in order to pull node configuration (Pull ExtensionAction).
- Install custom DSC modules to the Linux VM (Install ExtensionAction).
- Remove custom DSC modules from the Linux VM (Remove ExtensionAction).

 

## <a name="prerequisites"></a>Технические условия

### <a name="operating-system"></a>Операционная система

Расширение DSC для Linux поддерживает все [дистрибутивы Linux, рекомендованные для Azure](/azure/virtual-machines/linux/endorsed-distros), за исключением следующих.

| Дистрибуция | Версия |
|---|---|
| Debian | Все версии |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Подключение к Интернету

The DSCForLinux extension requires the target virtual machine to be connected to the internet. For example, the Register extension requires connectivity to the Automation service. For other actions such as Pull, Pull, Install requires connectivity to Azure Storage and GitHub. It depends on settings provided by the customer.

## <a name="extension-schema"></a>Схема расширения

### <a name="public-configuration"></a>Открытая конфигурация

Ниже приведены все поддерживаемые параметры открытой конфигурации:

* `FileUri`: (optional, string) The uri of the MOF file, meta MOF file, or custom resource zip file.
* `ResourceName`: (optional, string) The name of the custom resource module.
* `ExtensionAction` (необязательный, строчный): указывает, что делает расширения. Valid values are Register, Push, Pull, Install, and Remove. If not specified, it's considered a Push Action by default.
* `NodeConfigurationName`: (optional, string) The name of a node configuration to apply.
* `RefreshFrequencyMins`: (optional, int) Specifies how often (in minutes) that DSC attempts to obtain the configuration from the pull server. 
       If configuration on the pull server differs from the current one on the target node, it's copied to the pending store and applied.
* `ConfigurationMode` (необязательный, строчный): указывает, каким образом DSC следует применить конфигурацию. Valid values are ApplyOnly, ApplyAndMonitor, and ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins` (необязательный, целочисленный): указывает интервал (в минутах), с которым DSC проверяет, что конфигурация находится в требуемом состоянии.

> [!NOTE]
> If you use a version earlier than 2.3, the mode parameter is the same as ExtensionAction. Mode seems to be an overloaded term. To avoid confusion, ExtensionAction is used from version 2.3 onward. Для обеспечения обратной совместимости, расширение поддерживает как mode, так и ExtensionAction. 
>

### <a name="protected-configuration"></a>Защищенная конфигурация

Ниже приведены все поддерживаемые параметры защищенной конфигурации:

* `StorageAccountName`: (optional, string) The name of the storage account that contains the file
* `StorageAccountKey`: (optional, string) The key of the storage account that contains the file
* `RegistrationUrl`: (optional, string) The URL of the Azure Automation account
* `RegistrationKey`: (optional, string) The access key of the Azure Automation account


## <a name="scenarios"></a>Сценарии

### <a name="register-an-azure-automation-account"></a>Register an Azure Automation account
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell format
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Apply an MOF configuration file (in an Azure storage account) to the VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Apply an MOF configuration file (in public storage) to the VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell format
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Apply a meta MOF configuration file (in an Azure storage account) to the VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Применение файла конфигурации Meta MOF (в общедоступном хранилище) к виртуальной машине
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell format
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Install a custom resource module (a zip file in an Azure storage account) to the VM
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Install a custom resource module (a zip file in public storage) to the VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell format
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Удаление пользовательского модуля ресурсов из виртуальной машины
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell format
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Templates are ideal when you deploy one or more virtual machines that require post-deployment configuration, such as onboarding to Azure Automation. 

Пример шаблона Resource Manager — [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) и [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

For more information about the Azure Resource Manager template, see [Authoring Azure Resource Manager templates](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

### <a name="use-azure-cliazure-cli"></a>Use [Azure CLI][azure-cli]
Before you deploy the DSCForLinux extension, configure your `public.json` and `protected.json` according to the different scenarios in section 3.

#### <a name="classic"></a>Классический
The classic deployment mode is also called Azure Service Management mode. Его можно включить с помощью команды:
```
$ azure config mode asm
```

You can deploy the DSCForLinux extension by running:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Следующая команда позволяет узнать последнюю версию расширения:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Чтобы переключиться в режим Azure Resource Manager, выполните команду:
```
$ azure config mode arm
```

You can deploy the DSCForLinux extension by running:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> In Azure Resource Manager mode, `azure vm extension list` isn't available for now.
>

### <a name="use-azure-powershellazure-powershell"></a>Use [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Классический

You can sign in to your Azure account in Azure Service Management mode by running:

```powershell>
Add-AzureAccount
```

And deploy the DSCForLinux extension by running:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Change the content of $privateConfig and $publicConfig according to different scenarios in the previous section.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

You can sign in to your Azure account in Azure Resource Manager mode by running:

```powershell>
Login-AzAccount
```

To learn more about how to use Azure PowerShell with Azure Resource Manager, see [Manage Azure resources by using Azure PowerShell](../../azure-resource-manager/manage-resources-powershell.md).

You can deploy the DSCForLinux extension by running:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Change the content of $privateConfig and $publicConfig according to different scenarios in the previous section.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Data about the state of extension deployments can be retrieved from the Azure portal and by using the Azure CLI. To see the deployment state of extensions for a given VM, run the following command by using the Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Error code: 51 represents either unsupported distribution or unsupported extension action.
In some cases, DSC Linux extension fails to install OMI when a higher version of OMI already exists in the machine. [error response: (000003)Downgrade not allowed] (сообщение об ошибке: (000003) откат на более старую версию не разрешен)



### <a name="support"></a>Поддержка

If you need more help at any point in this article, contact the Azure experts on the [MSDN Azure and Stack Overflow forums](https://azure.microsoft.com/support/community/). Alternatively, you can file an Azure Support incident. Go to the [Azure Support site](https://azure.microsoft.com/support/options/), and select **Get support**. Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о расширениях виртуальных машин см. в обзоре [расширений и компонентов виртуальной машины для Linux](features-linux.md).
