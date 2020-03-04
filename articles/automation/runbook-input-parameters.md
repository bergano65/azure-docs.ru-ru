---
title: Входные параметры Runbook
description: Возможность настройки входных параметров увеличивает гибкость модулей Runbook, позволяя передавать данные в уже запущенный модуль. В этой статье описаны различные сценарии использования входных параметров в модулях Runbook.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ddb08f774bbb8aa3bc4b10bcd0dd213c8583465e
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249793"
---
# <a name="runbook-input-parameters"></a>Входные параметры Runbook

Входные параметры модуля Runbook увеличивают гибкость модуля Runbook, позволяя передавать данные в него при запуске. Эти параметры позволяют выполнять действия Runbook для конкретных сценариев и сред. В этой статье описывается конфигурация и использование входных параметров в модулях Runbook.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Настройка входных параметров

Вы можете настроить входные параметры для PowerShell, рабочего процесса PowerShell, графического модуля и модулей Runbook Python. Runbook может использовать несколько параметров с разными типами данных или вообще не использовать параметры. Входные параметры могут быть обязательными или необязательными. для необязательных параметров можно использовать значения по умолчанию.

При запуске модуля Runbook вы назначаете значения входным параметрам. Модуль Runbook можно запустить из портал Azure, веб-службы или PowerShell. Модуль можно также запустить как дочерний модуль Runbook, который вызывается с помощью встроенного вызова в другом Runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Настройка входных параметров в модулях Runbook PowerShell

Модули Runbook рабочих процессов PowerShell и PowerShell в службе автоматизации Azure поддерживают входные параметры, определяемые с помощью следующих свойств. 

| **Свойство** | **Описание** |
|:--- |:--- |
| Тип |Обязательный элемент. Предполагаемый тип данных значения параметра. Допускаются любые типы .NET. |
| Имя |Обязательный элемент. Имя параметра. Это имя должно быть уникальным в пределах модуля Runbook, должно начинаться с буквы и может содержать только буквы, цифры или символы подчеркивания. |
| Обязательный |Необязательный параметр. Логическое значение, указывающее, требуется ли для параметра значение. Если задано значение **true**, при запуске модуля Runbook необходимо указать значение. Если присвоить этому параметру значение **false**, оно является необязательным. Если не указать значение для **обязательного** свойства, то по умолчанию PowerShell считает входной параметр необязательным. |
| Значение по умолчанию |Необязательный параметр. Значение, которое используется для параметра, если при запуске модуля Runbook не передается входное значение. Модуль Runbook может установить значение по умолчанию для любого параметра. |

Windows PowerShell поддерживает больше атрибутов входных параметров, чем перечисленные выше, такие как проверка, псевдонимы и наборы параметров. Однако служба автоматизации Azure в настоящее время поддерживает только перечисленные свойства входного параметра.

Например, рассмотрим определение параметра в модуле Runbook рабочего процесса PowerShell. Это определение имеет следующую общую форму, в которой несколько параметров разделены запятыми.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Теперь настроим входные параметры для модуля Runbook рабочего процесса PowerShell, который выводит сведения о виртуальных машинах: одну виртуальную машину или все виртуальные машины в группе ресурсов. Этот модуль Runbook имеет два параметра, как показано на следующем снимке экрана: имя виртуальной машины (*VMName*) и имя группы ресурсов (*resourceGroupName*).

