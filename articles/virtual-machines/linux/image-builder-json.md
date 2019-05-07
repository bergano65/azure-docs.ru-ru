---
title: Создание шаблона Azure Image Builder (Предварительная версия)
description: Узнайте, как создать шаблон для использования с Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b4646879eb7eeecf41852baab7ab64e4053b05e1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159605"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Предварительный просмотр: Создание шаблона Azure Image Builder 

Azure Image Builder использует JSON-файл для передачи сведений в службу Image Builder. В этой статье мы будут рассмотрены разделы файла json, поэтому вы можете создавать свои собственные. Примеры полный JSON-файлы, см. в разделе [GitHub построитель образа Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Это формат базового шаблона:

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
        "<build timeout in minutes>": {}, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Тип и версии API

`type` — Тип ресурса, который должен быть `"Microsoft.VirtualMachineImages/imageTemplates"`. `apiVersion` Будут меняться со временем при изменении API, но должно быть `"2019-05-01-preview"` для предварительной версии.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Расположение

Расположение — регион, где будет создан пользовательский образ. Для предварительного просмотра конструктора изображений поддерживаются следующие регионы:

- Восточная часть США
- Восток США 2
- Западно-центральная часть США
- Запад США
- Западный регион США 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Зависит от (необязательно)

В этом дополнительном разделе позволяют убедиться, что зависимости завершены перед продолжением. 

```json
    "dependsOn": [],
```

Дополнительные сведения см. в разделе [определить зависимости ресурса](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Удостоверение
По умолчанию поддерживает Image Builder с помощью скриптов или копирования файлов из нескольких расположений, таких как GitHub и служба хранилища Azure. Чтобы использовать их, они должны быть общедоступным.

Также можно Azure User-Assigned управляемого удостоверения определенные пользователем, на доступ к Image Builder хранилища Azure, до тех пор, пока удостоверение имеет как минимум «Модуль чтения данных больших двоичных объектов хранилища» в учетной записи хранения Azure. Это означает, что необходимо сделать большие двоичные объекты хранилища доступными извне, или токены SAS установки.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Полный пример см. в разделе [ использование управляемого удостоверения Azure User-Assigned для доступа к файлам в службе хранилища Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Изображение поддержка построителя для назначаемого пользователем удостоверения: • поддерживает единую идентификацию только • не поддерживает пользовательские доменные имена

Дополнительные сведения см. в разделе [что такое управляемые удостоверения для ресурсов Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Дополнительные сведения о развертывании этой функции см. в разделе [Настройка управляемых удостоверений для ресурсов Azure на виртуальной Машине Azure с помощью Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Свойства: источника

`source` Раздел содержит сведения о образа источника, который будет использоваться Image Builder.

API требует «SourceType», который определяет источник для сборки образа, в настоящее время существует три типа:
- ISO — используется, если источником является RHEL ISO.
- PlatformImage - сообщил, что исходное изображение остается образа Marketplace.
- ManagedImage - использования это при запуске из регулярного управляемого образа.
- SharedImageVersion - используется, если при использовании версии образа в коллекции образов Shared как источник.

### <a name="iso-source"></a>Источник ISO

Azure Image Builder поддерживает только с помощью опубликованных Red Hat Enterprise Linux 7.x двоичных DVD-диска ISO, для предварительной версии. Image Builder поддерживает:
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Чтобы получить `sourceURI` и `sha256Checksum` значения, перейдите к `https://access.redhat.com/downloads` выберите продукт **Red Hat Enterprise Linux**и поддерживаемой версии. 

В списке **установщики и изображения для Red Hat Enterprise Linux Server**, необходимо скопировать ссылку на двоичные DVD-диска Red Hat Enterprise Linux 7.x и контрольная сумма.

> [!NOTE]
> Маркеры доступа из ссылок, обновляются с достаточно частым интервалом, поэтому каждый раз, вы хотите отправить шаблон, необходимо выполнять проверку компоновке RH адрес изменился.
 
### <a name="platformimage-source"></a>Источник PlatformImage 
Azure Image Builder поддерживает следующие образы Azure Marketplace:
* Ubuntu 18.04
* Ubuntu 16.04.
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


Здесь свойства совпадают, которые используются для создания Виртуальных машин с помощью CLI AZ, запустите ниже, чтобы получить свойства: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Версия не может быть «latest», необходимо использовать приведенной выше команде, чтобы получить номер версии. 

### <a name="managedimage-source"></a>Источник ManagedImage

Задает исходное изображение как существующий управляемый образ обобщенной виртуальной Машины или виртуального жесткого диска. Исходный управляемый образ должен быть из поддерживаемых операционных Системах и находиться в том же регионе, что шаблон Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` Должно быть ResourceId управляемого образа. Используйте `az image list` Чтобы получить список доступных образов.


### <a name="sharedimageversion-source"></a>Источник SharedImageVersion
Задает исходный образ существующей версии образа в коллекции образов Shared. Версия образа должна быть из поддерживаемых операционных Системах и изображение должно быть реплицировано на том же регионе, что шаблон Azure Image Builder. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` Должен быть идентификатор ресурса версии образа. Используйте [списка версию образа sig az](/cli/azure/sig/image-version#az-sig-image-version-list) для версии образа.

## <a name="properties-customize"></a>Свойства: Настройка


Image Builder поддерживает несколько «настройщики». Настройщики являются функциями, которые используются для настройки образа, например выполнение скриптов или перезагрузке серверов. 

При использовании `customize`: 
- Можно использовать несколько настройщики, но они должны иметь уникальный `name`.
- Настройщики выполняются в порядке, указанном в шаблоне.
- Если один настройщика завершается ошибкой, то весь настройки компонента не удастся и обратно сообщение об ошибке.
- Рассмотрите возможность сколько времени требуется образ сборки и измените свойство «buildTimeoutInMinutes», чтобы предоставить достаточно времени для завершения конструктора изображений.
- Настоятельно рекомендуется тщательно проверьте сценарий перед его использованием в шаблоне. Отладка сценария на виртуальной Машине будет проще.
- Не следует помещать конфиденциальные данные в скриптах. 
- Расположение сценария должны быть общедоступным, если вы не используете [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
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

 
В разделе Настройка является массивом. Azure Image Builder будет выполняться через настройщики в последовательном порядке. Любой сбой в любой настройщика произойдет сбой процесса построения. 
 
 
### <a name="shell-customizer"></a>Пользовательские оболочки

Поддерживает пользовательские оболочки, на выполнение сценариев оболочки, они должны быть общедоступными для IB для доступа к ним.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
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
 
Настроить свойства:

- **Тип** — оболочки 
- **имя** — имя для отслеживания настройки 
- **scriptUri** -URI расположения файла 
- **встроенный** -массив команд оболочки, разделенных запятыми.
 
> [!NOTE]
> При запуске оболочки настройщика с источником RHEL ISO, необходимо обеспечить дескрипторам оболочки первой настройки, регистрация на сервере право Red Hat, перед выполнением какой-либо настройки. После завершения настройки сценария необходимо отменить регистрацию на сервере назначения.

### <a name="windows-restart-customizer"></a>Windows перезапустите настройщика 
Настройщика перезапуска позволяет перезапустить виртуальную Машину Windows и дождаться его серьезная авария, это позволяет устанавливать программное обеспечение, требующее перезагрузки.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Поддержка ОС:  Windows
 
Настроить свойства:
- **Тип:** WindowsRestart
- **restartCommand** -команда для выполнения перезагрузки (необязательно). Значение по умолчанию — `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** — команду, чтобы проверить успешность перезапуска (необязательно). 
- **restartTimeout** -перезапустите времени ожидания, указанное в виде строки величина и единица. Например `5m` (5 минут) или `2h` (2 часа). По умолчанию используется: "5 мин"


### <a name="powershell-customizer"></a>PowerShell настройщика 
Оболочка настройщика поддерживает выполнение скриптов PowerShell и встроенные команды, скрипты должен быть общедоступным для IB для доступа к ним.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Поддержка ОС: Windows и Linux

Настроить свойства:

- **Тип** — PowerShell.
- **scriptUri** -URI расположения, файла сценария PowerShell. 
- **встроенный** — встроенные команды, которые выполняются, разделенных запятыми.
- **valid_exit_codes** — необязательно, команду допустимых кодов, которые могут быть возвращены из скрипта или в коде, это позволит избежать сообщаемые сбоя команды сценария или встроенная.

### <a name="file-customizer"></a>Файл настройщика

Файл настройщика позволяет построитель образа загрузки файла из GitHub или в хранилище Azure. Если у вас есть конвейер сборки образа, который зависит от артефактов сборки, можно затем задать настройщика файл для загрузки из папки сборки и переместить артефакты в образ.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Поддержка ОС: Linux и Windows 

Пользовательские свойства файла:

- **sourceUri** -конечную точку хранилища доступны, это может быть хранилище Azure или GitHub. Вы можете скачать только один файл, а не весь каталог. Если вам нужно загрузить каталог, сжатый файл, а затем распаковать его с помощью настройщики оболочки или PowerShell. 
- **Назначение** — это назначения полный путь и имя файла. Любой указанный путь и подкаталогах должны существует, используйте настройщики оболочки или PowerShell для настройки этих заранее. Настройщики скрипт можно использовать для создания пути. 

Это поддерживается Windows каталогов и путей к Linux, но существуют некоторые различия: 
- ОС Linux — единственный способ построитель можно записать образ является/TMP.
- Windows — без ограничений пути, но путь должен существовать.
 
 
Если возникает ошибка пытается загрузить файл или поместить его в указанном каталоге, Настройка шаг завершится сбоем, и он будет иметь customization.log.

Файлы в настройщика файл можно загрузить из хранилища Azure с помощью [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalize 
По умолчанию Azure Image Builder также будет выполняться код «отзыв» в конце каждого этапа настройки образа, чтобы обобщить образ. Обобщение — это процесс, где изображение настройки, чтобы он может быть использован для создания нескольких виртуальных машин. На виртуальных машинах Windows Azure Image Builder использует Sysprep. Для Linux, выполняется Azure Image Builder "waagent-deprovision". 

Команды Image Builder пользователей, чтобы подготовить к работе, не стоит применять для каждого случая, поэтому Azure Image Builder позволит вам настроить эта команда, при необходимости. 

Если вы переносите существующие настройки, и вы используете разные команды Sysprep/waagent, можно с помощью команд универсального Image Builder и если происходит сбой создания виртуальной Машины, используйте собственные команды Sysprep или waagent.

Если построитель образа Azure создает пользовательский образ Windows успешно и вы создаете виртуальную Машину из его, а затем найти Сбой создания виртуальной Машины, или не выполнена, необходимо будет в документации по Windows Server Sysprep или инициировать запрос на поддержку с помощью Поддержка для служб Windows Server Sysprep клиента группой, которая устранения связанных с информацией о правильное использование программы Sysprep.


#### <a name="default-sysprep-command"></a>По умолчанию команды Sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Команда отзыва Linux по умолчанию

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Переопределения команд
Чтобы переопределить команды, используйте виртуальную сценария PowerShell или оболочки для создания командных файлов с точное имя файла и поместить их в соответствующие папки:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder будет считывать эти команды, они записываются в журналы AIB «customization.log». См. в разделе [Устранение неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) о том, как собирать журналы.
 
## <a name="properties-distribute"></a>Свойства: распространение

Azure Image Builder поддерживает три задачи распространения: 

- **managedImage** — управляемый образ.
- **sharedImage** -общие коллекции образов.
- **Виртуальный жесткий ДИСК** -виртуального жесткого диска в учетную запись хранения.

Вы можете распространить образ на обоих типов целевого объекта в той же конфигурации, см. статью [примеры](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Так как имеется несколько целевых расположений для распространения Image Builder поддерживает свое состояние для каждого целевого объекта распространения, который может осуществляться путем запроса `runOutputName`.  `runOutputName` Является объект, можно выполнить запрос post распространения, сведения о дистрибутива. Например мог запрашивать расположение виртуального жесткого диска или регионах, где было реплицировано версию образа. Это свойство для каждого целевого объекта распространения. `runOutputName` Должно быть уникальным для каждой целевой рассылки.
 
### <a name="distribute-managedimage"></a>Распространение: managedImage

Вывод изображения будет ресурс управляемого образа.

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
 
Распространение свойств:
- **Тип** – managedImage 
- **imageId** — идентификатор ресурса конечного изображения, требуется формат: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **расположение** -расположение управляемого образа.  
- **runOutputName** — уникальное имя для идентификации распределение.  
- **artifactTags** -Необязательное пользовательское указано значение ключа пары тегов.
 
 
> [!NOTE]
> Конечная группа ресурсов должна существовать.
> Если требуется, чтобы изображение, распределенных в другом регионе, это увеличит время развертывания. 

### <a name="distribute-sharedimage"></a>Распространение: sharedImage 
Коллекции образов Azure Shared — это новая служба управления образами, который позволяет управлять репликации область изображения, управление версиями и совместное использование пользовательских образов. Azure Image Builder поддерживает распространение с помощью этой службы, их распространение образов для регионов, поддерживаемых галерей изображений Shared. 
 
Коллекция изображений общего состоит из: 
 
- Коллекция — контейнер для нескольких общих образов. Коллекция развертывается в одном регионе.
- Определения образа - концептуальной группирования для изображений. 
- Версии образов — это тип образа для развертывания виртуальной Машины или масштабируемого набора. Версии образов можно реплицировать в другие регионы, где должны быть развернуты виртуальные машины.
 
Прежде чем можно передавать коллекции образов, необходимо создать определение изображения и коллекции, см. в разделе [общие образы](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Распространение свойства для общего образа коллекции:

- **Тип** -sharedImage  
- **galleryImageId** — идентификатор коллекции общих образов. Формат: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** — уникальное имя для идентификации распределение.  
- **artifactTags** -Необязательное пользовательское указано значение ключа пары тегов.
- **replicationRegions** -массив областей для репликации. Один из регионов, должен быть регион, где развертывается коллекции.
 
> [!NOTE]
> Azure Image Builder можно использовать в другом регионе в коллекции, но служба Azure Image Builder потребуется для передачи изображения между центрами обработки данных, а это займет больше времени. Image Builder будет автоматически версии изображение, на основании является монотонной целым числом, невозможно указать для них в данный момент. 

### <a name="distribute-vhd"></a>Распределение. VHD  
Можно выводить на виртуальный жесткий диск. Затем можно скопировать виртуальный жесткий ДИСК и использовать его для публикации в Azure MarketPlace, или использовать с Azure Stack.  

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

Распространение параметров виртуального жесткого диска:

- **Тип** -виртуального жесткого диска.
- **runOutputName** — уникальное имя для идентификации распределение.  
- **теги** -Необязательное пользовательское указано значение ключа пары тегов.
 
Azure Image Builder не позволяет пользователю указать расположение учетной записи хранения, но вы можете запросить сведения о состоянии `runOutputs` для получения расположения.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> После создания виртуального жесткого диска, скопируйте его в другое расположение, как можно скорее. Виртуальный жесткий ДИСК хранится в учетной записи хранения в группе временных ресурсов, созданной при отправке шаблона образа в службу Azure Image Builder. Если удалить образ шаблона, вы потеряете виртуального жесткого диска. 
 
## <a name="next-steps"></a>Дальнейшие действия

Есть пример JSON-файлы для разных сценариев в [GitHub построитель образа Azure](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
