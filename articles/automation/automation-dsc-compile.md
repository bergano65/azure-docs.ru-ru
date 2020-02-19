---
title: Компилирование конфигураций в службе "Настройка состояния службы автоматизации Azure"
description: В этой статье описывается, как компилировать конфигурации службы настройки требуемого состояния (DSC) для службы автоматизации Azure.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4bdd28d2ad8f692b561d414af15b90b1609bac4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462128"
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

Сведения о компиляции см. [в разделе расширение настройки требуемого состояния с помощью шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Компиляция конфигурации DSC в конфигурации состояния Azure

### <a name="portal"></a>Портал

1. В учетной записи службы автоматизации нажмите кнопку**Конфигурации DSC**.
1. Нажмите кнопку**Конфигурации**, а затем щелкните имя конфигурации для компилирования.
1. Нажмите кнопку **Компилировать**.
1. Если в конфигурации нет параметров, вам будет предложено подтвердить, что вы хотите его скомпилировать. Если конфигурация содержит параметры, откроется колонка **Конфигурация компиляции** , где можно указать значения параметров.
1. Откроется страница Задание компиляции, с помощью которой можно отвести отработку состояния задания компиляции. Эту страницу также можно использовать для отслеживания конфигураций узлов (документов конфигурации MOF), которые располагает заданием на опрашивающем сервере конфигурации состояния службы автоматизации Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Чтобы начать компиляцию с помощью Windows PowerShell, можно использовать [Start-азаутоматиондсккомпилатионжоб](/powershell/module/az.automation/start-azautomationdsccompilationjob) . В следующем примере кода начинается компиляция конфигурации DSC с именем SampleConfig.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

Командлет **Start-азаутоматиондсккомпилатионжоб** возвращает объект задания компиляции, который можно использовать для трассировки его состояния. Затем можно использовать этот объект задания компиляции с помощью [Get-азаутоматиондсккомпилатионжоб](/powershell/module/az.automation/get-azautomationdsccompilationjob) для определения состояния задания компиляции, а также с помощью [Get-азаутоматиондсккомпилатионжобаутпут](/powershell/module/az.automation/get-azautomationdscconfiguration) для просмотра его потоков (Output). В следующем примере кода запускается компиляция конфигурации SampleConfig, выполняется ожидание ее завершения, а затем выводятся ее потоки.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Сведения о передаче учетных данных PSCredentials в качестве параметров см. в разделе [Активы учетных данных](#credential-assets) ниже.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Компиляция конфигураций, содержащих составные ресурсы, в службе автоматизации Azure

Функция **составных ресурсов** позволяет использовать конфигурации DSC в качестве вложенных ресурсов в конфигурации. Благодаря этому можно применить несколько конфигураций к одному ресурсу. Дополнительные сведения о составных ресурсах см. в разделе [Составные ресурсы. Использование конфигурации DSC в качестве ресурса](/powershell/scripting/dsc/resources/authoringresourcecomposite) .

> [!NOTE]
> Для правильной компиляции конфигураций, содержащих составные ресурсы, необходимо убедиться, что все ресурсы DSC, на которые опирается составной компонент, импортируются в службу автоматизации Azure.

Добавление составного ресурса DSC не отличается от добавления модуля PowerShell в службу автоматизации Azure. Этот процесс описан в статье [Управление модулями в службе автоматизации Azure](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Управление ConfigurationData при компиляции конфигураций в службе автоматизации Azure

Функция **ConfigurationData** позволяет разделять структурную конфигурацию из любой конфигурации, зависящей от среды, при использовании PowerShell DSC. Дополнительные сведения о ConfigurationData см. [в разделе "что" из "Where" в POWERSHELL DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) .

> [!NOTE]
> ConfigurationData можно использовать при компиляции в конфигурации состояния службы автоматизации Azure с помощью Azure PowerShell, но не в портал Azure.

В следующем примере конфигурация DSC использует ConfigurationData через ключевые слова $ConfigurationData и $AllNodes. Вам также понадобится [модуль xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) для этого примера:

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

Вы можете скомпилировать предыдущую конфигурацию DSC с помощью Windows PowerShell. Следующий скрипт добавляет две конфигурации узла в опрашивающую службу конфигурации состояния службы автоматизации Azure: ConfigurationDataSample. MyVM1 и ConfigurationDataSample. MyVM3.

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

Ссылки на ресурсы в конфигурации и модулях Runbook службы автоматизации Azure одинаковы. Дополнительную информацию см. в следующих разделах:

- [Сертификаты](automation-certificates.md)
- [Соединения](automation-connections.md)
- [Учетные данные](automation-credentials.md)
- [Переменные](automation-variables.md)

#### <a name="credential-assets"></a>Ресурсы учетных данных

Конфигурации DSC в службе автоматизации Azure могут ссылаться на ресурсы учетных данных службы автоматизации с помощью командлета **Get-AutomationPSCredential** . Если в конфигурации есть параметр, имеющий тип PSCredential, можно использовать командлет **Get-AutomationPSCredential** , передав строковое имя ресурса учетных данных службы автоматизации Azure для получения учетных данных. Затем этот объект можно использовать для параметра, для которого требуется объект PSCredential. Названный этим именем актив учетных данных, которые используются для службы автоматизации Azure, будет в фоновом режиме извлечен и передан в конфигурацию. В следующем примере показана эта операция в действии.

Чтобы обеспечить безопасность учетных данных в конфигурации узла, необходимо зашифровать учетные данные в MOF-файле конфигурации узла. Необходимо сообщить PowerShell DSC, что он имеет разрешение на вывод учетных данных в виде обычного текста во время создания MOF конфигурации узла. PowerShell DSC не знает, что служба автоматизации Azure шифрует весь MOF-файл после его создания с помощью задания компиляции.

Чтобы предоставить PowerShell DSC разрешение на вывод учетных данных в виде обычного текста в созданной конфигурации узла MOF с помощью данных конфигурации, передайте `PSDscAllowPlainTextPassword = $true`. Эти сведения можно передать через ConfigurationData для каждого имени блока узла, которое отображается в конфигурации DSC и использует учетные данные.

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

Вы можете компилировать конфигурацию DSC, показанную выше, с помощью PowerShell. 

Следующая оболочка PowerShell добавляет две конфигурации узла в опрашивающий сервер конфигурации состояния службы автоматизации Azure: CredentialSample. MyVM1 и CredentialSample. MyVM2.

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

>[!NOTE]
>После завершения компиляции может появиться сообщение об ошибке: **модуль Microsoft. PowerShell. Management не был импортирован, так как оснастка "Microsoft. PowerShell. Management" уже импортирована.** Это предупреждение можно проигнорировать.

## <a name="compiling-a-dsc-configuration-in-windows-powershell"></a>Компиляция конфигурации DSC в Windows PowerShell

Процесс компиляции конфигураций DSC в Windows PowerShell включен в документацию по PowerShell DSC [Создание, компиляция и применение конфигурации](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Процесс можно выполнить на рабочей станции разработчика или в службе сборки, например в [Azure DevOps](https://dev.azure.com).
Затем можно импортировать MOF для результирующих конфигураций узлов непосредственно в службу настройки состояния Azure. 

>[!NOTE]
>Размер файла конфигурации узла не должен превышать 1 МБ, чтобы его можно было импортировать в службу автоматизации Azure.

Вы также можете импортировать конфигурации узла (MOF-файлы), скомпилированные вне Azure. Существует несколько преимуществ этого подхода, включая производительность и надежность.

Компиляция в Windows PowerShell предоставляет возможность подписывать содержимое конфигурации, при этом агент DSC проверяет конфигурацию подписанного узла локально на управляемом узле. Проверка гарантирует, что конфигурация, применяемая к узлу, поступает из полномочного источника. Дополнительные сведения о подписывании конфигураций узлов см. [в разделе улучшения в WMF 5,1 — как подписывать конфигурацию и модуль](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Импорт конфигурации узла в портал Azure

1. В учетной записи службы автоматизации в разделе **Управление конфигурацией** щелкните **State configuration (DSC)** (Настройка состояния (DSC)).
1. На странице Конфигурация состояния (DSC) перейдите на вкладку **конфигурации** и нажмите кнопку **+ Добавить**.
1. На странице Импорт щелкните значок папки рядом с текстовым полем **файл конфигурации узла** , чтобы найти файл конфигурации узла (MOF) на локальном компьютере.

   ![Поиск локального файла](./media/automation-dsc-compile/import-browse.png)

1. В текстовом поле **Configuration Name** (Имя конфигурации) введите имя. Это имя должно совпадать с именем конфигурации, из которой была скомпилирована данная конфигурация узла.
1. Нажмите кнопку **ОК**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Импорт конфигурации узла с Azure PowerShell

Для импорта конфигурации узла в учетную запись службы автоматизации можно использовать командлет [Import-азаутоматиондскнодеконфигуратион](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) .

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Следующие шаги

- Чтобы приступить к работе, см. статью [Приступая к работе с конфигурацией состояния службы автоматизации Azure](automation-dsc-getting-started.md).
- Дополнительные сведения о компиляции конфигураций DSC с целью назначения их целевым узлам см. [в разделе Компиляция конфигураций в конфигурации состояния службы автоматизации Azure](automation-dsc-compile.md).
- Справочник по командлетам PowerShell см. в статье [командлеты конфигурации состояния службы автоматизации Azure](/powershell/module/az.automation).
- Сведения о ценах см. в разделе [цены на настройку состояния службы автоматизации Azure](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования конфигурации состояния службы автоматизации Azure в конвейере непрерывного развертывания см. в статье [непрерывное развертывание с помощью конфигурации состояния службы автоматизации Azure и шоколадного](automation-dsc-cd-chocolatey.md)развертывания.
