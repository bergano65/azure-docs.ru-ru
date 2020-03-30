---
title: Расширение Azure DSC для Linux
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
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250625"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Расширение DSC для Linux (Microsoft.OSTCExtensions.DSCForLinux)

Пожеланная конфигурация состояния (DSC) — это платформа управления, которую можно использовать для управления ИТ-инфраструктурой и инфраструктурой разработки с конфигурацией в качестве кода.

> [!NOTE]
> Расширение DSC для Linux и [расширение виртуальной машины Azure Monitor для Linux](/azure/virtual-machines/extensions/oms-linux) в настоящее время представляет собой конфликт и не поддерживается в параллельной конфигурации. Не используйте два решения вместе на одном и том же VM.

Расширение DSCForLinux публикуется и поддерживается корпорацией Майкрософт. Оно устанавливает агенты OMI и DSC на виртуальных машинах Azure. Расширение DSC также может выполнять следующие действия:


- Зарегистрируйте Linux VM на учетную запись Azure Automation, чтобы вытащить конфигурации из службы автоматизации Azure (Register ExtensionAction).
- Нажмите MOF конфигураций на Linux VM (Push ExtensionAction).
- Примените конфигурацию мета MOF к Linux VM, чтобы настроить сервер для вытягивания, чтобы вытащить конфигурацию узла (Pull ExtensionAction).
- Установите пользовательские модули DSC на Linux VM (Install ExtensionAction).
- Удалите пользовательские модули DSC из Linux VM (Удалить ExtensionAction).

 

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>Операционная система

Расширение DSC для Linux поддерживает все [дистрибутивы Linux, рекомендованные для Azure](/azure/virtual-machines/linux/endorsed-distros), за исключением следующих.

| Distribution | Версия |
|---|---|
| Debian | Все версии |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Подключение к Интернету

Расширение DSCForLinux требует подключения целевой виртуальной машины к Интернету. Например, расширение Регистра требует подключения к службе автоматизации. Для других действий, таких как Pull, Pull, Install требуется подключение к Хранилищам Azure и GitHub. Это зависит от настроек, предоставляемых клиентом.

## <a name="extension-schema"></a>Схема расширения

### <a name="public-configuration"></a>Открытая конфигурация

Ниже приведены все поддерживаемые параметры открытой конфигурации:

* `FileUri`: (необязательно, строка) uri файла MOF, файл meta MOF или пользовательский файл застежки-молнии ресурса.
* `ResourceName`: (необязательно, строка) Название модуля пользовательского ресурса.
* `ExtensionAction` (необязательный, строчный): указывает, что делает расширения. Допустимые значения регистрируются, нажимайте, вытягивайте, устанавливайте и удаляйте. Если не указано, это считается Push Action по умолчанию.
* `NodeConfigurationName`: (необязательно, строка) Название конфигурации узла для применения.
* `RefreshFrequencyMins`: (необязательно, int) определяет, как часто (в минутах), что DSC пытается получить конфигурацию от выдвижной сервера. 
       Если конфигурация на сервере вытягивания отличается от текущей на целевом узлах, она скопирована в ожидающий магазин и применена.
