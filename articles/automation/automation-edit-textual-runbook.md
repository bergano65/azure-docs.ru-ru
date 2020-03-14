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
ms.openlocfilehash: 72aefb8de57e27718b14dba6a6d82deb8b63466f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367284"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Изменение текстовых модулей Runbook в службе автоматизации Azure

Текстовый редактор в службе автоматизации Azure можно использовать для изменения [модулей Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) и [модулей Runbook рабочих процессов PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Этот редактор имеет стандартные функции других редакторов кода, таких как IntelliSense. Кроме того, в нем предусмотрены дополнительные специальные функции для доступа к ресурсам, общим для модулей Runbook. 

Текстовый редактор включает функцию, позволяющую вставлять в модуль runbook код для командлетов, ресурсов и дочерних модулей. Вместо того чтобы вводить код самостоятельно, можно выбрать из списка доступных ресурсов, и редактор вставит соответствующий код в модуль Runbook.

У каждого модуля Runbook в службе автоматизации Azure есть черновая и опубликованная версия. Вы можете изменить черновую версию модуля Runbook, а затем опубликовать ее для последующего использования. Опубликованную версию изменить нельзя. Дополнительные сведения см. в разделе [Publishing a runbook](manage-runbooks.md#publish-a-runbook) (Публикация модуля runbook).

В этой статье подробно описано, как использовать этот редактор для выполнения разнообразных задач. Они неприменимы к [графическим модулям Runbook](automation-runbook-types.md#graphical-runbooks). Сведения о работе с этими модулями Runbook см. [в разделе графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Изменение модуля Runbook с помощью портал Azure

Откройте модуль Runbook для изменения в текстовом редакторе, выполнив описанные ниже действия.

1. В портал Azure выберите свою учетную запись службы автоматизации.
2. В разделе **Автоматизация процессов** выберите **Модули Runbook**, чтобы открыть список модулей.
3. Выберите Runbook для редактирования и нажмите кнопку **изменить**.
4. Внесите изменения в модуль runbook.
5. Закончив изменения, нажмите кнопку **Сохранить** .
6. Нажмите кнопку **опубликовать** , если хотите опубликовать последнюю черновую версию модуля Runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Вставка командлета в модуль Runbook

1. На холсте текстового редактора поместите курсор в то место, где нужно поместить командлет.
2. Разверните узел **Командлеты** в элементе управления «Библиотека».
3. Разверните модуль, содержащий используемый командлет.
4. Щелкните правой кнопкой мыши имя командлета, которое нужно вставить, и выберите **Добавить на холст**. Если у командлета несколько наборов параметров, редактор добавляет набор по умолчанию. Вы также можете развернуть командлет и выбрать другой набор параметров.
5. Обратите внимание, что код для командлета вставляется со всем списком параметров.
6. Укажите соответствующее значение вместо значения, заключенного в угловые скобки (< >) для любых обязательных параметров. Удалите все ненужные параметры.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Вставка кода для дочернего модуля Runbook в Runbook

1. На холсте текстового редактора поместите курсор в то место, куда нужно поместить код для [дочернего модуля Runbook](automation-child-runbooks.md).
2. Разверните узел **Модули Runbook** в элементе управления «Библиотека».
3. Щелкните правой кнопкой мыши модуль runbook, который нужно вставить, и выберите **Добавить на холст**.
4. Код для дочернего модуля Runbook вставляется со всеми заполнителями для всех параметров модуля Runbook.
5. Замените заполнители на соответствующие значения для каждого параметра.

### <a name="insert-an-asset-into-a-runbook"></a>Вставка ресурса в модуль Runbook

1. В элементе управления Canvas текстового редактора поместите курсор в то место, куда нужно поместить код для дочернего модуля Runbook.
2. Разверните узел **Ресурс-контейнеры** в элементе управления «Библиотека».
3. Разверните узел для нужного типа ресурса.
4. Щелкните правой кнопкой мыши имя ресурса, которое нужно вставить, и выберите **Добавить на холст**. Для [переменных ресурсов](automation-variables.md)выберите либо **Добавить "получить переменную" к холсту** , либо **Добавить "задать переменную" на холст**, в зависимости от того, требуется ли получить или задать переменную.
5. Обратите внимание, что код ресурса вставляется в модуль Runbook.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Изменение модуля Runbook службы автоматизации Azure с помощью Windows PowerShell

Чтобы изменить модуль Runbook с помощью Windows PowerShell, воспользуйтесь редактором по своему усмотрению и сохраните модуль Runbook в **PS1** -файл. Для получения содержимого модуля Runbook можно использовать командлет [Export-азаутоматионрунбук](/powershell/module/Az.Automation/Export-AzAutomationRunbook) . С помощью командлета [Import-азаутоматионрунбук](/powershell/module/Az.Automation/import-azautomationrunbook) можно заменить существующий черновик Runbook на измененный.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Получение содержимого модуля Runbook с помощью Windows PowerShell

Команды в приведенном ниже примере показывают, как извлечь сценарий для модуля и сохранить его в файл сценария. В этом примере извлекается черновая версия. Можно также получить опубликованную версию модуля Runbook, хотя эта версия не может быть изменена.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Изменение содержимого модуля Runbook с помощью Windows PowerShell

Команды в приведенном ниже примере показывают, как заменить существующее содержимое модуля Runbook на содержимое файла сценария. Эта же процедура используется для примера в статье [об импорте модуля runbook из файла сценария с помощью Windows PowerShell](manage-runbooks.md#import-a-runbook).

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
* [Соединения](automation-connections.md)
* [Учетные данные](automation-credentials.md)
* [Расписания](automation-schedules.md)
* [Переменные](automation-variables.md)

