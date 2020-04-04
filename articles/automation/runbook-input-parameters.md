---
title: Входные параметры Runbook
description: Возможность настройки входных параметров увеличивает гибкость модулей Runbook, позволяя передавать данные в уже запущенный модуль. В этой статье описаны различные сценарии использования входных параметров в модулях Runbook.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656029"
---
# <a name="runbook-input-parameters"></a>Входные параметры Runbook

Параметры ввода Runbook повышают гибкость runbook, позволяя передавать данные ей при запуске. Эти параметры позволяют таргетировать действия runbook для конкретных сценариев и сред. В этой статье описывается конфигурация и использование параметров ввода в ваших runbooks.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="configuring-input-parameters"></a>Настройка параметров ввода

Можно настроить параметры ввода для powerShell, PowerShell Workflow, графических и Python runbooks. Runbook может использовать несколько параметров с разными типами данных или вообще не использовать параметры. Параметры ввода могут быть обязательными или необязательными, и вы можете использовать значения по умолчанию для дополнительных параметров.

При запуске значения ввода для запуска будут присваивать значения параметрам ввода. Вы можете запустить книгу запуска с портала Azure, веб-службы или PowerShell. Модуль можно также запустить как дочерний модуль Runbook, который вызывается с помощью встроенного вызова в другом Runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Настройка входных параметров в модулях Runbook PowerShell

Запуски PowerShell и PowerShell Workflow в Azure Automation поддерживают параметры ввода, которые определяются следующими свойствами. 

| **Свойство** | **Описание** |
|:--- |:--- |
| Type |Обязательный элемент. Предполагаемый тип данных значения параметра. Допускаются любые типы .NET. |
| name |Обязательный элемент. Имя параметра. Это имя должно быть уникальным в runbook, должны начать с буквы, и может содержать только буквы, цифры, или подчеркнуть символы. |
| Обязательный |Необязательный параметр. Значение Boolean, определяющее, требует ли параметр значения. Если вы установите это true, значение должно быть предоставлено при запуске. Если вы установите это на ложное значение не является обязательным. Если вы не указываете значение `Mandatory` для свойства, PowerShell считает параметр ввода необязательным по умолчанию. |
| Значение по умолчанию |Необязательный параметр. Значение, используемое для параметра, если при запуске запуска запуска не передается значение ввода. В runbook может быть установлено значение по умолчанию для любого параметра. |

Windows PowerShell поддерживает больше атрибутов параметров ввода, чем перечисленные выше, такие как проверка, псевдонимы и наборы параметров. Однако в настоящее время Azure Automation поддерживает только перечисленные свойства параметров ввода.

В качестве примера рассмотрим определение параметров в справочнике Рабочего процесса PowerShell. Это определение имеет следующую общую форму, где несколько параметров разделены запятыми.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Теперь давайте наладим параметры ввода для runbook PowerShell Workflow, который выводит сведения о виртуальных машинах, либо одном VM, либо обо всех виртуальных вгруппах ресурсов. Этот runbook имеет два параметра, как показано на следующем скриншоте: название виртуальной машины`VMName``resourceGroupName`() и название группы ресурсов ( ).