![Рабочий процесс PowerShell службы автоматизации](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

В этом определении параметра входные параметры являются простыми параметрами типа String.

Обратите внимание, что модули Runbook рабочих процессов PowerShell и PowerShell поддерживают все простые и сложные типы, такие как **Object** или **PSCredential** , для входных параметров. Если в модуле Runbook есть входной параметр объекта, для передачи значения необходимо использовать хэш-таблицу PowerShell с парами "имя — значение". Например, в модуле Runbook имеется следующий параметр.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

В этом случае в параметр можно передать следующее значение.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Если не передать значение необязательному строковому параметру со значением по умолчанию NULL, значение параметра будет пустой строкой, а не **null**.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Настройка входных параметров в графических модулях Runbook

Чтобы проиллюстрировать настройку входных параметров для графического модуля Runbook, создадим модуль Runbook, который выводит сведения о виртуальных машинах — либо одну виртуальную машину, либо все виртуальные машины в группе ресурсов. Дополнительные сведения см. в разделе [Мой первый графический Runbook](automation-first-runbook-graphical.md).

Графический модуль Runbook использует эти основные действия Runbook:

* Настройка учетной записи запуска от имени Azure для аутентификации в Azure. 
* Определение командлета [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) для получения свойств виртуальной машины.
* Используйте действие [записи-вывода](/powershell/module/microsoft.powershell.utility/write-output) для вывода имен виртуальных машин. 

Действие **Get-AzVM** определяет два входных значения: имя виртуальной машины и имя группы ресурсов. Так как эти имена могут отличаться при каждом запуске модуля Runbook, необходимо добавить входные параметры в модуль Runbook, чтобы они принимали эти входные данные. Ознакомьтесь с [графическим созданием в службе автоматизации Azure](automation-graphical-authoring-intro.md).

Выполните следующие действия, чтобы настроить входные параметры.

1. Выберите графический модуль Runbook на странице **модули Runbook** и нажмите кнопку **изменить**.
2. В графическом редакторе нажмите кнопку **входы и выход** , а затем **Добавить входные данные** , чтобы открыть панель входных параметров Runbook.

   ![Графический модуль Runbook службы автоматизации](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Элемент управления вводом и выходом отображает список входных параметров, определенных для модуля Runbook. Здесь можно либо добавить новый входной параметр, либо изменить конфигурацию существующего входного параметра. Чтобы добавить новый параметр для модуля Runbook, щелкните **Добавить входные данные** , чтобы открыть колонку **входного параметра Runbook** , где можно настроить параметры с помощью свойств, определенных в [графической разработке в службе автоматизации Azure](automation-graphical-authoring-intro.md).

    ![Добавление новых входных данных](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Создайте два параметра со следующими свойствами, которые будут использоваться действием **Get-AzVM** , и нажмите кнопку **ОК**.

   * Параметр 1:
        * **Имя** -- **VMName**
        * **Тип** --строка
        * **Обязательный** -- **нет**

   * Параметр 2:
        * **Имя** -- **resourceGroupName**
        * **Тип** --строка
        * **Обязательный** -- **нет**
        * **Значение по умолчанию** -- **настраиваемое**
        * Пользовательское значение по умолчанию — имя группы ресурсов, содержащей виртуальные машины.

5. Просмотр параметров в элементе управления вводом и выводом. 
6. Снова нажмите кнопку **ОК** , а затем нажмите кнопку **сохранить**.
7. Нажмите кнопку **опубликовать** , чтобы опубликовать модуль Runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Настройка входных параметров в модулях Runbook Python

В отличие от PowerShell, рабочего процесса PowerShell и графических модулей Runbook, модули Runbook Python не принимают именованные параметры. Редактор Runbook анализирует все входные параметры как массив значений аргументов. Доступ к массиву можно получить, импортировав модуль **sys** в скрипт Python, а затем используя массив **sys. argv** . Важно отметить, что первый элемент массива, `sys.argv[0]`, является именем скрипта. Поэтому первым фактическим входным параметром является *sys. argv [1]* .

Пример использования входных параметров в модуле Runbook Python см. в статье [Мой первый модуль Runbook Python в службе автоматизации Azure](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Назначение значений входным параметрам в модулях Runbook

В этом разделе описаны несколько способов передачи значений входным параметрам в модулях Runbook. Значения параметров можно назначать при выполнении следующих действий:

* [Запуск модуля Runbook](#start-a-runbook-and-assign-parameters)
* [Тестирование модуля Runbook](#test-a-runbook-and-assign-parameters)
* [Связывание расписания для модуля Runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Создание веб-перехватчика для модуля Runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Запуск Runbook и назначение параметров

Модуль Runbook можно запустить несколькими способами: через портал Azure, с помощью веб-перехватчика, командлетами PowerShell, с REST API или с помощью пакета SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Запуск опубликованного модуля Runbook с помощью портал Azure и назначение параметров

При [запуске модуля Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) в портал Azure откроется колонка **Запуск Runbook** , где можно ввести значения для созданных параметров.

![Начало работы с порталом](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

В метке под полем ввода можно просмотреть свойства, заданные для определения атрибутов параметров, например обязательные или необязательные, тип, значение по умолчанию. Всплывающая подсказка рядом с именем параметра также определяет ключевые сведения, необходимые для принятия решений о входных значениях параметров. 

> [!NOTE]
> Строковые параметры поддерживают пустые значения строкового типа. Если ввести **[EmptyString]** в текстовое поле входного параметра, ему передастся пустая строка. Кроме того, строковые параметры не поддерживают значение null. Если не передать какое-либо значение в строковый параметр, PowerShell интерпретирует его как null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Запуск опубликованного модуля Runbook с помощью командлетов PowerShell и назначение параметров

* **Azure Resource Manager командлетов:** Модуль Runbook службы автоматизации, созданный в группе ресурсов, можно запустить с помощью команды [Start-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

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
> При запуске модуля Runbook с помощью командлетов PowerShell параметр по умолчанию *микрософтаппликатионманажементстартедби*создается со значением **PowerShell**. Этот параметр можно просмотреть в области сведений о задании.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Запуск модуля Runbook с помощью пакета SDK и назначение параметров

* **Azure Resource Manager метод:** Модуль Runbook можно запустить с помощью пакета SDK языка программирования. Ниже приведен фрагмент кода C# для запуска модуля Runbook в вашей учетной записи службы автоматизации. Весь код можно просмотреть в нашем [репозитории GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Метод для классической модели развертывания Azure.** Запустить модуль runbook можно с помощью пакета SDK для используемого языка программирования. Ниже приведен фрагмент кода C# для запуска модуля Runbook в вашей учетной записи службы автоматизации. Весь код можно просмотреть в нашем [репозитории GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

  Чтобы запустить этот метод, создайте словарь для хранения параметров Runbook *VMName* и *resourceGroupName* и их значений. Запустите модуль Runbook. Ниже приведен фрагмент кода C# для вызова метода, определенного выше.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Запуск модуля Runbook с помощью REST API и назначение параметров

Вы можете создать и запустить задание Runbook с помощью REST API службы автоматизации Azure, используя метод **размещения** со следующим URI запроса: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

В URI запроса замените следующие параметры:

* *SubscriptionId*: идентификатор подписки Azure.  
* *resourceGroupName*: имя группы ресурсов для учетной записи службы автоматизации.
* *automationAccountName*: имя учетной записи службы автоматизации, размещенной в указанной облачной службе.  
* *jobName*: идентификатор GUID для задания. Идентификаторы GUID в PowerShell можно создать с помощью `[GUID]::NewGuid().ToString()*`.

Чтобы передать параметры в задание Runbook, используйте текст запроса. Он принимает следующие сведения, представленные в формате JSON:

* Имя Runbook: обязательно. Имя модуля Runbook для запуска задания.  
* Параметры модуля Runbook: необязательно. Словарь списка параметров в формате (имя, значение), где name имеет тип String, а value может быть любым допустимым значением JSON.

Если вы хотите запустить Runbook **Get-азуревмтекстуал** , созданный ранее с параметрами *VMName* и *RESOURCEGROUPNAME* , используйте следующий формат JSON для текста запроса.

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

Если задание успешно создано, возвращается код состояния HTTP 201. Дополнительные сведения о заголовках ответов и тексте ответа см. в разделе [Создание задания Runbook с помощью REST API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Тестирование Runbook и назначение параметров

При [тестировании черновой версии модуля Runbook](automation-testing-runbook.md) с помощью параметра тест открывается страница **тест** . Эта страница используется для настройки значений для созданных вами параметров.

![Тестирование и назначение параметров](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Связывание Runbook с расписанием и назначение параметров

Вы можете [связать расписание](automation-schedules.md) с модулем Runbook, чтобы он запускался в определенное время. При создании расписания вы назначаете входные параметры, значения которых модуль runbook использует во время запуска по расписанию. Расписание нельзя сохранить, если не указаны значения для всех обязательных параметров.

![Создание расписания и назначение параметров](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Создание объекта Webhook для модуля Runbook и назначение параметров

Для модуля Runbook можно создать объект [Webhook](automation-webhooks.md) и настроить входные параметры. Webhook нельзя сохранить, если не указаны значения для всех обязательных параметров.

![Создание Webhook и назначение параметров](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

При выполнении модуля Runbook с помощью веб-перехватчика отправляется предопределенный входной параметр *[WebhookData](automation-webhooks.md#details-of-a-webhook)* вместе с определяемыми входными параметрами. 

![Параметр WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Передача объекта JSON в модуль Runbook

Иногда может быть полезным хранить данные, которые необходимо передать в модуль runbook, в файле JSON. Например, можно создать JSON-файл, содержащий все параметры, которые необходимо передать в модуль Runbook. Для этого необходимо преобразовать код JSON в строку, а затем преобразовать строку в объект PowerShell перед передачей его в модуль Runbook.

В этом разделе используется пример, в котором сценарий PowerShell вызывает [Start-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) для запуска модуля Runbook PowerShell, передавая содержимое JSON-файла в модуль Runbook. Модуль Runbook PowerShell запускает виртуальную машину Azure, получая параметры для виртуальной машины из объекта JSON.

### <a name="create-the-json-file"></a>Создание файла JSON

Введите следующий код в текстовый файл и сохраните его как `test.json` где-либо на локальном компьютере.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Создание модуля runbook

Создайте новый Runbook PowerShell с именем **Test-JSON** в службе автоматизации Azure. См. [первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).

Чтобы принять данные JSON, модуль runbook должен принять объект в качестве входного параметра. Затем модуль Runbook может использовать свойства, определенные в файле JSON.

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

1. Войдите в Azure, как показано ниже. После этого вам будет предложено ввести учетные данные Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Для модулей Runbook PowerShell **Add-азаккаунт** и **Add-AzureRMAccount** являются псевдонимами для **Connect-азаккаунт**. Обратите внимание, что эти псевдонимы недоступны для графических модулей Runbook. Графический модуль Runbook может использовать только сам **Connect-азаккаунт** .

2. Получите содержимое сохраненного JSON-файла и преобразуйте его в строку. `JsonPath` — это путь, по которому был сохранен файл JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Преобразование строкового содержимого `$json` в объект PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Создайте таблицу хэширования для параметров для **Start-азаутоматионрунбук**. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Обратите внимание, что вы устанавливаете для *параметров* значение объекта PowerShell, который содержит значения из JSON файла.
1. Запуск модуля runbook

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Сведения об изменении текстового модуля Runbook см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
* Сведения об изменении графического модуля Runbook см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
