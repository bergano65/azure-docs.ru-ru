---
title: Изменение текстовых модулей Runbook в службе автоматизации Azure
description: В этой статье представлены различные процедуры для работы с модулями Runbook PowerShell и рабочих процессов PowerShell в службе автоматизации Azure с помощью текстового редактора.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ef7db244057bc8b3b2e4d938b9f3bdd11c7940a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406025"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Изменение текстовых модулей Runbook в службе автоматизации Azure

Текстовый редактор в службе автоматизации Azure можно использовать для изменения [модулей Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) и [модулей Runbook рабочих процессов PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Этот редактор имеет типичные особенности других редакторов кода, таких как IntelliSense. Он также имеет цветовое кодирование с дополнительными специальными функциями, чтобы помочь вам в доступе к ресурсам, общим для runbooks. 

Текстовый редактор включает функцию, позволяющую вставлять в модуль runbook код для командлетов, ресурсов и дочерних модулей. Вместо того, чтобы вводить код самостоятельно, вы можете выбрать из списка доступных ресурсов и редактор вставляет соответствующий код в runbook.

У каждого модуля Runbook в службе автоматизации Azure есть черновая и опубликованная версия. Вы можете изменить черновую версию модуля Runbook, а затем опубликовать ее для последующего использования. Опубликованную версию изменить нельзя. Дополнительные сведения см. в разделе [Publishing a runbook](manage-runbooks.md#publishing-a-runbook) (Публикация модуля runbook).

В этой статье подробно описано, как использовать этот редактор для выполнения разнообразных задач. Они не применимы к [графическим runbooks](automation-runbook-types.md#graphical-runbooks). Для работы с этими runbooks, см [Графический автор в Azure автоматизации](automation-graphical-authoring-intro.md).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="editing-a-runbook-with-the-azure-portal"></a>Редактирование книги с портала Azure

Откройте модуль Runbook для изменения в текстовом редакторе, выполнив описанные ниже действия.

1. На портале Azure выберите учетную запись Автоматизации.
2. В рамках **PROCESS AUTOMATION**выберите **Runbooks,** чтобы открыть список runbooks.
3. Выберите runbook для отоденать, а затем нажмите **Edit**.
4. Внесите изменения в модуль runbook.
5. По завершении редактирования нажмите **Сохранить**.
6. Нажмите **Опубликовать,** если вы хотите опубликовать последнюю версию проекта runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Вставьте cmdlet в книгу

1. В холсте текстового редактора поместите курсор, где вы хотите разместить cmdlet.
2. Разверните узел **Командлеты** в элементе управления «Библиотека».
3. Расширьте модуль, содержащий cmdlet для использования.
4. Право нажмите имя cmdlet вставить и выберите **Добавить в холст**. Если cmdlet имеет более одного набора параметров, редактор добавляет набор по умолчанию. Вы также можете развернуть командлет и выбрать другой набор параметров.
5. Обратите внимание, что код для cmdlet вставляется со всем списком параметров.
6. Обеспечить соответствующее значение вместо значения, окруженного угловые скобки (<>) для любых необходимых параметров. Удалите все параметры, которые вам не нужны.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Вставьте код для детской книги в книгу-запуск

1. В холсте текстового редактора поместите курсор, где вы хотите разместить код для [детского запуска.](automation-child-runbooks.md)
2. Разверните узел **Модули Runbook** в элементе управления «Библиотека».
3. Щелкните правой кнопкой мыши модуль runbook, который нужно вставить, и выберите **Добавить на холст**.
4. Код для дочернего модуля Runbook вставляется со всеми заполнителями для всех параметров модуля Runbook.
5. Замените заполнители на соответствующие значения для каждого параметра.

### <a name="insert-an-asset-into-a-runbook"></a>Вставьте актив в книгу выполнения

1. В управлении текстовым редактором Canvas поместите курсор, где вы хотите разместить код для детской книги.
2. Разверните узел **Ресурс-контейнеры** в элементе управления «Библиотека».
3. Расширьте узла для желаемого типа актива.
4. Нажмите правое нажатие имени актива, чтобы вставить и выбрать **Добавить в холст.** Для [переменных активов](automation-variables.md)выберите либо **добавить "Получить переменную" на холст** или **добавить "Установить переменные" на холст,** в зависимости от того, хотите ли вы получить или установить переменную.
5. Обратите внимание, что код для актива вставляется в книгу запуска.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Редактирование runbook автоматизации Azure с помощью Windows PowerShell

Чтобы отсеивать книгу с помощью Windows PowerShell, используйте редактор по вашему выбору и сохраните книгу в файле **.ps1.** Для получения содержимого свекольного сборника можно использовать cmdlet [Export-AzAutomationRunbook.](/powershell/module/Az.Automation/Export-AzAutomationRunbook) Вы можете использовать [импорт-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) cmdlet, чтобы заменить существующий проект runbook с измененным.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Извлекайте содержимое руники с помощью Windows PowerShell

Команды в приведенном ниже примере показывают, как извлечь сценарий для модуля и сохранить его в файл сценария. В этом примере извлекается черновая версия. Также можно получить опубликованную версию runbook, хотя эта версия не может быть изменена.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Измените содержимое руники с помощью Windows PowerShell

Команды в приведенном ниже примере показывают, как заменить существующее содержимое модуля Runbook на содержимое файла сценария. Эта же процедура используется для примера в статье [об импорте модуля runbook из файла сценария с помощью Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Связанные статьи

* [Управление модулями runbook в службе автоматизации Azure](manage-runbooks.md)
* [Изучение рабочего процесса PowerShell](automation-powershell-workflow.md)
* [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md)
* [Сертификаты](automation-certificates.md)
* [Подключения](automation-connections.md)
* [Учетные данные](automation-credentials.md)
* [Расписания](automation-schedules.md)
* [Переменные](automation-variables.md)
* [Справочник по командлетам PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
