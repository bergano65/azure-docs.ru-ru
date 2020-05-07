---
title: Управление переменными в службе автоматизации Azure
description: Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в службе автоматизации Azure.  В статье подробно рассматриваются переменные и работа с ними как в текстовых, так и в графических модулях.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bf7840daad02f679cad4c3b798d2add02c863a15
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651965"
---
# <a name="manage-variables-in-azure-automation"></a>Управление переменными в службе автоматизации Azure

Ресурсы переменных — это значения, доступные для всех модулей Runbook и конфигураций DSC в учетной записи службы автоматизации. Вы можете управлять ими из портал Azure, из PowerShell, в модуле Runbook или в конфигурации DSC.

Переменные автоматизации полезны в следующих случаях:

- Совместное использование значений между несколькими модулями Runbook или конфигурациями DSC.

- Совместное использование значения несколькими заданиями из одного модуля Runbook или конфигурации DSC.

- Управление значением, используемым модулями Runbook или конфигурациями DSC, с портала или из командной строки PowerShell. Примером является набор общих элементов конфигурации, таких как конкретный список имен виртуальных машин, определенная группа ресурсов, доменное имя AD и многое другое.  

Служба автоматизации Azure сохраняет переменные и делает их доступными даже в случае сбоя конфигурации модуля Runbook или DSC. Это поведение позволяет одному модулю Runbook или конфигурации DSC задать значение, которое затем используется другим модулем Runbook или тем же модулем Runbook или конфигурацией DSC при следующем запуске.

Служба автоматизации Azure хранит каждую зашифрованную переменную безопасно. При создании переменной можно указать ее шифрование и хранение с помощью службы автоматизации Azure в качестве безопасного ресурса. 

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Служба автоматизации Azure сохраняет ключ в управляемом системой Key Vault. Перед сохранением защищенного ресурса Автоматизация загружает ключ из Key Vault и затем использует его для шифрования ресурса. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Типы переменных

При создании переменной с портал Azure необходимо указать тип данных из раскрывающегося списка, чтобы портал мог отображать соответствующий элемент управления для ввода значения переменной. Ниже приведены типы переменных, доступные в службе автоматизации Azure.

* Строка
* Целое число
* Дата и время
* логический
* NULL

Переменная не ограничена указанным типом данных. Если необходимо указать значение другого типа, необходимо задать переменную с помощью Windows PowerShell. Если указать `Not defined`, то значение переменной будет равно null. Значение необходимо задать с помощью командлета [Set-азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) или внутреннего `Set-AutomationVariable` командлета.

