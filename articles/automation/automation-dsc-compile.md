---
title: Компиляция конфигураций DSC в конфигурации состояния службы автоматизации Azure
description: В этой статье описывается, как компилировать конфигурации службы настройки требуемого состояния (DSC) для службы автоматизации Azure.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: eeb60012ae607e49b1249fda13222cb2fa753911
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996064"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Компиляция конфигураций DSC в конфигурации состояния службы автоматизации Azure

Вы можете компилировать конфигурации требуемого состояния (DSC) в конфигурации состояния службы автоматизации Azure следующими способами.

- Служба компиляции конфигурации состояния Azure
  - Метод "новичок" с интерактивным пользовательским интерфейсом
   - Легко отслеживаемое состояние задания.

- Windows PowerShell
  - Вызов из Windows PowerShell на локальной рабочей станции или в службе сборок
  - Интеграция с конвейером тестирования разработки
  - Укажите значения сложных параметров
  - Работа с данными узла и без узла в масштабе
  - Значительное повышение производительности

Вы также можете использовать шаблоны Azure Resource Manager с расширением DSC для принудительной отправки конфигураций на виртуальные машины Azure. Расширение DSC Azure использует платформу агента Azure, чтобы доставлять и применять конфигурации DSC виртуальных машин Azure, а также сообщать об этих конфигурациях. Сведения о компиляции с помощью шаблонов Azure Resource Manager см. [в разделе расширение настройки требуемого состояния с помощью шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details). 

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Компиляция конфигурации DSC в конфигурации состояния Azure

### <a name="portal"></a>Портал

