---
title: Расширение пользовательского скрипта Azure для Windows
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
ms.openlocfilehash: 2c7cad2dfdcd55073a1cf09d79e5223b666ced5f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478156"
---
# <a name="custom-script-extension-for-windows"></a>Расширение Custom Script в ОС Windows

Расширение пользовательских сценариев загружает и запускает сценарии на виртуальных машинах Azure. Это расширение полезно для конфигурации после развертывания, установки программного обеспечения или любых других задач конфигурации или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения. Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager и может быть запущено с помощью azure CLI, PowerShell, портала Azure или API Azure Virtual Machine REST.

В этом документе объясняется, как использовать расширение пользовательских сценариев с помощью модуля Azure PowerShell и шаблонов Azure Resource Manager, а также подробно описываются действия по устранению неполадок в системах Windows.

## <a name="prerequisites"></a>Предварительные требования

> [!NOTE]  
> Не используйте расширение пользовательских сценариев для выполнения команды Update-AzVM на той же виртуальной машине, которая использовалась для параметра, так как команда выполнится через определенный период времени.  

### <a name="operating-system"></a>Операционная система

Пользовательский сценарий расширение для Windows будет работать на расширение поддерживается [Azure Extension supported operating systems](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)расширение ОС, для получения дополнительной информации, см.

### <a name="script-location"></a>Расположение сценария

Можно настроить расширение, чтобы использовать учетные данные хранилища Azure Blob для доступа к хранилищу Azure Blob. Местоположение скрипта может быть где угодно, пока VM может направиться к этой точке, например GitHub или внутренний файловый сервер.

### <a name="internet-connectivity"></a>Подключение к Интернету

