---
title: Средства переменных в службе автоматизации Azure
description: Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в службе автоматизации Azure.  В статье подробно рассматриваются переменные и работа с ними как в текстовых, так и в графических модулях.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4a4a92feb3e1b400c0f40076148f7898c4bdef1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365836"
---
# <a name="variable-assets-in-azure-automation"></a>Средства переменных в службе автоматизации Azure

Переменные активы – это значения, доступные для всех runbooks и конфигураций DSC в вашей учетной записи Automation. Управлять ими можно с портала Azure, от PowerShell, в рамках runbook или в конфигурации DSC.

Переменные автоматизации полезны в следующих случаях:

- Совместное использование значения между несколькими конфигурациями runbooks или DSC.

- Совместное использование значения между несколькими заданиями из одной и той же конфигурации runbook или DSC.

- Управление значением, используемым конфигурациями runbooks или DSC с портала или из командной строки PowerShell. Примером может служить набор общих элементов конфигурации, таких как определенный список имен VM, определенная группа ресурсов, доменное имя AD и многое другое.  

Azure Automation сохраняет переменные и делает их доступными, даже если конфигурация runbook или DSC выходит из строя. Такое поведение позволяет одной конфигурации runbook или DSC установить значение, которое затем используется другим runbook, или той же конфигурацией runbook или DSC при следующем запуске.

Azure Automation надежно хранит каждую зашифрованную переменную. При создании переменной можно указать ее шифрование и хранение в Azure Automation как безопасный актив. Другие безопасные ресурсы включают учетные данные, сертификаты и соединения. Azure Automation шифрует эти ресурсы и хранит их с помощью уникального ключа, который генерируется для каждой учетной записи Автоматизации. Ключ хранится в управляемом системой Key Vault. Перед хранением защищенного актива Azure Automation загружает ключ из Key Vault, а затем использует его для шифрования ресурса. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="variable-types"></a>Типы переменных

При создании переменной с порталом Azure необходимо указать тип данных из списка выпадающих данных, чтобы портал мог отображать соответствующий элемент управления для ввода переменного значения. Ниже приведены типы переменных, доступные в Azure Automation:

* Строка
* Целое число
* Дата и время
* Логическое
* NULL

Переменная не ограничивается обозначенным типом данных. Необходимо установить переменную с помощью Windows PowerShell, если вы хотите указать значение другого типа. Если вы `Not defined`указываете, значение переменной установлено на null, и вы должны установить значение `Set-AutomationVariable` с [Set-AzAutomationПеременный](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) cmdlet или деятельности.

Нельзя использовать портал Azure для создания или изменения значения для сложного типа переменных. Тем не менее, вы можете предоставить значение любого типа с помощью Windows PowerShell. Сложные типы извлекаются как [PSCustomObject.](/dotnet/api/system.management.automation.pscustomobject)

Можно сохранить несколько значений в отдельной переменной, создав массив или хэш-таблицу и сохранив ее в переменную.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Смдлеты PowerShell, которые создают и управляют переменными активами

Для модуля Az cmdlets в следующей таблице используются для создания и управления переменными активами автоматизации с помощью Windows PowerShell. Они погрузки в рамках [модуля Az.Automation](/powershell/azure/overview), который доступен для использования в автоматизации runbooks и DSC конфигураций.

