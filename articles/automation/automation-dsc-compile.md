---
title: Компилирование конфигураций в службе "Настройка состояния службы автоматизации Azure"
description: В этой статье описывается, как компилировать конфигурации службы настройки требуемого состояния (DSC) для службы автоматизации Azure.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b366777cce4dd4212a2e3ec0cdcd180e0d7164f4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757178"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Компилирование конфигураций DSC в службе "Настройка состояния службы автоматизации Azure"

Конфигурации желаемой конфигурации состояния (DSC) можно компилировать в конфигурации состояния автоматизации Azure следующим образом:

- Служба компиляции состояния Azure
  - Метод новичка с интерактивным пользовательским интерфейсом
   - Легко отслеживаемое состояние задания.

- Windows PowerShell
  - Звонок из Windows PowerShell на локальную рабочую станцию или службу сборки
  - Интеграция с тестом разработки
  - Обеспечение сложных значений параметров
  - Работа с данными узлов и неузлов в масштабе
  - Значительное улучшение производительности

Вы также можете использовать шаблоны менеджера ресурсов Azure с расширением Azure Desired State Configuration (DSC) для нажатия конфигураций на ваши M-вИ Azure. Расширение DSC Azure использует платформу агента Azure, чтобы доставлять и применять конфигурации DSC виртуальных машин Azure, а также сообщать об этих конфигурациях. Для сведения о компиляции с помощью шаблонов Azure Resource Manager [см.](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details) 

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Составление конфигурации DSC в конфигурации состояния Azure

### <a name="portal"></a>Портал

1. В учетной записи службы автоматизации нажмите кнопку**Конфигурации DSC**.
1. Нажмите кнопку**Конфигурации**, а затем щелкните имя конфигурации для компилирования.
1. Нажмите кнопку **Компилировать**.
1. Если конфигурация не имеет параметров, вам предлагается подтвердить, хотите ли вы ее скомпилировать. Если конфигурация имеет параметры, лезвие **конфигурации Компиляции** открывается, так что вы можете предоставить значения параметров.
1. Страница вакансии «Работа компиляции» открыта, чтобы можно было отслеживать состояние задания компиляции. Вы также можете использовать эту страницу для отслеживания конфигураций узлов (документов конфигурации MOF), размещенных на сервере azure Automation State Configuration Pull.

### <a name="azure-powershell"></a>Azure PowerShell

Для начала компиляции с Windows PowerShell можно использовать [Start-AzAutomationDscCompilationJob.](/powershell/module/az.automation/start-azautomationdsccompilationjob) Следующий пример кода начинает компиляцию конфигурации DSC под названием SampleConfig.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** возвращает объект компиляции, который можно использовать для отслеживания статуса задания. Затем можно использовать этот объект компиляции с [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) для определения состояния задания компиляции и [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) для просмотра его потоков (выходов). Следующий образец начинает компиляцию конфигурации SampleConfig, ждет, пока она не завершится, а затем отображает свои потоки.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Объявить основные параметры

Объявление параметров, в том числе типов и свойств параметров, в конфигурациях DSC выполняется так же, как и в модулях Runbook службы автоматизации Azure. Дополнительные сведения о параметрах модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

Чтобы определить значения свойств в конфигурации узла ParametersExample.sample, созданной во время компилирования, в следующем примере используются два параметра — *FeatureName* и *IsPresent*.

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

Можно компилировать конфигурации DSC, которые используют основные параметры на портале состояния автоматизации Azure или с Azure PowerShell.

#### <a name="portal"></a>Портал

Чтобы ввести значения параметров на портале, нажмите кнопку **Компилировать**.