![Рабочий процесс PowerShell службы автоматизации](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

В этом параметре определения входного параметра являются простыми параметрами строки типа.

Обратите внимание, что runbooks PowerShell и PowerShell Workflow `Object` поддерживают `PSCredential` все простые типы и сложные типы, такие как или для параметров ввода. Если в вашей книге runbook есть параметр ввода объекта, необходимо использовать хэш-таблицу PowerShell с парами именных значений для передачи значения. Например, в runbook есть следующий параметр.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

В этом случае можно передать следующее значение параметру.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Если вы не передаете значение дополнительному параметру строки с нулевой значением по умолчанию, значение параметра является пустой строкой вместо Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Настройка входных параметров в графических модулях Runbook

Чтобы проиллюстрировать конфигурацию входных параметров для графического runbook, давайте создадим книгу, которая выводит сведения о виртуальных машинах, одном ВМ или всех виртуальных вводах в группе ресурсов. Для получения подробной информации, [см.](automation-first-runbook-graphical.md)

Графический runbook использует эти основные мероприятия runbook:

* Конфигурация Azure Run As учетной записи для проверки подлинности с помощью Azure. 
* Определение [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet, чтобы получить свойства VM.
* Использование действия [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) для вывода имен VM. 

Действие `Get-AzVM` определяет два входных ввода: имя VM и имя группы ресурсов. Поскольку эти имена могут быть разными каждый раз, когда запускаться, необходимо добавить параметры ввода в свой runbook, чтобы принять эти входные данные. Обратитесь к [графическому автору в Azure Automation](automation-graphical-authoring-intro.md).

Выполните следующие действия, чтобы настроить параметры ввода.

1. Выберите графический runbook со страницы Runbooks, а затем нажмите **Edit**.
2. В графическом редакторе нажмите кнопку **ввода и вывода,** а затем **добавьте вход,** чтобы открыть панель параметра ввода Runbook.

   ![Графический модуль Runbook службы автоматизации](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Элемент управления ввода и вывода отображает список параметров ввода, которые определены для runbook. Здесь можно добавить новый параметр ввода или отсваить конфигурацию существующего параметра ввода. Чтобы добавить новый параметр для runbook, нажмите **Добавить входные данные,** чтобы открыть лезвие **параметра ввода Runbook,** где можно настроить параметры с помощью свойств, определенных в [графическом авторизации в Azure Automation.](automation-graphical-authoring-intro.md)

    ![Добавление новых входных данных](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Создайте два параметра со следующими `Get-AzVM` свойствами, которые будут использоваться в действии, а затем нажмите **OK.**

   * Параметр 1:
        * **Имя** -- **VMName**
        * **Тип** -- Строка
        * **Обязательное** -- **нет**

   * Параметр 2:
        * **Название** -- **ресурсаGroupName**
        * **Тип** -- Строка
        * **Обязательное** -- **нет**
        * **Default value** -- **Пользовательские** значения значения по умолчанию
        * Пользовательское значение по умолчанию - Название группы ресурсов, содержащей vMs

5. Просмотр параметров в элементе управления ввода и вывода. 
6. Нажмите **OK** снова, а затем нажмите **Сохранить**.
7. Нажмите **Опубликовать** опубликовать свой runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Настройка входных параметров в модулях Runbook Python

В отличие от PowerShell, Рабочего процесса PowerShell и графических рунбуков, runbooks Python не принимают именованные параметры. Редактор runbook разбирает все параметры ввода как массив значений аргумента. Вы можете получить доступ к `sys` массиву, импортируя модуль `sys.argv` в свой скрипт Python, а затем используя массив. Важно отметить, `sys.argv[0]`что первым элементом массива, является название скрипта. Поэтому первый фактический `sys.argv[1]`параметр ввода .

Пример использования входных параметров в модуле Runbook Python см. в статье [Мой первый модуль Runbook Python в службе автоматизации Azure](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Назначение значений вводным параметрам в runbooks

В этом разделе описано несколько способов передачи значений ввода мгновенным параметрам в runbooks. Вы можете назначить значения параметров, когда вы:

* [Запуск runbook](#start-a-runbook-and-assign-parameters)
* [Тестирование runbook](#test-a-runbook-and-assign-parameters)
* [Ссылка на расписание для runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Создание веб-перехватчика для runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Запуск Runbook и назначение параметров

Запуск можно запустить по-разному: через портал Azure, с веб-крючком, с cmdlets PowerShell, с REST API или с SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Запустите опубликованную книгу с помощью портала Azure и присвоите параметры

При [запуске запуска](start-runbooks.md#start-a-runbook-with-the-azure-portal) на портале Azure открывается лезвие **Start Runbook,** и вы можете ввести значения для созданных параметров.

![Начало работы с порталом](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

На метке под входним полем можно увидеть свойства, установленные для определения атрибутов параметра, например обязательные или факультативные, ввеч, значение по умолчанию. Воздушный шар справки рядом с именем параметра также определяет ключевую информацию, необходимую для принятия решений о значениях ввода параметра. 

> [!NOTE]
> Параметры строки поддерживают пустые значения типа String. Ввод `[EmptyString]` в поле параметра ввода передает пустую строку к параметру. Кроме того, параметры строки не поддерживают Null. Если вы не передаете какое-либо значение параметру строки, PowerShell интерпретирует его как Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Запустите опубликованную книгу с использованием cmdlets PowerShell и присвоите параметры

* **Менеджер ресурсов Azure cmdlets:** Вы можете запустить запуск запуска автоматизации, который был создан в группе ресурсов с помощью [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Командлеты для классической модели развертывания Azure.** Модуль runbook службы автоматизации, созданный в группе ресурсов по умолчанию, можно запустить с помощью командлета [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> При запуске runbook с использованием PowerShell cmdlets, параметр по умолчанию, `MicrosoftApplicationManagementStartedBy`создается со значением. `PowerShell` Этот параметр можно просмотреть на панели деталей работы.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Запустите книгу с помощью SDK и назначить параметры

* **Метод управления ресурсами Azure:** Запустить книгу запуска можно с помощью SDK языка программирования. Ниже приведен фрагмент кода C# для запуска модуля Runbook в вашей учетной записи службы автоматизации. Весь код можно просмотреть в нашем [репозитории GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Метод для классической модели развертывания Azure.** Запустить модуль runbook можно с помощью пакета SDK для используемого языка программирования. Ниже приведен фрагмент кода C# для запуска модуля Runbook в вашей учетной записи службы автоматизации. Весь код можно просмотреть в нашем [репозитории GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Чтобы запустить этот метод, создайте словарь для `VMName` `resourceGroupName` хранения параметров runbook и их значений. Запустите модуль Runbook. Ниже приведен фрагмент кода C# для вызова метода, определенного выше.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Запустите запуск с помощью REST API и назначайте параметры

Вы можете создать и начать работу в runbook с API Azure Automation REST с помощью метода `PUT` со следующим запросом URI:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

В URI запроса замените следующие параметры:

* `subscriptionId`: Идентификатор подписки Azure.  
* `resourceGroupName`: Название группы ресурсов для учетной записи Automation.
* `automationAccountName`: Имя учетной записи Automation, размещенной в указанном облачном сервисе.  
* `jobName`: GUID для работы. GUIDs в PowerShell могут `[GUID]::NewGuid().ToString()*`быть созданы с помощью .

Чтобы передать параметры в задания runbook, используйте тело запроса. Он принимает следующую информацию, представленную в формате JSON:

* Runbook Name — обязательный параметр. Имя модуля Runbook для запуска задания.  
* Runbook Parameters — необязательный параметр. Словарь списка параметров в формате (имя, значение), где имя типа String и значение может быть любым действительным значением JSON.

Если вы хотите запустить runbook **Get-AzureVMTextual,** созданный ранее с `VMName` параметрами и `resourceGroupName` в качестве параметров, используйте следующий формат JSON для тела запроса.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Если задание успешно создано, возвращается код состояния HTTP 201. Для получения дополнительной информации о заголовках [create a runbook job by using the REST API](/rest/api/automation/job/create)ответов и органе ответа см.

### <a name="test-a-runbook-and-assign-parameters"></a>Тестирование Runbook и назначение параметров

При [тестировании черновой версии своей книги](automation-testing-runbook.md) с помощью тестовой опции открывается страница Test. Используйте эту страницу для настройки значений для созданных параметров.

![Тестирование и назначение параметров](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Связывание Runbook с расписанием и назначение параметров

Вы можете [связать расписание](automation-schedules.md) с модулем Runbook, чтобы он запускался в определенное время. При создании расписания вы назначаете входные параметры, значения которых модуль runbook использует во время запуска по расписанию. Нельзя сохранить расписание до тех пор, пока не будут предоставлены все обязательные значения параметров.

![Создание расписания и назначение параметров](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Создание объекта Webhook для модуля Runbook и назначение параметров

Для модуля Runbook можно создать объект [Webhook](automation-webhooks.md) и настроить входные параметры. Вы не можете сохранить веб-крюк до тех пор, пока не будут предоставлены все обязательные значения параметров.

![Создание Webhook и назначение параметров](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

При выполнении runbook с помощью веб-крючка `[WebhookData](automation-webhooks.md)` отправляется предопределенный параметр ввода, а также параметры ввода, которые вы определяете. 

![Параметр WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Передача объекта JSON в справочник

Иногда может быть полезным хранить данные, которые необходимо передать в модуль runbook, в файле JSON. Например, можно создать файл JSON, содержащий все параметры, которые вы хотите передать в книгу запуска. Для этого необходимо преобразовать код JSON в строку, а затем преобразовать строку в объект PowerShell, прежде чем передать ее в рунбук.

В этом разделе используется пример, в котором скрипт PowerShell вызывает [Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) чтобы запустить runbook PowerShell, передав содержимое файла JSON в рунбук. Запуск PowerShell запускает Azure VM, извлекая параметры Для VM с объекта JSON.

### <a name="create-the-json-file"></a>Создание файла JSON

Введите следующий код в текстовом файле и сохраните его как **test.json** где-нибудь на локальном компьютере.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Создание модуля runbook

Создайте новую книгу PowerShell под названием **Test-Json** в Azure Automation. Смотрите [мой первый PowerShell runbook](automation-first-runbook-textual-powershell.md).

Чтобы принять данные JSON, модуль runbook должен принять объект в качестве входного параметра. Затем в runbook можно использовать свойства, определенные в файле JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Сохраните и опубликуйте этот модуль runbook в учетной записи службы автоматизации.

### <a name="call-the-runbook-from-powershell"></a>Вызов модуля runbook из PowerShell

Теперь можно вызвать модуль runbook с локального компьютера с помощью Azure PowerShell. 

1. Войти в Azure, как показано на рисунке. После этого вам будет предложено ввести учетные данные Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Для PowerShell runbooks, `Add-AzAccount` и `Add-AzureRMAccount` псевдонимы для `Connect-AzAccount`. Обратите внимание, что эти псевдонимы не доступны для графических runbooks. Графический runbook может `Connect-AzAccount` использовать только себя.

1. Получите содержимое сохраненного файла JSON и преобразуйте его в строку. `JsonPath`указывает путь, по которому вы сохранили файл JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Преобразуйте `$json` содержимое строки в объект PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Создайте хэш-таблицу для `Start-AzAutomationRunbook`параметров. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Обратите внимание, что вы задаете значение `Parameters` объекту PowerShell, который содержит значения из файла JSON.
1. Запустите модуль runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Сведения об изменении текстового модуля Runbook см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
* Сведения об изменении графического модуля Runbook см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