Нельзя использовать портал Azure для создания или изменения значения для типа сложных переменных. Однако можно указать значение любого типа с помощью Windows PowerShell. Сложные типы извлекаются как [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Можно сохранить несколько значений в отдельной переменной, создав массив или хэш-таблицу и сохранив ее в переменную.

>[!NOTE]
>Длина переменных имени виртуальной машины не может превышать 80 символов. Длина переменных группы ресурсов не может превышать 90 символов. См. [правила именования и ограничения для ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-to-access-variables"></a>Командлеты PowerShell для доступа к переменным

Командлеты, приведенные в следующей таблице, создают переменные службы автоматизации и управляют ими с помощью PowerShell. Они поставляются как часть [модулей AZ](modules.md#az-modules).

| Командлет | Описание |
|:---|:---|
|[Get-Азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Получает значение существующей переменной. Если значение является простым типом, то этот же тип извлекается. Если это сложный тип, извлекается `PSCustomObject` тип. <br>**Примечание.**  Этот командлет нельзя использовать для получения значения зашифрованной переменной. Единственный способ сделать это — использовать внутренний `Get-AutomationVariable` командлет в модуле Runbook или конфигурации DSC. См. раздел [внутренние командлеты для доступа к переменным](#internal-cmdlets-to-access-variables). |
|[New-Азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Создает новую переменную и устанавливает ее значение.|
|[Remove-Азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Удаляет существующую переменную.|
|[Set-Азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Получает значение существующей переменной. |

## <a name="internal-cmdlets-to-access-variables"></a>Внутренние командлеты для доступа к переменным

Внутренние командлеты в следующей таблице используются для доступа к переменным в модулях Runbook и конфигурациях DSC. Эти командлеты поставляются с `Orchestrator.AssetManagement.Cmdlets`глобальным модулем. Дополнительные сведения см. в разделе [внутренние командлеты](modules.md#internal-cmdlets).

| Внутренний командлет | Описание |
|:---|:---|
|`Get-AutomationVariable`|Получает значение существующей переменной.|
|`Set-AutomationVariable`|Получает значение существующей переменной.|

> [!NOTE]
> Избегайте использования переменных в `Name` параметре `Get-AutomationVariable` в модуле Runbook или конфигурации DSC. Использование переменных может усложнить обнаружение зависимостей между модулями Runbook и переменными автоматизации во время разработки.

`Get-AutomationVariable`не работает в PowerShell, но только в Runbook или конфигурации DSC. Например, чтобы просмотреть значение зашифрованной переменной, можно создать модуль Runbook, чтобы получить переменную, а затем записать ее в выходной поток:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Функции Python 2 для доступа к переменным

Функции в следующей таблице используются для доступа к переменным в модуле Runbook Python 2.

|Функции Python 2|Описание|
|:---|:---|
|`automationassets.get_automation_variable`|Получает значение существующей переменной. |
|`automationassets.set_automation_variable`|Получает значение существующей переменной. |

> [!NOTE]
> Чтобы получить доступ к `automationassets` функциям активов, необходимо импортировать модуль в верхней части модуля Runbook Python.

## <a name="create-and-get-a-variable"></a>Создание и получение переменной

>[!NOTE]
>Если требуется удалить шифрование для переменной, необходимо удалить переменную и создать ее повторно как незашифрованную.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Создание и получение переменной с помощью портал Azure

1. В учетной записи службы автоматизации щелкните плитку **ресурсы** , затем колонку **ресурсы** и выберите **переменные**.
2. На плитке **Переменные** выберите **Добавить переменную**.
3. Заполните параметры в колонке **Новая переменная** и нажмите кнопку **создать** , чтобы сохранить новую переменную.

> [!NOTE]
> После сохранения зашифрованной переменной ее нельзя просмотреть на портале. Его можно обновить только.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Создание и получение переменной в Windows PowerShell

Модуль Runbook или Конфигурация DSC использует `New-AzAutomationVariable` командлет для создания новой переменной и задания ее начального значения. Если переменная зашифрована, вызов должен использовать `Encrypted` параметр. Скрипт может получить значение переменной с помощью `Get-AzAutomationVariable`. 

>[!NOTE]
>Скрипт PowerShell не может получить зашифрованное значение. Единственный способ сделать это — использовать внутренний `Get-AutomationVariable` командлет.

В следующем примере показано, как создать строковую переменную, а затем вернуть ее значение.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

В следующем примере показано, как создать переменную с сложным типом, а затем извлечь ее свойства. В этом случае используется объект виртуальной машины из [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="textual-runbook-examples"></a>Примеры текстовых модулей Runbook

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Получение и установка простого значения из переменной

В следующем примере показано, как задать и получить переменную в текстовом модуле Runbook. В этом примере предполагается создание целочисленных переменных `NumberOfIterations` с `NumberOfRunnings` именами и и строковая `SampleMessage`переменная с именем.

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

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Получение и задание переменной в модуле Runbook Python 2

В следующем примере показано, как получить переменную, задать переменную и выполнить обработку исключения для несуществующей переменной в модуле Runbook Python 2.

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

## <a name="graphical-runbook-examples"></a>Примеры графических модулей Runbook

В графическом модуле Runbook можно добавлять действия для внутренних командлетов `Get-AutomationVariable` или. `Set-AutomationVariable` Просто щелкните правой кнопкой мыши каждую переменную в области библиотека графического редактора и выберите нужное действие.

![Добавление переменной в полотно](../media/variables/runbook-variable-add-canvas.png)

На следующем рисунке показаны примеры действий по обновлению переменной простым значением графического модуля Runbook. В этом примере действие для `Get-AzVM` извлекает одну виртуальную машину Azure и сохраняет имя компьютера в существующей строковой переменной службы автоматизации. Не имеет значения, [является ли ссылка конвейером или последовательностью](../automation-graphical-authoring-intro.md#links-and-workflow) , так как в коде требуется только один объект в выходных данных.

![Задание простой переменной](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о командлетах, используемых для доступа к переменным, см. в статье [Управление модулями в службе автоматизации Azure](modules.md).
* Общие сведения о модулях Runbook см. [в статье выполнение Runbook в службе автоматизации Azure](../automation-runbook-execution.md).
* Дополнительные сведения о конфигурациях DSC см. в разделе [Общие сведения о конфигурации состояния](../automation-dsc-overview.md).
