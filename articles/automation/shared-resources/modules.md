---
title: Администрирование модулей в службе автоматизации Azure
description: В этой статье описывается, как управлять модулями в Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770941"
---
# <a name="manage-modules-in-azure-automation"></a>Администрирование модулей в службе автоматизации Azure

Azure Automation позволяет импортировать модули PowerShell для включения cmdlets в рунбуки и ресурсы DSC в конфигурациях DSC. Модули, используемые в автоматизации Azure, включают:

* [Azure PowerShell Az.Автоматизация](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.Автоматизация](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Внутренний `Orchestrator.AssetManagement.Cmdlets` модуль для агента анализа журналов для Windows
* [AzureAutomationАвтортуликт](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Другие модули PowerShell
* Пользовательские модули, которые создаются 

При создании учетной записи автоматизации Azure Automation импортирует некоторые модули по умолчанию. Посмотреть [модули по умолчанию](#default-modules).

Когда Azure Automation выполняет задания компиляции Runbook и DSC, она загружает модули в песочницы, где могут работать руны и компилировать конфигурации DSC. Автоматизация также автоматически размещает любые ресурсы DSC в модулях на сервере DSC pull. Машины могут тянуть ресурсы при применении конфигураций DSC.

>[!NOTE]
>Не забудьте импортировать только те модули, которые действительно нужны вашим ступеням и конфигурациям DSC. Ни в коем случае не импортируйте модуль свертывания, например, Az.Automation.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="default-modules"></a>Модули по умолчанию

В следующей таблице перечислены модули, которые Azure Automation импортирует по умолчанию при создании учетной записи автоматизации. Автоматизация может импортировать новые версии этих модулей. Однако вы не можете удалить исходную версию из учетной записи Automation, даже если вы удалите новую версию. Обратите внимание, что эти модули по умолчанию включают несколько модулей AzureRM. 

> [!NOTE]
> Мы не рекомендуем изменять модули и книги в учетных записях автоматизации, которые содержат какие-либо решения. 

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
| Оркестр.AssetManagement.Cmdlets | 1 |
| PSDscРесурсы | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeРесурсы | 1 |
| xDSCDomainjoin | 1,1 |
| xPowerShellExecutionПолитика | 1.1.0.0 |
| xУдаленныйнанаАдмин | 1.1.0.0 |


Модули Az.Automation являются предпочтительными в ваших запусках и конфигурациях DSC. Однако Azure Automation не импортирует модуль Az автоматически в новые или существующие учетные записи автоматизации. Подробнее о работе с этими модулями читайте в [мигрирующих модулях Az.](#migrating-to-az-modules)

## <a name="internal-cmdlets"></a>Внутренние смдлеты

В следующей таблице определяются внутренние `Orchestrator.AssetManagement.Cmdlets` cmdlets, поддерживаемые модулем. Используйте их в своих runbooks и конфигурациях DSC для взаимодействия с активами автоматизации в учетной записи Автоматизации. Cmdlets предназначены для получения секретов из зашифрованных переменных, учетных данных и зашифрованных соединений. 

> [!NOTE]
> Смдлеты Azure PowerShell не могут получить секреты активов, которые могут получить внутренние cmdlets. 

|Имя|Описание|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Старт-АвтоматизацияRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Эти внутренние cmdlets доступны на Windows Hybrid Runbook работника, но не на Linux Гибридный Runbook работника. Они выполнены из песочницы оркестратора, которая используется гибридным работником.  Их использование не требует неявного подключения к Azure, как при использовании учетной записи Run As для проверки подлинности.

Вместо использования внутренних cmdlets используйте смлеты Az или AzureRM для запусков и конфигураций, работающих непосредственно на компьютере или против ресурсов в вашей среде. В этих случаях необходимо неявно подключиться к Azure при использовании cmdlets, как при использовании учетной записи Run As для проверки подлинности в Azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Модули, поддерживающие Get-AutomationPSCredential

Для локальной разработки с помощью инструмента автоматизации Azure, `Get-AutomationPSCredential` cmdlet является частью сборки [AzureAutomationAuthoringToolkit.](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9) Для Работы Azure с контекстом автоматизации `Orchestrator.AssetManagement.Cmdlets`cmdlet находится в . Подробнее об использовании учетных данных в Azure Automation можно найти [в ресурсах Azure Automation.](credentials.md)

## <a name="migrating-to-az-modules"></a>Переход к модулям Az

При использовании модулей Az в Azure Automation необходимо учитывать несколько вещей:

* Решения более высокого уровня в учетной записи автоматизации могут использовать рунки и модули. Поэтому редактирование runbooks или модули обновления потенциально могут вызвать проблемы с вашими решениями. Перед импортом модулей Az необходимо тщательно протестировать все рунбуки и решения в отдельной учетной записи Automation. 

* Любые изменения модулей могут негативно повлиять на [решение Start/Stop VMs в нерабочее время.](../automation-solution-vm-management.md) 

* Импорт модуля Az в учетную запись автоматизации не автоматически импортирует модуль в сеансе PowerShell, который использует runbooks. Модули импортируются в сеанс PowerShell в следующих ситуациях:

    * Когда беговая книга вызывает cmdlet из модуля
    * Когда runbook импортирует модуль явно `Import-Module` с cmdlet
    * Когда runbook импортирует другой зависимый модуль

После завершения миграции модулей не пытайтесь запускать запущенные книги с помощью модулей AzureRM в учетной записи Автоматизации. Также рекомендуется не импортировать и не обновлять модули AzureRM в учетной записи. Рассмотрим учетную запись, перенесенную на Az.Automation, и работайте только с модулями Az. 

>[!IMPORTANT]
>При создании новой учетной записи автоматизации Azure Automation устанавливает модули AzureRM по умолчанию. Вы все еще можете обновить учебники с помощью cmdlets AzureRM. Тем не менее, вы не должны запускать эти runbooks.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Остановить и разпланировать все запуски, в которые используются модули AzureRM

Чтобы убедиться, что вы не запустите существующие runbooks, которые используют модули AzureRM, остановите и разогнайте все затронутые запуски. Вы можете увидеть, какие графики существуют и какие графики удалить, запустив код, похожий на этот пример:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Важно, чтобы рассмотреть каждое расписание отдельно, чтобы убедиться, что вы можете перенести его в будущем для ваших runbooks, если это необходимо.

### <a name="import-the-az-modules"></a>Импорт модулей Az

В этом разделе рассказывается о том, как импортировать модули Az на портале Azure. Не забудьте импортировать только нужные модули Az, а не весь модуль Az.Automation. Поскольку [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) является зависимостью от других модулей Az, не забудьте импортировать этот модуль раньше любых других.

1. Из учетной записи автоматизации выберите **Модули** под **общими ресурсами.** 
2. Нажмите кнопку **Обзор коллекции**, чтобы открыть страницу Обзор коллекции.  
3. В панели поиска, например, `Az.Accounts`введите имя модуля. 
4. На странице модуля PowerShell щелкните **«Импорт»,** чтобы импортировать модуль в учетную запись автоматизации.

    ![Импортные модули для учетной записи автоматизации](../media/modules/import-module.png)

Этот процесс импорта также может быть выполнен через [галерею PowerShell](https://www.powershellgallery.com) путем поиска модуля для импорта. Как только вы найдете модуль, выберите его, выберите вкладку **Azure Automation** и нажмите **Развертывание в Azure Automation.**

![Импорт модулей непосредственно из коллекции](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Тестирование модулей runbook

После того как вы импортировали модули Az в учетную запись Автоматизация, вы можете начать редактирование runbooks для использования новых модулей. Большинство cmdlets имеют те же названия, что и для модулей AzureRM, за исключением того, что префикс AzureRM (или AzureRm) был изменен на Az. Список модулей, не выполняемых в этой конвенции о присвоении имен, смотрите [список исключений.](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)

Один из способов проверить модификацию runbook для использования новых `Enable-AzureRmAlias -Scope Process` cmdlets является использование в начале runbook. Добавляя эту команду в свой runbook, скрипт может работать без изменений.

## <a name="authoring-modules"></a>Авторинговые модули

Мы рекомендуем вам следовать соображениям в этом разделе, когда вы авторите модуль PowerShell для использования в Azure Automation.

### <a name="references-to-azurerm-and-az"></a>Ссылки на AzureRM и Az

Если ссылки на модули Az в модуле убедитесь, что вы также не ссылаетесь на модули AzureRM. Вы не можете использовать оба набора модулей одновременно. 

### <a name="version-folder"></a>Папка версии

НЕ включайте папку версии в пакет **.zip** для модуля.  Эта проблема в меньшей безопасности вызывает озабоченность в отношении runbooks, но вызывает проблемы со службой государственного конфигурации (DSC). Azure Automation автоматически создает папку версии при распределении модуля на узлы, управляемые DSC. Если папка версии существует, вы в конечном итоге с двумя экземплярами. Вот пример структуры папки для модуля DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Информация о справках

Включите резюме, описание и помощь URI для каждого cmdlet в вашем модуле. В PowerShell можно определить информацию о справках для cmdlets с помощью `Get-Help` cmdlet. Ниже приводится пример, как определить резюме и помочь URI в файле модуля **.psm1.**

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

  Предоставление этой информации показывает `Get-Help` текст справки через cmdlet в консоли PowerShell. Этот текст также отображается на портале Azure.

  ![Справка по модулям интеграции](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Тип соединений

Если модуль подключается к внешней службе, определите [тип соединения.](#adding-a-connection-type-to-your-module) Каждый cmdlet в модуле должен принимать объект соединения (экземпляр этого типа соединения) в качестве параметра. Пользователи отображают параметры актива соединения с соответствующими параметрами cmdlet каждый раз, когда они называют cmdlet. В следующем примере runbook, основанном на примере предыдущего раздела, используется актив соединения Contoso, призванный `ContosoConnection` получить доступ к ресурсам Contoso и вернуть данные из внешнего сервиса. В этом примере поля отображаются по `UserName` свойствам `Password` `PSCredential` объекта, а затем передаются в cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Более простой и лучший способ приблизиться к этому поведению — это прямое прохождение объекта соединения к cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Можно включить аналогичное поведение для ваших cmdlets, позволяя им принимать объект соединения непосредственно в качестве параметра, а не только поля соединения для параметров. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации Azure, мог вызывать командлеты, не создавая хэш-таблицу, используемую в качестве объекта подключения. Набор `UserAccount` параметров используется для передачи свойств поля соединения. `ConnectionObject`позволяет пройти соединение прямо через.

### <a name="output-type"></a>Тип выходных данных

Определите тип вывода для всех cmdlets в модуле. Определение типа вывода для cmdlet позволяет проектированию времени IntelliSense, чтобы помочь определить выходные свойства cmdlet во время авторизации. Эта практика особенно полезна во время графического автора runbook, для которого дизайн времени знания является ключом к простой пользовательский опыт с вашим модулем.

Добавьте, `[OutputType([<MyOutputType>])]` где `MyOutputType` является действительным типом. Чтобы узнать `OutputType`больше о, см. [About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) Следующий код является примером добавления `OutputType` в cmdlet:

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

### <a name="cmdlet-state"></a>Состояние Кмдлет

Сделайте все cmdlets в вашем модуле без состояния. Несколько заданий Runbook `AppDomain` могут одновременно работать в том же и том же процессе и песочнице. Если на этих уровнях есть какое-либо состояние, где имеются общие сведения, задания могут влиять друг на друга. Такое поведение может привести к периодическим и трудно диагностировать проблемы. Вот пример того, что НЕ делать:

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

Убедитесь, что модуль полностью содержится в пакете, который можно скопировать с помощью xcopy. Модули Azure Automation распространяются в песочницах автоматизации при выполнении рун. Модули должны работать независимо от узла, который их запускает. 

Вы должны быть в состоянии застегнуть и переместить модуль пакет и его функции, как обычно, когда импортируется в powerShell среды другого хозяина. Чтобы это произошло, убедитесь, что модуль не зависит от файлов за пределами папки модуля, которая застегивается при импорте модуля в Azure Automation. 

Ваш модуль не должен зависеть от каких-либо уникальных настроек реестра на хосте. Примерами являются настройки, которые создаются при установке продукта. 

### <a name="module-file-paths"></a>Пути файла модуля

Убедитесь, что все файлы в модуле имеют пути с менее чем 140 символов. Любые пути более 140 символов в длину вызывают проблемы с импортом runbooks. Azure Automation не может импортировать файл с размером пути более 140 символов в сеанс PowerShell с `Import-Module`помощью.

## <a name="importing-modules"></a>Импорт модулей

В этом разделе определено несколько способов ввоза модуля в учетную запись автоматизации. 

### <a name="import-modules-in-azure-portal"></a>Импортные модули на портале Azure

Импорт модуля на портале Azure:

1. Перейдите к учетной записи службы автоматизации.
2. Выберите **модули** под **общими ресурсами.**
3. Нажмите **Добавить модуль**. 
4. Выберите файл **.zip,** содержащий ваш модуль.
5. Нажмите **OK,** чтобы начать процесс импорта.

### <a name="import-modules-using-powershell"></a>Импортные модули с использованием PowerShell

Вы можете использовать смдlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) для импорта модуля в учетную запись автоматизации. Cmdlet принимает URL для пакета модуля .zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Вы также можете использовать тот же cmdlet импортировать модуль из PowerShell Галерея напрямую. Убедитесь в `ModuleName` `ModuleVersion` том, чтобы захватить и из [PowerShell Галерея](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Импортные модули из галереи PowerShell

Модули [PowerShell Gallery](https://www.powershellgallery.com) можно импортировать либо непосредственно из галереи, либо из учетной записи Автоматизации.

Импорт модуля непосредственно из галереи PowerShell:

1. Перейти https://www.powershellgallery.com к и искать модуль для импорта.
2. Нажмите **Развертывание для автоматизации Azure** на вкладке **Azure Automation** под параметры **установки.** Это действие открывает портал Azure. 
3. На странице Импорта выберите учетную запись Автоматизации и нажмите **OK**.

![Модуль импорта галереи PowerShell](../media/modules/powershell-gallery.png)

Импорт модуля PowerShell Gallery непосредственно из учетной записи автоматизации:

1. Выберите **модули** под **общими ресурсами.** 
2. На странице Модулей нажмите **Просмотр галереи**, а затем искать в галерее для модуля. 
3. Выберите модуль для импорта и нажмите **Импорт.** 
4. На странице Импорта нажмите **OK,** чтобы начать процесс импорта.

![Импорт Галереи PowerShell с портала Azure](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Модули деления

Если у вас возникли проблемы с модулем или вам нужно откатиться к предыдущей версии модуля, вы можете удалить его из учетной записи Автоматизация. Нельзя удалять исходные версии [модулей по умолчанию, импортируемых](#default-modules) при создании учетной записи Автоматизации. Если модуль для удаления является новой версией одного из [модулей по умолчанию,](#default-modules)он откатывается к версии, которая была установлена с вашей учетной записью Автоматизация. В противном случае любой модуль, удаленный из учетной записи Automation, удаляется.

### <a name="delete-modules-in-azure-portal"></a>Удаление модулей на портале Azure

Чтобы удалить модуль на портале Azure:

1. Перейдите к учетной записи автоматизации и выберите **модули** под **общими ресурсами.** 
2. Выберите модуль, который вы хотите удалить. 
3. На странице **Модуля** выберите **Удалить**. Если этот модуль является одним из [модулей по умолчанию,](#default-modules)он возвращается к версии, которая существовала при создании учетной записи Автоматизации.

### <a name="delete-modules-using-powershell"></a>Удаление модулей с помощью PowerShell

Чтобы удалить модуль через PowerShell, запустите следующую команду:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Добавление типа соединения в модуль

Вы можете предоставить пользовательский [тип соединения](../automation-connections.md) для использования в вашей учетной записи Automation, добавив дополнительный файл метаданных в модуль. В этом файле указывается тип подключения Azure Automation, который будет использоваться с cmdlets модуля в вашей учетной записи Automation. Чтобы узнать больше об авторизации модуля PowerShell, [см.](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)

![Используйте пользовательское соединение на портале Azure](../media/modules/connection-create-new.png)

Файл, указывающий свойства типа соединения, называется ** &lt;ModuleName-Automation.json&gt;** и находится в папке модуля сжатого файла **.zip.** Этот файл содержит поля соединения, которые необходимы для подключения к системе или службе, которую представляет модуль. Конфигурация позволяет создавать тип соединения в Azure Automation. С помощью этого файла можно установить имена полей, типы и то, зашифрованы ли поля или необязательны для типа соединения модуля. Ниже приводится шаблон в формате файла **.json,** определяющий свойства имени пользователя и пароля:

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

## <a name="next-steps"></a>Следующие шаги

* Для получения дополнительной информации об использовании модулей Azure PowerShell [см.](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)
* Чтобы узнать больше о создании модулей PowerShell, смотрите [Написать модуль Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
