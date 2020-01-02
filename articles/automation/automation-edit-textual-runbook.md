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
ms.openlocfilehash: 5a7eec0a7650f9c8e04a8d1062d32b6feb7d1d99
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850862"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Изменение текстовых модулей Runbook в службе автоматизации Azure

Текстовый редактор в службе автоматизации Azure можно использовать для изменения [модулей Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) и [модулей Runbook рабочих процессов PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Он обладает стандартными функциями других редакторов кода, включая поддержку технологии IntelliSense и цветовую схему кодирования, а также включает ряд дополнительных функций для работы с ресурсами, свойственными модулям Runbook. В этой статье подробно описано, как использовать этот редактор для выполнения разнообразных задач.

Текстовый редактор включает функцию, позволяющую вставлять в модуль runbook код для командлетов, ресурсов и дочерних модулей. Вместо того чтобы вводить этот код самостоятельно, выберите его из списка доступных ресурсов, и соответствующий код будет вставлен в модуль Runbook.

У каждого модуля Runbook в службе автоматизации Azure есть черновая и опубликованная версия. Вы можете изменить черновую версию модуля Runbook, а затем опубликовать ее для последующего использования. Опубликованную версию изменить нельзя. Дополнительные сведения см. в разделе [Publishing a runbook](manage-runbooks.md#publish-a-runbook) (Публикация модуля runbook).

Для работы с [графическими модулями Runbook](automation-runbook-types.md#graphical-runbooks) обратитесь к статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Изменение модуля Runbook с помощью портала Azure

Откройте модуль Runbook для изменения в текстовом редакторе, выполнив описанные ниже действия.

1. На портале Azure выберите свою учетную запись автоматизации.
2. В разделе **Автоматизация процессов** выберите **Модули Runbook**, чтобы открыть список модулей.
3. Вы берите модуль runbook, который нужно изменить, и нажмите кнопку **Изменить**.
4. Внесите изменения в модуль runbook.
5. Закончив изменения, нажмите кнопку **Сохранить** .
6. Чтобы опубликовать последнюю черновую версию модуля Runbook, нажмите кнопку **Опубликовать** .

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Вставка командлета в модуль Runbook

1. На холсте текстового редактора установите курсор в то место, куда нужно вставить командлет.
2. Разверните узел **Командлеты** в элементе управления «Библиотека».
3. Откройте модуль, в котором находится нужный командлет.
4. Щелкните правой кнопкой мыши командлет, который нужно вставить, и выберите **Добавить на холст**. Если у командлета больше одного набора параметров, будет добавлен набор параметров по умолчанию. Вы также можете развернуть командлет и выбрать другой набор параметров.
5. Код для командлета вставляется с полным списком параметров.
6. Для всех необходимых параметров укажите вместо типа данных соответствующее значение, заключив его в фигурные скобки <>. Все ненужные параметры удалите.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Вставка кода для дочернего модуля Runbook в модуль Runbook

1. На холсте текстового редактора установите курсор в то место, куда нужно вставить код для [дочернего модуля Runbook](automation-child-runbooks.md).
2. Разверните узел **Модули Runbook** в элементе управления «Библиотека».
3. Щелкните правой кнопкой мыши модуль runbook, который нужно вставить, и выберите **Добавить на холст**.
4. Код для дочернего модуля Runbook вставляется со всеми заполнителями для всех параметров модуля Runbook.
5. Замените заполнители на соответствующие значения для каждого параметра.

### <a name="to-insert-an-asset-into-a-runbook"></a>Вставка ресурс-контейнера в модуль Runbook

1. На холсте текстового редактора установите курсор в то место, куда нужно вставить код для дочернего модуля Runbook.
2. Разверните узел **Ресурс-контейнеры** в элементе управления «Библиотека».
3. Разверните узел для желаемого типа ресурс-контейнера.
4. Щелкните правой кнопкой мыши ресурс, который нужно вставить, и выберите **Добавить на холст**. Для [ресурсов-контейнеров переменных](automation-variables.md) выберите либо **Добавить "Получить переменную" на холст**, либо **Добавить "Установить переменную" на холст**, в зависимости от того, нужно ли вам получить или задать переменную.
5. Код для ресурс-контейнера вставляется в модуль Runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Изменение модуля Runbook в службе автоматизации Azure с помощью Windows PowerShell

Чтобы изменить модуль runbook с помощью Windows PowerShell, используйте любой редактор и сохраните его в виде файла `.ps1`. Командлет [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) позволяет извлечь содержимое модуля Runbook, а командлет [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) — заменить существующий черновой модуль Runbook на измененный.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Извлечение содержимого модуля Runbook с помощью Windows PowerShell

Команды в приведенном ниже примере показывают, как извлечь сценарий для модуля и сохранить его в файл сценария. В этом примере извлекается черновая версия. Извлечь можно также опубликованную версию модуля runbook, но изменить ее нельзя.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Изменение содержимого модуля Runbook с помощью Windows PowerShell

Команды в приведенном ниже примере показывают, как заменить существующее содержимое модуля Runbook на содержимое файла сценария. Эта же процедура используется для примера в статье [об импорте модуля runbook из файла сценария с помощью Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
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

