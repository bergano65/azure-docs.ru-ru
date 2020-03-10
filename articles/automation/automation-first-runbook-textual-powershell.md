---
title: Мой первый модуль runbook PowerShell в службе автоматизации Azure
description: Учебник, в котором описывается создание, тестирование и публикация простого модуля Runbook PowerShell.
keywords: Azure PowerShell, руководство по скриптам PowerShell, инструменты автоматизации PowerShell
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: b9808ddc3b61b0055642c5a0f2a82b0dc7553b33
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384858"
---
# <a name="my-first-powershell-runbook"></a>Мой первый модуль Runbook PowerShell

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

В этом руководстве описана процедура создания [модуля Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) в службе автоматизации Azure. Начните с простого модуля Runbook, который можно протестировать и опубликовать, а также узнать, как отвести состояние задания Runbook. Затем измените модуль Runbook, чтобы он фактически управлял ресурсами Azure, в этом случае запускает виртуальную машину Azure. Выполните инструкции из руководства, чтобы сделать модуль Runbook более надежным, добавив параметры Runbook.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется:

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись службы автоматизации](automation-quickstart-create-account.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure. Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Так как вы останавливаете и запускаете этот компьютер, он не должен быть рабочей виртуальной машиной.
* При необходимости [импортируйте модули Azure](shared-resources/modules.md) или [Обновите модули](automation-update-azure-modules.md) на основе используемых командлетов.

## <a name="differences-from-powershell-workflow-runbooks"></a>Отличия от модулей Runbook рабочих процессов PowerShell

Модули Runbook PowerShell имеют тот же жизненный цикл, возможности и управление, что и модули Runbook рабочего процесса PowerShell. Однако существуют некоторые различия и ограничения.

* Модули Runbook PowerShell выполняются быстрее по сравнению с модулями Runbook рабочих процессов PowerShell, так как они не используют этап компиляции.
* Книги PowerShell не поддерживают контрольные точки, которые используются в модулях Runbook рабочего процесса PowerShell. С помощью контрольных точек модули Runbook рабочих процессов PowerShell могут возобновлять работу с любой точки. Модуль Runbook PowerShell может возобновить работу только с самого начала.
* Модули Runbook PowerShell могут выполнять команды только последовательно. Модули Runbook рабочих процессов PowerShell поддерживают как последовательные, так и параллельные выполнения.
* Для модуля Runbook PowerShell все в скрипте выполняется в одном пространстве выполнения. В модуле Runbook рабочего процесса PowerShell действие, команда или блок скрипта могут содержать собственные пространства выполнения. 
* Модули Runbook PowerShell имеют некоторые [синтаксические отличия](https://technet.microsoft.com/magazine/dn151046.aspx) от модулей Runbook рабочих процессов PowerShell.

## <a name="step-1---create-runbook"></a>Шаг 1. Создание нового модуля Runbook

Для начала вы создадите простой модуль runbook, при помощи которого на экран выводится текст **Hello World**.

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **модули Runbook** в разделе **Автоматизация процессов** , чтобы открыть список модулей Runbook.
3. Создайте новый Runbook, выбрав **создать Runbook**.
4. Присвойте модулю Runbook имя **MyFirstRunbook-PowerShell**.
5. В этом случае будет создан [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks). Выберите **PowerShell** для **типа Runbook**.
6. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="step-2---add-code-to-the-runbook"></a>Шаг 2. Добавление кода в Runbook

Можно либо напрямую ввести код в модуль Runbook, либо выбрать командлеты, модули Runbook и ресурсы из элемента управления "Библиотека" и добавить их к модулю Runbook с любыми связанными параметрами. В этом руководстве мы будем вводить код непосредственно в модуль Runbook.

1. Модуль Runbook сейчас пуст. Введите `Write-Output "Hello World"` в тексте скрипта.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Сохраните модуль Runbook, щелкнув **Сохранить**.

## <a name="step-3---test-the-runbook"></a> Шаг 3. Тестирование модуля Runbook

Прежде чем публиковать модуль Runbook, чтобы сделать его доступным в рабочей среде, необходимо проверить его, чтобы убедиться, что он работает правильно. Тестирование модуля Runbook запускает его черновую версию и позволяет просматривать выходные данные в интерактивном режиме.

1. Щелкните **Область тестирования**.
2. Щелкните **Пуск** , чтобы начать тестирование. Активным должен быть только этот параметр.
3. Обратите внимание, что создается [Задание Runbook](automation-runbook-execution.md) , а его состояние отображается на панели.

   Состояние задания начинается в **очереди**, что означает, что задание ожидает, пока Рабочая роль Runbook в облаке станет доступной. Состояние меняется на **Запуск** , когда Рабочая роль заявляет задание. Наконец, при запуске модуля Runbook происходит состояние **выполняется** .

4. После завершения задания Runbook на панели "тест" отображаются выходные данные. В этом случае вы увидите текст **Hello World**.

   ![Вывод области тестирования](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Закройте область тестирования, чтобы вернуться на холст.

## <a name="step-4---publish-and-start-the-runbook"></a>Шаг 4. Публикация и запуск модуля Runbook

Созданный модуль Runbook по-прежнему находится в режиме черновика. Прежде чем запустить модуль в рабочей среде, его нужно опубликовать. При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. В нашем случае опубликованной версии не существует, поскольку модуль runbook создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
1. Прокрутите влево, чтобы просмотреть модуль Runbook на странице модули Runbook, и обратите внимание, что для параметра **Состояние создания** установлено значение **Опубликовано**.
1. Прокрутите экран вправо до области **MyFirstRunbook-PowerShell**.
   
   Параметры в верхней части окна позволяют запустить модуль Runbook сейчас, запланировать время запуска в будущем или создать веб- [перехватчик](automation-webhooks.md) , чтобы модуль Runbook можно было запустить через вызов HTTP.
1. Чтобы запустить runbook, выберите **Запуск** и нажмите кнопку **Да** в появившемся запросе. 
1. Откроется область задания для задания Runbook, которое было создано. Хотя эту область можно закрыть, оставьте ее открытой, чтобы можно было наблюдать за ходом выполнения задания. Состояние задания отображается в **сводке по заданию**, а возможные состояния — как описано в разделе Тестирование модуля Runbook.

   ![Сводные данные задания](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Когда состояние Runbook отобразится как **завершенное**, нажмите кнопку **выходные данные** , чтобы открыть страницу вывод, на которой можно увидеть **Hello World** .

   ![Выходные данные задания](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Закройте страницу выходных данных.
1. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. В потоке вывода должны отображаться только **Hello World** .

    Обратите внимание, что на панели «потоки» могут отображаться другие потоки для задания Runbook, такие как потоки подробных сведений и ошибок, если Runbook выполняет запись в них.

   ![Все журналы](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Закройте панель потоки и область заданий, чтобы вернуться на страницу **MyFirstRunbook-PowerShell** .
1. В разделе **сведения**щелкните **задания** , чтобы открыть страницу задания для этого модуля Runbook. На этой странице перечислены все задания, созданные модулем Runbook. Должно отобразиться только одно задание, так как задание выполняется только один раз.

   ![Список заданий](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Щелкните имя задания, чтобы открыть ту же область заданий, которая была просмотрена при запуске модуля Runbook. Используйте эту панель для просмотра сведений о любом задании, созданном для модуля Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Шаг 5. Добавление проверки подлинности для управления ресурсами Azure

Вы протестировали и опубликовали свой модуль runbook, но пока он не выполняет никаких полезных действий. Нужно, чтобы он управлял ресурсами Azure. Для этого модуль Runbook должен иметь возможность пройти проверку подлинности с помощью учетной записи запуска от имени, которая была создана автоматически при создании учетной записи службы автоматизации.

Как показано в примере ниже, подключение запуска от имени устанавливается с помощью командлета [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) . При управлении ресурсами в нескольких подписках необходимо использовать параметр *азконтекст* вместе с командлетом [Get-азконтекст](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Для модулей Runbook PowerShell **Add-азаккаунт** и **Add-AzureRMAccount** являются псевдонимами для **Connect-азаккаунт**. Вы можете использовать эти командлеты или [обновить модули](automation-update-azure-modules.md) в учетной записи службы автоматизации до последних версий. Вам может потребоваться обновить модули, даже если вы только что создали новую учетную запись службы автоматизации.

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
1. Откройте текстовый редактор, нажав кнопку **изменить** на странице **MyFirstRunbook-PowerShell** .
1. Строка **Write-Output** больше не нужна. Просто удалите его.
1. Введите или скопируйте и вставьте следующий код, который обрабатывает проверку подлинности с помощью учетной записи запуска от имени службы автоматизации.

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
1. Щелкните **Пуск** , чтобы начать тестирование. По завершении вы увидите результат, аналогичный приведенному ниже, который отображает основные сведения из вашей учетной записи. Этот вывод подтверждает допустимость учетной записи запуска от имени.

   ![Проверка подлинности](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Шаг 6. Добавление кода запуска виртуальной машины

Теперь, когда модуль runbook прошел аутентификацию в подписке Azure, вы можете управлять ресурсами. Добавим команду для запуска виртуальной машины. Вы можете выбрать любую виртуальную машину в подписке Azure и просто жестко это имя в модуле Runbook.

1. Добавьте в скрипт Runbook командлет [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) , чтобы запустить виртуальную машину. Как показано ниже, командлет запускает виртуальную машину с именем **VMName** и группой ресурсов с именем **ResourceGroupName**.

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
1. Нажмите кнопку **начать** , чтобы начать тест. После завершения работы убедитесь, что виртуальная машина запущена.

## <a name="step-7---add-an-input-parameter"></a>Шаг 7. Добавление входного параметра

Модуль Runbook в настоящее время запускает виртуальную машину, закодированную в модуле Runbook. Модуль Runbook будет полезнее, если вы укажете виртуальную машину при запуске модуля Runbook. Давайте добавим входные параметры в модуль Runbook, чтобы обеспечить эту функциональность.

1. В текстовом редакторе измените командлет **Start-AzVM** , чтобы использовать переменные для параметров *VMName* и *ResourceGroupName*. 

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
1. Завершите работу виртуальной машины, которая была запущена ранее.
1. Щелкните **Пуск** , чтобы запустить модуль Runbook. 
1. Введите значения для **VMNAME** и **RESOURCEGROUPNAME** для виртуальной машины, которую вы будете запускать, и нажмите кнопку **ОК**.<br><br> ![Передача параметра](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. После завершения работы модуля Runbook убедитесь, что виртуальная машина запущена.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о PowerShell, включая Справочник по языку и обучающие модули, см. в документации по [PowerShell](/powershell/scripting/overview).
* Чтобы приступить к работе с графическими модулями Runbook, см. раздел [Мой первый графический модуль Runbook](automation-first-runbook-graphical.md).
* Сведения о том, как начать работу с модулями Runbook рабочих процессов PowerShell, см. в статье [Первый Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md).
* Дополнительные сведения о типах модулей Runbook и их преимуществах и ограничениях см. в статье [типы Runbook службы автоматизации Azure](automation-runbook-types.md).
* Дополнительные сведения о функции поддержки сценариев PowerShell см. в статье [Поддержка сценариев PowerShell в службе автоматизации Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
