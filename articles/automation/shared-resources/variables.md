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
ms.openlocfilehash: e56a1c9a158974266b810d31a0e9bb898262761a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849434"
---
# <a name="variable-assets-in-azure-automation"></a>Средства переменных в службе автоматизации Azure

Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в учетной записи службы автоматизации. Ими можно управлять из портал Azure, PowerShell, в модуле Runbook или конфигурации DSC. Переменные автоматизации полезны в следующих случаях:

- совместное использование значения несколькими модулями Runbook или конфигурациями DSC;

- совместное использование значения несколькими заданиями из одного модуля Runbook или конфигурации DSC;

- Управление значением с портала или из командной строки PowerShell, используемой модулями Runbook или конфигурациями DSC, такими как набор общих элементов конфигурации, таких как конкретный список имен виртуальных машин, определенная группа ресурсов, доменное имя AD и многое другое.  

Так как переменные автоматизации сохраняются, они доступны даже в случае сбоя конфигурации модуля Runbook или DSC. Это поведение позволяет задать значение в одном модуле Runbook, который затем используется другим объектом, или использовать тот же модуль Runbook или конфигурацию DSC при следующем запуске.

При создании переменной можно указать, что она должна храниться в зашифрованном виде. Зашифрованные переменные безопасно хранятся в службе автоматизации Azure, и ее значение невозможно получить из командлета [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) , который поставляется в составе модуля Azure PowerShell. Единственный способ получить зашифрованное значение — использовать действие **Get-AutomationVariable** в модуле Runbook или конфигурации DSC. Если вы хотите изменить зашифрованную переменную на незашифрованную, можно удалить и повторно создать переменную как незашифрованную.

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в хранилище ключей, управляемом системой. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса. Этим процессом управляет служба автоматизации Azure.

## <a name="variable-types"></a>Типы переменных

При создании переменной с помощью портала Azure выберите тип данных из раскрывающегося списка, чтобы портал мог вывести соответствующий элемент управления для ввода значения переменной. Переменная не ограничена этим типом данных. Если необходимо указать значение другого типа, необходимо задать переменную с помощью Windows PowerShell. Если параметр **не определен**, значение переменной задается равным **$null**и необходимо задать значение с помощью командлета [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) или действия **Set-AutomationVariable** . Нельзя создать или изменить значение для типа сложной переменной на портале, но можно указать значение любого типа с помощью Windows PowerShell. Сложные типы возвращаются в виде [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Можно сохранить несколько значений в отдельной переменной, создав массив или хэш-таблицу и сохранив ее в переменную.

Ниже перечислены типы переменных, которые доступны в службе автоматизации.

* Строка
* Целое число
* Дата и время
* Логический
* Null

## <a name="azurerm-powershell-cmdlets"></a>Командлеты PowerShell для AzureRM

Для AzureRM командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell. Они поставляются как часть [модуля AzureRM. Automation](/powershell/azure/overview), который доступен для использования в модулях Runbook службы автоматизации и конфигурациях DSC.

| Командлеты | Описание |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Получает значение существующей переменной.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Создает новую переменную и устанавливает ее значение.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Удаляет существующую переменную.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Получает значение существующей переменной.|

## <a name="activities"></a>Действия

Действия, приведенные в следующей таблице, используются для доступа к переменным в Runbook и конфигурациях DSC. Разница между командлетами Get-AzureRmAutomationVariable и Get-AutomationVariable объясняется выше в начале этого документа.

| Действия | Описание |
|:---|:---|
|Get-AutomationVariable|Получает значение существующей переменной.|
|Set-AutomationVariable|Получает значение существующей переменной.|

> [!NOTE]
> Не следует использовать переменные в параметре –Name действия **Get-AutomationVariable** в модуле Runbook или конфигурации DSC, поскольку это усложняет определение зависимостей между модулями Runbook или конфигурацией DSC и переменными службы автоматизации во время разработки.

Функции, приведенные в следующей таблице, используются для доступа к переменным и их извлечения в модуле Runbook Python2.

|Функции Python2|Описание|
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

Приведенные ниже примеры команд демонстрируют задание и получение переменной в текстовом модуле Runbook. В этом примере предполагается, что были созданы переменные типа Integer с именем *NumberOfIterations* и *NumberOfRunnings* и переменная типа String с именем *SampleMessage* .

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

На следующем рисунке приведен пример добавления в переменную простого значения в графическом модуле Runbook. В этом примере **Get-AzureRmVM** извлекает одну виртуальную машину Azure, а имя компьютера сохраняет в существующую переменную службы автоматизации с типом строки. Не имеет значения, [является ли ссылка конвейером или последовательностью](../automation-graphical-authoring-intro.md#links-and-workflow), так как ожидается только одно значение в выходных данных.

![Задание простой переменной](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о соединении действий в графической разработке см. в разделе [Использование связей при создании графических модулей](../automation-graphical-authoring-intro.md#links-and-workflow).
- Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](../automation-first-runbook-graphical.md).
