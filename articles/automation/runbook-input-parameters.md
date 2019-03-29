---
title: Входные параметры Runbook
description: Возможность настройки входных параметров увеличивает гибкость модулей Runbook, позволяя передавать данные в уже запущенный модуль. В этой статье описаны различные сценарии использования входных параметров в модулях Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5f190d60a059108b9763f35e2ee8cf99ae77b694
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578158"
---
# <a name="runbook-input-parameters"></a>Входные параметры Runbook

Входные параметры Runbook увеличивают гибкость модулей Runbook, позволяя передавать данные в уже запущенный модуль. За счет настройки параметров можно оптимизировать действия Runbook согласно определенным сценариям и средам. В этой статье подробно описаны различные сценарии использования входных параметров в модулях runbook.

## <a name="configure-input-parameters"></a>Настройка входных параметров

Входные параметры настраиваются в модулях Runbook PowerShell, рабочих процессов PowerShell, Python и графических модулях Runbook. Runbook может использовать несколько параметров с разными типами данных или вообще не использовать параметры. Входные параметры могут быть обязательными или необязательными. Необязательные параметры могут иметь значения по умолчанию. Назначение значений входным параметрам для модуля Runbook выполняется при его запуске с помощью одного из доступных методов. Эти методы включают в себя запуск модуля Runbook с помощью портала Azure, веб-службы или PowerShell. Модуль можно также запустить как дочерний модуль Runbook, который вызывается с помощью встроенного вызова в другом Runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Настройка входных параметров в модулях Runbook PowerShell

Модули Runbook рабочих процессов PowerShell и PowerShell в службе автоматизации Azure поддерживают входные параметры, для определения которых используются следующие атрибуты.  

| **Свойство** | **Описание** |
|:--- |:--- |
| `Type` |Обязательный элемент. Предполагаемый тип данных значения параметра. Допускаются любые типы .NET. |
| `Name` |Обязательный элемент. Имя параметра. Оно должно быть уникальным для Runbook, может содержать только буквы, цифры или символы подчеркивания. Оно должно начинаться с буквы. |
| `Mandatory` |Необязательный элемент. Указывает, должно ли быть указано значение для параметра. Если установлено значение **\$true**, то при запуске модуля будет необходимо указывать значение. Если установлено значение **\$false**, указывать значение необязательно. |
| `Default value` |Необязательный элемент. Указывает значение, используемое для параметра, если при запуске модуля runbook не передано значение. Для любого параметра можно задать значение по умолчанию. Как результат, параметр автоматически станет необязательным, независимо от значения параметра "Обязательный". |

Наряду с перечисленными здесь входными параметрами Windows PowerShell поддерживает дополнительные атрибуты входных параметров, например проверки, псевдонимы наборы параметров. Но сейчас служба автоматизации Azure поддерживает только входные параметры, приведенные выше.

Для определения параметров модулей Runbook рабочих процессов PowerShell используется следующий общий формат, где несколько параметров разделяются запятыми.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> Если не указать атрибут **Mandatory** при определении параметров, то по умолчанию параметр будет необязательным. Кроме того, если в модулях runbook рабочего процесса PowerShell задать для параметра значение по умолчанию, то он будет считаться необязательным в PowerShell, независимо от значения атрибута **Mandatory**.

К примеру, настроим входные параметры Runbook рабочего процесса PowerShell, который выводит сведения об одной или всех виртуальных машинах в группе ресурсов. Как показано на снимке экрана ниже, у такого модуля Runbook будет два параметра — параметр имени виртуальной машины и имени группы ресурсов.