Если вам нужно загрузить скрипт извне, например, из GitHub или Azure Storage, то необходимо открыть дополнительные порты брандмауэра и группы сетевой безопасности. Например, если скрипт находится в хранилище Azure, можно разрешить доступ с помощью тегов службы nG Azure для [хранения.](../../virtual-network/security-overview.md#service-tags)

Если ваш скрипт находится на локальном сервере, то вам может потребоваться дополнительный брандмауэр и порты Группы сетевой безопасности, которые необходимо открыть.

### <a name="tips-and-tricks"></a>Советы и рекомендации

* Самый высокий показатель сбоев для этого расширения из-за ошибок синтаксиса в скрипте, тестирует сценарий без ошибок, а также внедывает дополнительный вход в скрипт, чтобы было легче найти, где он не удалось.
* Напишите сценарии, которые являются идемпотентными. Это гарантирует, что если они снова заработают случайно, это не вызовет системных изменений.
* Выполняемые скрипты не должны запрашивать ввод данных пользователем.
* На выполнение скрипта отводится 90 минут. Более продолжительное действие вызовет сбой подготовки расширения.
* Не следует помещать перезагрузки в скрипт, так как будут возникать проблемы с другими устанавливаемыми расширениями. После перезагрузки расширение прекратить работать.
* Если у вас есть скрипт, который вызовет перезагрузку, а затем установите приложения и запустите скрипты, вы можете запланировать перезагрузку с помощью запланированной задачи Windows или использовать такие инструменты, как DSC, Chef или Puppet.
* Расширение позволяет запустить скрипт только один раз. Если нужно выполнять скрипт при каждой загрузке, создайте запланированную задачу Windows с помощью расширения.
* Чтобы запланировать время выполнения скрипта, необходимо создать запланированную задачу Windows с помощью расширения.
* Во время выполнения скрипта вы увидите на портале Microsoft Azure или в CLI только переходное состояние расширения. Если требуются более частые обновления состояния выполняющегося скрипта, следует создать собственное решение.
* Пользовательское расширение скрипта не поддерживает прокси-серверы, однако вы можете использовать инструмент передачи файлов, который поддерживает прокси-серверы в вашем скрипте, такие как *Curl*
* Следует учитывать настраиваемые расположения каталогов, которые могут использоваться скриптами или командами, и иметь в распоряжении логику для обработки таких ситуаций.
* Пользовательское расширение скрипта будет работать под учетной записью LocalSystem

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
        "typeHandlerVersion": "1.10",
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
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> управляемое свойствоIdentity **не должно** использоваться в сочетании с свойствами storageAccountName или storageAccountKey

> [!NOTE]
> Только одна версия расширения может быть установлена на VM в данный момент времени, указывая пользовательский скрипт дважды в том же шаблоне менеджер ресурсов для того же VM не удастся.

> [!NOTE]
> Мы можем использовать эту схему внутри ресурса VirtualMachine или в качестве отдельного ресурса. Название ресурса должно быть в этом формате "virtualMachineName/extensionName", если это расширение используется в качестве отдельного ресурса в шаблоне ARM.

### <a name="property-values"></a>Значения свойств

| Имя | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2015-06-15 | Дата |
| publisher | Microsoft.Compute; | строка |
| type | CustomScriptExtension | строка |
| typeHandlerVersion | 1,10 | INT |
| fileUris (пример) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | массиве |
| метка времени (например) | 123456789 | 32-разрядное целое число |
| commandToExecute (пример) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | строка |
| storageAccountName (пример) | examplestorageacct | строка |
| storageAccountKey (пример) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | строка |
| управляемыйИдентичность (например) | Вопросы "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" или "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" | объект json |

>[!NOTE]
>В именах свойств учитывается регистр. Чтобы избежать проблем с развертыванием, используйте имена, как показано ниже.

#### <a name="property-value-details"></a>Сведения о значениях свойств

* `commandToExecute`: (**обязательное**, строка) выполняемый сценарий точки входа. Если команда содержит секретные данные, например пароли, или если значения fileUri являются конфиденциальным, используйте взамен это поле.
* `fileUris`: (необязательное, строковый массив) URL-адреса файлов для скачивания.
* `timestamp` (необязательный, 32-битное целое число) используется только для повторного запуска скрипта при изменении значения этого поля.  Любое целочисленное значение допустимо, оно только должно отличаться от предыдущего значения.
* `storageAccountName`: (необязательное, строка) имя учетной записи хранения. Если указаны учетные данные хранилища, все значения `fileUris` должны иметь формат URL-адресов для BLOB-объектов Azure.
* `storageAccountKey`: (необязательное, строка) ключ доступа для учетной записи хранения.
* `managedIdentity`: (необязательно, объект json) [управляемый итог](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) для загрузки файла (ы)
  * `clientId`: (необязательно, строка) идентификатор клиента управляемого удостоверения
  * `objectId`: (необязательно, строка) идентификатор объекта управляемого удостоверения

Указанные ниже значения можно задавать либо в открытых, либо в защищенных параметрах. Расширение отклонит любую конфигурацию, в которой приведенные ниже значения будут указаны в открытых и защищенных параметрах одновременно.

* `commandToExecute`

Использование общедоступных настроек может быть полезным для отладки, но рекомендуется использовать защищенные настройки.

Открытые параметры отправляются в виде открытого текста на виртуальную машину, где будет выполняться скрипт.  Защищенные параметры шифруются с помощью ключа, который известен только Azure и виртуальной машине. Настройки сохраняются в VM по мере их отправки, т.е. если настройки были зашифрованы, они сохраняются в зашифрованном виде на VM. Сертификат, используемый для расшифровки зашифрованных значений, хранится на виртуальной машине и применяется для расшифровки параметров (при необходимости) во время выполнения.

####  <a name="property-managedidentity"></a>Недвижимость: управляемоеИдентичность
> [!NOTE]
> Это свойство **должно** быть указано только в защищенных настройках.

CustomScript (версия 1.10 далее) поддерживает [управляемый итог](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) для загрузки файла (ы) из URL-адресов, представленных в настройках "fileUris". Это позволяет CustomScript получить доступ к частным каплям или контейнерам Azure Storage без необходимости передавать секреты, такие как токены SAS или ключи учетной записи хранилища.

Чтобы использовать эту функцию, пользователь должен добавить [установленную системой](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) или [назначенную пользователем](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) идентификацию в VM или VMSS, где ожидается запуск CustomScript, и [предоставить управляемый доступ к контейнеру хранения Azure или кабро.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)

Чтобы использовать установленное системой удостоверение на целевом VM/VMSS, установите поле "управляемой идентичности" на пустой объект json. 

> Пример
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Чтобы использовать установленное пользователем удостоверение личности на целевом VM/VMSS, назначаем поле "управляемой идентичности" с идентификатором клиента или идентификатором объекта управляемого удостоверения.

> Примеры:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> управляемое свойствоIdentity **не должно** использоваться в сочетании с свойствами storageAccountName или storageAccountKey

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Схема JSON, подробно описанная в предыдущем разделе, может быть использована в шаблоне менеджера ресурсов Azure для выполнения расширения пользовательского сценария во время развертывания. Приведенные ниже примеры демонстрируют использование расширения пользовательских сценариев.

* [Руководство. Развертывание расширений виртуальной машины с помощью шаблонов Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
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

В этом примере у вас есть три скрипта, которые используются для создания сервера. **КомандаToExecute** вызывает первый сценарий, затем у вас есть варианты того, как другие называются. Например, можно иметь основной скрипт, который контролирует выполнение, с правильной обработкой ошибок, журналом и управлением состоянием. Скрипты загружаются в локальную машину для запуска. Например, `1_Add_Tools.ps1` в `2_Add_Features.ps1` вызове, добавляя `.\2_Add_Features.ps1` к сценарию, и повторите `$settings`этот процесс для других сценариев, которые вы определяете в .

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
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Запуск скриптов из локальной общей папки

В этом примере можно использовать локальный sMB-сервер для определения местоположения скриптов. Делая это, вам не нужно предоставлять какие-либо другие настройки, кроме **commandToExecute.**

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Многократное выполнение пользовательского скрипта с помощью CLI

Чтобы выполнить расширение пользовательского скрипта более одного раза, должны соблюдаться следующие условия:

* Параметр **названия** расширения такой же, как и предыдущее развертывание расширения.
* Обновление конфигурации в противном случае команда не будет повторно выполнена. Можно добавить в команду динамическое свойство, например метку времени.

Кроме того, вы можете настроить свойство [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) на **истину.**

### <a name="using-invoke-webrequest"></a>Использование Запроса на веб-сайт

Если вы используете [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) в скрипте, `-UseBasicParsing` необходимо указать параметр, иначе вы получите следующую ошибку при проверке подробного состояния:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Масштабируемые наборы виртуальных машин Microsoft Azure

Для развертывания пользовательского расширения скрипта в наборе [шкалы см.](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>классические виртуальные машины;

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Для развертывания расширения пользовательских скриптов на классических вс-мизансценах можно использовать портал Azure или классические cmdlets Azure PowerShell.

### <a name="azure-portal"></a>Портал Azure

Перейдите на свой классический ресурс VM. Выберите **расширения** в **настройках.**

Нажмите **кнопку «Добавить»** и в списке ресурсов выберите **расширение пользовательского сценария.**

На странице **расширения Установить** выберите локальный файл PowerShell и заполните любые аргументы и нажмите **Ok**.

### <a name="powershell"></a>PowerShell

Использование [cmdlet Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) можно использовать для добавления расширения пользовательского сценария в существующую виртуальную машину.

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

### <a name="troubleshoot"></a>Диагностика

Данные о состоянии развертывания расширения можно получить на портале Azure, а также с помощью модуля Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Выход расширения регистрируется в файлах, найденных под следующей папкой на целевой виртуальной машине.

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

Информация о пути после первого сегмента `fileUris` URI хранится для файлов, загруженных через список свойств.  Как показано в следующей таблице, скачанные файлы распределяются по подкаталогам в соответствии со структурой значений `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Примеры скачанных файлов

| Универсальный код ресурса (URI) в fileUris | Относительное расположение скачанных файлов | Абсолютная загруженная местоположение <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Абсолютные пути каталога меняются в течение всего срока службы VM, но не в пределах одного выполнения расширения CustomScript.

### <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, вы можете связаться с экспертами Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/forums/) Можно также зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите Получить поддержку. Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
