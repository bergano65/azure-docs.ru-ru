---
title: Расширение пользовательских сценариев Azure в Windows | Документация Майкрософт
description: Сведения об автоматизации задач настройки виртуальных машин Windows с помощью расширения пользовательских сценариев
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: c0c160d9fc2fcfb8da004d02baae1dd410620cbb
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204206"
---
# <a name="custom-script-extension-for-windows"></a>Расширение Custom Script в ОС Windows

Расширение пользовательских сценариев загружает и запускает сценарии на виртуальных машинах Azure. Это расширение полезно для настройки после развертывания, установки программного обеспечения или любых других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения. Расширение пользовательских скриптов интегрируется с шаблонами Azure Resource Manager и может выполняться с помощью Azure CLI, PowerShell, портал Azure или виртуальной машины Azure REST API.

В этом документе объясняется, как использовать расширение пользовательских сценариев с помощью модуля Azure PowerShell и шаблонов Azure Resource Manager, а также подробно описываются действия по устранению неполадок в системах Windows.

## <a name="prerequisites"></a>Предварительные требования

> [!NOTE]  
> Не используйте расширение пользовательских сценариев для выполнения команды Update-AzVM на той же виртуальной машине, которая использовалась для параметра, так как команда выполнится через определенный период времени.  

### <a name="operating-system"></a>Операционная система

