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
ms.openlocfilehash: 5d72b474e5f5e62ded6423fcc756e1cd51b905f4
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850662"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Компилирование конфигураций DSC в службе "Настройка состояния службы автоматизации Azure"

Вы можете компилировать конфигурации требуемого состояния (DSC) двумя способами с помощью конфигурации состояния службы автоматизации Azure: в Azure и в Windows PowerShell. Приведенная ниже таблица поможет определить, когда и какой метод использовать с учетом характеристик каждого метода:

- Служба компиляции конфигурации состояния Azure
  - Метод "новичок" с интерактивным пользовательским интерфейсом
   - Легко отслеживаемое состояние задания.

- Windows PowerShell
  - Вызов из Windows PowerShell на локальной рабочей станции или в службе сборок
  - Интеграция с конвейером тестирования разработки
  - Укажите значения сложных параметров
  - Работа с данными узла и без узла в масштабе
  - Значительное повышение производительности

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Компиляция конфигурации DSC в конфигурации состояния Azure

### <a name="portal"></a>Портал

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

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
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

#### <a name="portal"></a>Портал

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

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Компиляция конфигураций в службе автоматизации Azure, содержащих составные ресурсы

**Составные ресурсы** позволяют использовать конфигурации DSC в качестве вложенных ресурсов в рамках конфигурации. Благодаря этому можно применить несколько конфигураций к одному ресурсу. См. статью [Composite resources: Using a DSC configuration as a resource ](/powershell/dsc/authoringresourcecomposite) (Составные ресурсы.Использование конфигурации DSC как ресурса) для получения дополнительных сведений о **составных ресурсах**.

> [!NOTE]
> Для правильной компиляции конфигураций, содержащих **Составные ресурсы** , необходимо сначала убедиться, что все ресурсы DSC, на которые полагается составной компонент, сначала импортируются в службу автоматизации Azure.

