---
title: Мой первый модуль runbook PowerShell в службе автоматизации Azure
description: Учебник, в котором описывается создание, тестирование и публикация простого модуля Runbook PowerShell.
keywords: Azure PowerShell, руководство по скриптам PowerShell, инструменты автоматизации PowerShell
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f083a5a9fe8027467eb95711a15725859f59e4fa
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438054"
---
# <a name="my-first-powershell-runbook"></a>Мой первый модуль Runbook PowerShell

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

В этом руководстве описана процедура создания [модуля Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) в службе автоматизации Azure. Сначала вы протестируете и опубликуете простой модуль runbook и узнаете, как отслеживать состояние его заданий. Затем мы изменим модуль runbook, настроив его для фактического управления ресурсами Azure (в нашем примере это запуск виртуальной машины Azure). Затем вы сделаете этот модуль runbook еще надежнее, добавив параметры runbook.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись службы автоматизации](automation-quickstart-create-account.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure. Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Это не должна быть рабочая виртуальная машина, так как для работы с этим руководством ее нужно будет остановить и запустить заново.

## <a name="create-new-runbook"></a>Создание модуля Runbook

Для начала создайте простой модуль runbook, выводящий на экран текст *Hello World*.

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **Модули Runbook** в разделе **Автоматизация процессов**, чтобы открыть список модулей.
3. Создайте модуль runbook. Для этого нажмите кнопку **+ Добавить Runbook**, а затем выберите **Создать новый Runbook**.
4. Присвойте модулю Runbook имя *MyFirstRunbook-PowerShell*.
5. В этом случае вы создадите [модуль runbook PowerShell](automation-runbook-types.md#powershell-runbooks), поэтому для параметра **Тип Runbook** выберите значение **PowerShell**.
6. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="add-code-to-the-runbook"></a>Добавление кода в модуль Runbook

Можно либо напрямую ввести код в модуль Runbook, либо выбрать командлеты, модули Runbook и ресурсы из элемента управления "Библиотека" и добавить их к модулю Runbook с любыми связанными параметрами. В этом пошаговом руководстве вы введете код напрямую в модуль runbook.

1. Сейчас модуль runbook пуст, поэтому введите *Write-Output "Hello World"*. в текст скрипта.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. Сохраните модуль Runbook, щелкнув **Сохранить**.

##<a name="a-namestep-3---test-the-runbook-test-the-runbook"></a><a name="step-3---test-the-runbook"> Тестирование модуля Runbook

Прежде чем опубликовать модуль runbook и, таким образом, сделать его доступным для рабочей среды, необходимо проверить, правильно ли он работает. Чтобы протестировать модуль Runbook, нужно запустить его **черновую** версию и проверить его работу в интерактивном режиме.

1. Щелкните **Область тестирования**.
2. Щелкните **Пуск** , чтобы начать тестирование. Активным должен быть только этот параметр.
3. При этом создается [задание Runbook](automation-runbook-execution.md) и отображается его состояние.

   Сначала задание получает состояние *В очереди*, которое означает, что задание ожидает доступа к рабочей роли runbook в облаке. Как только рабочая роль запросит задание, оно получит состояние *Запущено*, а после того как модуль runbook начнет выполняться, состояние изменится на *Выполняется*.  

4. Когда задание модуля Runbook будет выполнено, на экране появится результат. В вашем случае отобразится текст *Hello World*.

   ![Вывод области тестирования](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. Закройте область тестирования, чтобы вернуться на холст.

## <a name="publish-and-start-the-runbook"></a>Публикация и запуск модуля Runbook

Модуль runbook, который вы только что создали, все еще находится в режиме черновика. Прежде чем запустить модуль в рабочей среде, его нужно опубликовать.  При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией. У вас пока нет опубликованной версии, так как модуль runbook создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
1. Если сейчас прокрутить экран влево, чтобы просмотреть runbook на панели **Модули Runbook**, для параметра **Состояние создания** отобразится значение **Опубликовано**.
1. Прокрутите экран вправо до области **MyFirstRunbook-PowerShell**.  
   Параметры в верхней части экрана позволяют запускать и просматривать модуль runbook, назначать его запуск в определенное время в будущем, а также создавать вызовы [webhook](automation-webhooks.md), чтобы запускать модуль с помощью HTTP-вызова.
1. Нужно запустить модуль runbook, поэтому щелкните **Запустить**, а затем, когда откроется страница "Запуск Runbook", нажмите кнопку **ОК**.
1. Откроется страница заданий с созданным заданием runbook. Эту область можно закрыть, но пока оставьте ее открытой, чтобы следить за ходом выполнения задания.
1. Состояние задания отображается в поле **Сводка по заданию** и отражает состояния, которые вы наблюдали при тестировании модуля runbook.

   ![Сводные данные задания](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Когда в разделе **Обзор** состояние модуля runbook изменится на *Выполнено*, щелкните **Выходные данные**. Откроется область "Выходные данные" с текстом *Hello World*.

   ![Выходные данные задания](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Закройте страницу выходных данных.
1. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. В потоке вывода должен отображаться только текст *Hello World*, но могут присутствовать и другие потоки заданий runbook, например "Подробные сведения" и "Ошибка", если модуль записывает в них данные.

   ![Все журналы](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Закройте страницы "Потоки" и "Задание", чтобы вернуться на страницу MyFirstRunbook-PowerShell.
1. В разделе **Сведения** щелкните **Задания**, чтобы открыть область "Задания" для этого модуля runbook. Откроется страница всех заданий, созданных этим модулем Runbook. В нем должно быть только одно задание, так как вы запустили задание только один раз.

   ![Список заданий](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. Если щелкнуть это задание, откроется та же область заданий, что и при запуске модуля runbook. С помощью этого действия можно вернуться назад и просмотреть сведения о любом задании, созданном для конкретного модуля Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Добавление аутентификации для управления ресурсами Azure

Вы протестировали и опубликовали свой модуль runbook, но пока он не выполняет никаких полезных действий. Нужно, чтобы он управлял ресурсами Azure. Это станет возможным, только если модуль Runbook пройдет проверку подлинности с использованием подключения запуска от имени, которое создается автоматически при создании вашей учетной записи службы автоматизации. Подключение запуска от имени используется с командлетом **Connect-AzureRmAccount**. Если вы управляете ресурсами в нескольких подписках, вам нужно использовать команду [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext) с параметром **-AzureRmContext**.

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Откройте текстовый редактор, щелкнув **Изменить** на странице MyFirstRunbook-PowerShell.
1. Вам больше не потребуется использовать строку **Write-Output**, поэтому удалите ее.
1. Введите или скопируйте и вставьте следующий код, который выполнит проверку подлинности с помощью учетной записи запуска от имени службы автоматизации:

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** и **Login-AzureRmAccount** теперь являются псевдонимами для **Connect-AzureRMAccount**. Если командлет **Connect-AzureRMAccount** отсутствует, вы можете использовать командлет **Add-AzureRmAccount** или **Login-AzureRmAccount** либо обновить модули в своей учетной записи службы автоматизации до последних версий.

1. Щелкните **Тестовая область**, чтобы проверить модуль runbook.
1. Щелкните **Пуск** , чтобы начать тестирование. После его завершения на экране должны отобразиться приблизительно такие основные сведения из вашей учетной записи. Этот вывод подтверждает допустимость учетной записи запуска от имени.

   ![Проверка подлинности](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Добавление кода запуска виртуальной машины

Теперь, когда модуль runbook прошел аутентификацию в подписке Azure, вы можете управлять ресурсами. Добавьте команду для запуска виртуальной машины. Вы можете выбрать любую виртуальную машину в своей подписке Azure. Имя этой машины вы пропишете в модуле runbook.

1. После команды *Connect-AzureRmAccount* введите команду *Start-AzureRmVM -Name 'имя_ВМ' -ResourceGroupName 'имя_группы_ресурсов'*, указав имя виртуальной машины, которую нужно запустить, и имя ее группы ресурсов.  

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Сохраните модуль runbook, а затем щелкните **Тестовая область**, чтобы проверить модуль.
1. Щелкните **Пуск** , чтобы начать тестирование. После завершения теста проверьте, запущена ли виртуальная машина.

## <a name="add-an-input-parameter"></a>Добавление входного параметра

Созданный модуль runbook сейчас запускает виртуальную машину, указанную в коде runbook, но лучше указать виртуальную машину при запуске runbook. Для этого добавьте в модуль runbook входные параметры.

1. Добавьте значения для параметров *VMName* и *ResourceGroupName* в модуле runbook и используйте эти переменные в командлете **Start-AzureRmVM**, как показано в следующем примере.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Сохраните модуль Runbook и откройте область тестирования. Теперь можно указать значения двух входных переменных, которые будут использоваться в тесте.
1. Закройте область тестирования.
1. Щелкните **Опубликовать** , чтобы опубликовать новую версию модуля Runbook.
1. Остановите виртуальную машину, запущенную на предыдущем этапе.
1. Нажмите кнопку **ОК**, чтобы запустить модуль Runbook. Введите значения **VMName** и **ResourceGroupName** для виртуальной машины, которую нужно запустить.<br><br> ![Передача параметра](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
1. Когда модуль Runbook будет выполнен, проверьте, запустилась ли виртуальная машина.

## <a name="differences-from-powershell-workflow"></a>Отличия от рабочего процесса PowerShell

Модули runbook PowerShell имеют тот же жизненный цикл, возможности и средства управления, что и модули runbook рабочих процессов PowerShell, но существуют некоторые различия и ограничения.

1. Модули Runbook PowerShell выполняются быстрее (по сравнению с модулями Runbook рабочих процессов PowerShell), так как они не имеют этапа компиляции.
2. Модули Runbook рабочего процесса PowerShell поддерживают контрольные точки, с помощью которых их выполнение можно возобновить с любого момента в Runbook. Модули Runbook PowerShell можно возобновить только с самого начала.
3. Модули Runbook PowerShell поддерживают параллельное и последовательное выполнение. Модули Runbook PowerShell могут выполнять команды только последовательно.
4. В модуле Runbook рабочего процесса PowerShell действие, команда или блок скрипта могут содержать собственные пространства выполнения. В модуле Runbook PowerShell все содержимое скрипта выполняется в одном пространстве выполнения. Кроме того, существуют некоторые [синтаксические различия](https://technet.microsoft.com/magazine/dn151046.aspx) между собственным модулем Runbook PowerShell и модулем Runbook рабочих процессов PowerShell.

## <a name="next-steps"></a>Дополнительная информация

* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
* Чтобы получить дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях, обратитесь к статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md)
* Дополнительные сведения о функции поддержки скриптов PowerShell см. в статье [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Встроенная поддержка скриптов PowerShell в службе автоматизации Azure).
