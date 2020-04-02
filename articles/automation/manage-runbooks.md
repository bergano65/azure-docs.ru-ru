---
title: Управление модулями runbook в службе автоматизации Azure
description: В этой статье описывается управление модулями Runbook в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ad2a34691a00f217db6cf6835eefed18c8862d32
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547922"
---
# <a name="manage-runbooks-in-azure-automation"></a>Управление модулями runbook в службе автоматизации Azure

В Azure Automation можно добавить книгу запуска, [создав новый](#create-a-runbook) или [импортировав существующий](#import-a-runbook) из файла или [галерею Runbook.](automation-runbook-gallery.md) В этой статье рассказывается, как создавать и импортировать модули Runbook из файла. Вы можете получить все сведения о доступе к сообщества runbooks и модулей в [Runbook и модульных галерей для Azure автоматизации](automation-runbook-gallery.md).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="create-a-runbook"></a>Создание модуля runbook

Создать новый модуль в службе автоматизации Azure можно с помощью одного из порталов Azure или Windows PowerShell. Вновь созданный модуль Runbook можно изменить, следуя инструкциям в статьях [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md) и [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Создание модуля Runbook на портале Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Из концентратора выберите **Runbooks** под **автоматизацией процессов,** чтобы открыть список runbooks.
3. Нажмите **Создать runbook**.
4. Введите имя для runbook и выберите его [тип.](automation-runbook-types.md) Название runbook должно начинаться с буквы и может содержать буквы, цифры, подчеркнутые и тире.
5. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

### <a name="create-a-runbook-with-powershell"></a>Создание модуля Runbook с помощью PowerShell

Вы можете использовать смдlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) для создания пустого [смоченки рабочего процесса PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Используйте `Type` параметр, чтобы указать один `New-AzAutomationRunbook`из типов runbook, определенных для .

Ниже приводится следующий пример, как создать новую пустую книгу.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Импорт модуля Runbook

Вы можете создать новый runbook в Azure Automation путем импорта скрипта PowerShell или Рабочего процесса PowerShell (**.ps1**), экспортируемого графического runbook (**.graphrunbook**), или скрипта Python 2 (**.py**).  При этом необходимо указать [тип модуля Runbook](automation-runbook-types.md), который создается во время импорта, с учетом следующих рекомендаций.

* Файл **.ps1,** не содержащий рабочий процесс, может быть импортирован либо в [рунбус PowerShell,](automation-runbook-types.md#powershell-runbooks) либо в [справочник рабочего процесса PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Если вы импортируете его в книгу рабочего процесса PowerShell, он преобразуется в рабочий процесс. В этом случае комментарии включены в книгу для описания внесенных изменений.

* Файл **.ps1,** содержащий рабочий процесс PowerShell, может быть импортирован только в [книгу запуска рабочего процесса PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Если файл содержит несколько рабочих процессов PowerShell, импорт завершается сбой. Каждый рабочий процесс необходимо сохранить в отдельный файл и импортировать отдельно.

* Файл **.ps1,** содержащий рабочий процесс PowerShell, не должен импортироваться в [рунированную книгу PowerShell,](automation-runbook-types.md#powershell-runbooks)так как скриптонок PowerShell не может распознать его.

* Файл **.graphrunbook** может быть импортирован только в новый [графический runbook.](automation-runbook-types.md#graphical-runbooks) Обратите внимание, что создать графический runbook можно только из файла **.graphrunbook.**

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Импортировать книгу из файла на портале Azure

Для импорта файла сценария в службу автоматизации Azure можно использовать описанную ниже процедуру.

> [!NOTE]
> Вы можете импортировать файл **.ps1** только в справочник рабочего процесса PowerShell с помощью портала.

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Из концентратора выберите **Runbooks** под **автоматизацией процессов,** чтобы открыть список runbooks.
3. Нажмите **Импорт runbook**.
4. Нажмите **файл Runbook** и выберите файл для импорта.
5. Если поле **«Имя»** включено, у вас есть возможность изменить имя запуска. Название должно начинаться с буквы и может содержать буквы, цифры, подчеркнутые и тире.
6. [Тип Runbook](automation-runbook-types.md) выбирается автоматически, но его можно изменить, учитывая применимые ограничения.
7. Нажмите кнопку **Создать**. Новый модуль Runbook появится в списке модулей Runbook для учетной записи службы автоматизации.
8. Перед запуском модуля его необходимо [опубликовать](#publish-a-runbook) .

> [!NOTE]
> После импорта графического runbook или графического запуска PowerShell Workflow можно преобразовать его в другой тип. Тем не менее, вы не можете преобразовать один из этих графических runbooks в текстовый runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Импортировать книгу запуска из файла скрипта с помощью Windows PowerShell

Используйте смдlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) для импорта файла скрипта в качестве справочника рабочего процесса PowerShell. Если запуск уже существует, импорт завершается `Force` неудачей, если вы не используете параметр с cmdlet.

Ниже приводится пример, как импортировать файл скрипта в беговую книгу.

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

При тестировании модуля Runbook запускается его [черновая версия](#publish-a-runbook) и завершаются все действия, которые он выполняет. История задания не создается, но потоки [вывода](automation-runbook-output-and-messages.md#output-stream) и [предупреждения и ошибки](automation-runbook-output-and-messages.md#message-streams) отображаются в панели вывода test. Сообщения в [потоке Verbose](automation-runbook-output-and-messages.md#message-streams) отображаются в `VerbosePreference` панели вывода только в том случае, если переменная (автоматизация-запуск-выход-и-сообщения.md-preference-variables) настроена продолжить.

Несмотря на то что выполняется черновая версия, модуль Runbook работает в обычном режиме и выполняет все действия с использованием ресурсов среды. В связи с этим тестировать модули Runbook можно только в непроизводственных ресурсах.

Процедура тестирования каждого [типа runbook](automation-runbook-types.md) одинакова. Нет никакой разницы в тестировании между текстовым редактором и графическим редактором на портале Azure.

1. Откройте версию проекта runbook либо в [текстовом редакторе,](automation-edit-textual-runbook.md) либо в [графическом редакторе.](automation-graphical-authoring-intro.md)
1. Нажмите кнопку **Тест**, чтобы открыть страницу «Тест».
1. Если в runbook есть параметры, они перечислены в левом стеле, где можно предоставить значения, которые будут использоваться для теста.
1. Если вы хотите запустить тест на [гибридном Работнике Runbook,](automation-hybrid-runbook-worker.md)измените **настройки выполнения** на **гибридный рабочий** и выберите имя целевой группы.  В противном случае оставьте значение **Azure** по умолчанию, чтобы тест выполнялся в облаке.
1. Нажмите кнопку **«Пуск»,** чтобы начать тест.
1. Кнопки под выходным стеклом можно использовать для остановки или приостановки [рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) или [графического](automation-runbook-types.md#graphical-runbooks) runbook во время тестирования. В случае приостановки модуль Runbook завершает действие, начатое до приостановки. Приостановленный модуль Runbook можно остановить или перезапустить.
1. Проинспектируйте выход из свектора в панели вывода.

## <a name="publish-a-runbook"></a>Публикация модуля Runbook

Перед запуском вновь созданного или импортированного модуля Runbook его необходимо опубликовать. Каждый сборник в Azure Automation имеет версию проекта и опубликованную версию. Запустить можно только опубликованную версию, а изменить — только черновую. Изменения, внесенные в черновик, не влияют на опубликованную версию. Когда версия проекта должна быть доступна, вы публикуете ее, перезаписывая текущую опубликованную версию с версией проекта.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Публикация книги на портале Azure

1. Откройте модуль Runbook на портале Azure.
2. Щелкните **Правка**.
3. Нажмите **Опубликовать,** а затем **Да** в ответ на сообщение о проверке.

### <a name="publish-a-runbook-using-powershell"></a>Публикация runbook с использованием PowerShell

Используйте [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) cmdlet для публикации книги с Windows PowerShell. Ниже приводится следующий пример, как опубликовать пример runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

### <a name="schedule-a-runbook-in-the-azure-portal"></a>Расписание книги на портале Azure

Когда ваша книга была опубликована, вы можете запланировать его для работы.

1. Откройте модуль Runbook на портале Azure.
2. Выберите **Расписание** под **ресурсами**.
3. Выберите **Добавить расписание**.
4. В панели Runbook расписания выберите **ссылку на расписание для выполнения.**
5. Выберите **Создать новое расписание** в панели расписания.
6. Введите имя, описание и другие параметры в новое расписание панели. 
7. Как только расписание создано, выделите его и нажмите **OK**. Теперь она должна быть связана с вашей книги.
8. Ищите электронную почту в почтовом ящике, чтобы уведомить вас о статусе runbook.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как вы можете воспользоваться Runbook и PowerShell Модуль Галерея, см [Runbook и модуль галереи для Автоматизации Azure](automation-runbook-gallery.md).
* Чтобы узнать больше о редактировании учебников PowerShell и PowerShell Workflow с текстовым редактором, смотрите [Редактирование текстовых учебников в Azure Automation.](automation-edit-textual-runbook.md)
* Чтобы узнать больше об авторе графического runbook, смотрите [графическое авторство в Azure Automation](automation-graphical-authoring-intro.md).
