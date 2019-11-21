---
title: Manage Modules in Azure Automation
description: This article describes how to manage modules in Azure Automation
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 492dd182c782b0f6375c2f857cfa4921b065c546
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231579"
---
# <a name="manage-modules-in-azure-automation"></a>Manage Modules in Azure Automation

Azure Automation provides the ability to import PowerShell modules into your Automation Account to be used by the PowerShell based runbooks. These modules can be custom modules you've created, from the PowerShell Gallery, or the AzureRM and Az modules for Azure. When you create an Automation Account, some modules are imported by default.

## <a name="import-modules"></a>Импорт модулей

There are multiple ways that you can import a module into your Automation Account. The following sections show the different ways to import a module.

> [!NOTE]
> The max path of a file in a module to be used in Azure Automation is 140 characters. Any path over 140 characters will not be able to be imported into the PowerShell session with `Import-Module`.

### <a name="powershell"></a>PowerShell

You can use the [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) to import a module into your Automation Account. The cmdlet takes a url to a module zip package.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>портала Azure

In the Azure portal, navigate to your Automation Account and select **Modules** under **Shared Resources**. Click **+ Add a module**. Select a **.zip** file that contains your module and click **Ok** to start to import process.

### <a name="powershell-gallery"></a>Коллекция PowerShell

