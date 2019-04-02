---
title: Средства переменных в службе автоматизации Azure
description: Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в службе автоматизации Azure.  В статье подробно рассматриваются переменные и работа с ними как в текстовых, так и в графических модулях.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fc26c0357dcb071c4c75e8684fe47144a04177e4
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807042"
---
# <a name="variable-assets-in-azure-automation"></a>Средства переменных в службе автоматизации Azure

Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в учетной записи службы автоматизации. Ими можно управлять на портале Azure, PowerShell, в модуле runbook или конфигурации DSC. Переменные автоматизации полезны в следующих случаях:

- совместное использование значения несколькими модулями Runbook или конфигурациями DSC;

- совместное использование значения несколькими заданиями из одного модуля Runbook или конфигурации DSC;

- Управление значением из портала или из командной строки PowerShell, который используется модулями Runbook или конфигурациями DSC, таких как набор типичных элементов конфигурации как список имен виртуальных Машин, для определенной группы ресурсов, доменное имя AD и многое другое.  

Так как переменные автоматизации сохраняются, они доступны даже в случае сбоя модуля runbook или конфигурации DSC. Благодаря этому значение возможность задать один модуль, который затем используется в другом, или одного модуля runbook или конфигурации DSC при следующем запуске.

При создании переменной можно указать, что она должна храниться в зашифрованном виде. Зашифрованные переменные которые безопасно хранятся в службе автоматизации Azure и ее значение не может быть получено из [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) командлет, который поставляется в составе модуля Azure PowerShell. Единственный способ получить зашифрованное значение — использовать действие **Get-AutomationVariable** в модуле Runbook или конфигурации DSC.

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в хранилище ключей, управляемом системой. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса. Этим процессом управляет служба автоматизации Azure.

## <a name="variable-types"></a>Типы переменных

