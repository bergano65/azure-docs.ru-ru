---
title: Администрирование модулей в службе автоматизации Azure
description: В этой статье описывается, как управлять модулями в службе автоматизации Azure.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8d22e63be880c0cef0c4072e99ab85bf3250a1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82114280"
---
# <a name="manage-modules-in-azure-automation"></a>Администрирование модулей в службе автоматизации Azure

Служба автоматизации Azure позволяет импортировать модули PowerShell, чтобы включить командлеты в модулях Runbook и ресурсах DSC в конфигурациях DSC. Модули, используемые в службе автоматизации Azure, включают:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Внутренний `Orchestrator.AssetManagement.Cmdlets` модуль для агента log Analytics для Windows
* Другие модули PowerShell
* Создаваемые пользовательские модули 

При создании учетной записи службы автоматизации Azure по умолчанию импортирует некоторые модули. См. раздел [модули по умолчанию](#default-modules).

Когда служба автоматизации Azure выполняет задания компиляции Runbook и DSC, она загружает модули в песочницы, в которых могут выполняться модули Runbook, а конфигурации DSC могут быть скомпилированы. Автоматизация также автоматически размещает все ресурсы DSC в модулях на опрашивающем сервере DSC. Компьютеры могут извлекать ресурсы при применении конфигураций DSC.

>[!NOTE]
>Не забудьте импортировать только модули, которые действительно необходимы для модулей Runbook и конфигураций DSC. Мы не рекомендуем импортировать корневой модуль AZ, так как он включает множество других модулей, которые могут не потребоваться, что может вызвать проблемы с производительностью. Вместо этого импортируйте отдельные модули, например AZ. COMPUTE.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Модули по умолчанию

В следующей таблице перечислены модули, которые служба автоматизации Azure импортирует по умолчанию при создании учетной записи службы автоматизации. Автоматизация может импортировать более новые версии этих модулей. Однако вы не сможете удалить исходную версию из учетной записи службы автоматизации, даже если вы удалите более новую версию. Обратите внимание, что эти модули по умолчанию включают несколько модулей AzureRM. 

Служба автоматизации Azure не импортирует корневой модуль AZ автоматически в любые новые или существующие учетные записи службы автоматизации. Дополнительные сведения о работе с этими модулями см. [в разделе Переход на AZ modules](#migrating-to-az-modules).

> [!NOTE]
> В учетных записях службы автоматизации, содержащих [решение запуск и остановка виртуальных машин в нерабочее время в службе автоматизации Azure](../automation-solution-vm-management.md), не рекомендуется изменять модули и Runbook.

|Имя модуля|Версия|
|---|---|
| аудитполицидск | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage; | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| гпрегистриполиципарсер | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator. Ассетманажемент. командлеты | 1 |
| псдскресаурцес | 2.9.0.0 |
| секуритиполицидск | 2.1.0.0 |
| статеконфигкомпоситересаурцес | 1 |
| ксдскдомаинжоин | 1.1 |
| ксповершеллексекутионполици | 1.1.0.0 |
| ксремотедесктопадмин | 1.1.0.0 |

## <a name="az-module-cmdlets"></a>AZ Module командлеты

Для AZ. Automation большинство командлетов имеют те же имена, что и для модулей AzureRM, за исключением того, что префикс AzureRm был изменен на AZ. Список модулей AZ, которые не соответствуют соглашению об именовании, см. в разделе [список исключений](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Внутренние командлеты

В следующей таблице определены внутренние командлеты, поддерживаемые `Orchestrator.AssetManagement.Cmdlets` модулем. Используйте эти командлеты в модулях Runbook и конфигурациях DSC для взаимодействия с ресурсами Azure в учетной записи службы автоматизации. Командлеты предназначены для использования вместо командлетов Azure PowerShell для получения секретов из зашифрованных переменных, учетных данных и зашифрованных соединений. 

>[!NOTE]
>Внутренние командлеты доступны только при выполнении модулей Runbook в среде "песочницы" Azure или в гибридной рабочей роли Runbook Windows. 

|Имя|Описание|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-Аутоматионрунбук|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-Аутоматионжоб|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Обратите внимание, что внутренние командлеты отличаются по именам из командлетов AZ и AzureRM. Имена внутренних командлетов не содержат слов, например "Azure" или "AZ", в существительных, но используют слово "Automation". Мы рекомендуем использовать командлеты az или AzureRM во время выполнения Runbook в песочнице Azure или в гибридной рабочей роли Windows. Они занимают меньше параметров и выполняются в контексте уже выполняемого задания.

Мы рекомендуем использовать командлеты az или AzureRM для управления ресурсами службы автоматизации Azure вне контекста модуля Runbook. 

## <a name="module-supporting-get-automationpscredential"></a>Модуль, поддерживающий Get-AutomationPSCredential

`Get-AutomationPSCredential` Командлет является частью модуля `Orchestrator.AssetManagement.Cmdlets`. Этот командлет возвращает `PSCredential` объект, который ожидается большинством командлетов PowerShell, работающих с учетными данными. Дополнительные сведения об использовании учетных данных в службе автоматизации Azure см. в статье [ресурсы учетных данных в службе автоматизации Azure](credentials.md).

## <a name="migrating-to-az-modules"></a>Переход к модулям Az

### <a name="migration-considerations"></a>Вопросы миграции

В этом разделе приводятся рекомендации, которые необходимо учитывать при переходе на модули az в службе автоматизации Azure. См. также [миграция Azure PowerShell из AzureRM в AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>Использование модулей AzureRM и AZ modules в одной учетной записи службы автоматизации

 Мы не рекомендуем запускать модули AzureRM и az в одной и той же учетной записи службы автоматизации. Если вы уверены, что хотите выполнить миграцию с AzureRM на AZ, то лучше полностью зафиксировать полную миграцию. Наиболее важная причина в том, что служба автоматизации Azure часто использует песочницы в учетной записи службы автоматизации для экономии времени запуска. Если вы не сделаете полную миграцию модулей, вы можете запустить задание, используя только модули AzureRM, а затем запустить другое задание, используя только AZ modules. Вскоре произойдет сбой песочницы, и вы получите неустранимую ошибку, сообщающую, что модули не совместимы. Эта ситуация приводит к случайному возникновению сбоев для всех заданных модулей Runbook или конфигурации. 

#### <a name="import-of-az-modules-into-the-powershell-session"></a>Импорт модулей az в сеанс PowerShell

При импорте модуля az в учетную запись службы автоматизации модуль не импортируется автоматически в сеанс PowerShell, используемый модулями Runbook. Модули импортируются в сеанс PowerShell в следующих ситуациях:

* Когда модуль Runbook вызывает командлет из модуля
* Когда Runbook импортирует модуль явным образом с помощью командлета [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7)
* Когда модуль Runbook импортирует другой зависимый модуль
    
#### <a name="testing-for-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Тестирование модулей Runbook и конфигураций DSC до миграции модулей

Прежде чем переходить на модули AZ, тщательно протестируйте все модули Runbook и конфигурации DSC в отдельной учетной записи службы автоматизации. 

#### <a name="updates-for-tutorial-runbooks"></a>Обновления для учебных модулей Runbook 

При создании новой учетной записи службы автоматизации, даже после миграции в AZ Modules, служба автоматизации Azure по умолчанию устанавливает модули AzureRM. Вы по-прежнему можете обновить учебные модули Runbook с помощью командлетов AzureRM. Однако не следует запускать эти модули Runbook.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Останавливает и отменяет планирование всех модулей Runbook, использующих модули AzureRM

Чтобы не запускать существующие модули Runbook или конфигурации DSC, использующие AzureRM Modules, необходимо приостанавливаться и отменять планирование всех затронутых модулей Runbook и конфигураций. Сначала убедитесь, что каждый модуль Runbook или Конфигурация DSC и его расписания просматриваются отдельно, чтобы при необходимости можно было перепланировать элемент в будущем. 

Когда вы будете готовы удалить расписания, можно либо использовать портал Azure, либо командлет [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . См. раздел [Удаление расписания](schedules.md#removing-a-schedule).

### <a name="remove-the-azurerm-modules"></a>Удаление модулей AzureRM

Можно удалить модули AzureRM перед импортом модулей AZ. Однако удаление модулей AzureRM может привести к прерыванию синхронизации системы управления версиями и вызвать сбой всех сценариев, которые по-прежнему запланированы. Если вы решили удалить эти модули, см. раздел [uninstall AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-the-az-modules"></a>Импорт модулей Az

В этом разделе описывается, как импортировать модули az в портал Azure. Не забудьте импортировать только необходимые модули AZ, а не весь модуль AZ. Automation. Поскольку [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) является зависимостью от других модулей AZ, обязательно импортируйте этот модуль перед другими.

1. В учетной записи службы автоматизации выберите **модули** в разделе **Общие ресурсы**. 
2. Нажмите кнопку **Обзор коллекции**, чтобы открыть страницу Обзор коллекции.  
3. В строке поиска введите имя модуля, например `Az.Accounts`. 
4. На странице модуля PowerShell нажмите кнопку **Импорт** , чтобы импортировать модуль в учетную запись службы автоматизации.

    ![Импорт модулей в учетную запись службы автоматизации](../media/modules/import-module.png)

Этот процесс импорта можно также выполнить с помощью [коллекция PowerShell](https://www.powershellgallery.com) , выполнив поиск импортируемого модуля. Найдя модуль, выберите его, перейдите на вкладку Служба **автоматизации Azure** и щелкните **развернуть в службе автоматизации Azure**.

![Импорт модулей непосредственно из коллекции](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Тестирование модулей runbook

После импорта модулей az в учетную запись службы автоматизации можно начать редактирование модулей Runbook и конфигураций DSC, чтобы использовать новые модули. Одним из способов тестирования изменения модуля Runbook для использования новых командлетов является использование `Enable-AzureRmAlias -Scope Process` в начале модуля Runbook. При добавлении этой команды в модуль Runbook скрипт может выполняться без изменений. 

## <a name="authoring-modules"></a>Создание модулей

При создании модуля PowerShell для использования в службе автоматизации Azure рекомендуется следовать рекомендациям, приведенным в этом разделе.

### <a name="version-folder"></a>Папка версии

НЕ включайте папку версии в **ZIP** -пакет для модуля.  Эта проблема менее важна для модулей Runbook, но вызывает проблему со службой настройки состояния (DSC). Служба автоматизации Azure создает папку версии автоматически при распространении модуля на узлы, управляемые DSC. Если папка версии существует, то в итоге у вас будет два экземпляра. Ниже приведен пример структуры папок для модуля DSC.

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Справочная информация

Включите краткий обзор, описание и URI справки для каждого командлета в вашем модуле. В PowerShell можно определить справочную информацию для командлетов с помощью `Get-Help` командлета. В следующем примере показано, как определить краткий и справочный URI в файле модуля **PSM1** .

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

  При предоставлении этой информации в консоли PowerShell отображается `Get-Help` текст справки с помощью командлета. Этот текст также отображается в портал Azure.

  ![Справка по модулям интеграции](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Тип подключения

Если модуль подключается к внешней службе, определите [тип соединения](#adding-a-connection-type-to-your-module). Каждый командлет в модуле должен принимать объект соединения (экземпляр этого типа соединения) в качестве параметра. Пользователи сопоставляют параметры ресурса подключения с соответствующими параметрами командлета каждый раз, когда они вызывают командлет. Следующий пример модуля Runbook, основанный на примере из предыдущего раздела, использует ресурс-контейнер подключения Contoso `ContosoConnection` для доступа к ресурсам Contoso и возврата данных из внешней службы. В этом примере поля сопоставляются со свойствами `UserName` и `Password` `PSCredential` объекта, а затем передаются в командлет.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Проще и лучше подходить это поведение, напрямую передав объект подключения командлету:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Вы можете включить аналогичное поведение для командлетов, разрешая им принимать объект соединения непосредственно в качестве параметра, а не только полей соединения для параметров. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации Azure, мог вызывать командлеты, не создавая хэш-таблицу, используемую в качестве объекта подключения. Набор `UserAccount` параметров используется для передачи свойств поля соединения. `ConnectionObject`позволяет передавать соединение напрямую через.

### <a name="output-type"></a>Тип выходных данных

Определите тип выходных данных для всех командлетов в модуле. Определение типа выходных данных для командлета позволяет IntelliSense во время разработки определить выходные свойства командлета во время разработки. Такой подход особенно полезен во время графического создания модулей Runbook, для которого знание времени разработки является ключом к простоте взаимодействия с вашим модулем.

Добавить `[OutputType([<MyOutputType>])]` , `MyOutputType` где является допустимым типом. Дополнительные сведения о `OutputType`функциях см. в разделе [About functions аутпуттипеаттрибуте](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Следующий код является примером добавления `OutputType` в командлет:

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

  ![Тип выходных данных графического модуля Runbook](../media/modules/runbook-graphical-module-output-type.png)

  Это поведение похоже на упреждающий ввод выходных данных командлета в интегрированной среде сценариев PowerShell, при котором не нужно запускать командлет.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Состояние командлета

Сделайте все командлеты без отслеживания состояния модуля. Несколько заданий Runbook могут одновременно выполняться в одном `AppDomain` и том же процессе и в песочнице. Если на этих уровнях имеется общий доступ к состоянию, задания могут повлиять на друг друга. Такое поведение может привести к нерегулярному и сложному диагностике проблем. Ниже приведен пример того, что не нужно делать.

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

Убедитесь, что модуль полностью содержится в пакете, который можно скопировать с помощью XCOPY. Модули службы автоматизации Azure распространяются в песочницу службы автоматизации при выполнении модулей Runbook. Модули должны работать независимо от узла, на котором они выполняются. 

Вы сможете создать резервную копию и переместить пакет модуля и обеспечить его нормальную работу при импорте в среду PowerShell другого узла. Для этого убедитесь, что модуль не зависит от файлов, находящихся вне папки модуля, которая заархивирована при импорте модуля в службу автоматизации Azure. 

Модуль не должен зависеть от каких-либо уникальных параметров реестра на узле. Примерами являются параметры, выполняемые при установке продукта. 

### <a name="module-file-paths"></a>Пути к файлам модулей

Убедитесь, что все файлы в модуле имеют пути, длина которых не превышает 140 символов. Любой путь, длина которого превышает 140 символов, вызывает проблемы с импортом модулей Runbook. Службе автоматизации Azure не удается импортировать файл с размером пути свыше 140 символов в сеанс PowerShell с `Import-Module`.

## <a name="importing-modules"></a>Импорт модулей

В этом разделе описывается несколько способов импорта модуля в учетную запись службы автоматизации. 

### <a name="import-modules-in-azure-portal"></a>Импорт модулей в портал Azure

Чтобы импортировать модуль в портал Azure, выполните следующие действия.

1. Перейдите к учетной записи службы автоматизации.
2. Выберите **модули** в разделе **Общие ресурсы**.
3. Нажмите кнопку **Добавить модуль**. 
4. Выберите **ZIP** -файл, содержащий модуль.
5. Нажмите кнопку **ОК** , чтобы начать импорт процесса.

### <a name="import-modules-using-powershell"></a>Импорт модулей с помощью PowerShell

Для импорта модуля в учетную запись службы автоматизации можно использовать командлет [New-азаутоматионмодуле](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) . Командлет принимает URL-адрес для пакета Module. zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Вы также можете использовать тот же командлет для импорта модуля из коллекция PowerShell напрямую. Убедитесь, что вы `ModuleName` захватите и `ModuleVersion` [отколлекция PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Импорт модулей из коллекция PowerShell

Вы можете импортировать [коллекция PowerShell](https://www.powershellgallery.com) модули непосредственно из коллекции или из учетной записи службы автоматизации.

Чтобы импортировать модуль непосредственно из коллекция PowerShell:

1. Перейдите в https://www.powershellgallery.com раздел и найдите модуль для импорта.
2. Щелкните **развернуть в службе автоматизации Azure** на вкладке Служба **автоматизации Azure** в разделе **Параметры установки**. Это действие открывает портал Azure. 
3. На странице "Импорт" выберите учетную запись службы автоматизации и нажмите кнопку **ОК**.

![Модуль импорта коллекция PowerShell](../media/modules/powershell-gallery.png)

Чтобы импортировать модуль коллекция PowerShell непосредственно из учетной записи службы автоматизации, выполните следующие действия.

1. Выберите **модули** в разделе **Общие ресурсы**. 
2. На странице Модули щелкните **Обзор коллекции**, а затем выполните поиск модуля в коллекции. 
3. Выберите модуль для импорта и нажмите кнопку **Импорт**. 
4. На странице Импорт нажмите кнопку **ОК** , чтобы начать процесс импорта.

![коллекция PowerShell импорт из портал Azure](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Удаление модулей

Если у вас возникли проблемы с модулем или необходимо выполнить откат к предыдущей версии модуля, его можно удалить из учетной записи службы автоматизации. Нельзя удалить исходные версии [модулей по умолчанию](#default-modules) , которые импортируются при создании учетной записи службы автоматизации. Если удаляемый модуль является более новой версией одного из [модулей по умолчанию](#default-modules), он выполняет откат к версии, которая была установлена вместе с учетной записью службы автоматизации. В противном случае удаляется любой модуль, удаляемый из учетной записи службы автоматизации.

### <a name="delete-modules-in-azure-portal"></a>Удаление модулей в портал Azure

Чтобы удалить модуль из портал Azure:

1. Перейдите к учетной записи службы автоматизации и выберите **модули** в разделе **Общие ресурсы**. 
2. Выберите модуль, который требуется удалить. 
3. На странице **модуль** выберите **Удалить**. Если этот модуль является одним из [модулей по умолчанию](#default-modules), он выполняет откат к версии, существовавшей при создании учетной записи службы автоматизации.

### <a name="delete-modules-using-powershell"></a>Удаление модулей с помощью PowerShell

Чтобы удалить модуль с помощью PowerShell, выполните следующую команду:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Добавление типа подключения в модуль

Вы можете указать пользовательский [тип подключения](../automation-connections.md) для использования в учетной записи службы автоматизации, добавив в модуль необязательный файл метаданных. Этот файл указывает тип подключения службы автоматизации Azure, который будет использоваться с командлетами модуля в вашей учетной записи службы автоматизации. Дополнительные сведения о создании модуля PowerShell см. в статье [написание модуля сценариев PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Использовать пользовательское соединение в портал Azure](../media/modules/connection-create-new.png)

Файл, в котором указываются свойства типа подключения, называется ** &lt;ModuleName&gt;-Automation. JSON** и находится в папке Module сжатого файла **ZIP** . Этот файл содержит поля подключения, необходимые для подключения к системе или службе, которую представляет модуль. Конфигурация позволяет создать тип подключения в службе автоматизации Azure. С помощью этого файла можно задать имена полей, типы, а также указать, являются ли поля зашифрованными или необязательными для типа соединения модуля. Ниже приведен пример шаблона в формате **JSON** , который определяет свойства имени пользователя и пароля:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения об использовании модулей Azure PowerShell см. в статье [Приступая к работе с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Дополнительные сведения о создании модулей PowerShell см. в разделе [написание модуля Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
