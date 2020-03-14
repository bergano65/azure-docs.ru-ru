---
title: Первый runbook рабочего процесса PowerShell в службе автоматизации Azure
description: Учебник, в котором рассказывается, как создать, протестировать и опубликовать простой текстовый Runbook с помощью рабочего процесса PowerShell.
keywords: рабочий процесс PowerShell, примеры рабочего процесса powershell, рабочие процессы powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367267"
---
# <a name="my-first-powershell-workflow-runbook"></a>Первый Runbook рабочего процесса PowerShell

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

В данном учебнике описана процедура создания [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) в службе автоматизации Azure. Начните с простого модуля Runbook, который протестируется и публикуется, чтобы узнать, как отвести состояние задания Runbook. Затем измените модуль Runbook, чтобы он фактически управлял ресурсами Azure, проиллюстрированный запуском виртуальной машины Azure. Наконец, сделайте модуль Runbook более надежным, добавив параметры Runbook.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>предварительные требования

Для работы с этим учебником необходимы указанные ниже компоненты.

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись службы автоматизации](automation-offering-get-started.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure. Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Так как вы останавливаете и запускаете этот компьютер, он не должен быть рабочей виртуальной машиной.

## <a name="step-1---create-new-runbook"></a>Шаг 1. Создание нового модуля Runbook

Начните с создания простого модуля Runbook, который выводит текст `Hello World`.

1. На портале Azure выберите свою учетную запись службы автоматизации.

   Страница учетной записи в службе автоматизации позволяет быстро получить представление о ресурсах, доступных в этой учетной записи. Некоторые ресурсы уже должны быть доступны. Большая часть этих ресурсов включает модули, автоматически включаемые в новую учетную запись службы автоматизации. У вас также должен быть ресурс учетных данных, связанный с подпиской.
 
1. Выберите **модули Runbook** в разделе **Автоматизация процессов** , чтобы открыть список модулей Runbook.
1. Создайте новый Runbook, выбрав **создать Runbook**.
1. Присвойте Runbook имя **MyFirstRunbook-Workflow**.
1. В этом случае будет создан [Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Выберите **Рабочий процесс PowerShell** для **типа Runbook**.
1. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="step-2---add-code-to-the-runbook"></a>Шаг 2. Добавление кода в Runbook

Можно либо ввести код непосредственно в модуль Runbook, либо выбрать командлеты, модули Runbook и ресурсы из элемента управления "Библиотека" и добавить их в модуль Runbook с любыми связанными параметрами. В этом руководстве вы вводите код непосредственно в модуль Runbook.

1. В настоящее время модуль Runbook пуст только с обязательным `Workflow` ключевым словом, именем модуля Runbook и фигурными скобками, добавлен весь рабочий процесс.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Введите `Write-Output "Hello World"` между фигурными скобками.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Сохраните модуль Runbook, щелкнув **Сохранить**.

## <a name="step-3---test-the-runbook"></a>Шаг 3. Тестирование модуля Runbook

Прежде чем публиковать модуль Runbook, чтобы сделать его доступным в рабочей среде, необходимо проверить его, чтобы убедиться, что он работает правильно. Тестирование модуля Runbook запускает его черновую версию и позволяет просматривать выходные данные в интерактивном режиме.

1. Выберите **область тестирования** , чтобы открыть область тестирования.
1. Нажмите кнопку **Пуск** , чтобы начать тест с проверкой только включенного параметра.
1. Обратите внимание, что создается [Задание Runbook](automation-runbook-execution.md) , а его состояние отображается на панели.

   Состояние задания начинается как `Queued`, что означает, что задание ожидает, пока Рабочая роль Runbook в облаке станет доступной. Состояние меняется на `Starting`, когда Рабочая роль заявляет задание. Наконец, состояние `Running`, когда модуль Runbook фактически начинает выполняться.

1. После завершения задания Runbook на панели "тест" отображаются выходные данные. В этом случае вы увидите `Hello World`.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Закройте область тестирования, чтобы вернуться на холст.

## <a name="step-4---publish-and-start-the-runbook"></a>Шаг 4. Публикация и запуск модуля Runbook

Созданный Runbook все еще находится в черновом режиме. Его необходимо опубликовать перед запуском в рабочей среде. При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. В нашем случае опубликованной версии не существует, поскольку модуль runbook создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
1. Прокрутите влево, чтобы просмотреть модуль Runbook на странице **модули Runbook** , и обратите внимание, что для поля **состояние разработки** установлено значение **Опубликовано**.
1. Прокрутите экран вправо, чтобы просмотреть страницу для **MyFirstRunbook-Workflow**.

   Параметры в верхней части окна позволяют запустить модуль Runbook сейчас, запланировать время запуска в будущем или создать веб- [перехватчик](automation-webhooks.md) , чтобы модуль Runbook можно было запустить через вызов HTTP.

1. Чтобы запустить runbook, выберите **Запуск** и нажмите кнопку **Да** в появившемся запросе.

   ![Запуск модуля Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Откроется область задания для задания Runbook, которое было создано. В этом случае оставьте панель открытой, чтобы можно было наблюдать за ходом выполнения задания.

1. Обратите внимание, что состояние задания отображается в **сводке по заданию**. Это состояние соответствует состояниям, которые вы видели при тестировании модуля Runbook.

   ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Когда в модуле Runbook отобразится `Completed`, нажмите кнопку **выходные данные**. Откроется страница выходных данных, на которой можно просмотреть сообщение `Hello World`.

   ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Закройте страницу выходных данных.

1. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. В потоке вывода должны отображаться только `Hello World`. Обратите внимание, что на панели «потоки» могут отображаться другие потоки для задания Runbook, такие как потоки подробных сведений и ошибок, если Runbook выполняет запись в них.

   ![Сводные данные задания](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Закройте панель потоки и область задания, чтобы вернуться на страницу **MyFirstRunbook** .
1. Щелкните **задания** в разделе **ресурсы** , чтобы открыть страницу задания для этого модуля Runbook. На этой странице перечислены все задания, созданные модулем Runbook. Должно отобразиться только одно задание, так как задание выполняется только один раз.

   ![Задания](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Щелкните имя задания, чтобы открыть ту же область заданий, которая была просмотрена при запуске модуля Runbook. Используйте эту панель для просмотра сведений о любом задании, созданном для модуля Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Шаг 5. Добавление проверки подлинности для управления ресурсами Azure

Вы протестировали и опубликовали свой модуль runbook, но пока он не выполняет никаких полезных действий. Нужно, чтобы он управлял ресурсами Azure. Это не может сделать это, если только не будет выполнена проверка подлинности с использованием учетных данных для подписки. Для проверки подлинности используется командлет `Connect-AzAccount`.

>[!NOTE]
>Для модулей Runbook PowerShell `Add-AzAccount` и `Add-AzureRMAccount` являются псевдонимами для `Connect-AzAccount`. Вы можете использовать эти командлеты или [обновить модули](automation-update-azure-modules.md) в учетной записи службы автоматизации до последних версий. Вам может потребоваться обновить модули, даже если вы только что создали новую учетную запись службы автоматизации.

1. Перейдите на страницу **MyFirstRunbook-Workflow** и откройте текстовый редактор, нажав кнопку **изменить**.
2. Удалите `Write-Output` строку.
3. Поместите курсор в пустую строку между фигурными скобками.
4. Введите или скопируйте и вставьте следующий код, который обрабатывает проверку подлинности с помощью учетной записи запуска от имени службы автоматизации.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Щелкните **Тестовая область**, чтобы проверить модуль runbook.
1. Щелкните **Пуск** , чтобы начать тестирование. По завершении вы увидите результат, аналогичный приведенному ниже, который отображает основные сведения из вашей учетной записи. Это действие подтверждает допустимость учетных данных.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Шаг 6. Добавление кода запуска виртуальной машины

Теперь, когда модуль Runbook проходит проверку подлинности в подписке Azure, вы можете управлять ресурсами. Добавим команду для запуска виртуальной машины. Вы можете выбрать любую виртуальную машину в подписке Azure. Теперь вы прописано это имя в Runbook. Если вы управляете ресурсами в нескольких подписках, необходимо использовать параметр `AzContext` с командлетом [Get-азконтекст](/powershell/module/az.accounts/get-azcontext) .

1. Укажите имя и имя группы ресурсов для запуска виртуальной машины, введя вызов командлета [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) , как показано ниже. 

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
1. Щелкните **Пуск** , чтобы начать тестирование. После завершения убедитесь, что виртуальная машина запущена.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Шаг 7. Добавление входного параметра в модуль Runbook

Сейчас модуль Runbook запускает виртуальную машину, которая жестко закодирована в модуле Runbook. Это будет более полезно, если вы можете указать виртуальную машину при запуске модуля Runbook. Давайте добавим входные параметры в модуль Runbook, чтобы обеспечить эту функциональность.

1. Добавьте в модуль Runbook переменные для параметров `VMName` и `ResourceGroupName` и используйте переменные с командлетом `Start-AzVM`, как показано ниже.

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
5. Останавливает запущенную виртуальную машину.
6. Щелкните **Пуск** , чтобы запустить модуль Runbook. 
7. Введите значения для **VMNAME** и **RESOURCEGROUPNAME** для виртуальной машины, которую вы будете запускать.

   ![Запуск модуля Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. После завершения работы модуля Runbook убедитесь, что виртуальная машина запущена.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о PowerShell, включая Справочник по языку и обучающие модули, см. в документации по [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Чтобы приступить к работе с графическими модулями Runbook, см. раздел [Мой первый графический модуль Runbook](automation-first-runbook-graphical.md).
* Сведения о том, как начать работу с модулями Runbook, см. в статье [Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Дополнительные сведения о типах модулей Runbook и их преимуществах и ограничениях см. в статье [типы Runbook службы автоматизации Azure](automation-runbook-types.md).
* Дополнительные сведения о функциях поддержки скриптов PowerShell см. [в статье поддержка сценариев PowerShell в службе автоматизации Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