| Командлет | Описание |
|:---|:---|
|[Get-AzAutomationПеременный](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Получает значение существующей переменной. Вы не можете использовать этот cmdlet для получения значения зашифрованной переменной. Сделать это можно только с `Get-AutomationVariable` помощью действия в конфигурации runbook или DSC. |
|[Нью-AzAutomationПеременный](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Создает новую переменную и устанавливает ее значение.|
|[Удалить-AzAutomationПеременный](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Удаляет существующую переменную.|
|[Set-AzAutomationПеременный](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Получает значение существующей переменной. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Мероприятия по доступу к переменным в конфигурациях runbooks и DSC

Действия в следующей таблице используются для доступа к переменным в конфигурациях runbooks и DSC. Cmdlets для этих мероприятий поставляются с глобальным модулем. `Orchestrator.AssetManagement.Cmdlets`

| Действие | Описание |
|:---|:---|
|`Get-AutomationVariable`|Получает значение существующей переменной.|
|`Set-AutomationVariable`|Получает значение существующей переменной.|

> [!NOTE]
> Избегайте использования переменных в параметре `Name` в конфигурации `Get-AutomationVariable` runbook или DSC. Использование этого параметра может усложнить обнаружение зависимостей между рунами или конфигурациями DSC и переменными автоматизации во время проектирования.

Обратите `Get-AutomationVariable` внимание, что не работает в PowerShell, но только в runbook или DSC конфигурации. Например, чтобы увидеть значение зашифрованной переменной, можно создать запуск, чтобы получить переменную, а затем написать ее в выходной поток:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Функции для доступа к переменным в runbooks Python 2

Функции в следующей таблице используются для доступа к переменным в ступеньке Python 2.

|Функции Python 2|Описание|
|:---|:---|
|`automationassets.get_automation_variable`|Получает значение существующей переменной. |
|`automationassets.set_automation_variable`|Получает значение существующей переменной. |

> [!NOTE]
> Для доступа `automationassets` к функциям актива необходимо импортировать модуль в верхней части вектора Python.

## <a name="working-with-automation-variables"></a>Работа с переменными автоматизации

>[!NOTE]
>Если вы хотите удалить шифрование для переменной, необходимо удалить переменную и воссоздать ее как незашифрованную.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Создание новой переменной с помощью портала Azure

1. С вашей учетной записи автоматизации щелкните плитку **Активов,** затем лезвие **активов** и выберите **переменные.**
2. На плитке **Переменные** выберите **Добавить переменную**.
3. Заполните параметры на **новом лезвии переменной,** а затем нажмите **Создать,** чтобы сохранить новую переменную.

> [!NOTE]
> После того как вы сохранили зашифрованную переменную, ее нельзя просмотреть на портале. Он может быть только обновлен.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Создание и использование переменной в Windows PowerShell

Скрипт PowerShell `New-AzAutomationVariable` использует cmdlet, или его эквивалент модуля AzureRM, для создания новой переменной и установки ее начального значения. Если переменная зашифрована, вызов должен `Encrypted` использовать параметр.

Скрипт получает значение переменной с помощью [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Если значение является простым типом, cmdlet получает тот же тип. Если это сложный тип, `PSCustomObject` тип извлекается.

>[!NOTE]
>Скрипт PowerShell не может получить зашифрованное значение. Единственный способ сделать это `Get-AutomationVariable` — использовать действие в конфигурации runbook или DSC.

В следующем примере показано, как создать переменную строки типа, а затем вернуть ее значение.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

В следующем примере показано, как создать переменную со сложным типом, а затем получить ее свойства. В этом случае используется виртуальный объект машины от [Get-AzVM.](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Создание и использование переменной в конфигурации runbook или DSC

Единственный способ создать новую переменную из конфигурации runbook `New-AzAutomationVariable` или DSC — использовать cmdlet или эквивалент модуля AzureRM. Скрипт использует этот cmdlet для установки начального значения переменной. Затем скрипт может получить `Get-AzAutomationVariable`значение с помощью. Если значение является простым типом, тот же тип извлекается. Если это сложный тип, `PSCustomObject` то тип извлекается.

>[!NOTE]
>Единственный способ получить зашифрованное значение — `Get-AutomationVariable` использовать действие в конфигурации Runbook или DSC. 

### <a name="textual-runbook-samples"></a>Текстовые примеры модуля Runbook

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Установите и извлеките простое значение из переменной

Приведенные ниже примеры команд демонстрируют задание и получение переменной в текстовом модуле Runbook. Этот пример предполагает создание инкеме переменных имени `NumberOfIterations` и `NumberOfRunnings` `SampleMessage`строки переменной имени.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Установите и извлеките переменную в книге Python 2

В следующем примере показано, как использовать переменную, установить переменную и обрабатывать исключение для несуществующей переменной в runbook Python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Графические примеры для модуля Runbook

В графическом runbook можно `Get-AutomationVariable` добавить `Set-AutomationVariable` или действия. Просто нажмите на кнопку переменной в панели библиотеки графического редактора и выберите действия, которые вы хотите.

![Добавление переменной в полотно](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Установка значений в переменной

На следующем рисунке приведен пример добавления в переменную простого значения в графическом модуле Runbook. В этом `Get-AzVM` примере извлекается одна виртуальная машина Azure и сохраняет имя компьютера для существующей переменной строки Автоматизации. Не имеет значения, является ли [соединение конвейером или последовательностью,](../automation-graphical-authoring-intro.md#links-and-workflow) так как код ожидает только один объект на выходе.

![Задание простой переменной](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать больше о подключении деятельности в графическом авторизации, см [Ссылки в графическом авторизации](../automation-graphical-authoring-intro.md#links-and-workflow).
- Чтобы начать работу с графических runbooks, [см.](../automation-first-runbook-graphical.md)
