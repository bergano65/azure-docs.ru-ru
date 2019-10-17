---
title: Управление модулями в службе автоматизации Azure
description: В этой статье описывается, как управлять модулями в службе автоматизации Azure.
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82f02f0ac11c80161f709b3b493306bc8aafc8bd
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435455"
---
# <a name="manage-modules-in-azure-automation"></a>Управление модулями в службе автоматизации Azure

Служба автоматизации Azure предоставляет возможность импортировать модули PowerShell в учетную запись службы автоматизации для использования модулями Runbook на основе PowerShell. Эти модули могут быть созданными пользовательскими модулями, из коллекция PowerShell или модулей AzureRM и AZ для Azure. При создании учетной записи службы автоматизации некоторые модули импортируются по умолчанию.

## <a name="import-modules"></a>Импорт модулей

Существует несколько способов импортировать модуль в учетную запись службы автоматизации. В следующих разделах показаны различные способы импорта модуля.

> [!NOTE]
> Максимальный путь к файлу в модуле, который будет использоваться в службе автоматизации Azure, составляет 140 символов. Любой путь свыше 140 символов не сможет импортироваться в сеанс PowerShell с `Import-Module`.

### <a name="powershell"></a>PowerShell

Вы можете использовать [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) для импорта модуля в учетную запись службы автоматизации. Командлет принимает URL-адрес пакета модуля ZIP.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>портала Azure

В портал Azure перейдите к учетной записи службы автоматизации и выберите **модули** в разделе **Общие ресурсы**. Щелкните **+ Добавить модуль**. Выберите **ZIP** -файл, содержащий модуль, и нажмите кнопку **ОК** , чтобы начать импорт процесса.

### <a name="powershell-gallery"></a>Коллекция PowerShell

