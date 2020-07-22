---
title: Набор средств тестирования шаблонов ARM
description: Описывает, как запустить набор средств тестирования шаблона ARM в шаблоне. Набор средств позволяет увидеть, реализованы ли рекомендации.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255836"
---
# <a name="use-arm-template-test-toolkit"></a>Использование набора средств для тестирования шаблонов ARM

[Набор средств тестирования шаблонов ARM](https://aka.ms/arm-ttk) проверяет, использует ли шаблон рекомендованные методы. Если шаблон не соответствует рекомендуемым методикам, он возвращает список предупреждений с предлагаемыми изменениями. С помощью набора средств тестирования можно узнать, как избежать распространенных проблем при разработке шаблонов.

Набор тестов набора средств тестирования предоставляет ряд [проверок по умолчанию](test-cases.md). Эти тесты являются рекомендациями, но не требованиями. Вы можете решить, какие тесты соответствуют вашим целям, и настроить, какие тесты выполняются.

В этой статье описывается, как запустить набор средств для тестирования и как добавить или удалить тесты. Описание тестов по умолчанию см. в разделе [тестовые случаи набора инструментов](test-cases.md).

Набор средств — это набор сценариев PowerShell, которые можно запускать из команды в PowerShell или CLI.

## <a name="download-test-toolkit"></a>Загрузить набор средств тестирования

Чтобы использовать набор средств для тестирования, можно создать вилку и клонировать [репозиторий](https://aka.ms/arm-ttk) , содержащий скрипты, или [скачать последний ZIP-файл](https://aka.ms/arm-ttk-latest).

В зависимости от политики выполнения на компьютере, на котором выполняется сценарий, может возникнуть ошибка, возникающая при выполнении скриптов из Интернета. Необходимо либо изменить [политику выполнения](/powershell/module/microsoft.powershell.core/about/about_execution_policies) , либо [разблокировать файлы скриптов](/powershell/module/microsoft.powershell.utility/unblock-file).

## <a name="run-on-powershell"></a>Запуск в PowerShell

Перед выполнением тестов Импортируйте модуль.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

Чтобы выполнить тесты в **PowerShell**, используйте следующую команду:

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Запуск в Linux

Перед выполнением тестов установите [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux).

Чтобы запустить тесты в **Linux** в bash, используйте следующую команду:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

Также можно выполнить тест на pwsh.exe.

## <a name="run-on-macos"></a>Запуск в macOS

Перед выполнением тестов установите [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos). 

Установите `coreutils`:

```bash
brew install coreutils
```

Чтобы выполнить тесты в **macOS**, используйте следующую команду:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>Формат результата

Пройденные тесты отображаются **зеленым цветом** и начинаются с **[+]**.

Тесты, которые не пройдены, отображаются **красным цветом** и начинаются с **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="Просмотр результатов теста":::

Результаты будут:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Параметры тестирования

Когда вы предоставляете параметр **-TemplatePath** , набор средств ищет в этой папке шаблон с именем azuredeploy.json или maintemplate.json. Сначала он проверяет этот шаблон, а затем проверяет все остальные шаблоны в папке и ее вложенных папках. Другие шаблоны тестируются как связанные шаблоны. Если путь содержит файл с именем [CreateUiDefinition.js](../managed-applications/create-uidefinition-overview.md), он выполняет тесты, относящиеся к определению пользовательского интерфейса.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Чтобы проверить один файл в этой папке, добавьте параметр **-File** . Однако папка по-прежнему должна иметь основной шаблон с именем azuredeploy.json или maintemplate.js.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

По умолчанию выполняются все тесты. Чтобы указать отдельные тесты для выполнения, используйте параметр **-Test** . Укажите имя теста. Имена см. в разделе [тестовые случаи для набора инструментов](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Настройка тестов

Для шаблонов ARM набор средств выполняет все тесты в папке **\арм-ттк\тесткасес\деплойменттемплате**. Если вы хотите удалить тест без возможности восстановления, удалите этот файл из папки.

Для файлов [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) все тесты выполняются в папке **\арм-ттк\тесткасес\креатеуидефинитион**.

Чтобы добавить собственный тест, создайте файл с соглашением об именовании: **Your-Custom-Test-Name.test.ps1**.

Тест может получить шаблон в виде параметра объекта или строкового параметра. Как правило, используется один или другой, но можно использовать и то, и другое.

Используйте параметр Object, если необходимо получить раздел шаблона и выполнить итерацию по его свойствам. Тест, использующий параметр объекта, имеет следующий формат:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

Объект шаблона имеет следующие свойства:

* $schema
* contentVersion
* параметры
* variables
* ресурсов
* outputs

Например, можно получить коллекцию параметров с помощью `$TemplateObject.parameters` .

Используйте параметр String, если необходимо выполнить операцию со строкой для всего шаблона. Тест, использующий строковый параметр, имеет следующий формат:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Например, можно выполнить регулярное выражение строкового параметра, чтобы узнать, используется ли конкретный синтаксис.

Чтобы узнать больше о реализации теста, просмотрите другие тесты в этой папке.

## <a name="integrate-with-azure-pipelines"></a>Интеграция с Azure Pipelines

Вы можете добавить набор средств тестирования в конвейер Azure. С помощью конвейера тест можно выполнять каждый раз при обновлении шаблона или запускать его в рамках процесса развертывания.

Самый простой способ добавить набор средств тестирования в конвейер — с расширениями сторонних разработчиков. Доступны следующие два расширения:

* [Выполнение тестов ARM ТТК](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [Тестер шаблонов ARM](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Также можно реализовать собственные задачи. В следующем примере показано, как скачать набор средств тестирования.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

В следующем примере показано, как выполнять тесты.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о тестах по умолчанию см. в разделе [тестовые случаи для набора средств](test-cases.md).