1. В учетной записи службы автоматизации щелкните **Конфигурация состояния (DSC)**.
1. Нажмите кнопку**Конфигурации**, а затем щелкните имя конфигурации для компилирования.
1. Нажмите кнопку **Компилировать**.
1. Если в конфигурации нет параметров, вам будет предложено подтвердить, нужно ли его компилировать. Если конфигурация содержит параметры, откроется колонка **Конфигурация компиляции** , где можно указать значения параметров.
1. Откроется страница Задание компиляции, с помощью которой можно будет отвестить состояние задания компиляции. Эту страницу также можно использовать для отслеживания конфигураций узлов (документы конфигурации MOF), размещенных на опрашивающем сервере конфигурации состояния службы автоматизации Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Чтобы начать компиляцию с помощью Windows PowerShell, можно использовать [Start-азаутоматиондсккомпилатионжоб](/powershell/module/az.automation/start-azautomationdsccompilationjob) . В следующем примере кода начинается компиляция конфигурации DSC с именем **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`Возвращает объект задания компиляции, который можно использовать для мониторинга состояния задания. Затем можно использовать этот объект задания компиляции с помощью [Get-азаутоматиондсккомпилатионжоб](/powershell/module/az.automation/get-azautomationdsccompilationjob) , чтобы определить состояние задания компиляции и [получить-азаутоматиондсккомпилатионжобаутпут](/powershell/module/az.automation/get-azautomationdscconfiguration) для просмотра его потоков (Output). В следующем примере запускается компиляция конфигурации SampleConfig, выполняется ожидание ее завершения, а затем выводятся ее потоки.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Объявить базовые параметры

Объявление параметров, в том числе типов и свойств параметров, в конфигурациях DSC выполняется так же, как и в модулях Runbook службы автоматизации Azure. Дополнительные сведения о параметрах модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

В следующем примере используются `FeatureName` параметры `IsPresent` и, чтобы определить значения свойств в конфигурации узла **параметерсексампле. Sample** , созданные во время компиляции.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Конфигурации DSC, использующие основные параметры, можно скомпилировать на портале настройки состояния службы автоматизации Azure или с помощью Azure PowerShell.

#### <a name="portal"></a>Портал

Чтобы ввести значения параметров на портале, нажмите кнопку **Компилировать**.

![Параметры компиляции конфигурации](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell требует параметры в [хэш-таблице](/powershell/module/microsoft.powershell.core/about/about_hash_tables), где ключ совпадает с именем параметра, а значение равно значению параметра.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Сведения о передаче `PSCredential` объектов в качестве параметров см. в разделе [ресурсы учетных данных](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Компиляция конфигураций, содержащих составные ресурсы, в службе автоматизации Azure

Функция **составных ресурсов** позволяет использовать конфигурации DSC в качестве вложенных ресурсов в конфигурации. Эта функция обеспечивает применение нескольких конфигураций к одному ресурсу. Дополнительные сведения о составных ресурсах см. в разделе [Составные ресурсы. Использование конфигурации DSC в качестве ресурса](/powershell/scripting/dsc/resources/authoringresourcecomposite) .

> [!NOTE]
> Для правильной компиляции конфигураций, содержащих составные ресурсы, необходимо сначала импортировать в службу автоматизации Azure все ресурсы DSC, от которых зависит этот комплекс. Добавление составного ресурса DSC не отличается от добавления модуля PowerShell в службу автоматизации Azure. Этот процесс описан в статье [Управление модулями в службе автоматизации Azure](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Управление ConfigurationData при компиляции конфигураций в службе автоматизации Azure

`ConfigurationData`— Это встроенный параметр DSC, который позволяет отделить конфигурацию от любой конфигурации конкретной среды при использовании PowerShell DSC. Дополнительные сведения см. [в разделе отделение "What" от "Where" в POWERSHELL DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> При компиляции в конфигурации состояния службы автоматизации Azure можно использовать `ConfigurationData` в Azure PowerShell, но не в портал Azure.

В следующем примере конфигурации DSC используется `ConfigurationData` с `$ConfigurationData` помощью ключевых `$AllNodes` слов и. Кроме того, для этого примера нужен [модуль xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) .

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Вы можете скомпилировать предыдущую конфигурацию DSC с помощью Windows PowerShell. Следующий скрипт добавляет две конфигурации узла в опрашивающую службу конфигурации состояния службы автоматизации Azure: **ConfigurationDataSample. MyVM1** и **ConfigurationDataSample. MyVM3**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Работа с ресурсами в службе автоматизации Azure во время компиляции

Ссылки на ресурсы в конфигурации и модулях Runbook службы автоматизации Azure одинаковы. Дополнительные сведения см. в следующих разделах:

- [Сертификаты](automation-certificates.md)
- [Подключения](automation-connections.md)
- [Учетные данные](automation-credentials.md)
- [Переменные](automation-variables.md)

#### <a name="credential-assets"></a>Ресурсы учетных данных

Конфигурации DSC в службе автоматизации Azure могут ссылаться на ресурсы учетных данных службы автоматизации с помощью командлета `Get-AutomationPSCredential`. Если конфигурация имеет параметр, указывающий `PSCredential` объект, используйте `Get-AutomationPSCredential` , передав строковое имя ресурса учетных данных службы автоматизации Azure командлету для получения учетных данных. Затем используйте этот объект для параметра, который требует `PSCredential` объект. Названный этим именем актив учетных данных, которые используются для службы автоматизации Azure, будет в фоновом режиме извлечен и передан в конфигурацию. В приведенном ниже примере показан этот сценарий в действии.

Чтобы обеспечить безопасность учетных данных в конфигурациях узла (документы конфигурации MOF), учетные данные нужно зашифровать в MOF-файле конфигурации узла. В настоящее время необходимо предоставить разрешение PowerShell DSC на вывод учетных данных в виде обычного текста во время создания MOF конфигурации узла. PowerShell DSC не знает, что служба автоматизации Azure шифрует весь MOF-файл после его создания с помощью задания компиляции.

Вы можете сообщить PowerShell DSC, что учетные данные можно выпустить в виде обычного текста в созданной конфигурации узла MOF с помощью данных конфигурации. Для каждого имени `PSDscAllowPlainTextPassword = $true` блока `ConfigurationData` узла, которое отображается в конфигурации DSC и использует учетные данные, необходимо передать через.

В следующем примере показана конфигурация DSC, использующая актив учетных данных автоматизации.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Вы можете компилировать конфигурацию DSC, показанную выше, с помощью PowerShell. Следующий код PowerShell добавляет две конфигурации узла в опрашивающий сервер конфигурации состояния службы автоматизации Azure: **CredentialSample. MyVM1** и **CredentialSample. MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> После завершения компиляции может появиться сообщение `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` об ошибке, которое можно спокойно проигнорировать.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Компиляция конфигурации DSC в Windows PowerShell

Также можно импортировать конфигурации узлов (MOF-файлы), которые были скомпилированы за пределами Azure. Импорт включает компиляцию на рабочей станции разработчика или в службе, например [Azure DevOps](https://dev.azure.com). Этот подход имеет несколько преимуществ, включая производительность и надежность.

Компиляция в Windows PowerShell также предоставляет возможность подписывать содержимое конфигурации. Агент DSC проверяет конфигурацию подписанного узла локально на управляемом узле. Проверка гарантирует, что конфигурация, примененная к узлу, поступает из полномочного источника.

> [!NOTE]
> Чтобы разрешить службе автоматизации Azure импортировать файл конфигурации узла, он должен быть не больше 1 МБ.

Дополнительные сведения о подписывании конфигураций узлов см. [в разделе улучшения в WMF 5,1 — как подписывать конфигурацию и модуль](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compile-the-dsc-configuration"></a>Компиляция конфигурации DSC

Процесс компиляции конфигураций DSC в Windows PowerShell включен в документацию по PowerShell DSC [Создание, компиляция и применение конфигурации](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Этот процесс можно выполнить на рабочей станции разработчика или в службе сборки, например в [Azure DevOps](https://dev.azure.com). Затем можно импортировать MOF-файлы, созданные путем компиляции конфигурации, в службу настройки состояния Azure.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Импорт конфигурации узла в портал Azure

1. В учетной записи службы автоматизации щелкните **Конфигурация состояния (DSC)** в разделе **Управление конфигурацией**.
1. На странице Конфигурация состояния (DSC) перейдите на вкладку **конфигурации** и нажмите кнопку **Добавить**.
1. На странице Импорт щелкните значок папки рядом с полем **файл конфигурации узла** , чтобы найти MOF-файл конфигурации узла на локальном компьютере.

   ![Поиск локального файла](./media/automation-dsc-compile/import-browse.png)

1. Введите имя в поле **имя конфигурации** . Это имя должно совпадать с именем конфигурации, из которой была скомпилирована данная конфигурация узла.
1. Нажмите кнопку **ОК**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Импорт конфигурации узла с Azure PowerShell

Для импорта конфигурации узла в учетную запись службы автоматизации можно использовать командлет [Import-азаутоматиондскнодеконфигуратион](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) .

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе, см. статью [Приступая к работе с конфигурацией состояния службы автоматизации Azure](automation-dsc-getting-started.md).
- Дополнительные сведения о компиляции конфигураций DSC с целью назначения их целевым узлам см. [в разделе Компиляция конфигураций в конфигурации состояния службы автоматизации Azure](automation-dsc-compile.md).
- Справочник по командлетам PowerShell см. в документации по [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Сведения о ценах см. в разделе [цены на настройку состояния службы автоматизации Azure](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования конфигурации состояния службы автоматизации Azure в конвейере непрерывного развертывания см. в разделе [непрерывное развертывание на виртуальных машинах с помощью конфигурации состояния службы автоматизации Azure и шоколадного](automation-dsc-cd-chocolatey.md)развертывания.
