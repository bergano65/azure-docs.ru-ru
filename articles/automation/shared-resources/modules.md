---
title: Администрирование модулей в службе автоматизации Azure
description: Служба автоматизации Azure позволяет импортировать модули PowerShell, чтобы включить командлеты в модулях Runbook и ресурсах DSC в конфигурациях DSC.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84fdb5a9cf3c22048473cd00ee6f8e7ac36c9097
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864306"
---
# <a name="manage-modules-in-azure-automation"></a>Администрирование модулей в службе автоматизации Azure

Служба автоматизации Azure позволяет импортировать модули PowerShell, чтобы включить командлеты в модулях Runbook и ресурсах DSC в конфигурациях DSC. Модули, используемые в службе автоматизации Azure, включают:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0).
* Другие модули PowerShell.
* Внутренний `Orchestrator.AssetManagement.Cmdlets` модуль.
* Модули Python 2.
* Пользовательские модули, которые вы создадите.

При создании учетной записи службы автоматизации Azure по умолчанию импортирует некоторые модули. См. раздел [модули по умолчанию](#default-modules).

Когда Автоматизация выполняет задания компиляции Runbook и DSC, она загружает модули в песочницы, где могут выполняться модули Runbook, а конфигурации DSC могут быть скомпилированы. Автоматизация также автоматически размещает все ресурсы DSC в модулях на опрашивающем сервере DSC. Компьютеры могут извлекать ресурсы при применении конфигураций DSC.

>[!NOTE]
>Не забудьте импортировать только модули, которые действительно необходимы для модулей Runbook и конфигураций DSC. Не рекомендуется импортировать корневой модуль AZ. Он включает множество других модулей, которые могут не потребоваться, что может вызвать проблемы с производительностью. Вместо этого импортируйте отдельные модули, например AZ. COMPUTE.

>[!NOTE]
>В этой статье используется модуль Azure PowerShell AZ. Вы по-прежнему можете использовать модуль AzureRM. Дополнительные сведения о модуле AZ и совместимости AzureRM см. в разделе [Введение в новый модуль Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации модули можно обновить до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Модули по умолчанию

В следующей таблице перечислены модули, которые служба автоматизации Azure импортирует по умолчанию при создании учетной записи службы автоматизации. Автоматизация может импортировать более новые версии этих модулей. Однако вы не сможете удалить исходную версию из учетной записи службы автоматизации, даже если вы удалите более новую версию. Обратите внимание, что эти модули по умолчанию включают несколько модулей AzureRM. 

Автоматизация не импортирует корневой модуль AZ автоматически в любые новые или существующие учетные записи службы автоматизации. Дополнительные сведения о работе с этими модулями см. [в разделе Переход на AZ modules](#migrating-to-az-modules).

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

## <a name="az-modules"></a>AZ modules

Для AZ. Automation большинство командлетов имеют те же имена, что используются для модулей AzureRM, за исключением того, что префикс *AzureRM* был изменен на *AZ*. Список модулей AZ, которые не соответствуют соглашению об именовании, см. в [списке исключений](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Внутренние командлеты

Служба автоматизации Azure поддерживает внутренний `Orchestrator.AssetManagement.Cmdlets` модуль для агента log Analytics для Windows, установленного по умолчанию. В следующей таблице описаны внутренние командлеты. Эти командлеты предназначены для использования вместо командлетов Azure PowerShell для взаимодействия с общими ресурсами. Они могут получать секреты из зашифрованных переменных, учетных данных и зашифрованных соединений.

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

Обратите внимание, что внутренние командлеты отличаются по именам из командлетов AZ и AzureRM. Имена внутренних командлетов не содержат таких слов, как "Azure" или "AZ", в существительных, но используют *автоматизацию*Word. Мы рекомендуем использовать командлеты az или AzureRM во время выполнения Runbook в песочнице Azure или в гибридной рабочей роли Runbook Windows. Они занимают меньше параметров и выполняются в контексте уже запущенного задания.

Используйте командлеты az или AzureRM для манипулирования ресурсами службы автоматизации вне контекста модуля Runbook. 

## <a name="python-modules"></a>Модули Python

Модули Runbook Python 2 можно создавать в службе автоматизации Azure. Сведения о модуле Python см. [в статье Управление пакетами Python 2 в службе автоматизации Azure](../python-packages.md).

## <a name="custom-modules"></a>Пользовательские модули

Служба автоматизации Azure поддерживает пользовательские модули PowerShell, созданные для использования с модулями Runbook и конфигурациями DSC. Одним из типов настраиваемых модулей является модуль интеграции, который дополнительно содержит файл метаданных для определения пользовательской функциональности командлетов модуля. Пример использования модуля интеграции приведен в описании параметра [Добавление типа соединения](../automation-connections.md#add-a-connection-type).

Служба автоматизации Azure может импортировать пользовательский модуль, чтобы сделать его командлет доступным. В фоновом режиме он сохраняет модуль и использует его в изолированных средах Azure так же, как и другие модули.

## <a name="migrating-to-az-modules"></a>Переход к модулям Az

### <a name="migration-considerations"></a>Вопросы миграции

В этом разделе приводятся рекомендации, которые необходимо учитывать при переходе на модули az в службе автоматизации. Дополнительные сведения см. [в статье миграция Azure PowerShell из AzureRM в AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>Использование модулей AzureRM и AZ modules в одной учетной записи службы автоматизации

 Мы не рекомендуем запускать модули AzureRM и az в одной и той же учетной записи службы автоматизации. Если вы уверены, что хотите выполнить миграцию с AzureRM на AZ, то лучше полностью зафиксировать полную миграцию. Автоматизация часто использует "песочницы" в учетной записи службы автоматизации, чтобы сэкономить время запуска. Если вы не сделаете полную миграцию модуля, вы можете запустить задание, использующее только модули AzureRM, а затем запустить другое задание, использующее только модули AZ. Вскоре произойдет сбой "песочницы", и появится сообщение о том, что модули несовместимы. Это приводит к случайному возникновению сбоев для любого конкретного модуля или конфигурации. 

#### <a name="importing-az-modules-into-the-powershell-session"></a>Импорт модулей az в сеанс PowerShell

При импорте модуля az в учетную запись службы автоматизации модуль не импортируется автоматически в сеанс PowerShell, используемый модулями Runbook. Модули импортируются в сеанс PowerShell в следующих ситуациях:

* Когда модуль Runbook вызывает командлет из модуля.
* Когда Runbook импортирует модуль явным образом с помощью командлета [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7) .
* Когда модуль Runbook импортирует другой зависимый модуль.

#### <a name="testing-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Тестирование модулей Runbook и конфигураций DSC до миграции модулей

Прежде чем переходить на модули AZ, тщательно протестируйте все модули Runbook и конфигурации DSC в отдельной учетной записи службы автоматизации. 

#### <a name="updates-for-tutorial-runbooks"></a>Обновления для учебных модулей Runbook 

При создании новой учетной записи службы автоматизации даже после миграции на AZ modules по умолчанию службы автоматизации устанавливают модули AzureRM. Вы по-прежнему можете обновить учебные модули Runbook с помощью командлетов AzureRM. Однако не следует запускать эти модули Runbook.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Останавливает и отменяет планирование всех модулей Runbook, использующих модули AzureRM

Чтобы не запускать существующие модули Runbook или конфигурации DSC, использующие AzureRM Modules, необходимо приостанавливаться и отменять планирование всех затронутых модулей Runbook и конфигураций. Сначала убедитесь, что каждый модуль Runbook или Конфигурация DSC и его расписания просматриваются отдельно, чтобы при необходимости можно было перепланировать элемент в будущем. 

Когда вы будете готовы удалить расписания, можно либо использовать портал Azure, либо командлет [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . См. раздел [Удаление расписания](schedules.md#remove-a-schedule).

### <a name="remove-the-azurerm-modules"></a>Удаление модулей AzureRM

Можно удалить модули AzureRM перед импортом модулей AZ. Однако в этом случае можно прервать синхронизацию системы управления версиями и привести к сбою всех сценариев, по которым по-прежнему происходит сбой. Если вы решили удалить эти модули, см. раздел [uninstall AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-the-az-modules"></a>Импорт модулей Az

Модули AZ можно импортировать в портал Azure. Не забудьте импортировать только необходимые модули AZ, а не весь модуль AZ. Automation. Поскольку [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) является зависимостью от других модулей AZ, обязательно импортируйте этот модуль перед другими.

1. В учетной записи службы автоматизации в разделе **Общие ресурсы**выберите **модули**. 
2. Выберите **Обзор Галерея**.  
3. В строке поиска введите имя модуля (например, `Az.Accounts`). 
4. На странице **модуль PowerShell** выберите **Импорт** , чтобы импортировать модуль в учетную запись службы автоматизации.

    ![Снимок экрана: импорт модулей в учетную запись службы автоматизации](../media/modules/import-module.png)

Этот импорт можно также выполнить с помощью [коллекция PowerShell](https://www.powershellgallery.com), выполнив поиск импортируемого модуля. Если модуль найден, выберите его и перейдите на вкладку Служба **автоматизации Azure** . Выберите **развертывание в службе автоматизации Azure**.

![Снимок экрана: импорт модулей непосредственно из коллекция PowerShell](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Тестирование модулей runbook

После импорта модулей az в учетную запись службы автоматизации можно начать редактирование модулей Runbook и конфигураций DSC, чтобы использовать новые модули. Один из способов протестировать изменение модуля Runbook для использования новых командлетов — использовать `Enable-AzureRmAlias -Scope Process` команду в начале модуля Runbook. При добавлении этой команды в модуль Runbook скрипт может выполняться без изменений. 

## <a name="author-modules"></a>Модули автора

При создании пользовательского модуля PowerShell для использования в службе автоматизации Azure рекомендуется следовать рекомендациям, приведенным в этом разделе. Чтобы подготовить модуль к импорту, необходимо создать как минимум файл PSD1, PSM1 или PowerShell Module **. dll** с тем же именем, что и у папки Module. Затем вы заархивируйте папку модуля, чтобы служба автоматизации Azure могла импортировать ее как один файл. **ZIP** -пакет должен иметь то же имя, что и вложенная папка модуля. 

Дополнительные сведения о создании модуля PowerShell см. в статье [написание модуля сценариев PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

### <a name="version-folder"></a>Папка версии

НЕ включайте папку версии в **ZIP** -пакет для модуля. Эта проблема менее важна для модулей Runbook, но вызывает проблему со службой настройки состояния (DSC). Служба автоматизации Azure создает папку версии автоматически при распространении модуля на узлы, управляемые с помощью конфигурации состояния. Если папка версии существует, то в итоге у вас будет два экземпляра. Ниже приведен пример структуры папок для модуля DSC.

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

  ![Снимок экрана справки по модулю интеграции](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Тип подключения

Если модуль подключается к внешней службе, определите тип соединения с помощью [пользовательского модуля интеграции](#custom-modules). Каждый командлет в модуле должен принимать экземпляр этого типа соединения (объект соединения) в качестве параметра. Пользователи сопоставляют параметры ресурса подключения с соответствующими параметрами командлета каждый раз, когда они вызывают командлет. 

![Использовать пользовательское соединение в портал Azure](../media/modules/connection-create-new.png)

В следующем примере Runbook используется ресурс-контейнер подключения Contoso `ContosoConnection` для доступа к ресурсам Contoso и возврата данных из внешней службы. В этом примере поля сопоставляются со свойствами `UserName` и `Password` `PSCredential` объекта, а затем передаются в командлет.

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

Вы можете включить аналогичное поведение для командлетов, разрешая им принимать объект соединения непосредственно в качестве параметра, а не только полей соединения для параметров. Обычно для каждого параметра требуется задать набор параметров, чтобы пользователь, не использующий автоматизацию, мог вызывать командлеты без создания хэш-таблицы, которая будет использоваться в качестве объекта соединения. Набор `UserAccount` параметров используется для передачи свойств поля соединения. `ConnectionObject`позволяет передавать соединение напрямую через.

### <a name="output-type"></a>Тип выходных данных

Определите тип выходных данных для всех командлетов в модуле. Определение типа выходных данных для командлета позволяет IntelliSense во время разработки определить выходные свойства командлета во время разработки. Такой подход особенно полезен во время графического создания модулей Runbook, для которого знание времени разработки является ключом к простоте взаимодействия с вашим модулем.

Добавить `[OutputType([<MyOutputType>])]`, где `MyOutputType` является допустимым типом. Дополнительные сведения о `OutputType`функциях см. в разделе [About functions аутпуттипеаттрибуте](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Следующий код является примером добавления `OutputType` в командлет:

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

  ![Снимок экрана графического типа выходных данных Runbook](../media/modules/runbook-graphical-module-output-type.png)

  Такое поведение аналогично поведению "переупреждающее" функции выходных данных командлета в среде службы интеграции PowerShell без необходимости запуска.

  ![Снимок экрана POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

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

Убедитесь, что модуль полностью содержится в пакете, который можно скопировать с помощью XCOPY. Модули автоматизации распределяются в песочнице службы автоматизации при выполнении модулей Runbook. Модули должны работать независимо от узла, на котором они выполняются. 

Вы сможете создать резервную копию пакета модуля и переместить его в нормальном режиме при импорте в среду PowerShell другого узла. Для этого убедитесь, что модуль не зависит от файлов, находящихся вне папки модуля, которая заархивирована при импорте модуля в службу автоматизации. 

Модуль не должен зависеть от каких-либо уникальных параметров реестра на узле. Примерами являются параметры, выполняемые при установке продукта. 

### <a name="module-file-paths"></a>Пути к файлам модулей

Убедитесь, что все файлы в модуле имеют пути, длина которых не превышает 140 символов. Любые пути, длина которых превышает 140 символов, вызывают проблемы с импортом модулей Runbook. Службе автоматизации не удается импортировать файл с размером пути свыше 140 символов в сеанс PowerShell с `Import-Module`.

## <a name="import-modules"></a>Импорт модулей

В этом разделе описывается несколько способов импорта модуля в учетную запись службы автоматизации. 

### <a name="import-modules-in-the-azure-portal"></a>Импорт модулей в портал Azure

Чтобы импортировать модуль в портал Azure, выполните следующие действия.

1. Перейдите к учетной записи службы автоматизации.
2. В разделе **Общие ресурсы**выберите **модули**.
3. Выберите **Добавить модуль**. 
4. Выберите **ZIP** -файл, содержащий модуль.
5. Нажмите кнопку **ОК** , чтобы начать импорт процесса.

### <a name="import-modules-by-using-powershell"></a>Импорт модулей с помощью PowerShell

Для импорта модуля в учетную запись службы автоматизации можно использовать командлет [New-азаутоматионмодуле](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) . Командлет принимает URL-адрес для пакета Module. zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Вы также можете использовать тот же командлет для импорта модуля из коллекция PowerShell напрямую. Убедитесь, что вы `ModuleName` `ModuleVersion` захватите [коллекция PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Импорт модулей из коллекция PowerShell

Вы можете импортировать [коллекция PowerShell](https://www.powershellgallery.com) модули непосредственно из коллекции или из учетной записи службы автоматизации.

Чтобы импортировать модуль непосредственно из коллекция PowerShell:

1. Перейдите в https://www.powershellgallery.com раздел и найдите модуль для импорта.
2. В разделе **Параметры установки**на вкладке Служба **автоматизации Azure** выберите **развертывание в службе автоматизации Azure**. Это действие открывает портал Azure. 
3. На странице **Импорт** выберите учетную запись службы автоматизации и нажмите кнопку **ОК**.

![Снимок экрана: Модуль импорта коллекция PowerShell](../media/modules/powershell-gallery.png)

Чтобы импортировать модуль коллекция PowerShell непосредственно из учетной записи службы автоматизации, выполните следующие действия.

1. В разделе **Общие ресурсы**выберите **модули**. 
2. Выберите **Обзор Галерея**и найдите модуль в коллекции. 
3. Выберите модуль для импорта и щелкните **Импорт**. 
4. Нажмите кнопку **ОК** , чтобы начать процесс импорта.

![Снимок экрана: импорт модуля коллекция PowerShell из портал Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Удаление модулей

Если у вас возникли проблемы с модулем или необходимо выполнить откат к предыдущей версии модуля, его можно удалить из учетной записи службы автоматизации. Нельзя удалить исходные версии [модулей по умолчанию](#default-modules) , которые импортируются при создании учетной записи службы автоматизации. Если удаляемый модуль является более новой версией одного из [модулей по умолчанию](#default-modules), он выполняет откат к версии, которая была установлена вместе с учетной записью службы автоматизации. В противном случае удаляется любой модуль, удаляемый из учетной записи службы автоматизации.

### <a name="delete-modules-in-the-azure-portal"></a>Удаление модулей в портал Azure

Чтобы удалить модуль из портал Azure:

1. Перейдите к учетной записи службы автоматизации. В разделе **Общие ресурсы**выберите **модули**. 
2. Выберите модуль, который требуется удалить. 
3. На странице **модуль** выберите **Удалить**. Если этот модуль является одним из [модулей по умолчанию](#default-modules), он выполняет откат к версии, существовавшей при создании учетной записи службы автоматизации.

### <a name="delete-modules-by-using-powershell"></a>Удаление модулей с помощью PowerShell

Чтобы удалить модуль с помощью PowerShell, выполните следующую команду:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения об использовании модулей Azure PowerShell см. в статье [Приступая к работе с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Дополнительные сведения о создании модулей PowerShell см. в разделе [написание модуля Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
