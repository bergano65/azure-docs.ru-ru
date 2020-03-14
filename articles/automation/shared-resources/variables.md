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
ms.openlocfilehash: 598be26024c22ba81c3f33510423605abc854b13
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216833"
---
# <a name="variable-assets-in-azure-automation"></a>Средства переменных в службе автоматизации Azure

Ресурсы переменных — это значения, доступные для всех модулей Runbook и конфигураций DSC в учетной записи службы автоматизации. Вы можете управлять ими из портал Azure, из PowerShell, в модуле Runbook или в конфигурации DSC.

Переменные автоматизации полезны в следующих случаях:

- Совместное использование значений между несколькими модулями Runbook или конфигурациями DSC.

- Совместное использование значения несколькими заданиями из одного модуля Runbook или конфигурации DSC.

- Управление значением, используемым модулями Runbook или конфигурациями DSC, с портала или из командной строки PowerShell. Примером является набор общих элементов конфигурации, таких как конкретный список имен виртуальных машин, определенная группа ресурсов, доменное имя AD и многое другое.  

Так как переменные автоматизации сохраняются, они доступны даже в случае сбоя конфигурации модуля Runbook или DSC. Это поведение позволяет одному модулю Runbook или конфигурации DSC задать значение, которое затем используется другим модулем Runbook или тем же модулем Runbook или конфигурацией DSC при следующем запуске.

При создании переменной можно указать ее шифрование и хранение с помощью службы автоматизации Azure в качестве безопасного ресурса. К другим защищенным активам относятся учетные данные, сертификаты и подключения. Служба автоматизации Azure шифрует эти ресурсы и сохраняет их с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Ключ хранится в управляемом системой Key Vault. Перед сохранением защищенного ресурса служба автоматизации Azure загружает ключ из Key Vault и использует его для шифрования ресурса. 

Служба автоматизации Azure хранит каждую зашифрованную переменную безопасно. Его значение невозможно получить с помощью командлета [Get-азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) , который поставляется в составе модуля Azure PowerShell. Единственным способом получения зашифрованного значения является использование действия `Get-AutomationVariable` в модуле Runbook или конфигурации DSC.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Типы переменных

При создании переменной с портал Azure необходимо указать тип данных из раскрывающегося списка, чтобы портал мог отображать соответствующий элемент управления для ввода значения переменной. Ниже приведены типы переменных, доступные в службе автоматизации Azure.

* String
* Целое число
* Дата и время
* Логическое
* NULL

Переменная не ограничена назначенным типом данных. Если необходимо указать значение другого типа, необходимо задать переменную с помощью Windows PowerShell. Если указать **не определено**, значение переменной будет равно null, а значение необходимо задать с помощью командлета [Set-азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) или действия `Set-AutomationVariable`.