При создании переменной с помощью портала Azure выберите тип данных из раскрывающегося списка, чтобы портал мог вывести соответствующий элемент управления для ввода значения переменной. Переменная не ограничивается этим типом данных. Необходимо присвоить переменной, с помощью Windows PowerShell, если вы хотите указать значение другого типа. При указании **не определен**, а затем присваивает значение переменной **$null**, и необходимо указать значение с [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) командлета или **Set-AutomationVariable** действия. Не удается создать или изменить значение для переменной сложного типа на портале, но можно указать значение любого типа, с помощью Windows PowerShell. Сложные типы возвращаются в виде [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Можно сохранить несколько значений в отдельной переменной, создав массив или хэш-таблицу и сохранив ее в переменную.

Ниже перечислены типы переменных, которые доступны в службе автоматизации.

* Строка
* Целое число 
* DateTime
* Логическое
* Null

## <a name="azurerm-powershell-cmdlets"></a>Командлеты PowerShell для AzureRM

Для AzureRM командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell. Они поставляются как часть [модуля AzureRM.Automation](/powershell/azure/overview), которая доступна для использования в модулях Runbook службы автоматизации и конфигурации DSC.

| Командлеты | ОПИСАНИЕ |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Получает значение существующей переменной.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Создает новую переменную и устанавливает ее значение.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Удаляет существующую переменную.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Получает значение существующей переменной.|

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к учетным данным в модуле Runbook и конфигурациях DSC.

| Действия | ОПИСАНИЕ |
|:---|:---|
|Get-AutomationVariable|Получает значение существующей переменной.|
|Set-AutomationVariable|Получает значение существующей переменной.|

> [!NOTE]
> Не следует использовать переменные в параметре –Name действия **Get-AutomationVariable** в модуле Runbook или конфигурации DSC, поскольку это усложняет определение зависимостей между модулями Runbook или конфигурацией DSC и переменными службы автоматизации во время разработки.

Функции, приведенные в следующей таблице, используются для доступа к переменным и их извлечения в модуле Runbook Python2.

|Функции Python2|ОПИСАНИЕ|
|:---|:---|
|automationassets.get_automation_variable|Получает значение существующей переменной. |
|automationassets.set_automation_variable|Получает значение существующей переменной. |

> [!NOTE]
> Для доступа к функциям ресурсов, необходимо импортировать модуль automationassets в верхнюю часть модуля Runbook Python.

## <a name="creating-a-new-automation-variable"></a>Создание новой переменной автоматизации

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Создание новой переменной на портале Azure

1. В учетной записи службы автоматизации щелкните плитку **Ресурсы**, а затем в колонке **Ресурсы** выберите **Переменные**.
2. На плитке **Переменные** выберите **Добавить переменную**.
3. Укажите параметры в колонке **Новая переменная** и нажмите кнопку **Создать**, чтобы сохранить новую переменную.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Создание переменной с помощью Windows PowerShell

Командлет [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) создает переменную и задает ее исходное значение. Значение можно получить с помощью [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Если значение простого типа, возвращается тот же тип. Если это сложный тип, возвращается **PSCustomObject** .

Приведенные ниже примеры команд демонстрируют создание переменной строкового типа и возврат ее значения.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Приведенные ниже примеры команд демонстрируют создание переменной сложного типа и возврат ее свойств. В данном случае используется объект виртуальной машины из **Get-AzureRmVm**.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Использование переменной в модуле Runbook или конфигурации DSC

Используйте действие **Set-AutomationVariable**, чтобы задать значение переменной службы автоматизации в модуле Runbook PowerShell или конфигурации DSC, и действие **Get-AutomationVariable**, чтобы получить его. Не следует использовать командлеты **Set-AzureRMAutomationVariable** и **Get-AzureRMAutomationVariable** в runbook или конфигурации DSC, так как они менее эффективны, чем действия рабочего процесса. Также с помощью **Get-AzureRMAutomationVariable** невозможно получить значение безопасных переменных. Единственный способ создать переменную из runbook или конфигурации DSC — использовать командлет [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable).

### <a name="textual-runbook-samples"></a>Текстовые примеры модуля Runbook

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Установка и получение простого значения из переменной

Приведенные ниже примеры команд демонстрируют задание и получение переменной в текстовом модуле Runbook. В этом примере предполагается, что переменные целого типа с именем *NumberOfIterations* и *NumberOfRunnings* и переменной строкового типа с именем *SampleMessage* имеют была создана.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Задание и получение сложного объекта в переменной

В следующем примере кода показано, как добавить в переменную сложное значение в текстовом модуле Runbook. В этом примере виртуальная машина Azure вызывается с помощью команды **Get-AzureVM** и сохраняется в существующую переменную автоматизации.  Как указано в разделе [Типы переменных](#variable-types), значение сохраняется в виде PSCustomObject.

```powershell
$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
Set-AutomationVariable -Name "MyComplexVariable" -Value $vm
```

В следующем коде значение возвращается из переменной и используется для запуска виртуальной машины.

```powershell
$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
if ($vmObject.PowerState -eq 'Stopped') {
    Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
}
```

#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Задание и получение коллекции в переменной

В следующем примере кода показано, как использовать переменную с набором сложных значений в текстовом модуле Runbook. В этом примере имена нескольких виртуальных машин Azure возвращаются с помощью команды **Get-AzureVM** и затем сохраняются в существующую переменную автоматизации. Как указано в разделе [Типы переменных](#variable-types), значение сохраняется в виде коллекции PSCustomObject.

```powershell
$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}
Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms
```

В следующем коде коллекция значений возвращается из переменной и используется для запуска виртуальной машины.

```powershell
$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
ForEach ($vmValue in $vmValues)
{
    if ($vmValue.PowerState -eq 'Stopped') {
        Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
    }
}
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Задание и получение переменной в Python2

В следующем примере кода показано, как использовать и задать переменную, а также обрабатывать исключение для несуществующей переменной в модуле Runbook Python2.

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
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Графические примеры для модуля Runbook

Чтобы добавить команды **Get-AutomationVariable** и **Set-AutomationVariable** в графическом модуле Runbook, щелкните правой кнопкой мыши переменную в области библиотеки графического редактора и выберите необходимый процесс.

![Добавление переменной в полотно](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Задание значений в переменной

На следующем рисунке приведен пример добавления в переменную простого значения в графическом модуле Runbook. В этом образце **Get-AzureRmVM** извлекает, одной виртуальной машины Azure и имя компьютера сохраняет существующую переменную автоматизации строкового типа. Не имеет значения, [является ли ссылка конвейером или последовательностью](../automation-graphical-authoring-intro.md#links-and-workflow), так как ожидается только одно значение в выходных данных.

![Задание простой переменной](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о соединении действий в графической разработке см. в разделе [Использование связей при создании графических модулей](../automation-graphical-authoring-intro.md#links-and-workflow).
- Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](../automation-first-runbook-graphical.md).
