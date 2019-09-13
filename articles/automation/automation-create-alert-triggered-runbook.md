---
title: Использование оповещения для активации runbook службы автоматизации Azure
description: Узнайте, как настроить запуск runbook при появлении оповещения Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c818114df436dbbd3ac1a51b6eeec00b9eec4d3
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915725"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Использование оповещения для активации runbook службы автоматизации Azure

С помощью [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) можно отслеживать метрики и журналы базового уровня большинства служб Microsoft Azure. Модули runbook службы автоматизации Azure можно вызывать с помощью [групп действий](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) или с помощью классических оповещений, чтобы автоматизировать задачи на основе оповещений. В этой статье показано, как настроить и запустить runbook с помощью оповещений.

## <a name="alert-types"></a>Типы оповещений

Модули runbook службы автоматизации можно использовать с тремя типами оповещений:

* Распространенные оповещения
* Оповещения журнала действий
* оповещения на основе метрик почти в реальном времени.

> [!NOTE]
> Общая схема оповещений стандартизация возможностей использования уведомлений об оповещениях в Azure уже сегодня. Исторически, три типа оповещений в Azure уже сегодня (метрика, журнал и журнал действий) имеют собственные шаблоны электронной почты, схемы веб-перехватчиков и т. д. Дополнительные сведения см. в разделе [Общая схема предупреждений](../azure-monitor/platform/alerts-common-schema.md) .

Когда оповещение вызывает runbook, фактически осуществляется вызов веб-перехватчика с помощью HTTP-запроса POST. Текст запроса POST содержит объект в формате JSON с полезными свойствами, связанными с оповещением. В следующей таблице приведены ссылки на схему полезных данных для каждого типа оповещения.

|Оповещение  |Описание|Схема полезных данных  |
|---------|---------|---------|
|[Общее оповещение](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Общая схема оповещений, которая стандартизует возможности использования уведомлений об оповещениях в Azure уже сегодня.|Общая схема полезных данных оповещений|
|[Оповещение журнала действий](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Отправляет уведомление, когда какое-либо новое событие в журнале действий Azure соответствует определенным условиям. Например, если в **myProductionResourceGroup** выполняется операция `Delete VM` или возникает новое событие службы "Работоспособность служб Azure" в состоянии **Активное**.| [Схема полезных данных оповещения журнала действий](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Оповещения на основе метрик практически в реальном времени](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Отправляет уведомление быстрее, чем оповещения о метриках, когда одна или несколько метрик уровня платформы удовлетворяют указанным условиям. Например, если за последние 5 минут значение метрики **% ЦП** на виртуальной машине превышает **90** и значение метрики **Сеть (входящий трафик)** превышает **500 МБ**.| [Схема полезны данных оповещения на основе метрик почти в реальном времени](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Так как данные, предоставляемые для каждого типа оповещения, различны, оповещения разных типов обрабатываются по-разному. В следующем разделе вы узнаете, как создать runbook для обработки различных типов оповещений.

## <a name="create-a-runbook-to-handle-alerts"></a>Создание модуля Runbook для обработки оповещений

Чтобы применять автоматизацию к оповещениям, нужен runbook с логикой для управления полезными данными JSON оповещения, которые передаются в runbook. Приведенный ниже пример модуля Runbook должен быть вызван из оповещения Azure.

Как описано в предыдущем разделе, каждый тип оповещения имеет свою схему. Сценарий принимает данные веб-перехватчика во входном параметре runbook `WebhookData` из оповещения. Затем сценарий оценивает полезные данные JSON, чтобы определить, какой тип оповещения использован.

В этом примере используется оповещение из виртуальной машины. Он извлекает данные виртуальной машины из полезных данных и использует эту информацию для остановки виртуальной машины. Подключение должно быть настроено в учетной записи службы автоматизации, в которой выполняется runbook. При использовании оповещений для активации модулей runbook очень важно проверять состояние оповещения в активируемом модуле. Модуль runbook будет активирован каждый раз, когда состояние оповещения меняется. Оповещения имеют несколько состояний. Два наиболее распространенных — это `Activated` и `Resolved`. Проверьте это состояние в логике runbook, чтобы убедиться, что модуль runbook выполняется не более одного раза. Пример в этой статье показывает, как выполнять поиск только оповещений `Activated`.

Runbook будет использовать [учетную запись от имени](automation-create-runas-account.md) **AzureRunAsConnection** для аутентификации в Azure, чтобы выполнить действие управления с виртуальной машиной.

Используйте этот пример для создания runbook **Stop-AzureVmInResponsetoVMAlert**. Можно изменить сценарий PowerShell и использовать его для множества разных ресурсов.

1. Перейдите к своей учетной записи службы автоматизации Azure.
2. В разделе **Автоматизация процессов** щелкните **Модули Runbook**.
3. В верхней части списка модулей runbook выберите **+ Создать Runbook**.
4. На странице **Добавление модуля Runbook** введите в поле имя Runbook **-AzureVmInResponsetoVMAlert** . Для типа runbook выберите значение **PowerShell**. Затем выберите **Создать**.  
5. Скопируйте следующий пример PowerShell на страницу **Изменение**.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Нажмите кнопку **Опубликовать**, чтобы сохранить и опубликовать runbook.

## <a name="create-the-alert"></a>Создание оповещения

Оповещения используют группы действий, которые представляют собой коллекции действий, активируемых предупреждением. Модули runbook — это лишь одно из многих действий, доступных для групп действий.

1. В учетной записи службы автоматизации выберите **оповещения** в разделе **мониторинг**.
1. Выберите **+ новое правило генерации оповещений**.
1. Щелкните **выбрать** в разделе **ресурс**. На странице **Выбор ресурса** выберите виртуальную машину для оповещения и нажмите кнопку **Готово**.
1. Нажмите кнопку **Добавить условие** в поле **условие**. Выберите сигнал, который вы хотите использовать, например **процент загрузки ЦП** , и нажмите кнопку **Готово**.
1. На странице **Настройка логики сигнала** введите **пороговое значение** в разделе **логика оповещения**и нажмите кнопку **Готово**.
1. В разделе **Группы действий** выберите **Создать**.
1. На странице **Добавление группы действий** присвойте группе действий имя и короткое имя.
1. Присвойте действию имя. Для типа действия выберите **модуль Runbook службы автоматизации**.
1. Выберите **изменить сведения**. На странице **Настройка Runbook** в разделе **Источник модуля Runbook** выберите **Пользователь**.  
1. Выберите свои **подписку** и **учетную запись службы автоматизации**, затем выберите модуль runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Выберите **Да** **, чтобы включить общую схему оповещений**.
1. Нажмите кнопку **ОК**, чтобы создать группу действий.

    ![Страница добавления группы действий](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Можно использовать эту группу действий в создаваемых [оповещениях журнала действий](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) и [оповещениях почти в реальном времени](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json).

1. В разделе **сведения о предупреждении**добавьте имя и описание правила генерации оповещений и щелкните **создать правило генерации оповещений**.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о запуске runbook службы автоматизации с помощью веб-перехватчика см. в разделе [Запуск Runbook службы автоматизации Azure с помощью объекта webhook](automation-webhooks.md).
* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Дополнительные сведения о том, как создать оповещение журнала действий, см. в разделе [Создание оповещений журнала действий](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Дополнительные сведения о том, как создать оповещение почти в реальном времени, см. в разделе [Создание правила оповещения с помощью портала Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
