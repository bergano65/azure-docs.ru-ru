---
title: Создание шаблона шаблона «Создатель изображений» (предварительный просмотр)
description: Узнайте, как создать шаблон для использования с помощью Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246795"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Предварительный просмотр: Создание шаблона a-образного строителя 

Azure Image Builder использует файл .json для передачи информации в службу Image Builder. В этой статье мы будем переходить разделы файла Json, так что вы можете построить свой собственный. Для просмотра примеров полных файлов [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).json см.

Это основной формат шаблона:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Тип и API версия

Это `type` тип ресурса, который `"Microsoft.VirtualMachineImages/imageTemplates"`должен быть. Со `apiVersion` временем изменения будут меняться по `"2019-05-01-preview"` мере изменения API, но должны быть для предварительного просмотра.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Расположение

Местоположение — это область, в которой будет создано пользовательское изображение. Для предварительного просмотра «Строитель изображений» поддерживаются следующие регионы:

- Восточная часть США
- восточная часть США 2
- центрально-западная часть США
- западная часть США
- западная часть США 2
- Северная Европа
- Западная Европа


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
По умолчанию Image Builder будет использовать VM сборки "Standard_D1_v2", вы можете переопределить это, например, если вы хотите настроить изображение для GPU VM, вам нужен размер GPU VM. Водить описание не обязательно.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

По умолчанию Image Builder не будет изменять размер изображения, он будет использовать размер исходного изображения. Вы можете увеличить размер диска ОС (Win и Linux), это необязательно, а значение 0 означает оставить тот же размер, что и исходное изображение. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Если вы не укажете свойства VNET, то Image Builder создаст свой собственный VNET, Public IP и NSG. Общественный IP используется для связи службы с сборкой VM, однако, если вы не хотите, чтобы public IP или хотите, чтобы Image Builder был доступом к существующим ресурсам VNET, таким как серверы конфигурации (DSC, Chef, Puppet, Ansible), файлы и т.д. , затем вы можете указать VNET. Для получения дополнительной информации просмотрите [сетевую документацию,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)это необязательно.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Теги

Это пары ключей/значений, которые можно указать для созданного изображения.

## <a name="depends-on-optional"></a>Зависит от (необязательно)

Этот дополнительный раздел может быть использован для обеспечения завершения зависимостей до начала работы. 

```json
    "dependsOn": [],
```

Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)

## <a name="identity"></a>идентификации
По умолчанию Image Builder поддерживает использование скриптов или копирование файлов из нескольких мест, таких как GitHub и Хранилище Azure. Чтобы использовать их, они должны быть общедоступными.

Вы также можете использовать определенную вами управляемую идентификацию Azure User- Assigned, чтобы разрешить доступ к хранилищем Azure Storage Image Builder при только идентификационный элемент получил минимум "Хранение данных Blob Reader" в учетной записи хранения Azure. Это означает, что вам не нужно делать капли хранения внешне доступными, или настроить токены SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Для полного примера можно просмотреть для [доступа к файлам в Хранилище Azure.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)

Поддержка image Builder для удостоверения личности, назначенной пользователем: - Поддерживает только единую идентификацию - не поддерживает пользовательские доменные имена