Расширение пользовательских скриптов для Windows будет работать в поддерживаемых расширениях OSs. Дополнительные сведения см. в этой [операционной системе, поддерживаемой расширением Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Расположение сценария

Вы можете настроить расширение на использование учетных данных хранилища BLOB-объектов Azure для доступа к хранилищу BLOB-объектов Azure. Расположение скрипта может находиться в любом месте, если виртуальная машина может маршрутизироваться к этой конечной точке, такой как GitHub или внутренний файловый сервер.

### <a name="internet-connectivity"></a>Подключение к Интернету

Если необходимо скачать скрипт извне, например из GitHub или службы хранилища Azure, необходимо открыть дополнительные порты брандмауэра и группы безопасности сети. Например, если сценарий находится в службе хранилища Azure, вы можете разрешить доступ с помощью тегов службы Azure NSG для [хранилища](../../virtual-network/security-overview.md#service-tags).

Если сценарий находится на локальном сервере, вам может потребоваться открыть дополнительные порты брандмауэра и группы безопасности сети.

### <a name="tips-and-tricks"></a>Советы и рекомендации

* Наибольшая частота сбоев для этого расширения обусловлена синтаксическими ошибками в скрипте, тестом выполнения скрипта без ошибок, а также добавлением дополнительного входа в сценарий, чтобы упростить поиск места сбоя.
* Напишите скрипты, которые являются идемпотентными. Это гарантирует, что если они снова будут выполняться случайно, это не приведет к изменениям системы.
* Выполняемые скрипты не должны запрашивать ввод данных пользователем.
* На выполнение скрипта отводится 90 минут. Более продолжительное действие вызовет сбой подготовки расширения.
* Не следует помещать перезагрузки в скрипт, так как будут возникать проблемы с другими устанавливаемыми расширениями. После перезагрузки расширение прекратить работать.
* Если имеется сценарий, который вызывает перезагрузку, а затем устанавливает приложения и запускает сценарии, можно запланировать перезагрузку с помощью запланированной задачи Windows или использовать такие средства, как расширения DSC, Chef или Puppet.
* Расширение позволяет запустить скрипт только один раз. Если нужно выполнять скрипт при каждой загрузке, создайте запланированную задачу Windows с помощью расширения.
* Чтобы запланировать время выполнения скрипта, необходимо создать запланированную задачу Windows с помощью расширения.
* Во время выполнения скрипта вы увидите на портале Microsoft Azure или в CLI только переходное состояние расширения. Если требуются более частые обновления состояния выполняющегося скрипта, следует создать собственное решение.
* Расширение настраиваемых скриптов не имеет собственной поддержки прокси-серверов. Но можно использовать средство передачи файлов, которое поддерживает прокси-серверы в скрипте, например *Curl*.
* Следует учитывать настраиваемые расположения каталогов, которые могут использоваться скриптами или командами, и иметь в распоряжении логику для обработки таких ситуаций.
* Расширение пользовательских скриптов будет выполняться под учетной записью LocalSystem

## <a name="extension-schema"></a>Схема расширения

В конфигурации расширения пользовательских сценариев указываются такие параметры, как расположение сценария и команда для его выполнения. Эту конфигурацию можно хранить в файлах конфигурации, указать в командной строке или в шаблоне Azure Resource Manager.

Конфиденциальные данные можно хранить в зашифрованном виде в защищенной конфигурации, которая расшифровывается только внутри виртуальной машины. Защищенную конфигурацию можно использовать для команд, которые содержат секретные данные, например пароли.

Эти элементы следует рассматривать в качестве конфиденциальных данных. Их нужно задать в защищенной конфигурации параметров расширений. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
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
> Только одна версия расширения может быть установлена на виртуальную машину на момент времени, однако в одном шаблоне диспетчер ресурсов для одной и той же виртуальной машины один и тот же пользовательский скрипт дважды закончится ошибкой.

> [!NOTE]
> Эту схему можно использовать в ресурсе VirtualMachine или в качестве автономного ресурса. Имя ресурса должно быть в формате "virtualMachineName/extensionName", если это расширение используется в качестве автономного ресурса в шаблоне ARM. 

### <a name="property-values"></a>Значения свойств

| Название | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute; | строка |
| type | CustomScriptExtension | строка |
| typeHandlerVersion | 1.9 | int |
| fileUris (пример) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (например) | 123456789 | 32-битное целое число |
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

Использование общедоступных параметров может оказаться полезным для отладки, но рекомендуется использовать защищенные параметры.

Открытые параметры отправляются в виде открытого текста на виртуальную машину, где будет выполняться скрипт.  Защищенные параметры шифруются с помощью ключа, который известен только Azure и виртуальной машине. Параметры сохраняются на виртуальной машине по мере их отправки, то есть если параметры были зашифрованы, они сохраняются на виртуальной машине в зашифрованном виде. Сертификат, используемый для расшифровки зашифрованных значений, хранится на виртуальной машине и применяется для расшифровки параметров (при необходимости) во время выполнения.

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Схема JSON, подробно описанная в предыдущем разделе, может использоваться в шаблоне Azure Resource Manager для запуска расширения пользовательского скрипта во время развертывания. Приведенные ниже примеры демонстрируют использование расширения пользовательских сценариев.

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

### <a name="using-multiple-scripts"></a>Использование нескольких скриптов

В этом примере имеется три скрипта, которые используются для построения сервера. **CommandToExecute** вызывает первый сценарий, а значит, у вас есть параметры вызова других. Например, можно создать главный сценарий, управляющий выполнением, с правильной обработкой ошибок, ведением журнала и управлением состоянием. Скрипты загружаются на локальный компьютер для выполнения. Например, в `1_Add_Tools.ps1` можно вызвать `2_Add_Features.ps1` , добавив `.\2_Add_Features.ps1` к скрипту, и повторить этот процесс для других скриптов, определенных в `$settings`.

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

В этом примере может потребоваться использовать локальный сервер SMB для расположения скрипта. Таким образом, не нужно указывать другие параметры, кроме **commandToExecute**.

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

* Параметр **имени** расширения совпадает с предыдущим развертыванием расширения.
* Обновите конфигурацию. в противном случае команда не будет выполнена повторно. Можно добавить в команду динамическое свойство, например метку времени.

Кроме того, можно задать для свойства [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) **значение true**.

### <a name="using-invoke-webrequest"></a>Использование Invoke-WebRequest

Если вы используете [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) в скрипте, необходимо указать параметр `-UseBasicParsing` или при проверке подробного состояния появится следующая ошибка:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>классические виртуальные машины;

Чтобы развернуть расширение пользовательских сценариев на классических виртуальных машинах, можно использовать портал Azure или классические командлеты Azure PowerShell.

### <a name="azure-portal"></a>портала Azure

Перейдите к классическому ресурсу виртуальной машины. В разделе **Параметры**выберите **расширения** .

Щелкните **+ Добавить** и в списке ресурсов выберите **расширение настраиваемых сценариев**.

На странице **Установка расширения** выберите локальный файл PowerShell и заполните все аргументы и нажмите кнопку **ОК**.

### <a name="powershell"></a>PowerShell

С помощью командлета [Set-азуревмкустомскриптекстенсион](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) можно добавить расширение пользовательского скрипта в существующую виртуальную машину.

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

Выходные данные расширения записываются в файлы, находящиеся в следующей папке на целевой виртуальной машине.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Указанные файлы загружаются в следующую папку на целевой виртуальной машине.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

`<n>` — десятичное целое число, которое может измениться между выполнениями расширения.  Значение `1.*` соответствует фактическому текущему значению `typeHandlerVersion` расширения.  Например, фактическим каталогом может быть `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

При выполнении команды `commandToExecute` расширение устанавливает этот каталог (например, `...\Downloads\2`) в качестве текущего рабочего каталога. Этот процесс позволяет использовать относительные пути для поиска файлов, скачанных с помощью свойства `fileURIs`. Примеры приведены в следующей таблице.

Так как со временем абсолютный путь для скачивания может измениться, в строке `commandToExecute` лучше указывать относительные пути к сценариям или файлам, когда это возможно. Пример:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Сведения о пути после первого сегмента URI хранятся для файлов, скачанных с `fileUris` помощью списка свойств.  Как показано в следующей таблице, скачанные файлы распределяются по подкаталогам в соответствии со структурой значений `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Примеры скачанных файлов

| Универсальный код ресурса (URI) в fileUris | Относительное расположение скачанных файлов | Абсолютно загруженное расположение <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> абсолютные пути к каталогу меняются в течение времени существования виртуальной машины, но не в рамках одного выполнения расширения CustomScript.

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Можно также зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