Modules from the PowerShell gallery can either be imported from the [PowerShell Gallery](https://www.powershellgallery.com) directly or from your Automation Account.

To import a module from the PowerShell Gallery, go to https://www.powershellgallery.com and search for the module you want to import. Click **Deploy to Azure Automation** on the **Azure Automation** tab under **Installation Options**. Портал Azure можно открыть с помощью этого действия. On the **Import** page, select your Automation Account and click **OK**.

![PowerShell Gallery import module](../media/modules/powershell-gallery.png)

You can also import modules from the PowerShell Gallery directly from your Automation Account. In your Automation Account, select **Modules** under **Shared Resources**. On the modules page click **Browse gallery**, then search the PowerShell Gallery for a module. Select the module you want to import and click **Import**. On the **Import** page, click **OK** to start the import process.

![PowerShell Gallery import from Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Delete modules

If you have issues with a module or you need to roll back to a previous version of a module, you can delete it from your Automation Account. You can not delete the original version of the [default modules](#default-modules) that are imported when you create an Automation Account. If the module you want to delete is a newer version of one of the [default modules](#default-modules) installed, it will roll-back to the version that was installed with your Automation Account. Otherwise, any module you delete from your Automation Account will be removed.

### <a name="azure-portal"></a>портала Azure

In the Azure portal, navigate to your Automation Account and select **Modules** under **Shared Resources**. Select the module you want to remove. On the **Module** page, clcick **Delete**. If this module is one of the [default modules](#default-modules), it will be rolled back to the version that was present when the Automation Account was created.

### <a name="powershell"></a>PowerShell

To remove a module through PowerShell, run the following command:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Internal cmdlets

The following is a listing of cmdlets in the internal `Orchestrator.AssetManagement.Cmdlets` module that is imported into every Automation Account. These cmdlets are accessible in your runbooks and DSC configurations and allow you to interact with your assets within your Automation Account. Additionally, the internal cmdlets allow you to retrieve secrets from encrypted **Variable** values, **Credentials**, and encrypted **Connection** fields. The Azure PowerShell cmdlets are not able to retrieve these secrets. These cmdlets do not require you to implicitly connect to Azure when using them, such as using a Run As Account to authenticate to Azure.

|Name|Описание|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Add a connection type to your module

You can provide a custom [connection type](../automation-connections.md) for you to use in your Automation Account by adding an optional file to your module. This file is a metadata file specifying an Azure Automation connection type to be used with the module's cmdlets in your Automation Account. To achieve this, you must first know how to author a PowerShell module. For more information on module authoring, see [How to Write a PowerShell Script Module](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Use a custom connection in the Azure portal](../media/modules/connection-create-new.png)

To add an Azure Automation connection type, your module must contain a file with the name `<ModuleName>-Automation.json` that specifies the connection type properties. The json file is placed within the module folder of your compressed .zip file. This file contains the fields of a connection that is required to connect to the system or service the module represents. The configuration ends up creating a connection type in Azure Automation. Using this file you can set the field names, types, and whether the fields should be encrypted or optional, for the connection type of the module. The following example is a template in the json file format that defines a username and password property:

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

## <a name="module-best-practices"></a>Module best practices

Модули PowerShell можно импортировать в службу автоматизации Azure, чтобы командлеты можно было использовать в Runbook, а ресурсы DSC — в конфигурациях DSC. По сути служба автоматизации Azure хранит эти модули и при выполнении задания Runbook и компиляции DSC загружает их в песочницы службы автоматизации Azure, где выполняются модули Runbook и компилируются конфигурации DSC. Любые ресурсы DSC в модулях также автоматически размещаются на опрашиваемом сервере Automation DSC. Компьютеры могут извлекать их, когда они применяют конфигурации DSC.

We recommend you consider the following when you author a PowerShell module for use in Azure Automation:

* Do NOT include a version folder within the .zip package.  This issue is less of a concern for runbooks but will cause an issue with the State Configuration service.  Azure Automation will create the version folder automatically when the module is distributed to nodes managed by DSC, and if a version folder exists you will end up with two instances.  Example folder structure for a  DSC module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Добавьте краткие сведения, описание и справочный универсальный код ресурса (URI) в каждый командлет в модуле. В PowerShell можно указать некоторые справочные сведения для командлетов, чтобы предоставить пользователю возможность получать справку по их использованию с помощью командлета **Get-Help** . The following example shows how to define a synopsis and help URI for in a .psm1 module file:

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

  Если указать эти сведения, они будут отображаться при использовании командлета **Get-Help** в консоли PowerShell. This description is also displayed in the Azure portal.

  ![Справка по модулям интеграции](../media/modules/module-activity-description.png)

* If the module connects to an external service, it should contain a [connection type](#add-a-connection-type-to-your-module). Каждый командлет в модуле должен принимать объект соединения (экземпляр этого типа соединения) в качестве параметра. Users map parameters of the connection asset to the cmdlet's corresponding parameters each time they call a cmdlet. Based on the runbook example above, it uses an example Contoso connection asset called ContosoConnection to access Contoso resources and return data from the external service.

  In the following example, the fields are mapped to the UserName and Password properties of a `PSCredential` object and then passed to the cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  An easier and better way to approach this behavior is directly passing the connection object to the cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Вы можете задать для командлетов поведение, как в предыдущем примере, разрешив им принимать в качестве параметра напрямую объект подключения, а не только поля подключения. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации Azure, мог вызывать командлеты, не создавая хэш-таблицу, используемую в качестве объекта подключения. The parameter set `UserAccount`, is used to pass the connection field properties. `ConnectionObject` lets you pass the connection straight through.

* Define the output type for all cmdlets in the module. При этом можно воспользоваться технологией IntelliSense, используемой во время разработки, чтобы определить свойства выходных данных командлета, используемых при создании модуля. Это особенно полезно при графической разработке Runbook в службе автоматизации, при которой знание аспектов разработки упрощает работу пользователей с модулем.

Add `[OutputType([<MyOutputType>])]` where MyOutputType is a valid type. To learn more about OutputType, see [About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). The following code is an example of adding `OutputType` to a cmdlet:

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

* Не указывайте сведения о состоянии всех командлетов. Multiple runbook jobs can simultaneously run in the same AppDomain and the same process and sandbox. If there is any state shared on those levels, jobs can affect each other. This behavior can lead to intermittent and hard to diagnose issues.  Ниже представлен пример неправильного использования.

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

* The module should be fully contained in an xcopy-able package. Azure Automation modules are distributed to the Automation sandboxes when runbooks need to execute. Модули должны работать независимо от узла, на котором они запущены. Вы должны иметь возможность заархивировать и переместить пакет модуля и заставить его работать как обычно при импорте в среду PowerShell другого узла. Чтобы это произошло, модуль не должен зависеть от каких-либо файлов вне папки модуля. Эта папка архивируется при импорте модуля в службу автоматизации Azure. Модуль также не должен зависеть от каких-либо уникальных параметров реестра на узле, например от параметров, заданных при установке продукта. All files in the module should have a path fewer than 140 characters. Any paths over 140 characters will cause issues importing your runbook. При несоблюдении этой рекомендации модуль нельзя использовать в службе автоматизации Azure.  

* Если вы ссылаетесь на [модули Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) в своем модуле, убедитесь, что вы также не ссылаетесь на `AzureRM`. Модуль `Az` нельзя использовать в сочетании с модулями `AzureRM`. `Az` поддерживается в модулях Runbook, но по умолчанию не импортируется. Чтобы узнать о модулях `Az` и соображениях, которые необходимо учитывать, ознакомьтесь со статьей [Поддержка модулей Az в службе автоматизации Azure](../az-modules.md).

## <a name="default-modules"></a>Default modules

The following table lists the modules that are imported by default when an Automation Account is created. The modules listed below can have newer versions of them imported, but the original version can not be removed from your Automation Account even if you delete a newer version of them.

|Module name|Версия|
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

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о создании модулей PowerShell см. в статье [Writing a Windows PowerShell Module](/powershell/scripting/developer/windows-powershell) (Написание модуля Windows PowerShell).