Нельзя использовать портал для создания или изменения значения для типа сложных переменных. Однако можно указать значение любого типа с помощью Windows PowerShell. Сложные типы извлекаются как [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Можно сохранить несколько значений в одну переменную, создав массив или хэш-таблицу и сохранив ее в переменной.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Командлеты PowerShell для создания ресурсов переменных и управления ими

Для модуля AZ командлеты, приведенные в следующей таблице, используются для создания ресурсов переменных службы автоматизации и управления ими с помощью Windows PowerShell. Они поставляются в составе [модуля AZ. Automation](/powershell/azure/overview), который доступен для использования в модулях Runbook службы автоматизации и конфигурациях DSC.

| Командлет | Description |
|:---|:---|
|[Get-Азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Получает значение существующей переменной.|
|[New-Азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Создает новую переменную и устанавливает ее значение.|
|[Remove-Азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Удаляет существующую переменную.|
|[Set-Азаутоматионвариабле](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Получает значение существующей переменной.|

## <a name="activities-to-access-variables"></a>Действия для доступа к переменным

Действия, приведенные в следующей таблице, используются для доступа к переменным в модулях Runbook и конфигурациях DSC. Разница между `Get-AzAutomationVariable` и `Get-AutomationVariable` объясняется для зашифрованных переменных в начале этой статьи.

| Действие | Description |
|:---|:---|
|`Get-AutomationVariable`|Получает значение существующей переменной.|
|`Set-AutomationVariable`|Получает значение существующей переменной.|

> [!NOTE]
> Избегайте использования переменных в параметре `Name` `Get-AutomationVariable` в модуле Runbook или конфигурации DSC. Использование этого параметра может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и переменными автоматизации во время разработки.

Функции, приведенные в следующей таблице, используются для доступа к переменным и их извлечения в модуле Runbook Python2.

|Функции Python2|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Получает значение существующей переменной. |
|`automationassets.set_automation_variable`|Получает значение существующей переменной. |

> [!NOTE]
> Чтобы получить доступ к функциям активов, необходимо импортировать модуль `automationassets` в верхней части модуля Runbook Python.

## <a name="creating-a-new-automation-variable"></a>Создание новой переменной автоматизации

### <a name="create-a-new-variable-using-the-azure-portal"></a>Создайте новую переменную с помощью портал Azure

1. В учетной записи службы автоматизации щелкните плитку **ресурсы** , затем колонку **ресурсы** и выберите **переменные**.
2. На плитке **Переменные** выберите **Добавить переменную**.
3. Заполните параметры в колонке **Новая переменная** и нажмите кнопку **создать** , чтобы сохранить новую переменную.

>[!NOTE]
>Если требуется удалить шифрование для переменной, необходимо удалить переменную и создать ее повторно как незашифрованную.

### <a name="create-a-new-variable-with-windows-powershell"></a>Создание новой переменной с помощью Windows PowerShell

Скрипт использует командлет `New-AzAutomationVariable`, чтобы создать новую переменную и задать ее начальное значение. Затем он может получить значение с помощью `Get-AzAutomationVariable`. Если значение является простым типом, то этот же тип извлекается. Если это сложный тип, то извлекается тип `PSCustomObject`.

В следующем примере показано, как создать переменную типа String и затем вернуть ее значение.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

В следующем примере показано, как создать переменную с составным типом и затем извлечь ее свойства. В этом случае используется объект виртуальной машины из [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Использование переменной в модуле Runbook или конфигурации DSC

Используйте действие `Set-AutomationVariable`, чтобы задать значение переменной службы автоматизации в модуле Runbook PowerShell или конфигурации DSC, а также `Get-AutomationVariable`, чтобы получить его. Не следует использовать командлеты `Set-AzAutomationVariable` и `Get-AzAutomationVariable` или их эквиваленты модуля AzureRM в модуле Runbook или конфигурации DSC, так как они менее эффективны, чем действия рабочего процесса. 

Обратите внимание, что нельзя получить значение безопасной переменной с помощью `Get-AzAutomationVariable` или его эквивалента модуля AzureRM. 

Единственный способ создать новую переменную из модуля Runbook или конфигурации DSC — использовать командлет `New-AzAutomationVariable`.

### <a name="textual-runbook-samples"></a>Текстовые примеры модуля Runbook

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Установка и получение простого значения из переменной

Приведенные ниже примеры команд демонстрируют задание и получение переменной в текстовом модуле Runbook. В этом примере предполагается создание целочисленных переменных с именами `NumberOfIterations` и `NumberOfRunnings` и строковая переменная с именем `SampleMessage`.

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

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>Установка и получение переменной в модуле Runbook python2

В следующем примере показано, как использовать переменную, задать переменную и выполнить обработку исключения для несуществующей переменной в модуле Runbook python2.

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

В графическом модуле Runbook можно добавить действие `Get-AutomationVariable` или `Set-AutomationVariable`. Просто щелкните правой кнопкой мыши переменную в области библиотека графического редактора и выберите нужное действие.

![Добавление переменной в полотно](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Задание значений в переменной

На следующем рисунке приведен пример добавления в переменную простого значения в графическом модуле Runbook. В этом примере `Get-AzVM` извлекает одну виртуальную машину Azure и сохраняет имя компьютера в существующей строковой переменной службы автоматизации. Не имеет значения, [является ли ссылка конвейером или последовательностью](../automation-graphical-authoring-intro.md#links-and-workflow) , так как в коде требуется только один объект в выходных данных.

![Задание простой переменной](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о подключении действий в графической разработке см. [в разделе ссылки в графической разработке](../automation-graphical-authoring-intro.md#links-and-workflow).
- Чтобы приступить к работе с графическими модулями Runbook, см. раздел [Мой первый графический модуль Runbook](../automation-first-runbook-graphical.md).
