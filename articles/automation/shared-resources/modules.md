---
title: Администрирование модулей в службе автоматизации Azure
description: В этой статье рассказывается, как использовать модули PowerShell для включения командлетов в последовательностях runbook и ресурсов DSC в конфигурациях DSC.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82bec23ac35f4f0e6c65720d0c3a36355fa4224d
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713487"
---
# <a name="manage-modules-in-azure-automation"></a>Администрирование модулей в службе автоматизации Azure

Служба автоматизации Azure использует несколько модулей PowerShell для включения командлетов в последовательностях runbook и ресурсов DSC в конфигурациях DSC. Поддерживаются следующие модули:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0);
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0);
* другие модули PowerShell;
* внутренний модуль `Orchestrator.AssetManagement.Cmdlets`;
* модули Python 2;
* созданные вами пользовательские модули.

При создании учетной записи службы автоматизации Azure эта служба по умолчанию импортирует ряд модулей. См. раздел [Стандартные модули](#default-modules).

Когда служба автоматизации выполняет задания компиляции DSC и runbook, она загружает модули в песочницы, где могут выполняться runbook и компилироваться конфигурации DSC. Она также автоматически помещает все ресурсы DSC в модули на опрашиваемом сервере DSC. Компьютеры могут извлекать ресурсы при применении конфигураций DSC.

>[!NOTE]
>Импортируйте только те модули, которые необходимы для последовательностей runbook и конфигураций DSC. Не рекомендуется импортировать корневой модуль Az. Он включает множество других потенциально ненужных модулей, что может вызвать проблемы с производительностью. Вместо этого импортируйте отдельные модули, например Az.Compute.

## <a name="default-modules"></a>Стандартные модули

В следующей таблице перечислены модули, которые служба автоматизации Azure импортирует по умолчанию при создании в ней учетной записи. Служба автоматизации может импортировать более новые версии этих модулей. Однако вы не сможете удалить исходную версию из учетной записи службы автоматизации, даже если удалите более новую версию. Обратите внимание, что среди этих стандартных модулей есть несколько модулей AzureRM. 

Служба автоматизации не импортирует корневой модуль Az автоматически в новые или имеющиеся учетные записи службы автоматизации. Дополнительные сведения о работе с этими модулями см. в разделе [Миграция в модули Az](#migrate-to-az-modules).

> [!NOTE]
> В учетных записях службы автоматизации, используемых для развертывания компонента [Запуск и остановка виртуальных машин в нерабочее время](../automation-solution-vm-management.md), не рекомендуется изменять модули и последовательности runbook.

|Имя модуля|Версия|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage; | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1,1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Модули Az

Имена большинства командлетов в Az.Automation совпадают с именами модулей AzureRM, за исключением того, что вместо префикса `AzureRM` используется префикс `Az`. Список модулей Az, не соответствующих соглашению об именовании, см. в [списке исключений](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Внутренние командлеты

Служба автоматизации Azure поддерживает внутренний модуль `Orchestrator.AssetManagement.Cmdlets` для агента Log Analytics для Windows, который устанавливается по умолчанию. Внутренние командлеты определены в следующей таблице. При взаимодействии с общими ресурсами эти командлеты используются вместо командлетов Azure PowerShell. Они могут получать секреты из зашифрованных переменных, учетных данных и зашифрованных соединений.

>[!NOTE]
>Внутренние командлеты доступны только при выполнении последовательностей runbook в среде песочницы Azure или при использовании гибридной рабочей роли Runbook Windows. 

|Имя|Описание|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Обратите внимание, что именование внутренних командлетов отличается от командлетов Az и AzureRM. Имена внутренних командлетов не содержат таких слов, как `Azure` или `Az`, но в них используется слово `Automation`. Мы рекомендуем использовать их вместо командлетов Az и AzureRM во время выполнения runbook в песочнице Azure или при использовании гибридной рабочей роли Runbook. Им требуется меньше параметров, и они выполняются в контексте уже запущенного задания.

Командлеты Az и AzureRM следует использовать для выполнения операций с ресурсами службы автоматизации вне контекста runbook. 

## <a name="python-modules"></a>Модули Python

В службе автоматизации Azure можно создавать последовательности runbook для Python 2. Сведения о модуле Python см. в статье [Управление пакетами Python 2 в службе автоматизации Azure](../python-packages.md).

## <a name="custom-modules"></a>Пользовательские модули

Служба автоматизации Azure поддерживает пользовательские модули PowerShell, созданные для использования с runbook и конфигурациями DSC. Один из типов пользовательских модулей — модуль интеграции, который дополнительно содержит файл метаданных для определения пользовательской функциональности его командлетов. Пример использования модуля интеграции приведен в разделе [Добавление типа подключения](../automation-connections.md#add-a-connection-type).

Служба автоматизации Azure может импортировать пользовательский модуль, чтобы обеспечить доступность его командлетов. Она сохраняет модуль и использует его в песочнице Azure в фоновом режиме так же, как и другие модули.

## <a name="migrate-to-az-modules"></a>Миграция в модули Az

В этом разделе описывается миграция в модули Az в службе автоматизации. Дополнительные сведения см. в статье [Перенос Azure PowerShell с AzureRM на Az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

Мы не рекомендуем использовать модули AzureRM и Az в одной учетной записи службы автоматизации. Если вам непременно необходимо выполнить миграцию с AzureRM на Az, лучше перенести весь модуль полностью. Служба автоматизации часто повторно использует песочницы в учетной записи службы автоматизации, чтобы сократить время запуска. Если вы не перенесете модуль полностью и при этом сначала запустите задание, использующее только модули AzureRM, а затем — другое задание, использующее только модули Az, вскоре произойдет сбой песочницы и появится сообщение об ошибке из-за несовместимости модулей. Это приведет к возникновению случайных сбоев для продольных runbook и конфигураций. 

>[!NOTE]
>При создании новой учетной записи службы автоматизации даже после миграции на модули Az эта служба установит модули AzureRM по умолчанию. Вы по-прежнему сможете обновлять учебные runbook с помощью командлетов AzureRM. Однако запускать эти runbook не следует.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Тестирование runbook и конфигураций DSC перед миграцией модулей

Перед миграцией на модули Az тщательно протестируйте все runbook и конфигурации DSC в отдельной учетной записи службы автоматизации. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Остановка и отмена выполнения по расписанию всех runbook, использующих модули AzureRM

Чтобы имеющиеся runbook или конфигурации DSC, использующие модули AzureRM, не запускались, необходимо остановить их все и отменить их выполнение по расписанию. Сначала проверьте отдельно каждую последовательность runbook и конфигурацию DSC, а также их расписания и убедитесь, что вы сможете при необходимости перепланировать элемент в будущем. 

Когда будете готовы удалить расписания, можно использовать портал Azure или командлет [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0). См. раздел [Удаление расписания](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Удаление модулей AzureRM

Перед импортом модулей Az можно удалить модули AzureRM. Однако в этом случае синхронизация системы управления исходным кодом может прерваться, что приведет к сбою всех скриптов, выполнение которых еще запланировано. Если вы решили удалить эти модули, ознакомьтесь с разделом [Удаление AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-az-modules"></a>Импорт модулей Az

При импорте модуля Az в учетную запись службы автоматизации он не импортируется автоматически в сеанс PowerShell, используемый последовательностями runbook. Модули импортируются в сеанс PowerShell в следующих ситуациях:

* если runbook вызывает командлет из модуля;
* если runbook импортирует модуль явным образом с помощью командлета [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7);
* если runbook импортирует другой зависимый модуль.

Модули Az можно импортировать на портал Azure. Не забудьте, что импортировать следует только необходимые модули Az, а не весь модуль Az.Automation. Поскольку от модуля [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) зависят другие модули Az, обязательно импортируйте его перед остальными.

1. В учетной записи службы автоматизации в области **Общие ресурсы** выберите **Модули**. 
2. Выберите **Обзор коллекции**.  
3. В строке поиска введите имя модуля (например, `Az.Accounts`). 
4. На странице модулей PowerShell выберите **Импорт**, чтобы импортировать модуль в учетную запись службы автоматизации.

    ![Снимок экрана, на котором показан импорт необходимых модулей в учетную запись службы автоматизации](../media/modules/import-module.png)

Этот процесс импорта можно также выполнить с помощью [коллекции PowerShell](https://www.powershellgallery.com), выполнив поиск импортируемого модуля. Когда вы найдете модуль, выберите его и перейдите на вкладку **Служба автоматизации Azure**. Выберите **Deploy to Azure Automation** (Развертывание в службу автоматизации Azure).

![Снимок экрана, на котором показан импорт модулей непосредственно из коллекции PowerShell](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Тестирование модулей runbook

После импорта модулей Az в учетную запись службы автоматизации можно начать редактирование runbook и конфигураций DSC, чтобы использовать новые модули. Один из способов тестирования изменений в runbook для использования новых командлетов — воспользоваться `Enable-AzureRmAlias -Scope Process` в начале runbook. Если добавить эту команду в модуль runbook, скрипт может выполняться без изменений. 

## <a name="author-modules"></a>Создание модулей

При создании пользовательского модуля PowerShell для использования в службе автоматизации Azure рекомендуем следовать рекомендациям, приведенным в данном разделе. Чтобы подготовить модуль к импорту, необходимо создать по крайней мере **DLL**-файл модуля psd1, psm1 или PowerShell с тем же именем, что и у папки модуля. Затем заархивируйте папку модуля, чтобы служба автоматизации Azure могла импортировать ее как один файл. Пакет **ZIP** должен иметь то же имя, что и вложенная папка модуля. 

Дополнительные сведения о создании модуля PowerShell см. в статье [Как написать модуль сценария PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

### <a name="version-folder"></a>Папка версии

НЕ включайте папку версии в **ZIP**-пакет модуля. Это не так актуально для runbook, но при использовании службы State Configuration (DSC) могут возникнуть проблемы. Служба автоматизации Azure создает папку версии автоматически при распространении модуля на узлы под управлением State Configuration. Если папка версии существует, то в итоге у вас будет два экземпляра. Ниже приведен пример структуры папок для модуля DSC.

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Справочная информация

Добавьте краткие сведения, описание и справочный универсальный код ресурса (URI) в каждый командлет вашего модуля. В PowerShell справочную информацию для командлетов можно определить с помощью командлета `Get-Help`. В следующем примере показано, как определить URI для кратких сведений и справки в файле модуля **PSM1**.

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Если эта информация предоставлена, в консоли PowerShell можно отобразить текст справки с помощью командлета `Get-Help`. Этот тест также отображается на портале Azure.

  ![Снимок экрана со справкой по модулю интеграции](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Тип соединений

Если модуль подключается к внешней службе, определите тип соединения, используя [пользовательский модуль интеграции](#custom-modules). Каждый командлет в модуле должен принимать экземпляр этого типа соединения (объект соединения) в качестве параметра. Пользователи могут сопоставлять параметры ресурса подключения с соответствующими параметрами командлета при каждом вызове командлета. 

![Использование пользовательского подключения на портале Azure](../media/modules/connection-create-new.png)

В следующем примере runbook для доступа к ресурсам Contoso и возврата данных из внешней службы используется ресурс подключения Contoso с именем `ContosoConnection`. В этом примере поля сопоставляются со свойствами `UserName` и `Password` объекта `PSCredential`, а затем передаются в командлет.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Проще и лучше всего передать объект подключения непосредственно в командлет.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Вы можете задать аналогичное поведение для командлетов, разрешив им принимать в качестве параметра напрямую объект подключения, а не только поля подключения. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации, мог вызывать командлеты без создания хэш-таблицы, используемой в качестве объекта подключения. Для передачи свойств поля подключения используется набор параметров `UserAccount`. `ConnectionObject` позволяет передать подключение напрямую.

### <a name="output-type"></a>Тип выходных данных

Укажите тип выходных данных для всех командлетов в модуле. При этом можно воспользоваться технологией IntelliSense, используемой во время разработки, чтобы определить свойства выходных данных командлета, используемых при создании модуля. Такой подход особенно полезен при графической разработке runbook, в случае которой возможность получать данные о работе модуля во время разработки — залог удобства для пользователей в будущем.

Добавьте `[OutputType([<MyOutputType>])]`, где `MyOutputType` является допустимым типом. Дополнительные сведения об `OutputType` см. в статье [о функциях OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). В следующем коде приведен пример добавления `OutputType` в командлет.

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Снимок экрана, на котором показан графический тип выходных данных runbook](../media/modules/runbook-graphical-module-output-type.png)

  Это поведение похоже на упреждающий ввод выходных данных командлета в среде службы интеграции PowerShell, при котором не нужно запускать командлет.

  ![Снимок экрана POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Состояние командлета

Не указывайте сведения о состоянии ни для одного из командлетов. Несколько заданий runbook могут одновременно выполняться в одних и тех же `AppDomain`, процессе и песочнице. Если на этих уровнях предоставляются сведения о состоянии, задания могут повлиять на друг друга. Такое поведение может привести к прерыванию в работе и проблемам, которые сложно диагностировать. Ниже представлен пример неправильного подхода.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Зависимость модуля

Убедитесь, что модуль полностью содержится в пакете, который можно скопировать с помощью xcopy. Модули службы автоматизации распространяются в песочницы службы автоматизации при выполнении runbook. Модули должны работать независимо от узла, на котором они выполняются. 

Вы должны иметь возможность заархивировать и переместить пакет модуля так, чтобы он работал как обычно при импорте в среду PowerShell другого узла. Для этого убедитесь, что модуль не зависит от файлов, находящихся вне его папки, которая архивируется при его импорте в службу автоматизации. 

Модуль не должен зависеть от каких-либо уникальных параметров реестра на узле. Их примером могут служить параметры, которые задаются при установке продукта. 

### <a name="module-file-paths"></a>Пути к файлам модуля

Убедитесь, что длина путей ко всем файлам в модуле не превышает 140 символов. Любые пути, длина которых превышает 140 символов, вызывают проблемы с импортом последовательностей runbook. Службе автоматизации не удается импортировать файл в сеанс PowerShell с помощью `Import-Module`, если путь к такому файлу состоит из более чем 140 символов.

## <a name="import-modules"></a>Импорт модулей

В этом разделе описывается несколько способов импорта модуля в учетную запись службы автоматизации. 

### <a name="import-modules-in-the-azure-portal"></a>Импорт модулей на портале Azure

Чтобы импортировать модуль на портале Azure, выполните следующие действия.

1. Перейдите к учетной записи службы автоматизации.
2. В разделе **Общие ресурсы** выберите **Модули**.
3. Выберите **Добавить модуль**. 
4. Выберите файл **ZIP**, содержащий модуль.
5. Нажмите кнопку **ОК**, чтобы начать процесс импорта.

### <a name="import-modules-by-using-powershell"></a>Импорт модулей с помощью PowerShell

Для импорта модуля в учетную запись службы автоматизации можно использовать командлет [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0). Командлет принимает URL-адрес для пакета ZIP модуля.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Вы также можете использовать тот же командлет для импорта модуля напрямую из коллекции PowerShell. Не забудьте получить `ModuleName` и `ModuleVersion` из [коллекции PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Импорт модулей из коллекции PowerShell

Вы можете импортировать модули [коллекции PowerShell](https://www.powershellgallery.com) либо непосредственно из коллекции, либо из учетной записи службы автоматизации.

Чтобы импортировать модуль непосредственно из коллекции PowerShell, выполните следующие действия.

1. Перейдите на страницу https://www.powershellgallery.com и найдите модуль для импорта.
2. В разделе **Параметры установки** на вкладке **Служба автоматизации Azure** выберите **Deploy to Azure Automation** (Развертывание в службу автоматизации Azure). Портал Azure можно открыть с помощью этого действия. 
3. На странице "Импорт" выберите учетную запись службы автоматизации и нажмите кнопку **ОК**.

![Снимок экрана, на котором показан процесс импорта модуля коллекции PowerShell](../media/modules/powershell-gallery.png)

Чтобы импортировать модуль коллекции PowerShell непосредственно из учетной записи службы автоматизации, выполните следующие действия.

1. В разделе **Общие ресурсы** выберите **Модули**. 
2. Выберите **Обзор коллекции**, а затем найдите модуль в коллекции. 
3. Выберите модуль для импорта и нажмите **Импорт**. 
4. Нажмите кнопку **ОК**, чтобы начать процесс импорта.

![Снимок экрана, на котором показан импорт модуля коллекции PowerShell на портале Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Удаление модулей

Если у вас возникли проблемы с модулем или необходимо выполнить откат к его предыдущей версии, его можно удалить из учетной записи службы автоматизации. Вы не можете удалить исходные версии [стандартных модулей](#default-modules), импортированные при создании учетной записи службы автоматизации. Если удаляемый модуль является более новой версией одного из [стандартных модулей](#default-modules), выполняется откат к версии, которая была установлена вместе с учетной записью службы автоматизации. Все остальные модули, удаляемые из учетной записи службы автоматизации, удаляются полностью.

### <a name="delete-modules-in-the-azure-portal"></a>Удаление модулей на портале Azure

Чтобы удалить модуль на портале Azure, выполните следующие действия.

1. Перейдите к учетной записи службы автоматизации. В разделе **Общие ресурсы** выберите **Модули**. 
2. Выберите модуль, который требуется удалить. 
3. На странице "Модуль" выберите **Удалить**. Если это один из [стандартных модулей](#default-modules), выполняется откат к версии, имевшейся на момент создания учетной записи службы автоматизации.

### <a name="delete-modules-by-using-powershell"></a>Удаление модулей с помощью PowerShell

Чтобы удалить модуль с помощью PowerShell, выполните следующую команду:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Дальнейшие действия

* [Getting started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0) (Приступая к работе с Azure PowerShell)
* [Написание модуля Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)