Модули из коллекции PowerShell можно импортировать из [коллекция PowerShell](https://www.powershellgallery.com) напрямую или из учетной записи службы автоматизации.

Чтобы импортировать модуль из коллекция PowerShell, перейдите по адресу https://www.powershellgallery.com и найдите модуль, который нужно импортировать. Щелкните **развернуть в службе автоматизации Azure** на вкладке Служба **автоматизации Azure** в разделе **Параметры установки**. Портал Azure можно открыть с помощью этого действия. На странице **Импорт** выберите учетную запись службы автоматизации и нажмите кнопку **ОК**.

![Модуль импорта коллекция PowerShell](../media/modules/powershell-gallery.png)

Кроме того, можно импортировать модули из коллекция PowerShell непосредственно из учетной записи службы автоматизации. В учетной записи службы автоматизации выберите **модули** в разделе **Общие ресурсы**. На странице Модули щелкните **Обзор коллекции**, а затем выполните поиск по коллекция PowerShell модуля. Выберите модуль, который необходимо импортировать, и нажмите кнопку **Импорт**. На странице **Импорт** нажмите кнопку **ОК** , чтобы начать процесс импорта.

![коллекция PowerShell импорт из портал Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Удаление модулей

Если у вас возникли проблемы с модулем или необходимо выполнить откат к предыдущей версии модуля, его можно удалить из учетной записи службы автоматизации. Нельзя удалить исходную версию [модулей по умолчанию](#default-modules) , которые импортируются при создании учетной записи службы автоматизации. Если удаляемый модуль является более новой версией одного из установленных [модулей по умолчанию](#default-modules) , она будет восстановлена до версии, установленной вместе с учетной записью службы автоматизации. В противном случае все модули, удаляемые из учетной записи службы автоматизации, будут удалены.

### <a name="azure-portal"></a>портала Azure

В портал Azure перейдите к учетной записи службы автоматизации и выберите **модули** в разделе **Общие ресурсы**. Выберите модуль, который требуется удалить. На странице **модуль** клЦикк **Удалить**. Если этот модуль является одним из [модулей по умолчанию](#default-modules), он будет выполнен откат к версии, которая существовала при создании учетной записи службы автоматизации.

### <a name="powershell"></a>PowerShell

Чтобы удалить модуль с помощью PowerShell, выполните следующую команду:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Внутренние командлеты

Ниже приведен список командлетов во внутреннем модуле `Orchestrator.AssetManagement.Cmdlets`, который импортируется в каждую учетную запись службы автоматизации. Эти командлеты доступны в модулях Runbook и конфигурациях DSC и позволяют взаимодействовать с ресурсами в вашей учетной записи службы автоматизации. Кроме того, внутренние командлеты позволяют извлекать секреты из зашифрованных значений **переменных** , **учетных данных**и полей зашифрованного **соединения** . Командлеты Azure PowerShell не могут получить эти секреты. При использовании этих командлетов не требуется неявное подключение к Azure, например использование учетной записи запуска от имени для проверки подлинности в Azure.

|Name|Описание|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-Аутоматионрунбук|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-Аутоматионжоб|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Добавление типа подключения в модуль

Вы можете указать пользовательский [тип подключения](../automation-connections.md) для использования в учетной записи службы автоматизации, добавив дополнительный файл в модуль. Этот файл представляет собой файл метаданных, указывающий тип подключения службы автоматизации Azure, который будет использоваться с командлетами модуля в вашей учетной записи службы автоматизации. Чтобы добиться этого, необходимо сначала понять, как создать модуль PowerShell. Дополнительные сведения о создании модулей см. в разделе [написание модуля скрипта PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Использовать пользовательское соединение в портал Azure](../media/modules/connection-create-new.png)

Чтобы добавить тип подключения службы автоматизации Azure, модуль должен содержать файл с именем `<ModuleName>-Automation.json`, указывающий свойства типа соединения. JSON-файл помещается в папку Module сжатого ZIP-файла. Этот файл содержит поля соединения, необходимого для подключения к системе или службе, которую представляет модуль. Конфигурация завершает создание типа подключения в службе автоматизации Azure. С помощью этого файла можно задать имена полей, типы, а также указать, должны ли поля быть зашифрованными или необязательными для типа соединения модуля. Ниже приведен пример шаблона в формате JSON, который определяет свойство UserName и Password.

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

## <a name="module-best-practices"></a>Рекомендации по модулям

Модули PowerShell можно импортировать в службу автоматизации Azure, чтобы командлеты можно было использовать в Runbook, а ресурсы DSC — в конфигурациях DSC. По сути служба автоматизации Azure хранит эти модули и при выполнении задания Runbook и компиляции DSC загружает их в песочницы службы автоматизации Azure, где выполняются модули Runbook и компилируются конфигурации DSC. Любые ресурсы DSC в модулях также автоматически размещаются на опрашиваемом сервере Automation DSC. Компьютеры могут извлекать их, когда они применяют конфигурации DSC.

При создании модуля PowerShell для использования в службе автоматизации Azure рекомендуется учитывать следующее.

* НЕ включайте папку версии в ZIP-пакет.  Эта проблема менее важна для модулей Runbook, но вызывает проблему со службой настройки состояния.  Служба автоматизации Azure создаст папку версии автоматически при распространении модуля на узлы, управляемые DSC, и если существует папка версии, в итоге будут два экземпляра.  Пример структуры папок для модуля DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Добавьте краткие сведения, описание и справочный универсальный код ресурса (URI) в каждый командлет в модуле. В PowerShell можно указать некоторые справочные сведения для командлетов, чтобы предоставить пользователю возможность получать справку по их использованию с помощью командлета **Get-Help** . В следующем примере показано, как определить краткий и справочный URI для в файле модуля PSM1:

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

  Если указать эти сведения, они будут отображаться при использовании командлета **Get-Help** в консоли PowerShell. Это описание также отображается в портал Azure.

  ![Справка по модулям интеграции](../media/modules/module-activity-description.png)

* Если модуль подключается к внешней службе, он должен содержать [тип соединения](#add-a-connection-type-to-your-module). Каждый командлет в модуле должен принимать объект соединения (экземпляр этого типа соединения) в качестве параметра. Пользователи сопоставляют параметры ресурса подключения с соответствующими параметрами командлета каждый раз, когда они вызывают командлет. Основываясь на примере модуля Runbook выше, он использует пример ресурса подключения contoso с именем Контосоконнектион для доступа к ресурсам Contoso и возврата данных из внешней службы.

  В следующем примере поля сопоставляются с свойствами UserName и Password объекта `PSCredential`, а затем передаются в командлет.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Проще и лучше подходить это поведение напрямую, передав объект соединения в командлет:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Вы можете задать для командлетов поведение, как в предыдущем примере, разрешив им принимать в качестве параметра напрямую объект подключения, а не только поля подключения. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации Azure, мог вызывать командлеты, не создавая хэш-таблицу, используемую в качестве объекта подключения. Набор параметров `UserAccount` используется для передачи свойств поля соединения. `ConnectionObject` позволяет передавать соединение напрямую через.

* Определите тип выходных данных для всех командлетов в модуле. При этом можно воспользоваться технологией IntelliSense, используемой во время разработки, чтобы определить свойства выходных данных командлета, используемых при создании модуля. Это особенно полезно при графической разработке Runbook в службе автоматизации, при которой знание аспектов разработки упрощает работу пользователей с модулем.

Добавьте `[OutputType([<MyOutputType>])]`, где Мйоутпуттипе является допустимым типом. Дополнительные сведения о параметре OutputType см. в разделе [About functions аутпуттипеаттрибуте](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Следующий код является примером добавления `OutputType` в командлет:

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

* Не указывайте сведения о состоянии всех командлетов. Несколько заданий Runbook могут одновременно выполняться в одном домене AppDomain и в одном и том же процессе и в песочнице. Если на этих уровнях имеется общий доступ к состоянию, задания могут повлиять на друг друга. Такое поведение может привести к нерегулярному и сложному диагностике проблем.  Ниже представлен пример неправильного использования.

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

* Модуль должен быть полностью включен в пакет, поддерживающий xcopy. Модули службы автоматизации Azure распространяются в песочницу службы автоматизации, когда необходимо выполнить модули Runbook. Модули должны работать независимо от узла, на котором они запущены. Вы должны иметь возможность заархивировать и переместить пакет модуля и заставить его работать как обычно при импорте в среду PowerShell другого узла. Чтобы это произошло, модуль не должен зависеть от каких-либо файлов вне папки модуля. Эта папка архивируется при импорте модуля в службу автоматизации Azure. Модуль также не должен зависеть от каких-либо уникальных параметров реестра на узле, например от параметров, заданных при установке продукта. Длина пути для всех файлов в модуле должна быть меньше 140 символов. Любые пути свыше 140 символов могут вызвать проблемы при импорте модуля Runbook. При несоблюдении этой рекомендации модуль нельзя использовать в службе автоматизации Azure.  

* Если вы ссылаетесь на [модули Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) в своем модуле, убедитесь, что вы также не ссылаетесь на `AzureRM`. Модуль `Az` нельзя использовать в сочетании с модулями `AzureRM`. `Az` поддерживается в модулях Runbook, но по умолчанию не импортируется. Чтобы узнать о модулях `Az` и соображениях, которые необходимо учитывать, ознакомьтесь со статьей [Поддержка модулей Az в службе автоматизации Azure](../az-modules.md).

## <a name="default-modules"></a>Модули по умолчанию

В следующей таблице перечислены модули, которые импортируются по умолчанию при создании учетной записи службы автоматизации. Перечисленные ниже модули могут быть импортированы более поздними версиями, но исходная версия не может быть удалена из учетной записи службы автоматизации даже при удалении более новой версии.

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
| компутерманажементдск | 5.0.0.0 |
| гпрегистриполиципарсер | 0,2 |
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft. PowerShell. Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. Ассетманажемент. командлеты | 1 |
| псдскресаурцес | 2.9.0.0 |
| секуритиполицидск | 2.1.0.0 |
| статеконфигкомпоситересаурцес | 1 |
| ксдскдомаинжоин | 1,1 |
| ксповершеллексекутионполици | 1.1.0.0 |
| ксремотедесктопадмин | 1.1.0.0 |

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о создании модулей PowerShell см. в статье [Writing a Windows PowerShell Module](/powershell/scripting/developer/windows-powershell) (Написание модуля Windows PowerShell).
