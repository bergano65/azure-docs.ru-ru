---
title: Автоматическое обновление единиц обмена сообщениями (Служебная шина Azure)
description: В этой статье демонстрируется, как можно использовать runbook службы автоматизации Azure для автоматического обновления единиц обмена сообщениями в пространстве имен служебной шины.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341489"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Автоматическое обновление единиц обмена сообщениями в пространстве имен Служебной шины Azure 
В этой статье демонстрируется, как автоматизировать обновление [единиц обмена сообщениями](service-bus-premium-messaging.md) в пространстве имен служебной шины на основе данных о потреблении ресурса (ЦП или памяти). 

В статье приводится пример, который увеличивает число единиц обмена сообщениями для пространства имен служебной шины, когда загрузка ЦП пространством имен превышает 75 %. Для этого выполните следующие обобщенные действия.

1. Создайте runbook службы автоматизации Azure на основе скрипта PowerShell, который увеличивает масштаб (число единиц обмена сообщениями) для пространства имен служебной шины. 
2. Создайте оповещение об использовании ЦП для пространства имен служебной шины, чтобы вызывать скрипт PowerShell, когда загрузка ЦП пространством имен превышает 75 %. 

> [!IMPORTANT]
> Эта статья относится только к уровню **Премиум** Служебной шины Azure. 


## <a name="create-a-service-bus-namespace"></a>Создание пространства имен служебной шины
Создайте пространство имен служебной шины уровня "Премиум". Выполните действия из статьи [Создание пространства имен на портале Azure](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal), чтобы создать пространство имен. 

## <a name="create-an-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure
Создайте учетную запись службы автоматизации Azure, выполнив инструкции из статьи [Создание учетной записи службы автоматизации Azure](../automation/automation-quickstart-create-account.md). 

## <a name="import-azservice-module-from-gallery"></a>Импорт модуля Az.Service из коллекции
Импортируйте из коллекции модули `Az.Accounts` и `Az.ServiceBus` в эту учетную запись службы автоматизации. Модуль `Az.ServiceBus` зависит от модуля `Az.Accounts`, который по этой причине нужно установить первым. 

Подробные инструкции по импорту модуля из коллекции модулей см. в [этой статье](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>Создание и публикация runbook на основе PowerShell

1. Создайте runbook PowerShell, выполнив инструкции из [этой статьи](../automation/automation-quickstart-create-runbook.md). 

    Ниже приведен пример скрипта PowerShell, который можно применить для увеличения числа единиц обмена сообщениями для пространства имен служебной шины. Этот скрипт PowerShell в runbook службы автоматизации увеличивает число единиц обмена сообщениями с 1 до 2, с 2 до 4 или с 4 до 8. Допустимые значения этого свойства: 1, 2, 4 и 8. Вы можете создать еще один runbook для уменьшения количества единиц обмена сообщениями.

    Параметры **namespaceName** и **resourceGroupName** используются для изолированного тестирования скрипта, без подключения к обработке оповещений. 
    
    Параметр **WebHookData** позволяет оповещению передавать во время выполнения такие сведения, как имя группы ресурсов, имя ресурса и т. д. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
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
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Протестируйте книгу](../automation/manage-runbooks.md#test-a-runbook), указав значения для параметров **namespaceName** и **resourceGroupName**. Убедитесь, что в пространстве имен изменилось количество единиц обмена сообщениями. 
3. Если тест пройдет успешно, [опубликуйте книгу](..//automation/manage-runbooks.md#publish-a-runbook), чтобы она была готова для добавления в качестве действия к оповещению по пространству имен. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Создание оповещения по пространству имен для выполнения runbook
См. статью [Использование оповещения для активации runbook службы автоматизации Azure](../automation/automation-create-alert-triggered-runbook.md), чтобы настроить оповещение по пространству имен служебной шины, которое будет запускать только что созданный runbook службы автоматизации. Например, вы можете создать оповещение по метрике **CPU usage per namespace** (Использование ЦП по пространству имен) или **Memory size usage per namespace** (Использование памяти по пространству имен) и добавить к нему действие для запуска runbook службы автоматизации. Дополнительные сведения об этих метриках см. в разделе [Метрики использования ресурсов](service-bus-metrics-azure-monitor.md#resource-usage-metrics).

В следующей процедуре показано, как создать оповещение для запуска runbook службы автоматизации, когда **загрузка ЦП** пространством имен превышает **75 %** .

1. На странице **Пространство имен служебной шины** выберите в меню слева элемент **Оповещения**, а затем на панели инструментов выберите действие **+ Новое правило генерации оповещений**. 
    
    ![Страница "Оповещения" — кнопка "Новое правило генерации оповещений"](./media/automate-update-messaging-units/alerts-page.png)
2. На странице **Создание правила генерации оповещений** щелкните **Выбрать условие**. 

    ![Страница "Создание правила генерации оповещений" — выбор условия](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. На странице **Настройка логики сигнала** выберите сигнал **ЦП**. 

    ![Настройка логики сигнала — ЦП](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Введите **пороговое значение** (в нашем примере это **75 %** ) и щелкните **Готово**. 

    ![Настройка сигнала по загрузке ЦП](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Теперь на странице **Создание оповещения** щелкните **Выбрать группу действий**.
    
    ![Выбор группы действий](./media/automate-update-messaging-units/select-action-group-button.png)
6. Нажмите кнопку **Создать группу действий** на панели инструментов. 

    ![Кнопка "Создать группу действий"](./media/automate-update-messaging-units/create-action-group-button.png)
7. На странице **Добавить группу действий** сделайте следующее:
    1. Введите **имя** для группы действий. 
    2. Введите **короткое имя** для группы действий.
    3. Выберите **подписку**, в которой нужно создать группу действий.
    4. Выберите **группу ресурсов**. 
    5. В разделе **Действия** введите **имя действия** и выберите **Runbook автоматизации** в поле **Тип действия**. 

        ![Страница добавления группы действий](./media/automate-update-messaging-units/add-action-group-page.png)
8. На странице **Настройка runbook** выполните следующие действия:
    1. В поле **Источник runbook** выберите **Пользователь**. 
    2. В поле **Подписка** выберите **подписку Azure**, которая содержит учетную запись службы автоматизации. 
    3. В поле **Учетная запись автоматизации** выберите нужную **учетную запись**.
    4. В поле **Runbook** выберите нужный runbook. 
    5. Нажмите **ОК** на странице **Настройка runbook**. 
        ![Настройка runbook](./media/automate-update-messaging-units/configure-runbook.png)
9. Нажмите **ОК** на странице **Добавить группу действий**. 
5. Теперь на странице **Создание правила генерации оповещений** введите **имя правила** и щелкните **Создать правило генерации оповещений**. 
    ![Создание правила генерации оповещений](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Теперь, когда загрузка ЦП выбранным пространством имен превысит значение 75, оповещение активируется и запустит runbook автоматизации, который увеличит количество единиц обмена сообщениями для этого пространства имен служебной шины. Аналогичным образом можно создать оповещение для другого runbook автоматизации, который сокращает количество единиц обмена сообщениями, если загрузка ЦП пространством имен опускается ниже 25. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о единицах обмена сообщениями см. в [этой статье](service-bus-premium-messaging.md).
