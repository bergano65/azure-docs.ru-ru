---
title: Компилирование конфигураций в службе "Настройка состояния службы автоматизации Azure"
description: В этой статье описывается, как компилировать конфигурации службы настройки требуемого состояния (DSC) для службы автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d3cca9d8954d9ac158d88b393c46672da3faa19
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231718"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Компилирование конфигураций DSC в службе "Настройка состояния службы автоматизации Azure"

You can compile Desired State Configuration (DSC) configurations in two ways with Azure Automation State Configuration: in Azure and in Windows PowerShell. Приведенная ниже таблица поможет определить, когда и какой метод использовать с учетом характеристик каждого метода:

- Azure State Configuration compilation service
  - Beginner method with interactive user interface
   - Легко отслеживаемое состояние задания.

- Windows PowerShell
  - Call from Windows PowerShell on local workstation or build service
  - Integrate with development test pipeline
  - Provide complex parameter values
  - Work with node and non-node data at scale
  - Significant performance improvement

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compiling a DSC Configuration in Azure State Configuration

### <a name="portal"></a>Microsoft Azure

1. В учетной записи службы автоматизации нажмите кнопку**Конфигурации DSC**.
1. Нажмите кнопку**Конфигурации**, а затем щелкните имя конфигурации для компилирования.
1. Нажмите кнопку **Компилировать**.
1. Если нет параметров конфигурации, нужно подтвердить ее компиляцию. Если параметры конфигурации есть, отобразится колонка **Compile Configuration** (Компиляция конфигурации), в которой можно указать значения параметров. Дополнительные сведения о параметрах см. в разделе [**Базовые параметры**](#basic-parameters) ниже.
1. Откроется страница **Задание компилирования**, где вы можете отследить статус задания компилирования, а также конфигурации узла (документы конфигурации MOF), которые это задание расположило на опрашивающем сервере службы "Настройка состояния службы автоматизации Azure".

### <a name="azure-powershell"></a>Azure PowerShell

Чтобы начать компилирование с помощью Windows PowerShell, вы можете использовать [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) . В следующем примере кода запускается компилирование конфигурации DSC под именем **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` возвращает объект задания компилирования, с помощью которого вы можете отслеживать состояние. Затем можно использовать этот объект задания компилирования с [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob),
чтобы определить его статус, и [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
для просмотра его потоков (выходных данных). В следующем примере кода мы запускаем компилирование конфигурации **SampleConfig** , ждем, пока оно завершится, а затем отображаем его потоки.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Базовые параметры

Объявление параметров, в том числе типов и свойств параметров, в конфигурациях DSC выполняется так же, как и в модулях Runbook службы автоматизации Azure. Дополнительные сведения о параметрах модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

Чтобы определить значения свойств в конфигурации узла **ParametersExample.sample**, созданной во время компилирования, в следующем примере используются два параметра — **FeatureName** и **IsPresent**.

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

Вы можете компилировать конфигурации DSC, которые используют базовые параметры, на портале службы "Настройка состояния службы автоматизации Azure" или с помощью Azure PowerShell:

#### <a name="portal"></a>Microsoft Azure

Чтобы ввести значения параметров на портале, нажмите кнопку **Компилировать**.

![Параметры компиляции конфигурации](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Для модуля PowerShell нужны параметры в таблице [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) , в которой раздел соответствует имени параметра, а значение — значению параметра.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Сведения о передаче учетных данных PSCredentials в качестве параметров см. в разделе [Активы учетных данных](#credential-assets) ниже.

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Compiling configurations in Azure Automation that contain Composite Resources

**Составные ресурсы** позволяют использовать конфигурации DSC в качестве вложенных ресурсов в рамках конфигурации. Благодаря этому можно применить несколько конфигураций к одному ресурсу. См. статью [Составные ресурсы: использование DSC как ресурса](/powershell/scripting/dsc/resources/authoringresourcecomposite) для получения дополнительных сведений о **составных ресурсах**.

> [!NOTE]
> In order for configurations containing **Composite Resources** to compile correctly, you must first ensure that any DSC Resources that the composite relies on are first imported in to Azure Automation.

Adding a DSC **Composite Resource** is no different than adding any PowerShell module to Azure Automation.
The step by step instruction for this process is documented in the article [Manage Modules in Azure Automation](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Managing ConfigurationData when compiling configuration in Azure Automation

Параметр **ConfigurationData** позволяет при использовании PowerShell DSC отделить конфигурацию структуры от любой конфигурации среды. Дополнительные сведения о **ConfigurationData** см. в публикации блога [Separating "What" from "Where" in PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) (Разница между "что" и "где" в DSC PowerShell).

> [!NOTE]
> You can use **ConfigurationData** when compiling in Azure Automation State Configuration using Azure PowerShell but not in the Azure portal.

В приведенном ниже примере конфигурации DSC параметр **ConfigurationData** используется через ключевые слова **$ConfigurationData** и **$AllNodes**. Для этого примера также нужен [модуль **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

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

You can compile the preceding DSC configuration with Windows PowerShell. The following script adds two node configurations to the Azure Automation State Configuration Pull Service: **ConfigurationDataSample.MyVM1** and **ConfigurationDataSample.MyVM3**:

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Working with Assets in Azure Automation during compilation

Ссылки на ресурсы одинаковы в конфигурациях и модулях Runbook службы "Настройка состояния службы автоматизации Azure". Дополнительную информацию см. в следующих разделах.

- [Сертификаты](automation-certificates.md)
- [Подключения](automation-connections.md)
- [Учетные данные](automation-credentials.md)
- [Переменные](automation-variables.md)

#### <a name="credential-assets"></a>Активы учетных данных

Конфигурации DSC в службе автоматизации Azure могут ссылаться на ресурсы учетных данных службы автоматизации с помощью командлета `Get-AutomationPSCredential`. Если конфигурация включает параметр типа **PSCredential**, можно использовать командлет `Get-AutomationPSCredential`, передав имя строки ресурса учетных данных службы автоматизации Azure в командлет для получения учетных данных. Затем можно использовать этот объект для параметра, требующего объект **PSCredential**. Названный этим именем актив учетных данных, которые используются для службы автоматизации Azure, будет в фоновом режиме извлечен и передан в конфигурацию. Это действие показано в приведенном ниже примере.

Чтобы обеспечить безопасность учетных данных в конфигурациях узла (документы конфигурации MOF), учетные данные нужно зашифровать в MOF-файле конфигурации узла. Но сейчас в модуле DSC PowerShell нужно подтверждать, что учетные данные можно отображать в формате обычного текста во время создания MOF-файла конфигурации узла. Это связано с тем, что модулю DSC PowerShell неизвестно, что, когда MOF-файл будет создан с помощью задачи компилирования, служба автоматизации Azure будет шифровать его целиком.

You can tell PowerShell DSC that it is okay for credentials to be outputted in plain text in the generated node configuration MOFs using Configuration Data. `PSDscAllowPlainTextPassword = $true` следует передать через **ConfigurationData** для каждого имени блока узла, которое отображается в конфигурации DSC и для которого нужны учетные данные.

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

Вы можете компилировать конфигурацию DSC, показанную выше, с помощью PowerShell. При помощи следующей команды PowerShell в опрашивающий сервер службы "Настройка состояния службы автоматизации Azure" добавляются две конфигурации узла: **CredentialSample.MyVM1** и **CredentialSample.MyVM2**.

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> После завершения компиляции может появиться следующее сообщение об ошибке: **The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported** (Модуль Microsoft.PowerShell.Management не импортирован, так как уже импортирована оснастка Microsoft.PowerShell.Management). Это предупреждение можно спокойно проигнорировать.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Compiling configurations in Windows PowerShell and publishing to Azure Automation

Вы также можете импортировать конфигурации узла (MOF-файлы), скомпилированные вне Azure.
This includes compiling from a developer workstation or in a service such as [Azure DevOps](https://dev.azure.com).
There are multiple advantages to this approach including performance and reliability.
Compiling in Windows PowerShell also provides the option to sign configuration content.
Заверенная конфигурация узла проверяется локально на управляемом узле агентом DSC. Тем самым гарантируется, что конфигурация, применяемая к узлу, передана из авторизованного источника.

> [!NOTE]
> Размер файла конфигурации узла не должен превышать 1 МБ, чтобы его можно было импортировать в службу автоматизации Azure.

Дополнительные сведения о том, как подписывать конфигурации узла, см. в разделе [Модуль DSC и проверка подписи конфигурации](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Compiling a configuration in Windows PowerShell

The process to compile DSC configurations in Windows PowerShell is included in the PowerShell DSC documentation [Write, Compile, and Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
This can be executed from a developer workstation or within a build service such as [Azure DevOps](https://dev.azure.com).

The MOF file or files produced by compiling the configuration can then be imported directly in to the Azure State Configuration service.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Импорт конфигурации узла на портале Azure

1. В учетной записи службы автоматизации в разделе **Управление конфигурацией** щелкните **State configuration (DSC)** (Настройка состояния (DSC)).
1. На странице**State configuration (DSC)** (Настройка состояния (DSC)) выберите вкладку **Конфигурации**, а потом щелкните **+ Добавить**.
1. На странице **Import** (Импорт) щелкните значок папки рядом с текстовым полем **Node Configuration File** (Файл конфигурации узла), чтобы найти файл конфигурации узла (MOF) на локальном компьютере.

   ![Поиск локального файла](./media/automation-dsc-compile/import-browse.png)

1. В текстовом поле **Configuration Name** (Имя конфигурации) введите имя. Это имя должно совпадать с именем конфигурации, из которой была скомпилирована данная конфигурация узла.
1. Последовательно выберите **ОК**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importing a node configuration with Azure PowerShell

Для импорта конфигурации узла в учетную запись службы автоматизации можно использовать командлет [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration).

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
