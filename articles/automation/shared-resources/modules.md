---
title: Управление модули в службе автоматизации Azure
description: В этой статье описывается управление модули в службе автоматизации Azure
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd085164fc9804e0c1c822df1c72d3ef94093a07
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672785"
---
# <a name="manage-modules-in-azure-automation"></a>Управление модули в службе автоматизации Azure

Служба автоматизации Azure предоставляет возможность импортировать модули PowerShell в учетной записи службы автоматизации для использования модулей Runbook на основе PowerShell. Эти модули могут быть пользовательскими модулями, которые вы создали, из коллекции PowerShell, или модули AzureRM и Az для Azure. При создании учетной записи службы автоматизации некоторые модули импортируются по умолчанию.

## <a name="import-modules"></a>Импорт модулей

Существует несколько способов, что можно импортировать модуль в учетной записи службы автоматизации. В следующих разделах показаны различные способы импорта модуля.

> [!NOTE]
> Max путь файла в модуль для использования в службе автоматизации Azure — 140 символов. Любой путь, больше 140 знаков, не смогут быть импортированы в сеанс PowerShell с `Import-Module`.

### <a name="powershell"></a>PowerShell

Можно использовать [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) для импорта модуля в учетной записи службы автоматизации. Командлет принимает URL-адрес к ZIP-пакет модуля.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Портал Azure

На портале Azure, перейдите к учетной записи службы автоматизации и выберите **модули** под **общие ресурсы**. Нажмите кнопку **+ добавить модуль**. Выберите **ZIP-файл** файл, содержащий модуль и выберите команду **ОК** чтобы начать процесс импорта.

### <a name="powershell-gallery"></a>Коллекция PowerShell

