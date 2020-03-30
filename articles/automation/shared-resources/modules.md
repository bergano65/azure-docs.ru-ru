---
title: Управление модулями в автоматизации Azure
description: В этой статье описывается, как управлять модулями в Автоматизации Azure
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278341"
---
# <a name="manage-modules-in-azure-automation"></a>Управление модулями в автоматизации Azure

Azure Automation предоставляет возможность импортировать модули PowerShell в свой аккаунт автоматизации для использования в runbooks на основе PowerShell. Эти модули могут быть созданными вами модульами из галереи PowerShell или модулями AzureRM и Az для Azure. При создании учетной записи автоматизации некоторые модули импортируются по умолчанию.

## <a name="import-modules"></a>Импорт модулей

Существует несколько способов ввоза модуля в учетную запись автоматизации. В следующих разделах показаны различные способы импорта модуля.

> [!NOTE]
> Максимальная траектория файла в модуле, используемом в Azure Automation, составляет 140 символов. Любой путь, перемотанный на 140 символов, `Import-Module`не сможет быть импортирован в сеанс PowerShell с .

### <a name="powershell"></a>PowerShell

Для импорта модуля в учетную запись автоматизации можно использовать [New-AzureRmAutomationModule.](/powershell/module/azurerm.automation/new-azurermautomationmodule) Cmdlet принимает URL к пакету zip модуля.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Вы также можете использовать тот же cmdlet импортировать модуль из PowerShell Галерея напрямую. Убедитесь в том, чтобы захватить **ModuleName** и **ModuleVersion** из [PowerShell Галерея](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Портал Azure

На портале Azure перейдите на учетную запись автоматизации и выберите **модули** под **общими ресурсами.** Нажмите **и добавьте модуль**. Выберите файл **.zip,** содержащий ваш модуль, и нажмите **Ok,** чтобы начать процесс импорта.

### <a name="powershell-gallery"></a>Коллекция PowerShell

Модули из галереи PowerShell можно импортировать либо из [галереи PowerShell](https://www.powershellgallery.com) напрямую, либо из вашей учетной записи автоматизации.

Чтобы импортировать модуль из галереи https://www.powershellgallery.com PowerShell, перейдите и ищите модуль, который вы хотите импортировать. Нажмите **Развертывание для автоматизации Azure** на вкладке **Azure Automation** под параметры **установки.** Портал Azure можно открыть с помощью этого действия. На странице **Импорта** выберите учетную запись автоматизации и нажмите **OK**.

![Модуль импорта галереи PowerShell](../media/modules/powershell-gallery.png)

Вы также можете импортировать модули из галереи PowerShell непосредственно из вашей учетной записи автоматизации. В учетной записи автоматизации выберите **модули** под **общими ресурсами.** На странице модулей нажмите **Просмотр галереи**, а затем поиск PowerShell Галерея для модуля. Выберите модуль, который вы хотите импортировать, и нажмите **Импорт.** На странице **Импорта** нажмите **OK,** чтобы начать процесс импорта.

![Импорт Галереи PowerShell с портала Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Удаление модулей

Если у вас возникли проблемы с модулем или вам нужно откатиться к предыдущей версии модуля, вы можете удалить его из учетной записи автоматизации. При создании учетной записи автоматизации нельзя удалять исходную версию [импортируемых модулей по умолчанию.](#default-modules) Если модуль, который вы хотите удалить, представляет собой более новую версию одного из [установленных модулей по умолчанию,](#default-modules) он откатится к версии, которая была установлена с вашей учетной записью автоматизации. В противном случае любой модуль, удаленный из учетной записи автоматизации, будет удален.

### <a name="azure-portal"></a>Портал Azure

На портале Azure перейдите на учетную запись автоматизации и выберите **модули** под **общими ресурсами.** Выберите модуль, который вы хотите удалить. На странице **Модуля** выберите **Удалить**. Если этот модуль является одним из [модулей по умолчанию,](#default-modules)он будет отброшен к версии, которая присутствовала при создании учетной записи автоматизации.

### <a name="powershell"></a>PowerShell

Чтобы удалить модуль через PowerShell, запустите следующую команду:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Внутренние смдлеты

Ниже приводится список cmdlets `Orchestrator.AssetManagement.Cmdlets` во внутреннем модуле, который импортируется в каждый счет автоматизации. Эти cmdlets доступны в ваших runbooks и DSC конфигурациях и позволяют взаимодействовать с вашими активами в вашей учетной записи автоматизации. Кроме того, внутренние cmdlets позволяют извлекать секреты из зашифрованных **переменных** значений, **учетных данных**и зашифрованных полей **соединения.** Cmdlets Azure PowerShell не в состоянии получить эти секреты. Эти cmdlets не требуют от вас неявного подключения к Azure при их использовании, например, с помощью учетной записи Run As для проверки подлинности в Azure.

>[!NOTE]
>Эти внутренние cmdlets доступны на Windows Hybrid Runbook работника, они не доступны на Linux Гибридный Runbook работника. Используйте соответствующие [модули](../az-modules.md) [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) или Az для запущенных книг, работающих непосредственно на компьютере или против ресурсов в вашей среде. 
>

|name|Описание|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Старт-АвтоматизацияRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Добавление типа соединения в модуль

Вы можете предоставить пользовательский [тип соединения](../automation-connections.md) для использования в вашей учетной записи автоматизации, добавив дополнительный файл в свой модуль. Этот файл представляет собой файл метаданных, указывающий тип соединения Azure Automation, который будет использоваться с помощью cmdlets модуля в вашей учетной записи автоматизации. Для достижения этой цели необходимо сначала узнать, как автор модуля PowerShell. Для получения дополнительной информации об авторе модуля [см.](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)

![Используйте пользовательское соединение на портале Azure](../media/modules/connection-create-new.png)

Чтобы добавить тип подключения Azure Automation, модуль должен `<ModuleName>-Automation.json` содержать файл с именем, опознавательным свойствами типа соединения. Файл json помещается в папку модуля сжатого файла .zip. Этот файл содержит поля подключения, необходимые для подключения к системе или службе, которую представляет модуль. В итоге конфигурация создает тип соединения в Azure Automation. С помощью этого файла можно установить имена полей, типы и вопрос о том, следует ли шифровать или необязательно использовать поля для типа соединения модуля. Ниже приводится шаблон в формате файла json, определяющий имя пользователя и свойство пароля:

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

Модули PowerShell можно импортировать в службу автоматизации Azure, чтобы командлеты можно было использовать в Runbook, а ресурсы DSC — в конфигурациях DSC. По сути служба автоматизации Azure хранит эти модули и при выполнении задания Runbook и компиляции DSC загружает их в песочницы службы автоматизации Azure, где выполняются модули Runbook и компилируются конфигурации DSC. Любые ресурсы DSC в модулях также автоматически размещаются на опрашиваемом сервере Automation DSC. Компьютеры могут извлекать их, когда они применяют конфигурации DSC.

Мы рекомендуем вам рассмотреть следующие вопросы, когда вы авторите модуль PowerShell для использования в Azure Automation:

* НЕ включайте папку версии в пакет .zip.  Этот вопрос в меньшей раз беспокоит запусков, но вызовет проблему с Государственной службой конфигурации.  Azure Automation автоматически создаст папку версии, когда модуль будет распределен среди узлов, управляемых DSC, и если папка версии существует, вы получите два экземпляра.  Пример структуры папки для модуля DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Добавьте краткие сведения, описание и справочный универсальный код ресурса (URI) в каждый командлет в модуле. В PowerShell можно указать некоторые справочные сведения для командлетов, чтобы предоставить пользователю возможность получать справку по их использованию с помощью командлета **Get-Help** . Ниже приводится следующий пример, как определить резюме и помочь URI для в файле модуля .psm1:

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

  Если указать эти сведения, они будут отображаться при использовании командлета **Get-Help** в консоли PowerShell. Это описание также отображается на портале Azure.

  ![Справка по модулям интеграции](../media/modules/module-activity-description.png)

* Если модуль подключается к внешней службе, он должен содержать [тип соединения.](#add-a-connection-type-to-your-module) Каждый командлет в модуле должен принимать объект соединения (экземпляр этого типа соединения) в качестве параметра. Пользователи отображают параметры актива соединения с соответствующими параметрами cmdlet каждый раз, когда они называют cmdlet. Основываясь на приведенном выше примере runbook, он использует пример актива соединения Contoso, называемого ContosoConnection, для доступа к ресурсам Contoso и возврата данных из внешнего сервиса.

  В следующем примере поля отображаются в свойствах `PSCredential` UserName и Password объекта, а затем передаются в cmdlet.

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

  Вы можете задать для командлетов поведение, как в предыдущем примере, разрешив им принимать в качестве параметра напрямую объект подключения, а не только поля подключения. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации Azure, мог вызывать командлеты, не создавая хэш-таблицу, используемую в качестве объекта подключения. Набор `UserAccount`параметров, используется для передачи свойств поля соединения. `ConnectionObject`позволяет пройти соединение прямо через.

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

* Модуль должен полностью содержаться в пакете, способном кскопии. Модули Azure Automation распространяются в песочницах автоматизации, когда руны должны выполняться. Модули должны работать независимо от узла, на котором они запущены. Вы должны иметь возможность заархивировать и переместить пакет модуля и заставить его работать как обычно при импорте в среду PowerShell другого узла. Чтобы это произошло, модуль не должен зависеть от каких-либо файлов вне папки модуля. Эта папка архивируется при импорте модуля в службу автоматизации Azure. Модуль также не должен зависеть от каких-либо уникальных параметров реестра на узле, например от параметров, заданных при установке продукта. Все файлы в модуле должны иметь путь менее 140 символов. Любые пути более 140 символов вызовет проблемы импорта вашего runbook. При несоблюдении этой рекомендации модуль нельзя использовать в службе автоматизации Azure.  

* Если вы ссылаетесь на [модули Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) в своем модуле, убедитесь, что вы также не ссылаетесь на `AzureRM`. Модуль `Az` нельзя использовать в сочетании с модулями `AzureRM`. `Az` поддерживается в модулях Runbook, но по умолчанию не импортируется. Чтобы узнать о модулях `Az` и соображениях, которые необходимо учитывать, ознакомьтесь со статьей [Поддержка модулей Az в службе автоматизации Azure](../az-modules.md).

## <a name="default-modules"></a>Модули по умолчанию

В следующей таблице перечислены модули, импортируемые по умолчанию при создании учетной записи автоматизации. Перечисленные ниже модули могут иметь более новые версии импортируемых версий, но оригинальная версия не может быть удалена из учетной записи автоматизации, даже если вы удалите более новую версию.

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

* Дополнительные сведения о создании модулей PowerShell см. в статье [Writing a Windows PowerShell Module](/powershell/scripting/developer/windows-powershell) (Написание модуля Windows PowerShell).
