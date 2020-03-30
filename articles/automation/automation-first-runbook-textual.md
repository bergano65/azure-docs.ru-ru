---
title: Первый runbook рабочего процесса PowerShell в службе автоматизации Azure
description: Учебник, в котором рассказывается, как создать, протестировать и опубликовать простой текстовый Runbook с помощью рабочего процесса PowerShell.
keywords: рабочий процесс PowerShell, примеры рабочего процесса powershell, рабочие процессы powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367267"
---
# <a name="my-first-powershell-workflow-runbook"></a>Первый Runbook рабочего процесса PowerShell

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

В данном учебнике описана процедура создания [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) в службе автоматизации Azure. Начните с простой книги, которую вы тестируете и публикуете, изучая, как отслеживать состояние задания runbook. Затем измените runbook, чтобы фактически управлять ресурсами Azure, проиллюстрировано запуском виртуальной машины Azure. Наконец, сделайте runbook более надежным, добавив параметры runbook.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником необходимы указанные ниже компоненты.

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись службы автоматизации](automation-offering-get-started.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure. Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Так как вы остановитесь и запустите эту машину, она не должна быть производством VM.

## <a name="step-1---create-new-runbook"></a>Шаг 1. Создание нового модуля Runbook

Начните с создания простого runbook, который выводит текст. `Hello World`

1. На портале Azure выберите свою учетную запись службы автоматизации.

   Страница учетной записи в службе автоматизации позволяет быстро получить представление о ресурсах, доступных в этой учетной записи. Некоторые ресурсы уже должны быть доступны. Большинство из этих активов являются модулями, автоматически включенными в новую учетную запись Автоматизация. Актив credential также должен быть связан с подпиской.
 
1. Выберите **Runbooks** под **автоматизацией процессов,** чтобы открыть список runbooks.
1. Создайте новый runbook, выбрав **Создать runbook**.
1. Присвойте Runbook имя **MyFirstRunbook-Workflow**.
1. В этом случае вы собираетесь создать [книгу рабочего процесса PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Выберите **рабочий процесс PowerShell** для **типа Runbook.**
1. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="step-2---add-code-to-the-runbook"></a>Шаг 2. Добавление кода в Runbook

Вы можете ввести код непосредственно в книгу запуска, либо выбрать cmdlets, runbooks и активы из управления библиотекой и добавить их в книгу с помощью любых связанных с этим параметров. Для этого учебника вы вводите код непосредственно в книгу.

1. Ваша книга в настоящее время `Workflow` пуста только с требуемым ключевым словом, названием runbook и скобками, которые обусловливают весь рабочий процесс.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Введите `Write-Output "Hello World"` между скобками.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Сохраните модуль Runbook, щелкнув **Сохранить**.

## <a name="step-3---test-the-runbook"></a>Шаг 3. Тестирование модуля Runbook

Перед публикацией runbook, чтобы сделать его доступным в производстве, вы должны проверить его, чтобы убедиться, что он работает должным образом. Тестирование runbook запускает свою версию проекта и позволяет просматривать его вывод в интерактивном режиме.

1. Выберите **тестовое стекло,** чтобы открыть тестовое стекло.
1. Нажмите **Кнопка Начало,** чтобы начать тест, с тестированием единственной включенной опции.
1. Обратите внимание, что [создается задание runbook](automation-runbook-execution.md) и отображается его статус в панели.

   Статус задания `Queued`начинается как, указывая, что задание ждет сотрудника runbook в облаке, чтобы стать доступным. Статус меняется `Starting` на время выполнения требования работника. Наконец, статус `Running` становится, когда запуск фактически начинает работать.

1. Когда задание runbook завершается, панель test отображает его выход. В этом случае, `Hello World`вы видите .

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Закройте область тестирования, чтобы вернуться на холст.

## <a name="step-4---publish-and-start-the-runbook"></a>Шаг 4. Публикация и запуск модуля Runbook

Созданная книга по разработке по-прежнему находится в режиме Проекта. Вы должны опубликовать его, прежде чем вы сможете запустить его в производство. При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. В нашем случае опубликованной версии не существует, поскольку модуль runbook создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
1. Прокрутите влево, чтобы просмотреть книгу **runbook** на странице Runbooks и обратите внимание, что поле **статуса автора** настроено **на опубликовано.**
1. Прокрутите назад вправо, чтобы просмотреть страницу для **MyFirstRunbook-Рабочий процесс**.

   Параметры в верхней части позволяют начать runbook сейчас, запланировать будущее время начала, или создать [веб-крюк,](automation-webhooks.md) так что runbook может быть запущен через http вызова.

1. Чтобы запустить runbook, выберите **Запуск** и нажмите кнопку **Да** в появившемся запросе.

   ![Запуск модуля Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Панель вакансий вакансии открыта для созданного задания runbook. В этом случае оставьте панель открытой, чтобы вы могли наблюдать за ходом выполнения задания.

1. Обратите внимание, что статус задания отображается в **Резюме вакансий**. Этот статус соответствует статусам, которые вы видели при тестировании runbook.

   ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Как только состояние `Completed`runbook показывает, щелкните **выход**. Открывается страница вывода, где можно `Hello World` увидеть ваше сообщение.

   ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Закройте страницу выходных данных.

1. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. Вы должны `Hello World` видеть только в потоке вывода. Обратите внимание, что панель потоков может отображать другие потоки для выполнения задания, такие как многословные и потоки ошибок, если runbook пишет им.

   ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Закройте панель потоков и панель вакансий, чтобы вернуться на страницу **MyFirstRunbook.**
1. Нажмите **"Вакансии** под **ресурсами",** чтобы открыть страницу "Вакансии" для этого сборника. На этой странице перечислены все задания, созданные вашего runbook. Вы должны видеть только одну работу в списке, так как вы завели задание только один раз.

   ![Задания](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Нажмите на имя задания, чтобы открыть ту же панель вакансий, что вы просматривали при запуске. Используйте эту панель для просмотра деталей любого задания, созданного для runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Шаг 5. Добавление проверки подлинности для управления ресурсами Azure

Вы протестировали и опубликовали свой модуль runbook, но пока он не выполняет никаких полезных действий. Нужно, чтобы он управлял ресурсами Azure. Он не может этого сделать, если он не проверяет подлинность с помощью учетных данных для подписки. Аутентификация использует `Connect-AzAccount` cmdlet.

>[!NOTE]
>Для PowerShell runbooks, `Add-AzAccount` и `Add-AzureRMAccount` псевдонимы для `Connect-AzAccount`. Вы можете использовать эти cmdlets или вы можете [обновить свои модули](automation-update-azure-modules.md) в вашей учетной записи автоматизации до последних версий. Возможно, потребуется обновить модули, даже если вы только что создали новую учетную запись Automation.

1. Перейдите на страницу **MyFirstRunbook-Workflow** и откройте текстовый редактор, нажав **на Edit**.
2. Удалите строку. `Write-Output`
3. Поместите курсор в пустую строку между фигурными скобками.
4. Введите или скопируйте и вставьте следующий код, который обрабатывает аутентификацию с помощью учетной записи Automation Run As.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Щелкните **Тестовая область**, чтобы проверить модуль runbook.
1. Щелкните **Пуск** , чтобы начать тестирование. Как только он завершается, вы должны увидеть выход, похожий на следующий, отображающий основную информацию из вашей учетной записи. Это действие подтверждает, что учетные данные действительны.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Шаг 6. Добавление кода запуска виртуальной машины

Теперь, когда ваша книга аутентификации под подпиской Azure позволяет управлять ресурсами. Давайте добавим команду для запуска виртуальной машины. Вы можете выбрать любой VM в подписке Azure, и сейчас вы хардкодирования, что имя в runbook. Если вы управляете ресурсами по нескольким подпискам, необходимо использовать `AzContext` параметр с помощью cmdlet [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Предоставьте имя и название группы ресурсов VM, чтобы начать, введя вызов в cmdlet [Start-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) как показано ниже. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Сохраните модуль runbook, а затем щелкните **Тестовая область**, чтобы проверить модуль.
1. Щелкните **Пуск** , чтобы начать тестирование. Как только он завершается, убедитесь, что VM был запущен.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Шаг 7. Добавление входного параметра в модуль Runbook

Ваш runbook в настоящее время начинает VM, что вы hardcoded в runbook. Это будет более полезно, если вы можете указать VM, когда запуск запущен. Давайте добавим параметры ввода в книгу, чтобы обеспечить эту функциональность.

1. Добавьте переменные `VMName` `ResourceGroupName` для и параметров в беговую книгу и используйте переменные с `Start-AzVM` cmdlet, как показано ниже.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Сохраните модуль Runbook и откройте область тестирования. Теперь можно указать значения двух входных переменных, которые будут использоваться при тестировании.
3. Закройте область тестирования.
4. Щелкните **Опубликовать** , чтобы опубликовать новую версию модуля Runbook.
5. Остановите VM, который вы начали.
6. Щелкните **Пуск** , чтобы запустить модуль Runbook. 
7. Введите значения для **VMNAME** и **RESOURCEGROUPNAMEдля** для VM, которые вы собираетесь начать.

   ![Запуск модуля Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Когда запуск завершается, убедитесь, что VM запущен.

## <a name="next-steps"></a>Дальнейшие действия

* Для получения дополнительной информации о PowerShell, включая языковые справочные и учебные модули, обратитесь к [документам PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Чтобы начать работу с графических runbooks, [см.](automation-first-runbook-graphical.md)
* Чтобы начать работу с PowerShell runbooks, [см.](automation-first-runbook-textual-powershell.md)
* Чтобы узнать больше о типах runbook [Azure Automation runbook types](automation-runbook-types.md)и их преимуществах и ограничениях, см.
* Для получения дополнительной информации о функциях поддержки скриптов PowerShell [см.](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
