---
title: Расширение пользовательских сценариев Azure в Windows | Документация Майкрософт
description: Сведения об автоматизации задач настройки виртуальных машин Windows с помощью расширения пользовательских сценариев
services: virtual-machines-windows
manager: carmonm
author: georgewallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/15/2019
ms.author: gwallace
ms.openlocfilehash: e2b36633996f961d100f0a98abb09135fd4393e4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007089"
---
# <a name="custom-script-extension-for-windows"></a>Расширение Custom Script в ОС Windows

Расширение пользовательских сценариев загружает и запускает сценарии на виртуальных машинах Azure. Это расширение полезно для настройки после развертывания, установки программного обеспечения, или любые другие задач настройки и управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения. Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager и могут выполняться с помощью Azure CLI, PowerShell, портала Azure или API REST виртуальной машины Azure.

В этом документе объясняется, как использовать расширение пользовательских сценариев с помощью модуля Azure PowerShell и шаблонов Azure Resource Manager, а также подробно описываются действия по устранению неполадок в системах Windows.

## <a name="prerequisites"></a>Технические условия

> [!NOTE]  
> Не используйте расширение пользовательских сценариев для выполнения команды Update-AzVM на той же виртуальной машине, которая использовалась для параметра, так как команда выполнится через определенный период времени.  

### <a name="operating-system"></a>Операционная система

