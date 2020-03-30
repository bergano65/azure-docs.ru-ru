---
title: Мой первый модуль runbook PowerShell в службе автоматизации Azure
description: Учебник, в котором описывается создание, тестирование и публикация простого модуля Runbook PowerShell.
keywords: Azure PowerShell, руководство по скриптам PowerShell, инструменты автоматизации PowerShell
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367318"
---
# <a name="my-first-powershell-runbook"></a>Мой первый модуль Runbook PowerShell

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

В этом руководстве описана процедура создания [модуля Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) в службе автоматизации Azure. Начните с простой книги, которую вы можете протестировать и опубликовать, изучая, как отслеживать состояние задания runbook. Затем измените руникум, чтобы фактически управлять ресурсами Azure, в данном случае запуская виртуальную машину Azure. Заполните учебник, чтобы сделать runbook более надежным, добавив параметры runbook.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется:

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись службы автоматизации](automation-quickstart-create-account.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure. Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Так как вы остановитесь и запустите эту машину, она не должна быть производством VM.
* При необходимости [импортируйте модули Azure](shared-resources/modules.md) или [обновляйте модули](automation-update-azure-modules.md) на основе использоваемых cmdlets.

## <a name="differences-from-powershell-workflow-runbooks"></a>Отличия от стыковых книг PowerShell Workflow

Runbooks PowerShell имеют тот же жизненный цикл, возможности и управление, что и runbooks Workflow PowerShell. Однако существуют некоторые различия и ограничения.

| Характеристика  | Бегбуки PowerShell | Runbooks Рабочего процесса PowerShell |
| ------ | ----- | ----- |
| Speed | Запуск быстро, поскольку они не используют шаг компиляции. | Бегите медленнее. |
| Контрольные точки | Не поддерживайте контрольно-пропускные пункты. Запуск PowerShell может возобновить работу только с самого начала. | Используйте контрольно-пропускные пункты, которые позволяют трудовой книжке возобновить работу с любой точки. |
| Выполнение командования | Поддержка только последовательного исполнения. | Поддержка как последовательного, так и параллельного выполнения.|
| Пространство выполнения | Одно пространство запуска запускает все в скрипте. | Отдельное пространство запуска может быть использовано для действия, команды или блока скриптов. |

В дополнение к этим различиям, Runbooks PowerShell имеют некоторые [синтаксические отличия](https://technet.microsoft.com/magazine/dn151046.aspx) от Runbooks PowerShell Workflow.

## <a name="step-1---create-runbook"></a>Шаг 1. Создание нового модуля Runbook

Начните с создания простого runbook, который выводит текст. `Hello World`

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **Runbooks** под **автоматизацией процессов,** чтобы открыть список runbooks.
3. Создайте новый runbook, выбрав **Создать runbook**.
4. Присвойте модулю Runbook имя **MyFirstRunbook-PowerShell**.
5. В этом случае вы собираетесь создать [runbook PowerShell.](automation-runbook-types.md#powershell-runbooks) Выберите **PowerShell** для **типа Runbook**.
6. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="step-2---add-code-to-the-runbook"></a>Шаг 2. Добавление кода в Runbook

Можно либо напрямую ввести код в модуль Runbook, либо выбрать командлеты, модули Runbook и ресурсы из элемента управления "Библиотека" и добавить их к модулю Runbook с любыми связанными параметрами. Для этого учебника вы собираетесь ввести код непосредственно в книгу.

1. Ваша книга в настоящее время пуста. Введите `Write-Output "Hello World"` тело скрипта.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Сохраните модуль Runbook, щелкнув **Сохранить**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Шаг 3 - Проверьте книгу

Перед публикацией runbook, чтобы сделать его доступным в производстве, вы должны проверить его, чтобы убедиться, что он работает должным образом. Тестирование runbook запускает свою версию проекта и позволяет просматривать его вывод в интерактивном режиме.

1. Щелкните **Область тестирования**.
2. Щелкните **Пуск** , чтобы начать тестирование. Активным должен быть только этот параметр.
3. Обратите внимание, что [создается задание runbook](automation-runbook-execution.md) и отображается его статус в панели.

   Статус задания `Queued`начинается как, указывая, что задание ждет сотрудника runbook в облаке, чтобы стать доступным. Статус меняется `Starting` на время выполнения требования работника. Наконец, статус `Running` становится, когда запуск фактически начинает работать.

4. Когда задание runbook завершается, панель test отображает его выход. В этом случае, `Hello World`вы видите .

   ![Вывод области тестирования](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Закройте область тестирования, чтобы вернуться на холст.

## <a name="step-4---publish-and-start-the-runbook"></a>Шаг 4. Публикация и запуск модуля Runbook

Созданная книга по разработке по-прежнему находится в режиме Draft. Прежде чем запустить модуль в рабочей среде, его нужно опубликовать. При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. В нашем случае опубликованной версии не существует, поскольку модуль runbook создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
1. Прокрутите влево, чтобы просмотреть книгу на странице Runbooks, и обратите внимание, что значение **статуса автора** настроено на **опубликовано.**
1. Прокрутите экран вправо до области **MyFirstRunbook-PowerShell**.
   
   Параметры в верхней части позволяют начать runbook сейчас, запланировать будущее время начала, или создать [веб-крюк,](automation-webhooks.md) так что runbook может быть запущен через http вызова.
1. Чтобы запустить runbook, выберите **Запуск** и нажмите кнопку **Да** в появившемся запросе. 
1. Панель вакансий вакансии открыта для созданного задания runbook. Хотя вы можете закрыть это стекло, оставьте его открытым прямо сейчас, так что вы можете наблюдать за ходом работы. Статус задания отображается в **Резюме вакансий,** а возможные статусы описаны для тестирования runbook.

   ![Сводные данные задания](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Как только состояние `Completed`runbook показывает, щелкните **выход,** чтобы открыть страницу вывода, где вы можете увидеть `Hello World` отображаемые.

   ![Выходные данные задания](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Закройте страницу выходных данных.
1. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. Вы должны `Hello World` видеть только в потоке вывода.

    Обратите внимание, что панель потоков может отображать другие потоки для выполнения задания, такие как Verbose и Ошибка потоков, если runbook пишет им.

   ![Все журналы](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Закройте панель потоков и панель вакансий, чтобы вернуться на страницу **MyFirstRunbook-PowerShell.**
1. Под **подробная информация,** нажмите **Вакансии,** чтобы открыть страницу Вакансии для этого runbook. На этой странице перечислены все задания, созданные вашего runbook. Вы должны видеть только одну работу в списке, так как вы только запустить работу один раз.

   ![Список заданий](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Нажмите на имя задания, чтобы открыть ту же панель вакансий, что вы просматривали при запуске. Используйте эту панель для просмотра деталей любого задания, созданного для runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Шаг 5. Добавление проверки подлинности для управления ресурсами Azure

Вы протестировали и опубликовали свой модуль runbook, но пока он не выполняет никаких полезных действий. Нужно, чтобы он управлял ресурсами Azure. Для этого автордолжен иметь возможность аутентифицировать с помощью учетной записи Run As, которая была автоматически создана при создании учетной записи Автоматизации.

Как показано в приведенном ниже примере, соединение Run As производится с cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Если вы управляете ресурсами по нескольким подпискам, необходимо использовать `AzContext` параметр с [Get-AzContext.](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)

> [!NOTE]
> Для PowerShell runbooks, `Add-AzAccount` и `Add-AzureRMAccount` псевдонимы для `Connect-AzAccount`. Вы можете использовать эти cmdlets или вы можете [обновить свои модули](automation-update-azure-modules.md) в вашей учетной записи автоматизации до последних версий. Возможно, потребуется обновить модули, даже если вы только что создали новую учетную запись Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. Откройте текстовый редактор, **нажав** на страницу **MyFirstRunbook-PowerShell.**
1. Вам больше не `Write-Output` нужна линия. Просто идите вперед и удалите его.
1. Введите или скопируйте и вставьте следующий код, который обрабатывает аутентификацию с помощью учетной записи Automation Run As.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Щелкните **Тестовая область**, чтобы проверить модуль runbook.
1. Щелкните **Пуск** , чтобы начать тестирование. Как только он завершается, вы должны увидеть выход, похожий на следующий, отображающий основную информацию из вашей учетной записи. Этот вывод подтверждает допустимость учетной записи запуска от имени.

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Шаг 6. Добавление кода запуска виртуальной машины

Теперь, когда модуль runbook прошел аутентификацию в подписке Azure, вы можете управлять ресурсами. Давайте добавим команду для запуска виртуальной машины. Вы можете выбрать любую виртуальную машину в подписке Azure, и просто жесткий код, что имя в runbook на данный момент.

1. К сценарию runbook добавьте [cmdlet Start-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) чтобы запустить виртуальную машину. Как показано ниже, cmdlet запускает виртуальную `VMName` машину с `ResourceGroupName`именем и с группой ресурсов под названием .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Сохраните модуль runbook, а затем щелкните **Тестовая область**, чтобы проверить модуль.
1. Нажмите **Начало,** чтобы начать тест. Как только он завершается, убедитесь, что виртуальная машина началась.

## <a name="step-7---add-an-input-parameter"></a>Шаг 7 - Добавить параметр ввода

В настоящее время в вашем runbook запускается виртуальная машина, которую вы закодировали в runbook. Runbook будет более полезным, если вы укажете виртуальную машину, когда запуск запущен. Давайте добавим параметры ввода в книгу, чтобы обеспечить эту функциональность.

1. В текстовом редакторе `Start-AzVM` изменяйте cmdlet, чтобы `VMName` `ResourceGroupName`использовать переменные для параметров и . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Сохраните модуль Runbook и откройте область тестирования. Теперь вы можете указать значения двух входных переменных, которые будут использоваться в тесте.
1. Закройте область тестирования.
1. Щелкните **Опубликовать** , чтобы опубликовать новую версию модуля Runbook.
1. Остановите виртуальную машину, которую вы запустили ранее.
1. Щелкните **Пуск** , чтобы запустить модуль Runbook. 
1. Введите значения для **VMNAME** и **RESOURCEGROUPNAMEдля** для виртуальной машины, которую вы собираетесь запустить, а затем нажмите **OK**.<br><br> ![Передача параметра](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Когда runbook завершается, убедитесь, что виртуальная машина была запущена.

## <a name="next-steps"></a>Дальнейшие действия

* Для получения дополнительной информации о PowerShell, включая [PowerShell Docs](/powershell/scripting/overview)языковые справочные и учебные модули, см.
* Чтобы начать работу с графических runbooks, [см.](automation-first-runbook-graphical.md)
* Сведения о том, как начать работу с модулями Runbook рабочих процессов PowerShell, см. в статье [Первый Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md).
* Чтобы узнать больше о типах runbook [Azure Automation runbook types](automation-runbook-types.md)и их преимуществах и ограничениях, см.
* Для получения дополнительной информации о функции поддержки скриптов PowerShell [см.](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
