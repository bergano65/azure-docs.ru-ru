---
title: Обработчик расширения Desired State Configuration в Azure | Документы Майкрософт
description: С помощью расширения DSC вы можете отправлять и применять конфигурацию DSC PowerShell к виртуальной машине Azure.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 9f81e2b7537a5ecc6778baa93a1bab23dd30ff8a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451915"
---
# <a name="powershell-dsc-extension"></a>Расширение PowerShell DSC

## <a name="overview"></a>Обзор

Расширение DSC PowerShell для Windows публикуется и поддерживается корпорацией Майкрософт. Расширение отправляет и применяет конфигурацию DSC PowerShell к виртуальной машине Azure. Расширение DSC вызывает DSC PowerShell, чтобы применить полученную конфигурацию DSC к виртуальной машине. В этом документе подробно описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины DSC для Windows.

## <a name="prerequisites"></a>Технические условия

### <a name="operating-system"></a>Операционная система

Расширение DSC поддерживает следующие операционные системы:

Windows Server 2019 г., Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1, клиент Windows 7, 8.1, 10

### <a name="internet-connectivity"></a>Подключение к Интернету

Расширение DSC для Windows требует, что целевая виртуальная машина может взаимодействовать с Azure и расположение пакета конфигурации (ZIP-файл), если они хранятся в месте за пределами Azure. 

## <a name="extension-schema"></a>Схема расширения

В приведенном ниже JSON-файле показана схема для части settings расширения DSC в шаблоне Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2018-10-01 | дата |
| publisher | Microsoft.Powershell.DSC | строка |
| Тип | DSC | строка |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Значения свойства параметров

| ИМЯ | Тип данных | ОПИСАНИЕ
| ---- | ---- | ---- |
| settings.wmfVersion | строка | Указывает версию Windows Management Framework, которую необходимо установить на виртуальной машине. Если задать для этого свойства значение latest, будет установлена последняя версия Windows Management Framework. Для этого свойства доступны только такие значения: 4.0, 5.0 и latest. Возможные значения зависят от обновлений. По умолчанию используется значение latest. |
| settings.configuration.url | строка | Указывает URL-адрес расположения, из которого можно скачать ZIP-файл конфигурации DSC. Если для доступа к предоставленному URL-адресу требуется маркер SAS, для свойства protectedSettings.configurationUrlSasToken будет необходимо задать значение маркера SAS. Это свойство обязательное, если заданы свойства settings.configuration.script и (или) settings.configuration.function.
| settings.configuration.script | строка | Указывает имя файла скрипта, содержащего определение вашей конфигурации DSC. Этот скрипт должен находиться в корневом каталоге ZIP-файла, скачанного по URL-адресу, указанному в свойстве configuration.url. Это свойство обязательное, если заданы свойства settings.configuration.url и (или) settings.configuration.script.
| settings.configuration.function | строка | Указывает имя вашей конфигурации DSC. Указанную конфигурацию необходимо добавить в скрипт, заданный в свойстве configuration.script. Это свойство обязательное, если заданы свойства settings.configuration.url и (или) settings.configuration.function.
| settings.configurationArguments | Коллекция | Определяет параметры, которые необходимо передать в конфигурацию DSC. Это свойство не будет зашифровано.
| settings.configurationData.url | строка | Указывает URL-адрес расположения, из которого можно скачать файл данных конфигурации (в формате PDS1), используемый в качестве входных данных для вашей конфигурации DSC. Если для доступа к предоставленному URL-адресу требуется маркер SAS, для свойства protectedSettings.configurationDataUrlSasToken будет необходимо задать значение маркера SAS.
| settings.privacy.dataEnabled | строка | Включает или отключает сбор данных телеметрии. Для этого свойства доступны только такие значения: Enable, Disable, '' или $null. Если для этого свойства не задано значение или задано значение NULL, сбор данных телеметрии будет выполняться.
| settings.advancedOptions.forcePullAndApply | Bool | Этот параметр предназначен для улучшения работы с расширением регистрацию узлов с помощью DSC службы автоматизации Azure.  Если значение равно `$true`, расширение будет ожидать первого выполнения конфигурации берется из службы перед возвратом об успехе или сбое.  Если значение задано значение $false, состояние, возвращаемое расширение будет ссылаться только на ли узел с помощью настройки состояния службы автоматизации Azure успешно зарегистрирован и конфигурации узла не будет выполнено во время регистрации.
| settings.advancedOptions.downloadMappings | Коллекция | Определяет альтернативные расположения для скачивания зависимостей, таких как WMF и .NET.

### <a name="protected-settings-property-values"></a>Значения свойств защищенных параметров

| ИМЯ | Тип данных | ОПИСАНИЕ
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | строка | Определяет параметры, которые необходимо передать в конфигурацию DSC. Это свойство будет зашифровано. |
| protectedSettings.configurationUrlSasToken | строка | Указывает маркер SAS для доступа к URL-адресу, определенному в свойстве configuration.url. Это свойство будет зашифровано. |
| protectedSettings.configurationDataUrlSasToken | строка | Указывает маркер SAS для доступа к URL-адресу, определенному в свойстве configurationData.url. Это свойство будет зашифровано. |


## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager.
Шаблоны идеально подходят для развертывания одной или нескольких виртуальных машин, требующих настройки после развертывания.
Пример шаблона Resource Manager, который включает в себя расширение DSC для Windows можно найти на [коллекции быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также использовав Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Пакет расширения скачивается и развертывается в этом расположении на виртуальной машине Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Файл состояния расширения содержит вложенное состояние и успех или ошибка коды состояния, а также подробные сведения об ошибке и описание для каждого выполнения расширения.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Журналы выходных данных расширения регистрируются в следующем каталоге:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Коды ошибок и их описание

| Код ошибки | Значение | Возможное действие |
| :---: | --- | --- |
| 1000 | Общая ошибка | Сообщение об этой ошибке выдается при возникновении конкретного исключения и приводится в журналах расширения. |
| 52 | Ошибка установки расширения | Сообщение об этой ошибке выдается при возникновении конкретного исключения. |
| 1002 | Ошибка установки WMF | Произошла ошибка при установке WMF. |
| 1004 | Недопустимый ZIP-пакет | Недопустимый ZIP-файл. Ошибка при распаковке ZIP-файла. |
| 1100 | Ошибка аргумента | Указывает на проблему во входных данных, введенных пользователем. Сообщение об этой ошибке выдается при возникновении конкретного исключения|


### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