![Параметры компиляции конфигурации](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell требует параметров в [хэштаблице,](/powershell/module/microsoft.powershell.core/about/about_hash_tables)где ключ совпадает с именем параметра, а значение равно значению параметра.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Сведения о передаче учетных данных PSCredentials в качестве параметров см. в разделе [Активы учетных данных](#credential-assets) ниже.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Компилировать конфигурации, содержащие композитные ресурсы в автоматизации Azure

Функция **Composite Resources** позволяет использовать конфигурации DSC в качестве вложенных ресурсов внутри конфигурации. Эта функция позволяет применять несколько конфигураций к одному ресурсу. [См. Композитные ресурсы: Использование конфигурации DSC в качестве ресурса](/powershell/scripting/dsc/resources/authoringresourcecomposite) для получения дополнительной информации о композитных ресурсах.

> [!NOTE]
> Так что конфигурации, содержащие композитные ресурсы, компилируются правильно, необходимо сначала импортировать в Azure Automation любые ресурсы DSC, на которые опираются композиты. Добавление композитного ресурса DSC ничем не отличается от добавления любого модуля PowerShell в Azure Automation. Этот процесс задокументирован в [модулях управления в Azure Automation.](/azure/automation/shared-resources/modules)

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Управление ConfigurationData при компиляции конфигураций в автоматизации Azure

Параметр **ConfigurationData** позволяет при использовании PowerShell DSC отделить конфигурацию структуры от любой конфигурации среды. Для получения дополнительной информации, [см. Отдел "Что" от "Где" в PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> При компиляции в конфигурации состояния автоматизации Azure можно использовать **ConfigurationData** в Azure PowerShell, но не на портале Azure.

В приведенном ниже примере конфигурации DSC параметр **ConfigurationData** используется через ключевые слова $ConfigurationData и $AllNodes. Для этого примера также нужен [модуль xWebAdministration.](https://www.powershellgallery.com/packages/xWebAdministration/)

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

Вы можете компилировать предыдущую конфигурацию DSC с Windows PowerShell. Следующий скрипт добавляет две конфигурации узлов в службу диагностики состояния автоматизации Azure: ConfigurationDataSample.MyVM1 и ConfigurationDataSample.MyVM3.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Работа с активами в Azure Automation во время компиляции

Ссылки на активы одинаковы как в конфигурации состояния автоматизации Azure, так и в сенах. Дополнительные сведения см. в следующих разделах:

- [Сертификаты](automation-certificates.md)
- [Подключения](automation-connections.md)
- [Учетные данные](automation-credentials.md)
- [Переменные](automation-variables.md)

#### <a name="credential-assets"></a>Учетные активы

Конфигурации DSC в Azure Automation могут ссылаться на учетные данные автоматизации с помощью cmdlet **Get-AutomationPSCredential.** Если конфигурация имеет параметр, обопивующий объект **PSCredential,** используйте **Get-AutomationPSCredential,** передав строку объекта учетных данных Azure Automation в cmdlet для получения учетных данных. Затем используйте этот объект для параметра, требующего объекта **PSCredential.** Названный этим именем актив учетных данных, которые используются для службы автоматизации Azure, будет в фоновом режиме извлечен и передан в конфигурацию. Приведенный ниже пример показывает этот сценарий в действии.

Чтобы обеспечить безопасность учетных данных в конфигурациях узла (документы конфигурации MOF), учетные данные нужно зашифровать в MOF-файле конфигурации узла. В настоящее время вы должны дать PowerShell DSC разрешение на вывод учетных данных в простом тексте во время генерации mOF конфигурации узлов. PowerShell DSC не знает, что Azure Automation шифрует весь файл MOF после его генерации с помощью задания компиляции.

Вы можете сказать PowerShell DSC, что это нормально для учетных данных, которые будут выдуваться в простом тексте в сгенерированной конфигурации узлов MOFs с использованием конфигурационных данных. Вы должны `PSDscAllowPlainTextPassword = $true` пройти через **ConfigurationData** для каждого названия блока узлов, который появляется в конфигурации DSC и использует учетные данные.

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

Вы можете компилировать конфигурацию DSC, показанную выше, с помощью PowerShell. Следующий код PowerShell добавляет две конфигурации узлов на сервер Azure Automation State Configuration: CredentialSample.MyVM1 и CredentialSample.MyVM2.

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
> Когда компиляция завершена, вы можете получить сообщение об ошибке "Модуль Microsoft.PowerShell.Management" не был импортирован, потому что "Microsoft.PowerShell.Management" snap-in уже импортировался". Это сообщение можно игнорировать.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Составление конфигурации DSC в Windows PowerShell

Вы также можете импортировать конфигурации узла (MOF-файлы), скомпилированные вне Azure. Импорт включает компиляцию с рабочей станции разработчика или в службе, такой как [Azure DevOps.](https://dev.azure.com) Этот подход имеет множество преимуществ, включая производительность и надежность.

Компиляция в Windows PowerShell также предоставляет возможность подписать содержимое конфигурации. Агент DSC проверяет конфигурацию подписанного узла локально на управляемом узлах. Проверка гарантирует, что конфигурация, применяемая к узлам, поступает из авторизованного источника.

> [!NOTE]
> Размер файла конфигурации узла не должен превышать 1 МБ, чтобы его можно было импортировать в службу автоматизации Azure.

Для получения дополнительной информации о подписании конфигураций узлов [см.](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)

### <a name="compile-the-dsc-configuration"></a>Составить конфигурацию DSC

Процесс компиляции конфигураций DSC в Windows PowerShell включен в документацию PowerShell DSC [Запись, компилирование и применение конфигурации.](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)
Вы можете выполнить этот процесс с рабочей станции разработчика или в службе сборки, например [с Azure DevOps.](https://dev.azure.com) Затем можно импортировать файл MOF( созданный путем компиляции конфигурации в службу государственной конфигурации Azure.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Импортировать конфигурацию узлов на портале Azure

1. В учетной записи службы автоматизации в разделе **Управление конфигурацией** щелкните **State configuration (DSC)** (Настройка состояния (DSC)).
1. На странице конфигурации состояния (DSC) нажмите на вкладку **Конфигурации,** а затем нажмите **кнопку «Добавить.**
1. На странице Импорта щелкните значок папки рядом с текстовым ящиком **файла конфигурации узлов,** чтобы просмотреть файл конфигурации узлов (MOF) на локальном компьютере.

   ![Поиск локального файла](./media/automation-dsc-compile/import-browse.png)

1. Введите имя в поле **«Имя конфигурации».** Это имя должно совпадать с именем конфигурации, из которой была скомпилирована данная конфигурация узла.
1. Нажмите кнопку **ОК**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Импортировать конфигурацию узлов с Azure PowerShell

Для импорта в учетную запись автоматизации можно использовать cmdlet [Import-AzAutomationDscScConfiguration.](/powershell/module/az.automation/import-azautomationdscnodeconfiguration)

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Дальнейшие действия

- Для начала см. [Начало работы с конфигурацией состояния автоматизации Azure.](automation-dsc-getting-started.md)
- Чтобы узнать о компиляции конфигураций DSC, чтобы можно было назначить их целевым узлам, см. [Конфигурации компиляции в конфигурации состояния azure Automation.](automation-dsc-compile.md)
- Для справки PowerShell cmdlet [см.](/powershell/module/az.automation)
- Для получения информации о ценах на цены [см.](https://azure.microsoft.com/pricing/details/automation/)
- Чтобы увидеть пример использования конфигурации состояния автоматизации Azure в непрерывном конвейере развертывания, [см. Непрерывное развертывание виртуальных машин с использованием конфигурации состояния Azure Automation и Chocolatey.](automation-dsc-cd-chocolatey.md)
