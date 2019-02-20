---
title: Создание модуля интеграции службы автоматизации Azure
description: В этом руководстве приводятся пошаговые инструкции по созданию и тестированию модулей интеграции, а также примеры их использования в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9122cf5cc908d578d8b781c6fdc49d7b04b0ab58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990348"
---
# <a name="azure-automation-integration-modules"></a>Модули интеграции службы автоматизации Azure

Служба автоматизации Azure создана с использованием технологии PowerShell. Поэтому модули PowerShell — основной компонент для расширения службы автоматизации Azure. Из этой статьи вы узнаете, как служба автоматизации Azure использует модули PowerShell, которые также называются модулями интеграции. Вы также узнаете рекомендации по созданию собственных модулей PowerShell, чтобы убедиться, что они работают как модули интеграции в службе автоматизации Azure. 

## <a name="what-is-a-powershell-module"></a>Что такое модуль PowerShell?

Модуль PowerShell — это группа командлетов PowerShell, таких как **Get-Date** или **Copy-Item**, которые можно использовать из:

* консоли PowerShell;
* сценариев;
* workflows
* модулей runbook;
* ресурсов DSC.

Все функциональные возможности PowerShell предоставляются через командлеты и ресурсы DSC. Каждый командлет и ресурс DSC поддерживаются модулем PowerShell, многие из которых поставляются с PowerShell. Например, командлет **Get-Date** — часть модуля `Microsoft.PowerShell.Utility` PowerShell, **Copy-Item** входит в состав модуля `Microsoft.PowerShell.Management` PowerShell, а ресурс Package DSC содержится в модуле PSDesiredStateConfiguration PowerShell. Эти модули поставляются с PowerShell. Множество модулей PowerShell не поставляются в составе PowerShell. Эти модули распределяются с продуктами основного или стороннего производителя или в таких местах, как коллекция PowerShell. Эти модули полезны, так как упрощают сложные задачи благодаря инкапсулированным возможностям.  Дополнительные сведения о модулях PowerShell см. в [библиотеке MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Что такое модуль интеграции службы автоматизации Azure?

Модуль интеграции работает аналогично модулю PowerShell. Это просто модуль PowerShell с одним дополнительным файлом — файлом метаданных, содержащим сведения о типе соединения службы автоматизации Azure, которое нужно использовать в командлетах модуля в Runbook. Модули PowerShell можно импортировать в службу автоматизации Azure, чтобы командлеты можно было использовать в Runbook, а ресурсы DSC — в конфигурациях DSC. По сути служба автоматизации Azure хранит эти модули и при выполнении задания Runbook и компиляции DSC загружает их в песочницы службы автоматизации Azure, где выполняются модули Runbook и компилируются конфигурации DSC. Любые ресурсы DSC в модулях также автоматически размещаются на опрашиваемом сервере Automation DSC. Компьютеры могут извлекать их, когда они применяют конфигурации DSC.  

В службе автоматизации Azure доступно несколько модулей Azure PowerShell. Модули PowerShell также можно импортировать в любую систему, службу или средство для последующей интеграции.

> [!NOTE]
> В службе автоматизации некоторые модули поставляются как **глобальные модули**. Эти глобальные модули предоставляются после создания учетной записи службы автоматизации. Иногда мы их обновляем, и эти изменения автоматически отправляются в учетную запись службы автоматизации. Если вы не хотите, чтобы они обновлялись автоматически, вы всегда можете импортировать в службу такой же модуль самостоятельно. Он имеет приоритет над версией глобального модуля, поставляемого в службе.

Пакет модуля интеграции импортируется в виде сжатого файла с тем же именем, что и у модуля, и расширением ZIP. Он содержит модуль Windows PowerShell и все вспомогательные файлы, в том числе файл манифеста (PSD1), если он есть в модуле.

Если модуль должен содержать сведения о типе соединения для службы автоматизации Azure, в нем также должен быть файл с именем `<ModuleName>-Automation.json`, в котором определены свойства типа соединения. Это файл JSON, который находится в папке модуля в сжатом ZIP-файле. Этот файл содержит поля **подключения**, необходимые для подключения к системе или службе, которую представляет модуль. В результате этой конфигурации в службе автоматизации Azure создается тип соединения. С помощью этого файла можно задать имена и типы полей, а также указать, нужно ли шифровать поля и/или являются ли они необязательными для типа соединения модуля. В следующем примере представлен шаблон JSON-файла.

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
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
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Если вы уже развернули Service Management Automation и создали пакеты модулей интеграции для Runbook службы автоматизации, этот шаблон должен быть вам знаком.

## <a name="authoring-best-practices"></a>Рекомендации по созданию

Хотя модули интеграции — это модули PowerShell, мы все равно рекомендуем учитывать некоторые моменты при создании модуля PowerShell для использования в службе автоматизации Azure. Некоторые рекомендации полезны для правильной работы модулей в рабочем процессе PowerShell.

1. Добавьте краткие сведения, описание и справочный универсальный код ресурса (URI) в каждый командлет в модуле. В PowerShell можно указать некоторые справочные сведения для командлетов, чтобы предоставить пользователю возможность получать справку по их использованию с помощью командлета **Get-Help** . Например, вот как можно определить краткие сведения и справочный URI для модуля PowerShell, записанного в файле с расширением PSM1. 
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```

   Если указать эти сведения, они будут отображаться при использовании командлета **Get-Help** в консоли PowerShell. Эти сведения также доступны в службе автоматизации Azure.  (например, при вставке действий во время создания модуля Runbook). Если щелкнуть "Просмотреть подробную справку", в браузере, используемом для получения доступа к службе автоматизации Azure, откроется вкладка со справочным URI.

   ![Справка по модулям интеграции](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. Если модуль работает в удаленной системе, нужно выполнить следующие требования.

   1. Модуль должен содержать файл метаданных модуля интеграции, содержащий сведения, необходимые для подключения к этой удаленной системе, то есть тип соединения.
   2. Каждый командлет в модуле должен принимать объект соединения (экземпляр этого типа соединения) в качестве параметра.  

    Использование командлетов в модуле в службе автоматизации Azure можно упростить, разрешив передачу объекта с полями типа соединения в качестве параметра в командлет. Благодаря этому пользователи могут сопоставлять параметры ресурса подключения с соответствующими параметрами командлета при каждом вызове командлета.

    В примере Runbook выше видно, что он использует ресурс подключения Twilio (CorpTwilio), чтобы получить доступ к Twilio и вернуть все номера телефонов в учетной записи.  Обратите внимание на то, как он сопоставляет поля со сведениями о подключении с параметрами командлета.

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Проще и лучше всего передать объект подключения непосредственно в командлет:

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    Вы можете задать для командлетов поведение, как в предыдущем примере, разрешив им принимать в качестве параметра напрямую объект подключения, а не только поля подключения. Обычно для каждого командлета нужно указать набор параметров, чтобы пользователь, который не использует службу автоматизации Azure, мог вызывать командлеты, не создавая хэш-таблицу, используемую в качестве объекта подключения. Набор параметров **SpecifyConnectionFields** используется для последовательной передачи свойств поля подключения. **UseConnectionObject** позволяет передать подключение напрямую. Как видно, командлет Send-TwilioSMS в [модуле Twilio PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) позволяет выполнять передачу любым из следующих способов:

    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```

3. Укажите тип выходных данных для всех командлетов в модуле. При этом можно воспользоваться технологией IntelliSense, используемой во время разработки, чтобы определить свойства выходных данных командлета, используемых при создании модуля. Это особенно полезно при графической разработке Runbook в службе автоматизации, при которой знание аспектов разработки упрощает работу пользователей с модулем.

   ![Тип выходных данных графического модуля Runbook](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   Это поведение похоже на упреждающий ввод выходных данных командлета в интегрированной среде сценариев PowerShell, при котором не нужно запускать командлет.

   ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. Командлеты в модуле не должны принимать сложные типы объектов в качестве параметров. Рабочий процесс PowerShell отличается от PowerShell, так как сложные типы объектов хранятся в нем в десериализованной форме. Примитивные типы остаются примитивными, а сложные типы преобразуются в десериализованные типы, которые по сути являются контейнерами свойств. Например, если использовать командлет **Get-Process** в модуле Runbook (или в рабочем процессе PowerShell, если на то пошло), будет возвращен объект типа [Deserialized.System.Diagnostic.Process], а не [System.Diagnostic.Process]. У этого типа те же свойства, что и у недесериализованного, но в нем нет соответствующих методов. При попытке передать это значение в качестве параметра в командлет, в котором для этого параметра требуется значение [System.Diagnostic.Process], возникнет следующая ошибка: *Не удается обработать преобразование аргументов для параметра "process". Ошибка: "Не удается преобразовать значение System.Diagnostics.Process (CcmExec) типа Deserialized.System.Diagnostics.Process в тип System.Diagnostics.Process".*   Она возникает из-за несоответствия требуемого типа [System.Diagnostic.Process] и указанного типа [Deserialized.System.Diagnostic.Process]. Для устранения этой проблемы нужно обеспечить, чтобы командлеты модуля не принимали сложные типы для параметров. Вот как не следует это делать:

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    Нужно принять примитивный тип, который используется в командлете для получения и использования сложного объекта. Так как командлеты выполняются в контексте PowerShell, а не рабочего процесса PowerShell, для параметра $process в командлете указывается правильный тип [System.Diagnostic.Process].  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Ресурсы подключения в модулях Runbook — это хэш-таблицы сложного типа. Но благодаря параметру 'Connection их можно без проблем передавать в командлеты. При такой передаче исключение приведения не происходит. С технической точки зрения некоторые типы PowerShell можно должным образом приводить из сериализованной формы в десериализованную. Следовательно, их можно передавать в командлеты для параметров, принимающих недесериализованный тип. Один из таких типов — хэш-таблица. Определенные разработчиком типы модуля можно реализовать так, чтобы их можно было правильно десериализовать. Тем не менее и при таком подходе есть некоторые недостатки. Для типа должен быть настроен конструктор по умолчанию, все его свойства должны быть общедоступными, а также он должен содержать параметр PSTypeConverter. Тем не менее невозможно преобразовать уже определенные типы, которыми не владеет разработчик модуля, поэтому рекомендуется не использовать сложные типы для всех параметров. Совет по созданию runbook. Если необходимо, чтобы командлеты принимали параметр сложного типа, для рабочего процесса PowerShell поместите командлет, создающий сложный тип, и командлет, использующий сложный тип, в оболочку одного действия InlineScript. Так как InlineScript выполняет содержимое в PowerShell, а не в рабочем процессе PowerShell, командлет, создающий сложный тип, создаст правильный, а не десериализованный сложный тип.

5. Не указывайте сведения о состоянии всех командлетов. Рабочий процесс PowerShell выполняет каждый командлет, вызванный в рабочем процессе, в отдельном сеансе. Это значит, что все командлеты, зависящие от состояния сеанса, которые создают или изменяют другие командлеты в том же модуле, не будут работать в Runbook рабочего процесса PowerShell.  Ниже представлен пример неправильного использования.
   
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

6. Модуль должен полностью находиться в пакете с поддержкой Xcopy. Модули службы автоматизации Azure распределяются в песочницы службы автоматизации, когда необходимо выполнить модули Runbook. Модули должны работать независимо от узла, на котором они запущены. Вы должны иметь возможность заархивировать и переместить пакет модуля и заставить его работать как обычно при импорте в среду PowerShell другого узла. Чтобы это произошло, модуль не должен зависеть от каких-либо файлов вне папки модуля. Эта папка архивируется при импорте модуля в службу автоматизации Azure. Модуль также не должен зависеть от каких-либо уникальных параметров реестра на узле, например от параметров, заданных при установке продукта. При несоблюдении этой рекомендации модуль нельзя использовать в службе автоматизации Azure.  

7. Если вы ссылаетесь на [модули Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) в своем модуле, убедитесь, что вы также не ссылаетесь на `AzureRM`. Модуль `Az` нельзя использовать в сочетании с модулями `AzureRM`. `Az` поддерживается в модулях Runbook, но по умолчанию не импортируется. Чтобы узнать о модулях `Az` и соображениях, которые необходимо учитывать, ознакомьтесь со статьей [Поддержка модулей Az в службе автоматизации Azure](az-modules.md).

## <a name="next-steps"></a>Дополнительная информация

* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
* Дополнительные сведения о создании модулей PowerShell см. в статье [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx) (Написание модуля Windows PowerShell).