Чтобы узнать больше, смотрите [Что управляется идентификаторами для ресурсов Azure?.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
Для получения дополнительной информации о развертывании этой функции см. [Наверстные управляемые идентификаторы для ресурсов Azure в Azure VM с помощью Azure CLI.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)

## <a name="properties-source"></a>Свойства: источник

Раздел `source` содержит информацию об исходном изображении, которое будет использоваться Image Builder.

API требует 'SourceType', который определяет источник для сборки изображений, в настоящее время существует три типа:
- PlatformImage - указано исходное изображение является изображением Marketplace.
- ManagedImage - используйте это при запуске с обычного управляемого изображения.
- SharedImageVersion - это используется при использовании версии изображений в общей галерее изображений в качестве источника.

### <a name="iso-source"></a>Источник ИСО
Мы deprecating эту функциональность от строителя изображения, по мере того как теперь [RHEL принесите ваши собственные изображения подписки,](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)пожалуйста просмотрите timelines ниже:
    * 31 марта 2020 г. - Шаблоны изображений с источниками RHEL ISO теперь будут больше приниматься поставщиком ресурсов.
    * 30 апреля 2020- Шаблоны изображений, содержащие источники RHEL ISO, больше не будут обрабатываться.

### <a name="platformimage-source"></a>Источник PlatformImage 
Azure Image Builder поддерживает Windows Server и клиента, а также linux Azure Marketplace изображения, смотрите [здесь](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) для полного списка. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Свойства здесь те же, которые используются для создания VM, используя A CLI, запустить ниже, чтобы получить свойства: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Вы можете использовать "последний" в версии, версия оценивается, когда сборка изображения происходит, а не когда шаблон представлен. Если вы используете эту функциональность с местом назначения Shared Image Gallery, вы можете избежать повторной отправки шаблона и повторного запуска изображения с интервалами, так что ваши изображения воссоздаются из последних изображений.

### <a name="managedimage-source"></a>Управляемый источник Изображения

Устанавливает исходное изображение как существующее управляемое изображение обобщенных VHD или VM. Исходное изображение должно быть поддерживаемой ОС и находиться в том же регионе, что и шаблон Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Ресурс `imageId` должен быть ResourceId управляемого изображения. Используйте `az image list` для списка доступных изображений.


### <a name="sharedimageversion-source"></a>Источник SharedImageVersion
Устанавливает исходное изображение в существующую версию изображения в общей галерее изображений. Версия изображения должна быть поддерживаемой ОС, а изображение должно быть воспроизведено в той же области, что и шаблон Azure Image Builder. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Должна `imageVersionId` быть ResourceId версии изображения. Используйте [список изображений-версий az sig](/cli/azure/sig/image-version#az-sig-image-version-list) для списка версий изображений.

## <a name="properties-buildtimeoutinminutes"></a>Свойства: buildTimeoutInMinutes

По умолчанию Image Builder будет работать в течение 240 минут. После этого, он будет тайм-аут и остановить, независимо от того, является ли изображение сборки завершена. Если тайм-аут хит, вы увидите ошибку, похожую на это:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Если вы не укажете значение сборкиTimeoutInMinutes или установите его до 0, это будет использовать значение по умолчанию. Вы можете увеличить или уменьшить значение, до максимума 960mins (16hrs). Для Windows, мы не рекомендуем устанавливать это ниже 60 минут. Если вы обнаружите, что вы удара тайм-аут, просмотрите [журналы,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)чтобы увидеть, если шаг настройки ждет что-то вроде пользовательского ввода. 

Если вы обнаружите, что вам нужно больше времени для настройки для завершения, установите это на то, что вы думаете, что вам нужно, с небольшими накладными расходами. Но, не устанавливайте его слишком высоко, потому что вы, возможно, придется ждать его тайм-аут, прежде чем увидеть ошибку. 


## <a name="properties-customize"></a>Свойства: настроить

Image Builder поддерживает несколько "настраивателей". Настраивающие функции — это функции, которые используются для настройки изображения, такие как запуск скриптов или перезагрузка серверов. 

При `customize`использовании: 
- Вы можете использовать несколько настраивателей, `name`но они должны иметь уникальный.
- Настраиваемые выполняются в порядке, указанном в шаблоне.
- Если один настраиватель не удается, то весь компонент настройки выйдет из строя и сообщит об ошибке.
- Настоятельно рекомендуется тщательно протестировать сценарий перед его использованием в шаблоне. Отладка скрипта на собственном VM будет проще.
- Не помещайте конфиденциальные данные в скрипты. 
- Местоположение скриптов должно быть общедоступным, если вы не используете [MSI.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
Настраиваемый раздел представляет собой массив. Azure Image Builder будет работать через настройки в последовательном порядке. Любой сбой в любом настраивателе не сможет выполнить процесс сборки. 
 
 
### <a name="shell-customizer"></a>Настройка оболочки

Настраиваемый оболочки поддерживает запущенные скрипты оболочки, они должны быть общедоступны для ДОСТУПА к ним IB.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Поддержка ОС: Linux 
 
Настройка свойств:

- **тип** - Shell 
- **имя** - имя для отслеживания настройки 
- **scriptUri** - URI к расположению файла 
- **inline** - массив команд оболочки, разделенных запятыми.
- **sha256Checksum** - Значение проверки sha256 файла, вы генерируете это локально, а затем Image Builder будет проверять и проверять.
    * Для создания sha256Checksum, используя терминал на Mac/Linux:`sha256sum <fileName>`


Для того, чтобы команды запускались с супер привилегиями пользователя, они должны быть `sudo`прикрепллены.

> [!NOTE]
> При запуске настраивателя оболочки с источником RHEL ISO необходимо обеспечить регистрацию первых ручек оболочки на сервер Red Hat до получения какой-либо настройки. После завершения настройки скрипт должен быть отрегистррован на сервере прав.

### <a name="windows-restart-customizer"></a>Настройка перезагрузки Windows 
Настройка перезагрузки позволяет перезапустить Windows VM и ждать его возвращения в Интернете, это позволяет установить программное обеспечение, которое требует перезагрузки.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Поддержка ОС: Windows
 
Настройка свойств:
- **Тип**: WindowsRestart
- **restartCommand** - Команда для выполнения перезагрузки (необязательно). Значение по умолчанию — `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **перезапускCheckCommand** - Команда, чтобы проверить, если перезагрузка удалось (необязательно). 
- **перезапускTimeout** - Перезапуск тайм-аута, указанный как строка величины и единицы. Например, `5m` (5 минут) или `2h` (2 часа). По умолчанию: '5m'

### <a name="linux-restart"></a>Перезагрузка Linux  
Существует не Linux перезагрузки настройки, однако, если вы устанавливаете драйверы, или компоненты, которые требуют перезагрузки, вы можете установить их и вызвать перезагрузку с помощью настройки Shell, есть 20min SSH тайм-аут для сборки VM.

### <a name="powershell-customizer"></a>Настройка PowerShell 
Настраивающий оболочку поддерживает запуск скриптов PowerShell и внее команды, скрипты должны быть общедоступны для ДОСТУПА к ним IB.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Поддержка ОС: Windows и Linux

Настройка свойств:

- **тип** - PowerShell.
- **scriptUri** - URI к расположению файла сценария PowerShell. 
- **вонлайн** — Внеочередные команды, которые должны быть запущены, разделенные запятыми.
- **действительныйКоды -** Дополнительные, действительные коды, которые могут быть возвращены из команды скрипта/входящие, это позволит избежать зарегистрированного сбоя команды скрипта/входящие.
- **runElevated** - Необязательный, boolean, поддержка выполнения команд и скриптов с повышенными разрешениями.
- **sha256Checksum** - Значение проверки sha256 файла, вы генерируете это локально, а затем Image Builder будет проверять и проверять.
    * Для генерации sha256Checksum, с помощью PowerShell на Windows [Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Настраиваемый файл

Настройка файлов позволяет строитель изображений загружать файл из хранилища GitHub или Azure. Если у вас есть конвейер сборки изображений, который опирается на артефакты сборки, можно настроить настраиватель файлов для загрузки из доли сборки и переместить артефакты в изображение.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Поддержка ОС: Linux и Windows 

Свойства настраиваемых файлов:

- **sourceUri** - доступная конечная точка хранения, это может быть GitHub или Хранилище Azure. Вы можете скачать только один файл, а не весь каталог. Если вам нужно скачать каталог, используйте сжатый файл, а затем распаковать его с помощью настройки Shell или PowerShell. 
- **пункт назначения** – это полный путь назначения и имя файла. Любой ссылки путь и субдиректоров должны существовать, использовать Shell или PowerShell настройки для настройки этих заранее. Настройки скрипта можно использовать для создания пути. 

Это поддерживается каталогами Windows и linux пути, но Есть некоторые различия: 
- Linux OS's - единственный путь Image builder может написать на это / tmp.
- Windows - Нет ограничения пути, но путь должен существовать.
 
 
При ошибке, при попытке загрузить файл или поместить его в указанный каталог, шаг настройки не сработает, и это будет в customization.log.

> [!NOTE]
> Настраиватель файлов подходит только для небольших загрузок файлов, < 20МБ. Для больших загрузок файлов используйте скрипт или внеочередную команду, `curl`используйте `Invoke-WebRequest`код для загрузки файлов, таких как Linux `wget` или Windows, .

Файлы в настройке файлов можно загрузить из хранилища Azure с помощью [MSI.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)

### <a name="windows-update-customizer"></a>Настройка обновления Windows
Этот настраиватель построен на [сообществе Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) для Packer, который является проектом с открытым исходным кодом, поддерживаемым сообществом Packer. Корпорация Майкрософт тестирует и проверяет поставщик с помощью службы Image Builder и будет поддерживать изучение проблем с ним и работать над устранением проблем, однако проект с открытым исходным кодом официально не поддерживается корпорацией Майкрософт. Для получения подробной документации и помощи в предоставлении обновлений Windows, пожалуйста, ознакомьтесь с репозиторием проекта.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Поддержка ОС: Windows

Настройка свойств:
- **тип** - WindowsUpdate.
- **searchCriteria** - Необязательный, определяет, какой тип обновлений установлен (Рекомендуемые, Важные и т.д.), BrowseOnly '0 и IsInstalled '0 (Рекомендуется) является по умолчанию.
- **фильтры** - Необязательно, позволяет указать фильтр для включения или исключения обновлений.
- **updateLimit** - Необязательно, определяет, сколько обновлений может быть установлено, по умолчанию 1000.
 
 

### <a name="generalize"></a>Generalize 
По умолчанию Azure Image Builder также запустит код «дезавуирования» в конце каждой фазы настройки изображения, чтобы «обобщить» изображение. Обобщение — это процесс, в котором настроено изображение, чтобы его можно было повторно использовать для создания нескольких VMS. Для Вымотворения Windows Azure Image Builder использует Sysprep. Для Linux, Azure Image Builder запускает 'waagent-deprovision'. 

Команды пользователей Image Builder для обобщения могут быть неподходящими для любой ситуации, поэтому Azure Image Builder позволит вам настроить эту команду, если это необходимо. 

Если вы мигрируете существующей настройки, и вы используете различные sysprep / waagent команды, вы можете использовать Image Builder общие команды, и если создание VM не удается, используйте свои собственные sysprep или waagent команды.

Если Azure Image Builder успешно создает пользовательское изображение Windows и создаете из него VM, а затем обнаружите, что создание VM не завершено или не завершено успешно, вам нужно будет просмотреть документацию Windows Server Sysprep или поднять запрос на поддержку с Команда службы поддержки клиентских служб Windows Server Sysprep, которая может устранить неполадки и проконсультировать по правильному использованию Sysprep.


#### <a name="default-sysprep-command"></a>Команда Sysprep по умолчанию
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Команда де-положения Linux по умолчанию

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Преодоление команд
Чтобы переопределить команды, используйте предостережек скриптов PowerShell или Shell для создания командных файлов с точным именем файла и поместите их в правильные каталоги:

* Windows: c:'DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder будет читать эти команды, они выписаны в журналы AIB, 'customization.log'. Смотрите [устранение неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) о том, как собирать журналы.
 
## <a name="properties-distribute"></a>Свойства: распространять

Azure Image Builder поддерживает три цели распределения: 

- **managedImage** - управляемый образ.
- **sharedImage** - Общая фотогалерея.
- **VHD** - VHD в учетной записи хранилища.

Изображение можно распределить по обоим целевым типам в одной и той же конфигурации, пожалуйста, смотрите [примеры.](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)

Поскольку у вас может быть несколько целей для распространения, Image Builder поддерживает состояние для `runOutputName`каждой цели распределения, к которым можно получить доступ, задав запрос.  Объект, `runOutputName` который можно запросить распространение публикации для получения информации об этом распределении. Например, можно запросить местоположение VHD или регионов, в которых была реплицирована версия изображения, или создана версия SIG Image. Это свойство каждой цели распределения. Она `runOutputName` должна быть уникальной для каждой цели распределения. Вот пример, это запрос общего распределения галереи изображений:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Выходные данные:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Распространение: управляемыйИзображение

Выход изображения будет управляемым ресурсом изображения.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Распределить свойства:
- **тип** - управляемыйИзображение 
- **imageId** - Идентификатор ресурса изображения назначения, ожидаемый формат: /подписка/подписка/ подписка/>/ресурсгруппы/направлениеResourceGroupName\<\<>/провайдеры/Microsoft.Compute/images/imageName\<>
- **местоположение** - расположение управляемого изображения.  
- **runOutputName** - уникальное название для определения дистрибутива.  
- **artifactTags** - Необязательный пользователь указал теги ключевых значений.
 
 
> [!NOTE]
> Группа ресурсов назначения должна существовать.
> Если необходимо, чтобы изображение было распределено в другой регион, это увеличит время развертывания. 

### <a name="distribute-sharedimage"></a>Распространение: sharedImage 
Общая галерея изображений Azure — это новая служба управления изображениями, которая позволяет управлять репликацией, версиями и общим ими пользовательских изображений. Azure Image Builder поддерживает распространение с помощью этой службы, так что вы можете распространять изображения в регионах, поддерживаемых общими галереями изображений. 
 
Общая фотогалерея состоит из: 
 
- Галерея - Контейнер для нескольких общих изображений. Галерея развернута в одном регионе.
- Определения изображений - концептуальная группировка для изображений. 
- Версии изображений - это тип изображения, используемый для развертывания VM или набора масштаба. Версии изображений могут быть воспроизведены в других регионах, где необходимо развертывать ВМ.
 
Прежде чем вы сможете распространить в Image Gallery, необходимо создать галерею и определение изображения, [см.](shared-images.md) 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Распределить свойства для общих галерей изображений:

- **тип** - sharedImage  
- **galleryImageId** - Идентификатор общей галереи изображений. \<Формат: /подписка/подпискаId\<>/resourceGroups/resourceGroupname>/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName\<>/images/ imageGalleryName>.
- **runOutputName** - уникальное название для определения дистрибутива.  
- **artifactTags** - Необязательный пользователь указал теги ключевых значений.
- **репликацияРегионы** - массив регионов для репликации. Одним из регионов должен быть регион, где развернута Галерея.
 
> [!NOTE]
> В другой области можно использовать систему «Азово-образный разработка», но служба Azure Image Builder должна будет перенести изображение между центрами обработки данных, и это займет больше времени. Image Builder будет автоматически версия изображения, на основе монотонического integer, вы не можете указать его в настоящее время. 

### <a name="distribute-vhd"></a>Распространение: VHD  
Вы можете выйти на VHD. Затем можно скопировать VHD и использовать его для публикации в Azure MarketPlace или использовать с azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Поддержка ОС: Windows и Linux

Распределить параметры VHD:

- **тип** - VHD.
- **runOutputName** - уникальное название для определения дистрибутива.  
- **теги** - Необязательный пользователь указал ключевые теги пары значений.
 
Azure Image Builder не позволяет пользователю указать местоположение учетной записи `runOutputs` хранилища, но можно задать запрос о статусе, чтобы получить местоположение.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Как только VHD был создан, скопируйте его в другое место, как можно скорее. VHD хранится в учетной записи хранилища во временной группе ресурсов, созданной при отправке шаблона изображения в службу Azure Image Builder. Если вы удалите шаблон изображения, то вы потеряете VHD. 
 
## <a name="next-steps"></a>Дальнейшие действия

В [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder)есть примеры файлов .json для различных сценариев.
 
