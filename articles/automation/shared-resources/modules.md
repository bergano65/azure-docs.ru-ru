---
title: Управление модулями в автоматизации Azure
description: В этой статье описывается, как управлять модулями в Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859eea66d10e07a3503e33166bc77c8a97577acd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548936"
---
# <a name="manage-modules-in-azure-automation"></a>Управление модулями в автоматизации Azure

Azure Automation позволяет импортировать модули PowerShell в учетную запись автоматизации, которые будут использоваться в runbooks на основе PowerShell. Эти модули могут быть созданными вами модулями, модулями из галереи PowerShell или модулями AzureRM и Az для Azure. При создании учетной записи Автоматизации некоторые модули импортируются по умолчанию.

## <a name="import-modules"></a>Импорт модулей

Существует несколько способов ввоза модуля в учетную запись автоматизации. В следующих разделах показаны различные способы импорта модуля.

> [!NOTE]
> Максимальный размер пути для файла в модуле, используемом в Автоматизации Azure, составляет 140 символов. Автоматизация не может импортировать файл с размером пути более 140 символов в сеанс PowerShell с `Import-Module`помощью.

### <a name="powershell"></a>PowerShell

Для импорта модуля в учетную запись автоматизации можно использовать смдlet [New-AzureRmAutomationModule.](/powershell/module/azurerm.automation/new-azurermautomationmodule) Cmdlet принимает URL для пакета модуля .zip.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Вы также можете использовать тот же cmdlet импортировать модуль из PowerShell Галерея напрямую. Убедитесь в `ModuleName` `ModuleVersion` том, чтобы захватить и из [PowerShell Галерея](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Портал Azure

Импорт модуля на портале Azure:

1. Перейдите к учетной записи службы автоматизации.
2. Выберите **модули** под **общими ресурсами.**
3. Нажмите **Добавить модуль**. 
4. Выберите файл **.zip,** содержащий ваш модуль.
5. Нажмите **OK,** чтобы начать процесс импорта.

### <a name="powershell-gallery"></a>Коллекция PowerShell

Модули [PowerShell Gallery](https://www.powershellgallery.com) можно импортировать либо непосредственно из галереи, либо из учетной записи Автоматизации.

Импорт модуля непосредственно из галереи PowerShell:

1. Перейти https://www.powershellgallery.com к и искать модуль для импорта.
2. Нажмите **Развертывание для автоматизации Azure** на вкладке **Azure Automation** под параметры **установки.** Портал Azure можно открыть с помощью этого действия. 
3. На странице Импорта выберите учетную запись Автоматизации и нажмите **OK**.

![Модуль импорта галереи PowerShell](../media/modules/powershell-gallery.png)

Импорт модуля PowerShell Gallery непосредственно из учетной записи автоматизации:

1. Выберите **модули** под **общими ресурсами.** 
2. На странице Модулей нажмите **Просмотр галереи**, а затем искать в галерее для модуля. 
3. Выберите модуль для импорта и нажмите **Импорт.** 
4. На странице Импорта нажмите **OK,** чтобы начать процесс импорта.

![Импорт Галереи PowerShell с портала Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Удаление модулей

Если у вас возникли проблемы с модулем или вам нужно откатиться к предыдущей версии модуля, вы можете удалить его из учетной записи Автоматизация. Нельзя удалять исходные версии [модулей по умолчанию, импортируемых](#default-modules) при создании учетной записи Автоматизации. Если модуль для удаления является новой версией одного из [модулей по умолчанию,](#default-modules)он откатывается к версии, которая была установлена с вашей учетной записью Автоматизация. В противном случае любой модуль, удаленный из учетной записи Automation, удаляется.

### <a name="azure-portal"></a>Портал Azure

Чтобы удалить модуль на портале Azure:

1. Перейдите к учетной записи автоматизации и выберите **модули** под **общими ресурсами.** 
2. Выберите модуль, который вы хотите удалить. 
3. На странице **Модуля** выберите **Удалить**. Если этот модуль является одним из [модулей по умолчанию,](#default-modules)он возвращается к версии, которая существовала при создании учетной записи Автоматизации.

### <a name="powershell"></a>PowerShell

Чтобы удалить модуль через PowerShell, запустите следующую команду:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Внутренние смдлеты

В таблице ниже перечислены cmdlets во внутреннем `Orchestrator.AssetManagement.Cmdlets` модуле, импортируемом в каждую учетную запись Автоматизации. Эти cmdlets доступны в ваших runbooks и DSC конфигурациях и позволяют взаимодействовать с активами в вашей учетной записи Автоматизация. Кроме того, внутренние cmdlets позволяют извлекать секреты из зашифрованных переменных, учетных данных и зашифрованных соединений. Cmdlets Azure PowerShell не в состоянии получить эти секреты. Эти cmdlets не требуют от вас неявного подключения к Azure при их использовании, как при использовании учетной записи Run As для проверки подлинности в Azure.

>[!NOTE]
>Эти внутренние cmdlets доступны на Windows Hybrid Runbook работника, но не на Linux Гибридный Runbook работника. Используйте соответствующие смлеты [модуля](../az-modules.md) [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) или Az для запусков, работающих непосредственно на компьютере или против ресурсов в вашей среде. 

|Имя|Описание|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Старт-АвтоматизацияRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Добавление типа соединения в модуль

Вы можете предоставить пользовательский [тип соединения](../automation-connections.md) для использования в вашей учетной записи Automation, добавив дополнительный файл метаданных в модуль. В этом файле указывается тип подключения Azure Automation, который будет использоваться с cmdlets модуля в вашей учетной записи Automation. Для достижения этой цели необходимо сначала узнать, как автор модуля PowerShell. [Узнайте, как написать модуль сценария PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

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

## <a name="module-best-practices"></a>Модуль передовой практики

Модули PowerShell можно импортировать в Azure Automation, чтобы сделать их cmdlets доступными в runbooks и их ресурсах DSC, доступных в конфигурациях DSC. За кулисами Azure Automation хранит эти модули. Во время выполнения задания и времени выполнения выполнения выполнения компиляции DSC автоматизация загружает их в песочницы Azure Automation, где выполняются записи runbooks и компилируются конфигурации DSC. Любые ресурсы DSC в модулях также автоматически размещаются на опрашиваемом сервере Automation DSC. Машины могут тянуть их, когда они применяют сяочку DSC.

Мы рекомендуем вам учитывать следующее, когда вы авторите модуль PowerShell для использования в Azure Automation:

* НЕ включайте папку версии в пакет **.zip.**  Эта проблема в меньшей безопасности вызывает озабоченность у runbooks, но вызывает проблемы с Государственной службой конфигурации. Azure Automation автоматически создает папку версии при распределении модуля на узлы, управляемые DSC. Если папка версии существует, вы в конечном итоге с двумя экземплярами. Вот пример структуры папки для модуля DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Добавьте краткие сведения, описание и справочный универсальный код ресурса (URI) в каждый командлет в модуле. В PowerShell можно определить информацию о справках для cmdlets с помощью `Get-Help` cmdlet. Ниже приводится следующий пример, как определить резюме и помочь URI в файле модуля **.psm1:**

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

* Если модуль подключается к внешней службе, определите [тип соединения.](#add-a-connection-type-to-your-module) Каждый cmdlet в модуле должен принимать объект соединения (экземпляр этого типа соединения) в качестве параметра. Пользователи отображают параметры актива соединения с соответствующими параметрами cmdlet каждый раз, когда они называют cmdlet. Основываясь на приведенном выше примере runbook, он `ContosoConnection` использует пример актива соединения Contoso, который вызывается для доступа к ресурсам Contoso и возврата данных из внешнего сервиса.

  В следующем примере поля отображаются по `PSCredential` свойствам `UserName` объекта, `Password` а затем передаются в cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Более простой и лучший способ приблизиться к этому поведению — непосредственно передать объект соединения к cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Можно включить аналогичное поведение для ваших cmdlets, позволяя им принимать объект соединения непосредственно в качестве параметра, а не только поля соединения для параметров. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации Azure, мог вызывать командлеты, не создавая хэш-таблицу, используемую в качестве объекта подключения. Набор `UserAccount`параметров, используется для передачи свойств поля соединения. `ConnectionObject`позволяет пройти соединение прямо через.

* Определите тип вывода для всех cmdlets в модуле. При этом можно воспользоваться технологией IntelliSense, используемой во время разработки, чтобы определить свойства выходных данных командлета, используемых при создании модуля. Это особенно полезно при графической разработке Runbook в службе автоматизации, при которой знание аспектов разработки упрощает работу пользователей с модулем.

Добавьте, `[OutputType([<MyOutputType>])]` где MyOutputType является действительным типом. Чтобы узнать больше о OutputType, [см.](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) Следующий код является примером добавления `OutputType` в cmdlet:

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

* Не указывайте сведения о состоянии всех командлетов. Несколько заданий Runbook могут одновременно работать в том же AppDomain и тот же процесс и песочнице. Если на этих уровнях есть какое-либо состояние, где имеются общие сведения, задания могут влиять друг на друга. Такое поведение может привести к прерывистым и трудно диагностировать проблемы.  Ниже представлен пример неправильного использования.

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

* Модуль должен полностью содержаться в пакете, способном кскопии. Модули Azure Automation распространяются в песочницах автоматизации, когда руны должны выполняться. Модули должны работать независимо от узла, на котором они запущены. Вы должны быть в состоянии застегнуть и переместить модуль пакет и его функции, как обычно, когда импортируется в powerShell среды другого хозяина. Для этого модуль не должен зависеть от файлов за пределами папки модуля, которая застегивается при импорте модуля в Azure Automation. Модуль также не должен зависеть от каких-либо уникальных параметров реестра на узле, например от параметров, заданных при установке продукта. Все файлы в модуле должны иметь путь менее 140 символов. Любые пути более 140 символов вызывают проблемы с импортом вашего runbook. Если вы не будете следовать этой передовой практике, модуль не используется в Azure Automation.  

* Если в модуле ссылается [на модуль Azure PowerShell Az,](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) убедитесь, `AzureRM`что вы также не ссылаетесь на вас. Вы не можете `Az` использовать модуль в `AzureRM` сочетании с модулем. `Az`поддерживается в runbooks, но не импортируется по умолчанию. Чтобы узнать `Az` о модуле и соображениях, которые следует учитывать, [см.](../az-modules.md)

## <a name="default-modules"></a>Модули по умолчанию

В следующей таблице перечислены модули, импортируемые по умолчанию при создании учетной записи Автоматизации. Автоматизация может импортировать новые версии этих модулей. Однако вы не можете удалить исходную версию из учетной записи Automation, даже если вы удалите новую версию.

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

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать больше о создании модулей PowerShell, смотрите [Написать модуль Windows PowerShell](/powershell/scripting/developer/windows-powershell).
