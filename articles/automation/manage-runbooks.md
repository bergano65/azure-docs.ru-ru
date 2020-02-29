---
title: Управление модулями runbook в службе автоматизации Azure
description: В этой статье описывается управление модулями Runbook в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 4531e5d483508ca99722182f97c6a7aa0e0b68f3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191118"
---
# <a name="manage-runbooks-in-azure-automation"></a>Управление модулями runbook в службе автоматизации Azure

Модуль Runbook можно добавить в службу автоматизации Azure, [создав новый](#create-a-runbook) или [импортировав существующий](#import-a-runbook) файл из файла или [коллекции Runbook](automation-runbook-gallery.md). В этой статье рассказывается, как создавать и импортировать модули Runbook из файла. Вы можете получить все сведения о доступе к модулям Runbook сообщества и модули в модулях [Runbook и коллекциях модулей для службы автоматизации Azure](automation-runbook-gallery.md).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Создание модуля runbook

Создать новый модуль в службе автоматизации Azure можно с помощью одного из порталов Azure или Windows PowerShell. Вновь созданный модуль Runbook можно изменить, следуя инструкциям в статьях [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md) и [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Создание модуля Runbook на портале Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В центре выберите **модули Runbook** в разделе **Автоматизация процессов** , чтобы открыть список модулей Runbook.
3. Щелкните **создать Runbook**.
4. Введите имя модуля Runbook и выберите его [тип](automation-runbook-types.md). Имя Runbook должно начинаться с буквы и может содержать буквы, цифры, символы подчеркивания и тире.
5. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

### <a name="create-a-runbook-with-powershell"></a>Создание модуля Runbook с помощью PowerShell

Для создания пустого [модуля Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)можно использовать командлет [New-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) . Используйте параметр *типа* , чтобы указать один из типов Runbook, определенных для командлета **New-азаутоматионрунбук**.

В следующем примере показано, как создать новый пустой Runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Импорт модуля Runbook

Вы можете создать новый модуль Runbook в службе автоматизации Azure, импортировав сценарий PowerShell или рабочий процесс PowerShell (**PS1**), экспортированный графический Runbook ( **. graphrunbook**) или сценарий Python 2 ( **. копировать**).  При этом необходимо указать [тип модуля Runbook](automation-runbook-types.md), который создается во время импорта, с учетом следующих рекомендаций.

* Файл **PS1** , который не содержит рабочий процесс, можно импортировать в [модуль Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) или [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Если импортировать его в модуль Runbook рабочего процесса PowerShell, он преобразуется в рабочий процесс. В этом случае комментарии включаются в модуль Runbook для описания внесенных изменений.

* Файл **PS1** , содержащий рабочий процесс PowerShell, можно импортировать только в [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Если файл содержит несколько рабочих процессов PowerShell, импорт завершится ошибкой. Каждый рабочий процесс необходимо сохранить в отдельный файл и импортировать отдельно.

* Файл **PS1** , содержащий рабочий процесс PowerShell, не должен быть импортирован в [модуль Runbook PowerShell](automation-runbook-types.md#powershell-runbooks), так как обработчик сценариев PowerShell не может его распознать.

* Файл **. graphrunbook** можно импортировать только в новый [графический модуль Runbook](automation-runbook-types.md#graphical-runbooks). Обратите внимание, что можно создать только графический Runbook из **graphrunbook** -файла.

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Импорт модуля Runbook из файла с портал Azure

Для импорта файла сценария в службу автоматизации Azure можно использовать описанную ниже процедуру.

> [!NOTE]
> Вы можете импортировать **PS1** -файл в Runbook рабочего процесса PowerShell с помощью портала.

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В центре выберите **модули Runbook** в разделе **Автоматизация процессов** , чтобы открыть список модулей Runbook.
3. Щелкните **Импорт модуля Runbook**.
4. Щелкните **файл Runbook** и выберите файл для импорта.
5. Если поле **имя** включено, можно изменить имя модуля Runbook. Имя должно начинаться с буквы и может содержать буквы, цифры, символы подчеркивания и тире.
6. [Тип Runbook](automation-runbook-types.md) выбирается автоматически, но его можно изменить, учитывая применимые ограничения.
7. Нажмите кнопку **Создать**. Новый модуль Runbook появится в списке модулей Runbook для учетной записи службы автоматизации.
8. Перед запуском модуля его необходимо [опубликовать](#publish-a-runbook) .

> [!NOTE]
> После импорта графического модуля Runbook или графического модуля Runbook рабочего процесса PowerShell его можно преобразовать в другой тип. Однако вы не можете преобразовать один из этих графических модулей Runbook в текстовый модуль Runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Импорт модуля Runbook из файла скрипта с помощью Windows PowerShell

Используйте командлет [Import-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) , чтобы импортировать файл скрипта в качестве черновика Runbook рабочего процесса PowerShell. Если модуль Runbook уже существует, импорт завершится ошибкой, если не использовать параметр *Force* с командлетом.

В следующем примере показано, как импортировать файл скрипта в модуль Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Тестирование runbook

При тестировании модуля Runbook запускается его [черновая версия](#publish-a-runbook) и завершаются все действия, которые он выполняет. Журнал заданий не создается, но [выходные данные](automation-runbook-output-and-messages.md#output-stream) и потоки [предупреждений и ошибок](automation-runbook-output-and-messages.md#message-streams) отображаются в области вывода теста. Сообщения в [подробный поток](automation-runbook-output-and-messages.md#message-streams) отображаются в области вывода только в том случае, если для [переменной $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) задано значение **Continue**.

Несмотря на то что выполняется черновая версия, модуль Runbook работает в обычном режиме и выполняет все действия с использованием ресурсов среды. В связи с этим тестировать модули Runbook можно только в непроизводственных ресурсах.

Процедура проверки каждого [типа модуля Runbook](automation-runbook-types.md) одинакова. Нет никакой разницы при тестировании между текстовым редактором и графическим редактором в портал Azure.

1. Откройте черновую версию модуля Runbook в [текстовом редакторе](automation-edit-textual-runbook.md) или в [графическом редакторе](automation-graphical-authoring-intro.md).
1. Нажмите кнопку **Тест**, чтобы открыть страницу «Тест».
1. Если у модуля Runbook есть параметры, они будут перечислены в левой области, где можно указать значения, которые будут использоваться для теста.
1. Если вы хотите выполнить тест в [гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md), измените **Параметры запуска** на **гибридную рабочую роль** и выберите имя целевой группы.  В противном случае оставьте значение **Azure** по умолчанию, чтобы тест выполнялся в облаке.
1. Нажмите кнопку " **Пуск** ", чтобы начать тест.
1. Можно использовать кнопки в области вывода, чтобы остановить или приостановить [Рабочий процесс PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) или [графический](automation-runbook-types.md#graphical-runbooks) модуль Runbook во время тестирования. В случае приостановки модуль Runbook завершает действие, начатое до приостановки. Приостановленный модуль Runbook можно остановить или перезапустить.
1. Проверьте выходные данные модуля Runbook в области вывода.

## <a name="publish-a-runbook"></a>Публикация модуля Runbook

Перед запуском вновь созданного или импортированного модуля Runbook его необходимо опубликовать. Каждый модуль Runbook в службе автоматизации Azure имеет черновую версию и опубликованную версию. Запустить можно только опубликованную версию, а изменить — только черновую. Изменения, внесенные в черновик, не влияют на опубликованную версию. Когда необходимо сделать черновую версию доступной, опубликуйте ее, перезаписав текущую опубликованную версию с черновой версией.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Публикация модуля Runbook в портал Azure

1. Откройте модуль Runbook на портале Azure.
2. Нажмите кнопку **Изменить**.
3. Щелкните **опубликовать** , а затем **Да** в ответ на проверочное сообщение.

### <a name="publish-a-runbook-using-powershell"></a>Публикация модуля Runbook с помощью PowerShell

Используйте командлет [Publish-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) для публикации модуля Runbook с помощью Windows PowerShell. В следующем примере показано, как опубликовать пример Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как можно воспользоваться преимуществами модуля Runbook и коллекции модулей PowerShell, см. статью [Runbook и коллекции модулей для службы автоматизации Azure](automation-runbook-gallery.md).
* Дополнительные сведения об изменении модулей Runbook PowerShell и рабочих процессов PowerShell с помощью текстового редактора см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
* Дополнительные сведения о создании графических модулей Runbook см. [в статье графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
