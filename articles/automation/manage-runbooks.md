---
title: Управление модулями runbook в службе автоматизации Azure
description: В этой статье описывается управление модулями Runbook в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da3b09998d163ffcc16bfcbbf9f516467dd3311d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418483"
---
# <a name="manage-runbooks-in-azure-automation"></a>Управление модулями runbook в службе автоматизации Azure

Чтобы добавить модуль Runbook в службу автоматизации Azure, можно [создать новый модуль](#creating-a-new-runbook) или импортировать уже существующий модуль из файла или из [коллекции Runbook](automation-runbook-gallery.md). В этой статье рассказывается, как создавать и импортировать модули Runbook из файла.  Информацию о получении доступа к модулям Runbook сообществ см. в статье [Коллекции модулей Runbook и других модулей для службы автоматизации Azure](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Создание модуля runbook

Создать новый модуль в службе автоматизации Azure можно с помощью одного из порталов Azure или Windows PowerShell. Вновь созданный модуль Runbook можно изменить, следуя инструкциям в статьях [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md) и [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Создание модуля Runbook на портале Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В концентраторе выберите **Модули Runbook**, чтобы открыть список модулей Runbook.
3. Нажмите кнопку **Добавить Runbook**, а затем **Создать новый Runbook**.
4. Введите **имя** для модуля Runbook и выберите его [тип](automation-runbook-types.md). Имя модуля Runbook должно начинаться с буквы и содержать буквы, цифры, символы подчеркивания и дефисы.
5. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

### <a name="create-a-runbook-with-powershell"></a>Создание модуля Runbook с помощью PowerShell

Командлет [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) позволяет создать пустой [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Используйте параметр **Тип** для указания одного из четырех типов модулей Runbook.

Команды в приведенном ниже примере демонстрируют создание пустого модуля Runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Импорт модуля Runbook

Для создания модуля Runbook в службе автоматизации Azure можно импортировать сценарий или рабочий процесс PowerShell (с расширением PS1) либо экспортировать графический модуль Runbook (с расширением GRAPHRUNBOOK) или скрипт Python 2 (с расширением PY).  При этом необходимо указать [тип модуля Runbook](automation-runbook-types.md), который создается во время импорта, с учетом следующих рекомендаций.

* Файл `.graphrunbook` может быть импортирован только в новый [графический модуль Runbook](automation-runbook-types.md#graphical-runbooks), а графические модули Runbook могут быть созданы только из файла `.graphrunbook`.
* Файл `.ps1` с рабочим процессом PowerShell можно импортировать только в [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Если файл содержит несколько рабочих процессов PowerShell, импорт завершится ошибкой. Каждый рабочий процесс необходимо сохранить в отдельный файл и импортировать отдельно.
* Файл `.ps1` без рабочего процесса можно импортировать либо в [модуль Runbook PowerShell](automation-runbook-types.md#powershell-runbooks), либо в [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Если файл импортируется в модуль Runbook рабочего процесса PowerShell, он преобразуется в рабочий процесс, а в модуль Runbook включаются комментарии с описанием внесенных изменений.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Импорт модуля Runbook из файла с помощью портала Azure

Для импорта файла сценария в службу автоматизации Azure можно использовать описанную ниже процедуру.  

> [!NOTE]
> Обратите внимание, что с помощью портала PS1-файл можно импортировать только в модуль Runbook рабочего процесса PowerShell.

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В концентраторе выберите **Модули Runbook**, чтобы открыть список модулей Runbook.
3. Нажмите кнопку **Добавить Runbook**, а затем **Импорт**.
4. Щелкните **файл модуля Runbook** и выберите файл для импорта.
5. Если поле **Имя** активно, его можно изменить.  Имя модуля Runbook должно начинаться с буквы и содержать буквы, цифры, символы подчеркивания и дефисы.
6. [Тип Runbook](automation-runbook-types.md) выбирается автоматически, но его можно изменить, учитывая применимые ограничения. 
7. Новый модуль Runbook появится в списке модулей Runbook для учетной записи службы автоматизации.
8. Перед запуском модуля его необходимо [опубликовать](#publishing-a-runbook) .

> [!NOTE]
> После импорта графического модуля Runbook или графического модуля Runbook рабочего процесса PowerShell появляется возможность, при необходимости, преобразования модуля в другой тип. Выполнить преобразование в текстовый модуль Runbook невозможно.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Импорт модуля Runbook из файла сценария с помощью Windows PowerShell

Чтобы импортировать файл сценария как черновик модуля Runbook рабочего процесса PowerShell, можно воспользоваться командлетом [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook). Если модуль Runbook уже существует, импорт завершится ошибкой. Чтобы этого не произошло, необходимо использовать параметр *-Force*.

Приведенные ниже примеры команд демонстрируют импорт файла сценария в модуль Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Тестирование runbook

При тестировании модуля Runbook запускается его [черновая версия](#publishing-a-runbook) и завершаются все действия, которые он выполняет. Журнал заданий не создается, однако в области вывода теста отображаются потоки [выходных данных](automation-runbook-output-and-messages.md#output-stream) и [предупреждений и ошибок](automation-runbook-output-and-messages.md#message-streams). Сообщения, предназначенные для [подробного потока](automation-runbook-output-and-messages.md#message-streams), отображаются в области выходных данных, только если [переменная $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) имеет значение Continue.

Несмотря на то что выполняется черновая версия, модуль Runbook работает в обычном режиме и выполняет все действия с использованием ресурсов среды. В связи с этим тестировать модули Runbook можно только в непроизводственных ресурсах.

Процедура тестирования для всех [типов модулей Runbook](automation-runbook-types.md) одна и та же и выполняется одинаково и в текстовом, и в графическом редакторе на портале Azure.  

1. Откройте черновую версию модуля Runbook в [текстовом](automation-edit-textual-runbook.md) или [графическом редакторе](automation-graphical-authoring-intro.md).
1. Нажмите кнопку **Тест**, чтобы открыть страницу «Тест».
1. Если модуль Runbook имеет параметры, они отображаются в левой области, где можно указать значения для теста.
1. Если вы хотите запустить тест в [гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md), то измените **Параметры запуска** на **Гибридную рабочую роль** и выберите имя целевой группы.  В противном случае оставьте значение **Azure** по умолчанию, чтобы тест выполнялся в облаке.
1. Нажмите кнопку **Запуск** , чтобы запустить тест.
1. Если модуль Runbook является [модулем рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) или [графическим](automation-runbook-types.md#graphical-runbooks), то вы можете остановить или приостановить его в процессе тестирования с помощью кнопок под областью выходных данных. В случае приостановки модуль Runbook завершает действие, начатое до приостановки. Приостановленный модуль Runbook можно остановить или перезапустить.
1. Проверьте выходные данные модуля Runbook в области выходных данных.

## <a name="publish-a-runbook"></a>Публикация модуля Runbook

Перед запуском вновь созданного или импортированного модуля Runbook его необходимо опубликовать.  У каждого Runbook в службе автоматизации есть черновая и опубликованная версия. Запустить можно только опубликованную версию, а изменить — только черновую. Изменения, внесенные в черновик, не влияют на опубликованную версию. Если требуется черновая версия, ее можно опубликовать, перезаписав опубликованную версию черновой.

### <a name="azure-portal"></a>Портал Azure

1. Откройте модуль Runbook на портале Azure.
2. Нажмите кнопку **Edit** (Изменить).
3. Нажмите кнопку **Опубликовать**, а в проверочном сообщении — кнопку **Да**.

### <a name="powershell"></a>PowerShell

Командлет [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) позволяет опубликовать модуль Runbook с помощью Windows PowerShell. Команды в приведенном ниже примере показывают, как опубликовать образец модуля Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о преимуществах использования коллекции модулей Runbook и PowerShell см. в статье [Коллекции модулей Runbook и других модулей для службы автоматизации Azure](automation-runbook-gallery.md).
* Дополнительные сведения о редактировании модулей Runbook PowerShell и рабочих процессов PowerShell с помощью текстового редактора см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
* Дополнительные сведения о графической разработке модулей Runbook см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