![Рабочий процесс PowerShell службы автоматизации](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

В этом определении параметры **\$VMName** и **\$resourceGroupName** являются простыми параметрами строки типа. Однако модули Runbook PowerShell и рабочих процессов PowerShell поддерживают все простые и сложные типы, например **object** или **PSCredential**, для входных параметров.

Чтобы передать значение при наличии в runbook входного параметра типа object, следует использовать хэш-таблицу PowerShell с парами (имя — значение). Например, если в Runbook есть такой параметр:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

вы можете передать для него следующее значение:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Если вы не передаете значение для необязательного параметра типа `[String]`, который имеет _значение по умолчанию_ `\$null`, тогда значение параметра будет _пустой строкой_, а **не** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Настройка входных параметров в графических модулях Runbook

Чтобы показать, как [настроить графический модуль Runbook](automation-first-runbook-graphical.md), мы создадим графический модуль Runbook, который выводит сведения об одной или всех виртуальных машинах в группе ресурсов. Настройка модуля состоит из двух основных операций, как описано ниже.

[**Аутентификация модулей Runbook в Azure с помощью учетной записи запуска от имени Azure**](automation-sec-configure-azure-runas-account.md).

Получение свойств виртуальной машины с помощью командлета [**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm).

Для вывода имен виртуальных машин можно использовать действие [**Write-Output**](/powershell/module/microsoft.powershell.utility/write-output). Действие **Get-AzureRmVm** принимает два параметра — **имя виртуальной машины** и **имя группы ресурсов**. Так как при каждом запуске модуля Runbook для этих параметров могут требоваться различные значения, вы можете добавить в модуль входные параметры. Их добавление предусматривает три шага:

1. В колонке **Модули Runbook** выберите графический модуль Runbook и щелкните [**Изменить**](automation-graphical-authoring-intro.md).
2. В редакторе модуля Runbook щелкните **Входные и выходные данные**, чтобы открыть колонку **Входные и выходные данные**.

   ![Графический модуль Runbook службы автоматизации](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. В колонке **Входные и выходные данные** отображается список входных параметров, определенных для модуля Runbook. В этой колонке можно добавить новый входной параметр или изменить конфигурацию существующего. Чтобы добавить новый параметр для модуля Runbook, щелкните **Добавить входные данные**. Откроется колонка **Входной параметр Runbook**. В ней можно настроить следующие параметры:

   | **Свойство** | **Описание** |
   |:--- |:--- |
   | `Name` |Обязательный элемент. Имя параметра. Оно должно быть уникальным для Runbook, может содержать только буквы, цифры или символы подчеркивания. Оно должно начинаться с буквы. |
   | `Description` |Необязательный элемент. Описание предназначения входного параметра. |
   | `Type` |Необязательный элемент. Предполагаемый тип данных значения параметра. Поддерживаются параметры **строкового**, **логического**, **объектного** типа, а также типа **Int32**, **Int64**, **DateTime** и типа **десятичного** числа. Если тип данных не выбран, то по умолчанию используется значение **строкового**типа. |
   | `Mandatory` |Необязательный элемент. Указывает, должно ли быть указано значение для параметра. Если для переключателя установлено положение **Да**, при запуске модуля будет необходимо указывать значение. Если для переключателя установлено положение **Нет**, ввод значения при запуске модуля не будет обязательным и можно установить значение по умолчанию. |
   | `Default Value` |Необязательный элемент. Указывает значение, используемое для параметра, если при запуске модуля runbook не передано значение. Для необязательного параметра можно задать значение по умолчанию. Чтобы установить значение по умолчанию, выберите **Пользовательское**. Это значение используется, если при запуске модуля не указано другое значение. Установите переключатель в положение **Нет** , если не нужно использовать значения по умолчанию. |

    ![Добавление новых входных данных](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Создайте два параметра со следующими свойствами, которые будут использоваться действием **Get-AzureRmVm**:

   * **Параметр 1:**
     * Имя — VMName.
     * Тип: строчный.
     * Необязательный параметр.
   * **Параметр 2:**
     * Имя — resourceGroupName.
     * Тип: строчный.
     * Необязательный параметр.
     * Настраиваемое значение по умолчанию.
     * Настраиваемое значение по умолчанию — \<имя группы ресурсов, содержащей виртуальные машины\>

5. После добавления параметров нажмите кнопку **ОК**. Теперь вы можете просмотреть их на странице **Входные и выходные данные**. Нажмите кнопку **ОК** еще раз, а затем — **Сохранить** и **Опубликовать**, чтобы сохранить параметры и опубликовать модуль Runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Настройка входных параметров в модулях Runbook Python

В отличие от модулей Runbook PowerShell, рабочих процессов PowerShell и графических модулей Runbook, модули Runbook Python не принимают именованные параметры.
Все входные параметры анализируются как массив значений аргументов.
Для доступа к массиву импортируйте модуль `sys` в свой сценарий Python, а затем используйте массив `sys.argv`.
Обратите внимание, что первый элемент массива (`sys.argv[0]`) является именем сценария, поэтому первый фактический входной параметр — `sys.argv[1]`.

Пример использования входных параметров в модуле Runbook Python см. в статье [Мой первый модуль Runbook Python в службе автоматизации Azure](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Назначение значений входным параметрам в модулях Runbook

Передавать значения для входных параметров в модули runbook можно в сценариях, описанных ниже.

### <a name="start-a-runbook-and-assign-parameters"></a>Запуск Runbook и назначение параметров

Существует много способов для запуска модуля Runbook — с помощью пользовательского интерфейса портала Azure, объекта webhook, командлетов PowerShell, REST API или пакета SDK. Ниже рассмотрены различные методы запуска модуля Runbook и назначения параметров.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Запуск опубликованного модуля Runbook на портале Azure и назначение параметров

При [запуске модуля runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) открывается колонка **Запуск Runbook**, где можно ввести значения для созданных параметров.

![Начало работы с порталом](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

В надписи под полем ввода можно увидеть атрибуты, которые были заданы для параметра. Атрибуты являются обязательными или необязательными и содержат тип и значение по умолчанию. Подсказка рядом с именем параметра позволяет просмотреть все основные сведения, необходимые для принятия решений об установке входных значений параметра. Эти сведения указывают, является параметр обязательным или нет. Они также содержат тип и значение по умолчанию (при его наличии) и другие полезные примечания.

> [!NOTE]
> Параметры строкового типа поддерживают **пустые** строковые значения.  Если ввести **[EmptyString]** в текстовое поле входного параметра, ему передастся пустая строка. Параметры строкового типа не поддерживают передачу значений **NULL** . Если строковому параметру не передать значение, PowerShell интерпретирует отсутствие значения как значение NULL.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Запуск опубликованного модуля Runbook с помощью командлетов PowerShell и назначение параметров

* **Командлеты Azure Resource Manager.** Модуль Runbook службы автоматизации, созданный в группе ресурсов, вы можете запустить с помощью командлета [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Пример.**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Командлеты классической модели развертывания Azure.** Модуль Runbook автоматизации, созданный в группе ресурсов по умолчанию, вы можете запустить с помощью командлета [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Пример.**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> При запуске модуля Runbook с помощью командлетов PowerShell создается параметр по умолчанию, **MicrosoftApplicationManagementStartedBy**, со значением **PowerShell**. Этот параметр вы можете просмотреть на странице **Сведения о задании**.  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Запуск Runbook с использованием пакета SDK и назначение параметров

* **Метод Azure Resource Manager.** Для запуска модуля Runbook можете использовать пакет SDK для языка программирования. Ниже приведен фрагмент кода C# для запуска модуля Runbook в вашей учетной записи службы автоматизации. Весь код можно просмотреть в нашем [репозитории GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Метод классической модели развертывания Azure.** Для запуска модуля Runbook можете использовать пакет SDK для языка программирования. Ниже приведен фрагмент кода C# для запуска модуля Runbook в вашей учетной записи службы автоматизации. Весь код можно просмотреть в нашем [репозитории GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

  Чтобы запустить этот метод, создайте словарь для хранения параметров Runbook, **VMName** и **resourceGroupName**, а также их значений. Запустите модуль Runbook. Ниже приведен фрагмент кода C# для вызова метода, определенного выше.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Запуск Runbook с использованием REST API и назначение параметров

Задание Runbook можно создать и запустить с помощью REST API службы автоматизации Azure. Для этого необходимо использовать метод **PUT** и следующий универсальный URI запроса: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


В URI запроса замените следующие параметры:

* **subscriptionId:** идентификатор подписки Azure;  
* **resourceGroupName:** имя группы ресурсов для учетной записи службы автоматизации;
* **automationAccountName:** имя учетной записи службы автоматизации в указанной облачной службе;  
* **jobName:** GUID для задания. Чтобы создать GUID в PowerShell, можно использовать команду **[GUID]::NewGuid().ToString()** .

Чтобы передать параметры в задание модуля Runbook, используйте текст запроса. Он принимает следующие два свойства, представленные в формате JSON:

* **Имя модуля Runbook.** Обязательный элемент. Имя модуля Runbook для запуска задания.  
* **Параметры модуля Runbook.** Необязательный элемент. Это словарь со списком параметров (именами и значениями) в формате, где имя должно быть строкового типа, а значение может быть любым допустимым значением JSON.

Если вы хотите запустить модуль Runbook **Get-AzureVMTextual**, созданный ранее с использованием параметров **VMName** и **resourceGroupName**, используйте следующий формат JSON в тексте запроса.

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

Если задание успешно создано, возвращается код состояния HTTP 201. Дополнительные сведения о заголовках и тексте ответа см. в статье о [создании задания runbook с помощью REST API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Тестирование Runbook и назначение параметров

Во время [тестирования черновой версии runbook](automation-testing-runbook.md) с использованием параметра тестирования откроется страница **Тест**, где можно настроить значения для созданных параметров.

![Тестирование и назначение параметров](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Связывание Runbook с расписанием и назначение параметров

Вы можете [связать расписание](automation-schedules.md) с модулем Runbook, чтобы он запускался в определенное время. При создании расписания вы назначаете входные параметры, значения которых модуль runbook использует во время запуска по расписанию. Расписание нельзя сохранить, если не указаны значения для всех обязательных параметров.

![Создание расписания и назначение параметров](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Создание объекта Webhook для модуля Runbook и назначение параметров

Для модуля Runbook можно создать объект [Webhook](automation-webhooks.md) и настроить входные параметры. Webhook нельзя сохранить, если не указаны значения для всех обязательных параметров.

![Создание Webhook и назначение параметров](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

При выполнении модуля Runbook с помощью объекта webhook происходит отправка предопределенного входного параметра **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** и определенных входных параметров. Щелкните параметр **WebhookData** , чтобы развернуть его и просмотреть дополнительные сведения.

![Параметр WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Передача объекта JSON в Runbook

Иногда может быть полезным хранить данные, которые необходимо передать в модуль runbook, в файле JSON.
Например, можно создать файл JSON, который содержит все параметры, которые необходимо передать в модуль runbook. Чтобы сделать это, необходимо преобразовать файл JSON в строку, а затем преобразовать строку в объект PowerShell перед передачей содержимого файла в модуль Runbook.

В этом примере вы видите сценарий PowerShell, который вызывает [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) для запуска модуля Runbook PowerShell, передавая содержимое файла JSON в Runbook.
Модуль runbook PowerShell запускает виртуальную машину Azure, получая параметры для нее из переданного файла JSON.

### <a name="create-the-json-file"></a>Создание файла JSON

Введите следующий текст в текстовый файл и сохраните его как `test.json` в любом месте на своем локальном компьютере.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Создание модуля runbook

Создайте новый модуль runbook PowerShell с именем Test-Json в службе автоматизации Azure.
Дополнительные сведения о создании модуля runbook в PowerShell см. в статье [Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).

Чтобы принять данные JSON, модуль runbook должен принять объект в качестве входного параметра.

Модуль runbook может использовать свойства, определенные в JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Сохраните и опубликуйте этот модуль runbook в учетной записи службы автоматизации.

### <a name="call-the-runbook-from-powershell"></a>Вызов модуля runbook из PowerShell

Теперь можно вызвать модуль runbook с локального компьютера с помощью Azure PowerShell.
Выполните следующие команды PowerShell:

1. Войдите в Azure.

   ```powershell
   Connect-AzureRmAccount
   ```

   Появится запрос ввести учетные данные Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** теперь является псевдонимом для **Connect-AzureRMAccount**. Если при поиске в библиотеке элементов вы не видите элемент **Connect-AzureRMAccount**, можно использовать **Add-AzureRmAccount** или обновить модули в своей учетной записи службы автоматизации.

1. Получите содержимое файла JSON и преобразуйте его в строку:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` — это путь, по которому был сохранен файл JSON.

1. Преобразуйте содержимое строки `$json` в объект PowerShell:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Создайте хэш-таблицу для параметров команды `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Обратите внимание, что вы задаете значение `Parameters` объекту PowerShell, который содержит значения из файла JSON.
1. Запуск модуля runbook

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Сведения об изменении текстового модуля Runbook см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
* Сведения об изменении графического модуля Runbook см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
