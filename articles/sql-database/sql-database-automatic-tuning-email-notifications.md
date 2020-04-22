---
title: Автоматическая настройка уведомлений по электронной почте как руководство
description: Сведения о включении уведомлений по электронной почте об автоматической настройке службы "База данных SQL Azure".
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: b3b235833e794e48ae655d184bf938effc0d7ac0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768380"
---
# <a name="email-notifications-for-automatic-tuning"></a>Уведомления по электронной почте об автоматической настройке

Рекомендации по настройке базы данных SQL создает функция [автоматической настройки](sql-database-automatic-tuning.md) службы "База данных SQL Azure". Это решение постоянно отслеживает и анализирует рабочие нагрузки баз данных SQL, предоставляя для каждой отдельной базы данных специализированные рекомендации по настройке, связанные с созданием индексов, удалением индексов и оптимизацией планов выполнения запросов.

Рекомендации по автоматической настройке базы данных SQL можно просмотреть на [портале Azure](sql-database-advisor-portal.md), а также получить с помощью вызовов [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) либо с помощью команд [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) и [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction). Эта статья основана на использовании сценария PowerShell для получения рекомендаций по автоматической настройке.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Для этих cmdlets, см [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Автоматизация уведомлений по электронной почте о рекомендациях по автоматической настройке

Приведенное ниже решение автоматизирует отправку уведомлений по электронной почте, содержащих рекомендации по автоматической настройке. Описываемое решение включает в себя автоматизацию выполнения сценария PowerShell для получения рекомендаций по настройке с помощью [службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro) и автоматизацию планирования задания доставки электронных сообщений с помощью [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure

Чтобы использовать службу автоматизации Azure, сначала следует создать учетную запись службы автоматизации и настроить для нее ресурсы Azure, чтобы выполнять сценарий PowerShell. Дополнительные сведения об использовании службы автоматизации Azure см. в статье [Начало работы со службой автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Выполните следующие действия, чтобы создать учетную запись службы автоматизации Azure, выбрав и настроив приложение Automation из Marketplace.

1. Войдите на портал Azure.
1. Нажмите на кнопку **"Создайте ресурс"** в левом верхнем углу.
1. Поиск "**Автоматизация**" (нажмите ввод).
1. Нажмите на приложение Automation в результатах поиска.

    ![Добавление службы автоматизации Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

1. Оказавшись внутри панели "Создать учетную запись автоматизации", нажмите на кнопку **"Создать".**
1. Заселите требуемую информацию: введите имя для этой учетной записи автоматизации, выберите идентификатор подписки Azure и ресурсы Azure, которые будут использоваться для выполнения сценария PowerShell.
1. Для**опции «Создайте Azure Run As account»** выберите **«Да»,** чтобы настроить тип учетной записи, под которым скрипт PowerShell работает с помощью Azure Automation. Чтобы узнать больше о типах учетных записей, [см.](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
1. Заключите создание учетной записи автоматизации, нажав на **Create**.

> [!TIP]
> Запишите имя учетной записи службы автоматизации Azure, идентификатор подписки и ресурсы (например, скопировав и вставив их в Блокнот), введенные при создании приложения Automation. Эти сведения потребуются вам позже.
>

Если у вас несколько подписок Azure, для которых вы хотите использовать одинаковые параметры автоматизации, необходимо повторить эту процедуру для других подписок.

## <a name="update-azure-automation-modules"></a>Обновление модулей службы автоматизации Azure

В скрипте PowerShell для получения рекомендации по автоматическому тюнингу используются команды [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) и [Get-AzSqlDatabaseRecommendedAction,](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) для которых требуется версия Azure Module 4 и выше.

- В случае необходимости обновления модулей [Az module support in Azure Automation](../automation/shared-resources/modules.md)Azure см.

## <a name="create-azure-automation-runbook"></a>Создание runbook службы автоматизации Azure

Следующим шагом является создание модуля runbook в службе автоматизации Azure, содержащего сценарий PowerShell для получения рекомендаций по настройке.

Выполните приведенные ниже действия, чтобы создать runbook службы автоматизации Azure

1. Получите доступ к учетной записи Azure Automation, созданной на предыдущем этапе.
1. После того, как в панели учетной записи автоматизации, нажмите на пункт меню **"Runbooks"** на левой стороне, чтобы создать новую книгу автоматизации Azure с помощью сценария PowerShell. Чтобы узнать больше о создании модулей runbook службы автоматизации, ознакомьтесь с разделом [Создание или импорт модуля Runbook в службе автоматизации Azure](../automation/manage-runbooks.md#creating-a-runbook).
1. Чтобы добавить новый runbook, нажмите на опцию меню **«Добавить runbook»,** а затем нажмите на кнопку **«Быстрое создание – Создайте новый runbook».**
1. В панели Runbook введите имя вашего runbook (для целей этого примера, "**AutomaticTuningEmailAutomation**" используется), выберите тип runbook как **PowerShell** и напишите описание этого runbook, чтобы описать его назначение.
1. Нажмите на кнопку **«Создание»,** чтобы закончить создание нового runbook.

    ![Добавление runbook службы автоматизации Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Выполните следующие действия, чтобы загрузить сценарий PowerShell в созданный runbook.

1. Внутри панели **"Edit PowerShell Runbook",** выберите "**RUNBOOKS**" в дереве меню и расширьте представление, пока не увидите название своей книги (в этом примере "**AutomaticTuningEmailAutomation**"). Выберите этот runbook.
1. На первой строке "Edit PowerShell Runbook" (начиная с числа 1), копировать-вставить следующий код сценария PowerShell. Этот сценарий PowerShell предоставляется "как есть" и дает возможность приступить к работе. Измените его в соответствии со своими потребностями.

В заголовке предоставленного сценария PowerShell необходимо заменить `<SUBSCRIPTION_ID_WITH_DATABASES>` своим идентификатором подписки Azure. Чтобы узнать, как получить идентификатор подписки Azure, прочитайте раздел [Getting your Azure Subscription GUID (new portal)](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/) (Получение GUID подписки Azure на новом портале).

В случае нескольких подписок, вы можете добавить их в качестве запятой,делимитированных в "$subscriptions" свойство в заголовке скрипта.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Нажмите кнопку **«Сохранить»** в правом верхнем углу, чтобы сохранить сценарий. Когда вы удовлетворены сценарием, нажмите кнопку **«Опубликуйте»** для публикации этого runbook.

На главном панели runbook вы можете нажать на кнопку **«Начать»** для **тестирования** скрипта. Нажмите на кнопку **"Выход",** чтобы просмотреть результаты выполненного скрипта. Эти выходные данные будут отображаться в получаемых электронных сообщениях. На следующем снимке экрана можно увидеть пример выходных данных сценария.

![Представление рекомендаций по автоматической настройке с помощью службы автоматизации Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Обязательно измените содержимое, настроив сценарий PowerShell в соответствии со своими потребностями.

После выполнения указанных выше шагов сценарий PowerShell для получения рекомендаций по автоматической настройке загружается в службу автоматизации Azure. Следующим шагом является автоматизация и планирование задания доставки электронных сообщений.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Автоматизация заданий доставки электронных сообщений с помощью Microsoft Flow

Для завершения решения последним шагом является создание потока автоматизации в Microsoft Flow, состоящего из трех действий (заданий).

 - "**Azure Automation - Создание задания**" - используется для выполнения сценария PowerShell для получения рекомендаций автоматической настройки внутри запуска Azure Automation.
 - "**Azure Automation - Get job output**" - используется для получения вывода из выполненного скрипта PowerShell.
 - "**Office 365 Outlook - Отправить по электронной почте**" - используется для отправки электронной почты. Электронные сообщения отправляются с помощью учетной записи Office 365 пользователя, создавшего поток.

Чтобы узнать больше о возможностях Microsoft Flow, прочитайте статью [Начало работы с Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Необходимым условием для выполнения этого шага является регистрация для получения учетной записи [Microsoft Flow](https://flow.microsoft.com) и выполнение входа с ее помощью. Перейдя в это решение, выполните следующие действия, чтобы настроить **новый поток**.

1. Доступ "**Мои потоки**" пункт меню.
1. Внутри Моих потоков выберите ссылку **«Создание из пустой»** в верхней части страницы.
1. Нажмите на ссылку "**Поиск сотен разъемов и триггеров**" в нижней части страницы.
1. В поисковом поле типа **"повторение",** и выберите "**Расписание - Повторение**" из результатов поиска, чтобы запланировать работу доставки электронной почты для запуска.
1. В области "Повторение" в поле "Частота" выберите частоту планирования для выполнения этого потока. Например, можно настроить отправку автоматизированных электронных сообщений каждую минуту, час, день, неделю и т. д.

Следующим шагом является добавление в только что созданный повторяющийся поток трех заданий (создание, получение выходных данных и отправка электронного сообщения). Чтобы добавить необходимые задания в поток, выполните следующие действия.

1. Создайте действие для выполнения сценария PowerShell, который получает рекомендации по настройке.

   - Выберите «**«Новый шаг**», за которым следует «**Добавить действие**« внутри панели потока повторения.
   - В области поиска типа **"автоматизация"** и выбрать "**Azure Автоматизация - Создание задания**" из результатов поиска.
   - В области "Создание задания" настройте свойства задания. Для этой конфигурации потребуются сведения об идентификаторе подписки Azure, группе ресурсов и учетной записи службы автоматизации, **сохраненные ранее** в **области учетной записи службы автоматизации**. Чтобы узнать больше о параметрах, доступных в этом разделе, изучите раздел [Служба автоматизации Azure](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Полное создание этого действия, нажав на "**Сохранить поток**".

2. Создание действия для получения выходных данных выполненного сценария PowerShell

   - Выберите «**«Новый шаг**», после чего «**Добавить действие**» внутри панели потока повторения
   - В поиске подан тип **«автоматизация»** и выберите «**Автоматизацию Azure - Получить выход задания**» из результатов поиска. Чтобы узнать больше о параметрах, доступных в этом разделе, изучите раздел [Служба автоматизации Azure](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Требуется заполнить поля (по аналогии с созданием предыдущего задания) - заполнить идентификатор подписки Azure, группу ресурсов и учетную запись автоматизации (как введено в панель учетной записи автоматизации).
   - Нажмите внутри поля "**Идентификатор работы**" для меню **"Динамического содержимого",** чтобы показать. Из этого меню выберите опцию "**Идентификатор работы**".
   - Полное создание этого действия, нажав на "**Сохранить поток**".

3. Создание действия для отправки электронных сообщений с помощью интеграции Office 365

   - Выберите «**«Новый шаг**», за которым следует «**Добавить действие**« внутри панели потока повторения.
   - В поиске подан тип "**отправить письмо**" и выберите "**Office 365 Outlook - Отправить письмо**" из результатов поиска.
   - В поле типа **«To»** введите адрес электронной почты, на который необходимо отправить электронное письмо с уведомлением.
   - В **"Субъекте"** тип поля в теме вашей электронной почты, например "Автоматическая настройка рекомендаций по электронной почте уведомления".
   - Нажмите внутри поля "**Body**" для меню **"Динамического содержимого",** чтобы показать. В пределах этого меню, под «**Получить выход работы**«, выберите «**Содержание**«.
   - Полное создание этого действия, нажав на "**Сохранить поток**".

> [!TIP]
> Чтобы отправлять автоматизированные электронные сообщения другим получателям, создайте отдельные потоки. В этих дополнительных потоках измените адрес электронной почты получателя в поле "To" и строку темы электронной почты в поле "Subject". Создание в службе автоматизации Azure модулей runbook с пользовательскими сценариями PowerShell (например, для изменения идентификатора подписки Azure) обеспечивает дальнейшую настройку автоматизированных сценариев. Например, можно настроить отправку по электронной почте отдельным получателям рекомендаций по автоматической настройке для отдельных подписок.
>

На этом действия, необходимые для настройки рабочего процесса задания доставки электронных сообщений, завершены. На следующем рисунке показан весь поток, состоящий из трех действий.

![Представление потока уведомлений по электронной почте об автоматической настройке](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Чтобы проверить поток, нажмите на кнопку **"Беги сейчас"** в правом верхнем углу внутри стека.

Статистику выполнения автоматизированных заданий, отображающую успешность отправки уведомлений по электронной почте, можно просмотреть в области "Аналитика потока".

![Выполнение потока уведомлений по электронной почте об автоматической настройке](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Аналитика потока полезна для наблюдения за успешностью выполнения задания и устранения неполадок, когда это необходимо.  В случае устранение неполадок также может потребоваться просмотреть журнал выполнения сценария PowerShell, доступный в приложении службы автоматизации Azure.

Конечные выходные данные автоматизированных электронных сообщений аналогичны приведенному ниже электронному сообщению, полученному после выполнения сборки и запуска этого решения.

![Пример выходных данных уведомлений по электронной почте об автоматической настройке](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Изменяя параметры сценария PowerShell, можно настроить выходные данные и форматирование автоматизированных электронных сообщений в соответствии со своими потребностями.

Можно дополнительно настроить решение для создания уведомлений по электронной почте на основании определенного события настройки, отправляемых нескольким получателям, для нескольких подписок или баз данных. Это может зависеть от ваших конкретных сценариев.

## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о том, как автоматическая настройка может помочь повысить производительность базы данных, ознакомившись с разделом [Автоматическая настройка в Базе данных SQL Azure](sql-database-automatic-tuning.md).
- Дополнительные сведения о том, как включить автоматическую настройку в базе данных SQL Azure, чтобы управлять рабочей нагрузкой, см. в статье [Включение автоматической настройки](sql-database-automatic-tuning-enable.md).
- Дополнительные сведения о том, как вручную проверить и применить рекомендации автоматической настройки, см. в статье [Поиск и применение рекомендаций по производительности](sql-database-advisor-portal.md).