* `ConfigurationMode` (необязательный, строчный): указывает, каким образом DSC следует применить конфигурацию. Действительные значения применяютсятолькотолько, ApplyAndMonitor и ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins` (необязательный, целочисленный): указывает интервал (в минутах), с которым DSC проверяет, что конфигурация находится в требуемом состоянии.

> [!NOTE]
> Если вы используете версию раньше 2.3, параметр режима такой же, как ExtensionAction. Режим, кажется, перегруженный термин. Чтобы избежать путаницы, ExtensionAction используется из версии 2.3. Для обеспечения обратной совместимости, расширение поддерживает как mode, так и ExtensionAction. 
>

### <a name="protected-configuration"></a>Защищенная конфигурация

Ниже приведены все поддерживаемые параметры защищенной конфигурации:

* `StorageAccountName`: (необязательно, строка) Имя учетной записи хранилища, содержащей файл
* `StorageAccountKey`: (необязательно, строка) Ключ учетной записи хранилища, содержащий файл
* `RegistrationUrl`: (необязательно, строка) URL учетной записи автоматизации Azure
* `RegistrationKey`: (необязательно, строка) Ключ доступа к учетной записи Azure Automation


## <a name="scenarios"></a>Сценарии

### <a name="register-an-azure-automation-account"></a>Регистрация учетной записи Автоматизации Azure
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

Формат PowerShell
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Примените файл конфигурации MOF (в учетной записи хранения Azure) к VM

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

Формат PowerShell
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


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Примените файл конфигурации MOF (в общедоступном хранилище) к VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Формат PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Примените файл конфигурации мета MOF (в учетной записи хранения Azure) к VM

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

Формат PowerShell
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
Формат PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Установка пользовательского модуля ресурсов (файл застежки-молнии в учетную запись хранения Azure) в VM
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

Формат PowerShell
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Установка пользовательского модуля ресурсов (файл застежки-молнии в общедоступном хранилище) в VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Формат PowerShell
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
Формат PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Шаблоны идеально подходят при развертывании одной или нескольких виртуальных машин, требующих конфигурации после развертывания, например, для развертывания в Azure Automation. 

Пример шаблона Resource Manager — [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) и [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Для получения дополнительной информации о шаблоне менеджера ресурсов Azure [см.](../../azure-resource-manager/templates/template-syntax.md)


## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

### <a name="use-azure-cliazure-cli"></a>Используйте «Azure CLI»azure-cli
Перед развертыванием расширения DSCForLinux настройте и `public.json` `protected.json` в соответствии с различными сценариями в разделе 3.

#### <a name="classic"></a>Классический

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Классический режим развертывания также называется режимом управления службами Azure. Его можно включить с помощью команды:
```
$ azure config mode asm
```

Расширение DSCForLinux можно развернуть:
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

Расширение DSCForLinux можно развернуть:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> В режиме `azure vm extension list` менеджера ресурсов Azure пока недоступен.
>

### <a name="use-azure-powershellazure-powershell"></a>Используйте «Azure PowerShell» (лазурная силовая оболочка)

#### <a name="classic"></a>Классический

Вы можете войти в учетную запись Azure в режиме управления службой Azure, запустив:

```powershell>
Add-AzureAccount
```

И развернуть расширение DSCForLinux, запустив:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Измените содержание $privateConfig и $publicConfig в соответствии с различными сценариями в предыдущем разделе.
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

Вы можете войти в учетную запись Azure в режиме менеджера ресурсов Azure, запустив:

```powershell>
Login-AzAccount
```

Подробнее о том, как использовать Azure PowerShell с помощью ресурсов Azure, можно узнать, как использовать Azure PowerShell, [спомощьи ресурсов Azure PowerShell.](../../azure-resource-manager/management/manage-resources-powershell.md)

Расширение DSCForLinux можно развернуть:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Измените содержание $privateConfig и $publicConfig в соответствии с различными сценариями в предыдущем разделе.
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

Данные о состоянии развертывания расширений можно получить с портала Azure и с помощью Azure CLI. Чтобы увидеть состояние развертывания расширений для данного VM, запустите следующую команду, используя Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Код ошибки: 51 представляет собой либо неподдерживаемое действие дистрибутива, либо неподдерживаемое действие расширения.
В некоторых случаях расширение DSC Linux не удается установить OMI, когда в машине уже существует более высокая версия OMI. [error response: (000003)Downgrade not allowed] (сообщение об ошибке: (000003) откат на более старую версию не разрешен)



### <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, обратитесь к экспертам Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/community/) Кроме того, можно подать запрос об инциденте службы поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/)и выберите **Получить поддержку.** Для получения информации об использовании службы поддержки Azure прочитайте [часто задаваемые вопросы поддержки Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о расширениях виртуальных машин см. в обзоре [расширений и компонентов виртуальной машины для Linux](features-linux.md).