Пользовательский сценарий расширения для Windows выполняется на поддерживаемых расширений OSs, Дополнительные сведения, см. в разделе, это [расширение Azure поддерживаемые операционные системы](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Расположение сценария

Расширение для доступа к хранилищу BLOB-объектов Azure с помощью учетных данных хранилища BLOB-объектов Azure можно настроить. Расположение скрипта может быть в любом месте, до тех пор, пока виртуальная машина может направлять к этой конечной точки, такие как GitHub или внутренний файловый сервер.

### <a name="internet-connectivity"></a>Подключение к Интернету

Если вам нужно загрузить сценария извне, такого как GitHub или хранилища Azure, затем дополнительный брандмауэр и группу безопасности сети порты необходимо открыть. Например, если сценарий находится в службе хранилища Azure, можно разрешить доступ с помощью тегов службы группы безопасности сети Azure для [хранения](../../virtual-network/security-overview.md#service-tags).

Если сценарий находится на локальном сервере, вам по-прежнему потребуется дополнительный брандмауэр и группу безопасности сети порты необходимо открыть.

### <a name="tips-and-tricks"></a>Советы и рекомендации

* Самый высокий уровень сбоев для этого расширения из-за синтаксических ошибок в скрипте теста, сценарий выполняется без ошибок, и также поместить в дополнительные записи в журнал в сценарий, чтобы было проще найти, которой произошел сбой.
* Создать сценарий, являются идемпотентными. Это гарантирует, что они запущены снова случайно не возникает изменений в системе.
* Выполняемые скрипты не должны запрашивать ввод данных пользователем.
* На выполнение скрипта отводится 90 минут. Более продолжительное действие вызовет сбой подготовки расширения.
* Не следует помещать перезагрузки в скрипт, так как будут возникать проблемы с другими устанавливаемыми расширениями. После перезагрузки расширение прекратить работать.
* Если у вас есть скрипт, который будет приводить к перезагрузке, затем устанавливать приложения и выполнять скрипты, можно запланировать перезагрузки, с помощью Windows запланированную задачу, или использовать средства, такие как расширения DSC, Chef или Puppet.
* Расширение позволяет запустить скрипт только один раз. Если нужно выполнять скрипт при каждой загрузке, создайте запланированную задачу Windows с помощью расширения.
* Чтобы запланировать время выполнения скрипта, необходимо создать запланированную задачу Windows с помощью расширения.
* Во время выполнения скрипта вы увидите на портале Microsoft Azure или в CLI только переходное состояние расширения. Если требуются более частые обновления состояния выполняющегося скрипта, следует создать собственное решение.
* Расширение настраиваемых скриптов не имеет собственной поддержки прокси-серверов. Но можно использовать средство передачи файлов, которое поддерживает прокси-серверы в скрипте, например *Curl*.
* Следует учитывать настраиваемые расположения каталогов, которые могут использоваться скриптами или командами, и иметь в распоряжении логику для обработки таких ситуаций.

## <a name="extension-schema"></a>Схема расширения

В конфигурации расширения пользовательских сценариев указываются такие параметры, как расположение сценария и команда для его выполнения. Эту конфигурацию можно хранить в файлах конфигурации, указать в командной строке или в шаблоне Azure Resource Manager.

Конфиденциальные данные можно хранить в зашифрованном виде в защищенной конфигурации, которая расшифровывается только внутри виртуальной машины. Защищенную конфигурацию можно использовать для команд, которые содержат секретные данные, например пароли.

Эти элементы следует рассматривать в качестве конфиденциальных данных. Их нужно задать в защищенной конфигурации параметров расширений. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Только одна версия расширения можно установить на виртуальной Машине в момент времени, указание пользовательского скрипта дважды в один и тот же шаблон Resource Manager для той же виртуальной Машине завершится ошибкой.

### <a name="property-values"></a>Значения свойств

| Name | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2015-06-15 | date |
| publisher | Microsoft.Compute; | строка |
| Тип | CustomScriptExtension | строка |
| typeHandlerVersion | 1.9 | int |
| fileUris (пример) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| метка времени (например) | 123456789 | 32-битное целое число |
| commandToExecute (пример) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | строка |
| storageAccountName (пример) | examplestorageacct | строка |
| storageAccountKey (пример) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | строка |

>[!NOTE]
>В именах свойств учитывается регистр. Чтобы избежать проблем с развертыванием, используйте имена, как показано ниже.

#### <a name="property-value-details"></a>Сведения о значениях свойств

* `commandToExecute`: (**обязательное**, строка) выполняемый сценарий точки входа. Если команда содержит секретные данные, например пароли, или если значения fileUri являются конфиденциальным, используйте взамен это поле.
* `fileUris`: (необязательное, строковый массив) URL-адреса файлов для скачивания.
* `timestamp` (необязательный, 32-битное целое число) используется только для повторного запуска скрипта при изменении значения этого поля.  Любое целочисленное значение допустимо, оно только должно отличаться от предыдущего значения.
* `storageAccountName`: (необязательное, строка) имя учетной записи хранения. Если указаны учетные данные хранилища, все значения `fileUris` должны иметь формат URL-адресов для BLOB-объектов Azure.
* `storageAccountKey`: (необязательное, строка) ключ доступа для учетной записи хранения.

Указанные ниже значения можно задавать либо в открытых, либо в защищенных параметрах. Расширение отклонит любую конфигурацию, в которой приведенные ниже значения будут указаны в открытых и защищенных параметрах одновременно.

* `commandToExecute`

С помощью открытых параметров может быть полезно для отладки, но его рекомендуется использовать защищенные параметры.

Открытые параметры отправляются в виде открытого текста на виртуальную машину, где будет выполняться скрипт.  Защищенные параметры шифруются с помощью ключа, который известен только Azure и виртуальной машине. Параметры сохраняются в виртуальную машину, так как они были отправлены, то есть, если параметры были зашифрованы их сохранении зашифрованных на виртуальной Машине. Сертификат, используемый для расшифровки зашифрованных значений, хранится на виртуальной машине и применяется для расшифровки параметров (при необходимости) во время выполнения.

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Схему JSON, который описан в предыдущем разделе, можно в шаблоне Azure Resource Manager для запуска расширения пользовательских сценариев во время развертывания. Приведенные ниже примеры демонстрируют использование расширения пользовательских сценариев.

* [Руководство. Развертывание расширений виртуальной машины с помощью шаблонов Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md).
* [Развертывание двухуровневого приложения в Windows и базе данных Azure SQL](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Выполнив команду `Set-AzVMCustomScriptExtension`, расширение пользовательских сценариев можно добавить на существующую виртуальную машину. Дополнительные сведения см. в статье о [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Дополнительные примеры

### <a name="using-multiple-scripts"></a>С помощью нескольких сценариев

В этом примере имеется три сценария, которые используются для создания сервера. **CommandToExecute** вызывает первый скрипт, то у вас есть параметры на остальных вызовом. Например может иметь основной сценарий, который управляет выполнением, с правом обработки ошибок, ведения журнала и управление состоянием. Сценарии скачиваются на локальном компьютере, для запуска. В примере `1_Add_Tools.ps1` вызывается `2_Add_Features.ps1` , добавив `.\2_Add_Features.ps1` к скрипту и повторите этот процесс для других сценариев можно определить в `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Запуск скриптов из локальной общей папки

В этом примере можно использовать локальный сервер SMB для расположение вашего сценария. Таким образом, не нужно указать другие параметры, за исключением **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Многократное выполнение пользовательского скрипта с помощью CLI

Чтобы выполнить расширение пользовательского скрипта более одного раза, должны соблюдаться следующие условия:

* Расширение **имя** параметра совпадает со значением предыдущего развертывания расширения.
* Обновите конфигурацию в противном случае не будет повторно выполнить команду. Можно добавить в команду динамическое свойство, например метку времени.

## <a name="classic-vms"></a>классические виртуальные машины;

Чтобы развернуть расширение пользовательских скриптов на классических виртуальных машин, можно использовать портал Azure или классическом командлетов Azure PowerShell.

### <a name="azure-portal"></a>Портал Azure

Перейдите к ресурсу классической виртуальной Машины. Выберите **расширения** под **параметры**.

Нажмите кнопку **+ добавить** и в списке ресурсов выберите **расширение пользовательских скриптов**.

На **установить расширение** выберите локальный файл PowerShell и заполните все аргументы и нажмите кнопку **ОК**.

### <a name="powershell"></a>PowerShell

Используйте [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) командлет можно использовать для добавления расширения пользовательских сценариев для существующей виртуальной машины.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также с помощью модуля Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Выходные данные расширения регистрируются в файле, расположенном в следующей папке на целевой виртуальной машине.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Указанные файлы загружаются в следующую папку на целевой виртуальной машине.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

`<n>` — десятичное целое число, которое может измениться между выполнениями расширения.  Значение `1.*` соответствует фактическому текущему значению `typeHandlerVersion` расширения.  Например, фактическим каталогом может быть `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

При выполнении команды `commandToExecute` расширение устанавливает этот каталог (например, `...\Downloads\2`) в качестве текущего рабочего каталога. Этот процесс позволяет использовать относительные пути для поиска файлов, скачанных с помощью свойства `fileURIs`. Примеры приведены в следующей таблице.

Так как со временем абсолютный путь для скачивания может измениться, в строке `commandToExecute` лучше указывать относительные пути к сценариям или файлам, когда это возможно. Например: 

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Сведения о пути после первого сегмента URI хранится для файлов, скачанных с помощью `fileUris` списка свойств.  Как показано в следующей таблице, скачанные файлы распределяются по подкаталогам в соответствии со структурой значений `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Примеры скачанных файлов

| Универсальный код ресурса (URI) в fileUris | Относительное расположение скачанных файлов | Абсолютное расположение скачанных файлов <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> абсолютные пути каталогов изменение в течение времени существования виртуальной машины, но не внутри одного выполнения расширения CustomScript.

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Можно также зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).