Модули из коллекции PowerShell, либо можно импортировать из [коллекции PowerShell](https://www.powershellgallery.com) напрямую или из учетной записи службы автоматизации.

Чтобы импортировать модуль из коллекции PowerShell, перейдите в раздел https://www.powershellgallery.com и выполните поиск модуля, который требуется импортировать. Нажмите кнопку **развернуть в службе автоматизации Azure** на **службы автоматизации Azure** открыть, выбрав вкладку **параметры установки**. Это действие открывает портал Azure. На **импорта** выберите учетную запись службы автоматизации и щелкните **ОК**.

![Модуль импорта из коллекции PowerShell](../media/modules/powershell-gallery.png)

Можно также импортировать модули из коллекции PowerShell непосредственно из учетной записи службы автоматизации. В учетной записи службы автоматизации выберите **модули** под **общие ресурсы**. На странице "Модули", нажмите кнопку **Обзор коллекции**. Это открывает **Обзор коллекции** страницы. Эту страницу можно использовать для поиска в коллекции PowerShell для модуля. Выберите модуль, который требуется импортировать и нажмите кнопку **импорта**. На **импорта** щелкните **ОК** чтобы начать процесс импорта.

![Импорт коллекции PowerShell с портала Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Удалить модули

При наличии проблем с модулем, или вам необходимо выполнить откат к предыдущей версии модуля, его можно удалить из учетной записи службы автоматизации. Не удается удалить исходную версию [по умолчанию модули](#default-modules) , импортируются при создании учетной записи службы автоматизации. Если удалить модуль является более новой версии одного из [по умолчанию модули](#default-modules) установлен, он будет отката к версии, которая была установлена с помощью учетной записи службы автоматизации. В противном случае любой модуль, который был удален из вашей учетной записи службы автоматизации будут удалены.

### <a name="azure-portal"></a>Портал Azure

На портале Azure, перейдите к учетной записи службы автоматизации и выберите **модули** под **общие ресурсы**. Выберите модуль, который требуется удалить. На **модуль** страницы, clcick **удалить**. Если этот модуль является одним из [по умолчанию модули](#default-modules) его будет откатить обратно до версии, которая была текущей, при создании учетной записи службы автоматизации.

### <a name="powershell"></a>PowerShell

Чтобы удалить модуль с помощью PowerShell, выполните следующую команду:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Внутренняя командлетов

Ниже приведен список командлетов во внутреннем `Orchestrator.AssetManagement.Cmdlets` модуль, который импортируется в каждой учетной записи службы автоматизации. Эти командлеты доступны в модулях Runbook и конфигурациях DSC и позволяют взаимодействовать с ресурсами в учетной записи службы автоматизации. Кроме того, внутренняя командлеты, которые позволяют получать секреты из зашифрованы **переменной** значения, **учетные данные**и зашифрованные **подключения** поля. Командлеты Azure PowerShell не удалось извлечь эти секреты. Эти командлеты не требуется явно подключиться к Azure, при их использовании. Это полезно для сценариев, где имеется подключение, например Run As Account, необходимо использовать для проверки подлинности в Azure.

|ИМЯ|Описание|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|AutomationJob ожидания|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Добавление типа соединения модуля

Можно указать пользовательское [тип подключения](../automation-connections.md) для использования в учетной записи службы автоматизации, добавив дополнительный файл для вашего модуля. Этот файл является файлом метаданных, типе соединения службы автоматизации Azure для использования с командлетами модуля в учетной записи службы автоматизации. Для этого сначала необходимо знать, как создать модуль PowerShell. Дополнительные сведения о создании модуля см. в разделе [как написать модуль сценария PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Использовать пользовательское подключение на портале Azure](../media/modules/connection-create-new.png)

Чтобы добавить тип подключения службы автоматизации Azure, ваш модуль должен содержать файл с именем `<ModuleName>-Automation.json` , указывающий свойства типа подключения. Это файл JSON, который находится в папке модуля в сжатом ZIP-файле. Этот файл содержит поля подключения, необходимые для подключения к системе или службы, который представляет модуль. В результате этой конфигурации в службе автоматизации Azure создается тип соединения. С помощью этого файла можно задать имена полей, типов, и должен ли поля, зашифрованные или необязательными для типа соединения модуля. Следующий пример — это шаблон, в формат json-файла, который определяет свойство, имя пользователя и пароль:

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

## <a name="module-best-practices"></a>Рекомендации по обеспечению модуля

Модули PowerShell можно импортировать в службу автоматизации Azure, чтобы командлеты можно было использовать в Runbook, а ресурсы DSC — в конфигурациях DSC. По сути служба автоматизации Azure хранит эти модули и при выполнении задания Runbook и компиляции DSC загружает их в песочницы службы автоматизации Azure, где выполняются модули Runbook и компилируются конфигурации DSC. Любые ресурсы DSC в модулях также автоматически размещаются на опрашиваемом сервере Automation DSC. Компьютеры могут извлекать их, когда они применяют конфигурации DSC.

Мы рекомендуем при создании модуля PowerShell для использования в службе автоматизации Azure, учитывайте следующее:

* Добавьте краткие сведения, описание и справочный универсальный код ресурса (URI) в каждый командлет в модуле. В PowerShell можно указать некоторые справочные сведения для командлетов, чтобы предоставить пользователю возможность получать справку по их использованию с помощью командлета **Get-Help** . Приведенный ниже показано, как определить краткие сведения и справочный URI для в файле с расширением psm1 модуля:

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

  Если указать эти сведения, они будут отображаться при использовании командлета **Get-Help** в консоли PowerShell. Кроме того, это описание отображается на портале Azure.

  ![Справка по модулям интеграции](../media/modules/module-activity-description.png)

* Если модуль подключается к внешней службе, она должна содержать [тип соединения](#add-a-connection-type-to-your-module). Каждый командлет в модуле должен принимать объект соединения (экземпляр этого типа соединения) в качестве параметра. Благодаря этому пользователи могут сопоставлять параметры ресурса подключения с соответствующими параметрами командлета при каждом вызове командлета. Исходя из приведенный выше пример runbook, он использует ресурс подключения Contoso примере вызывается ContosoConnection для доступа к ресурсам Contoso и возврат данных из внешней службы.

  В следующем примере сопоставления полей к свойствам имя пользователя и пароль `PSCredential` объекта и затем передается в командлет.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Проще и лучше всего это поведение непосредственно передать объект подключения в командлет:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Вы можете задать для командлетов поведение, как в предыдущем примере, разрешив им принимать в качестве параметра напрямую объект подключения, а не только поля подключения. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации Azure, мог вызывать командлеты, не создавая хэш-таблицу, используемую в качестве объекта подключения. Набор параметров `UserAccount`, используемый для передачи свойств поля подключения. `ConnectionObject` позволяет передать подключение напрямую через.

* Укажите тип выходных данных для всех командлетов в модуле. При этом можно воспользоваться технологией IntelliSense, используемой во время разработки, чтобы определить свойства выходных данных командлета, используемых при создании модуля. Это особенно полезно при графической разработке Runbook в службе автоматизации, при которой знание аспектов разработки упрощает работу пользователей с модулем.

  Это можно сделать, добавив `[OutputType([<MyOutputType>])]` где MyOutputType имеет допустимый тип. Дополнительные сведения о OutputType, см. в разделе [о OutputTypeAttribute функции](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Ниже приведен пример добавления `OutputType` в командлет:

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

* Не указывайте сведения о состоянии всех командлетов. Несколько заданий runbook одновременно могут выполняться в том же домене приложения и тем же процесс и "песочницы". Если любое состояние, на этих уровнях, задания могут повлиять на друг с другом. Это поведение может привести к непостоянной и ее трудно диагностировать проблемы.  Ниже представлен пример неправильного использования.

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

* Модуль должен полностью находиться в пакете с поддержкой xcopy. При необходимости выполнения модулей Runbook, модули службы автоматизации Azure распространяются среди песочницы службы автоматизации. Модули должны работать независимо от узла, на котором они запущены. Вы должны иметь возможность заархивировать и переместить пакет модуля и заставить его работать как обычно при импорте в среду PowerShell другого узла. Чтобы это произошло, модуль не должен зависеть от каких-либо файлов вне папки модуля. Эта папка архивируется при импорте модуля в службу автоматизации Azure. Модуль также не должен зависеть от каких-либо уникальных параметров реестра на узле, например от параметров, заданных при установке продукта. Все файлы в модуле должен иметь путь не более 140 символов. Все пути, больше 140 знаков приведет к проблемам, импорт модуля runbook. При несоблюдении этой рекомендации модуль нельзя использовать в службе автоматизации Azure.  

* Если вы ссылаетесь на [модули Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) в своем модуле, убедитесь, что вы также не ссылаетесь на `AzureRM`. Модуль `Az` нельзя использовать в сочетании с модулями `AzureRM`. `Az` поддерживается в модулях Runbook, но по умолчанию не импортируется. Чтобы узнать о модулях `Az` и соображениях, которые необходимо учитывать, ознакомьтесь со статьей [Поддержка модулей Az в службе автоматизации Azure](../az-modules.md).

## <a name="default-modules"></a>По умолчанию модули

Ниже перечислены модули, которые импортируются по умолчанию при создании учетной записи службы автоматизации. Модули, перечисленные ниже, могут иметь более новых версиях их импорта, но исходная версия не может быть удален из вашей учетной записи службы автоматизации даже в случае удаления их более новые версии.

|Имя модуля|Version|
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

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о создании модулей PowerShell см. в статье [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx) (Написание модуля Windows PowerShell).