Добавление составного **ресурса** DSC не отличается от добавления любого модуля PowerShell в службу автоматизации Azure.
Пошаговые инструкции для этого процесса описаны в статье [Управление модулями в службе автоматизации Azure](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Управление ConfigurationData при компиляции конфигурации в службе автоматизации Azure

Параметр **ConfigurationData** позволяет при использовании PowerShell DSC отделить конфигурацию структуры от любой конфигурации среды. Дополнительные сведения о **ConfigurationData** см. в публикации блога [Separating "What" from "Where" in PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) (Разница между "что" и "где" в DSC PowerShell).

> [!NOTE]
> Вы можете использовать **ConfigurationData** при компиляции в конфигурации состояния службы автоматизации Azure с помощью Azure PowerShell, но не в портал Azure.

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

Вы можете скомпилировать предыдущую конфигурацию DSC с помощью Windows PowerShell. Следующий скрипт добавляет две конфигурации узла в опрашивающую службу настройки состояния службы автоматизации Azure: **CredentialSample.MyVM1** и **CredentialSample.MyVM3**.

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

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Работа с ресурсами в службе автоматизации Azure во время компиляции

Ссылки на ресурсы одинаковы в конфигурациях и модулях Runbook службы "Настройка состояния службы автоматизации Azure". Дополнительную информацию см. в следующих разделах.

- [Certificates](automation-certificates.md)
- [Подключения](automation-connections.md)
- [Учетные данные](automation-credentials.md)
- [Переменные](automation-variables.md)

#### <a name="credential-assets"></a>Активы учетных данных

Конфигурации DSC в службе автоматизации Azure могут ссылаться на ресурсы учетных данных службы автоматизации с помощью командлета `Get-AutomationPSCredential`. Если конфигурация включает параметр типа **PSCredential**, можно использовать командлет `Get-AutomationPSCredential`, передав имя строки ресурса учетных данных службы автоматизации Azure в командлет для получения учетных данных. Затем можно использовать этот объект для параметра, требующего объект **PSCredential**. Названный этим именем актив учетных данных, которые используются для службы автоматизации Azure, будет в фоновом режиме извлечен и передан в конфигурацию. Это действие показано в приведенном ниже примере.

Чтобы обеспечить безопасность учетных данных в конфигурациях узла (документы конфигурации MOF), учетные данные нужно зашифровать в MOF-файле конфигурации узла. Но сейчас в модуле DSC PowerShell нужно подтверждать, что учетные данные можно отображать в формате обычного текста во время создания MOF-файла конфигурации узла. Это связано с тем, что модулю DSC PowerShell неизвестно, что, когда MOF-файл будет создан с помощью задачи компилирования, служба автоматизации Azure будет шифровать его целиком.

Вы можете сообщить PowerShell DSC, что учетные данные можно выпустить в виде обычного текста в созданной конфигурации узла MOF с помощью данных конфигурации. `PSDscAllowPlainTextPassword = $true` следует передать через **ConfigurationData** для каждого имени блока узла, которое отображается в конфигурации DSC и для которого нужны учетные данные.

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

Вы можете компилировать конфигурацию DSC, показанную выше, с помощью PowerShell. При помощи следующей команды PowerShell в опрашиваемый сервер конфигурации состояния службы автоматизации Azure добавляются две конфигурации узла: **CredentialSample.MyVM1** и **CredentialSample.MyVM2**.

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
> После завершения компиляции может появиться следующее сообщение об ошибке: **Модуль Microsoft.PowerShell.Management не импортирован, так как уже импортирована оснастка Microsoft.PowerShell.Management**. Это предупреждение можно спокойно проигнорировать.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Компиляция конфигураций в Windows PowerShell и публикация в службе автоматизации Azure

Вы также можете импортировать конфигурации узла (MOF-файлы), скомпилированные вне Azure.
Это включает компиляцию из рабочей станции разработчика или службы, например [Azure DevOps](https://dev.azure.com).
Этот подход имеет несколько преимуществ, включая производительность и надежность.
Компиляция в Windows PowerShell также предоставляет возможность подписывать содержимое конфигурации.
Заверенная конфигурация узла проверяется локально на управляемом узле агентом DSC. Тем самым гарантируется, что конфигурация, применяемая к узлу, передана из авторизованного источника.

> [!NOTE]
> Размер файла конфигурации узла не должен превышать 1 МБ, чтобы его можно было импортировать в службу автоматизации Azure.

Дополнительные сведения о том, как подписывать конфигурации узла, см. в разделе [Модуль DSC и проверка подписи конфигурации](/powershell/wmf/5.1/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Компиляция конфигурации в Windows PowerShell

Процесс компиляции конфигураций DSC в Windows PowerShell включен в документацию по PowerShell DSC [Создание, компиляция и применение конфигурации](/powershell/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Это можно выполнить на рабочей станции разработчика или в службе сборки, такой как [Azure DevOps](https://dev.azure.com).

MOF-файл или файлы, созданные при компиляции конфигурации, можно импортировать непосредственно в службу настройки состояния Azure.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Импорт конфигурации узла на портале Azure

1. В учетной записи службы автоматизации в разделе **Управление конфигурацией** щелкните **State configuration (DSC)** (Настройка состояния (DSC)).
1. На странице**State configuration (DSC)** (Настройка состояния (DSC)) выберите вкладку **Конфигурации**, а потом щелкните **+ Добавить**.
1. На странице **Import** (Импорт) щелкните значок папки рядом с текстовым полем **Node Configuration File** (Файл конфигурации узла), чтобы найти файл конфигурации узла (MOF) на локальном компьютере.

   ![Поиск локального файла](./media/automation-dsc-compile/import-browse.png)

1. В текстовом поле **Configuration Name** (Имя конфигурации) введите имя. Это имя должно совпадать с именем конфигурации, из которой была скомпилирована данная конфигурация узла.
1. Нажмите кнопку **ОК**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Импорт конфигурации узла с Azure PowerShell

Для импорта конфигурации узла в учетную запись службы автоматизации можно использовать командлет [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration).

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Следующие шаги

- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
