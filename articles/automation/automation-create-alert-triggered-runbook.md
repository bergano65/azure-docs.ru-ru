---
title: Использование оповещения для активации runbook службы автоматизации Azure
description: Узнайте, как настроить запуск runbook при появлении оповещения Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: e8ddcaf6a5c9ab51147e540e2426ef8c4a1fdd3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392368"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Использование оповещения для активации runbook службы автоматизации Azure

С помощью [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) можно отслеживать метрики и журналы базового уровня большинства служб Microsoft Azure. Модули runbook службы автоматизации Azure можно вызывать с помощью [групп действий](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) или с помощью классических оповещений, чтобы автоматизировать задачи на основе оповещений. В этой статье показано, как настроить и запустить runbook с помощью оповещений.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="alert-types"></a>Типы оповещений

Модули runbook службы автоматизации можно использовать с тремя типами оповещений:

* Распространенные оповещения
* Оповещения журнала действий
* оповещения на основе метрик почти в реальном времени.

> [!NOTE]
> Схема общего оповещения стандартизирует работу по потреблению уведомлений в Azure сегодня. Исторически сложилось так, что три типа оповещений в Azure сегодня (метрика, журнал и журнал активности) имели свои собственные шаблоны электронной почты, схемы веб-крючка и т.д. Чтобы узнать больше, [см.](../azure-monitor/platform/alerts-common-schema.md)

Когда оповещение вызывает runbook, фактически осуществляется вызов веб-перехватчика с помощью HTTP-запроса POST. Текст запроса POST содержит объект в формате JSON с полезными свойствами, связанными с оповещением. В следующей таблице приведены ссылки на схему полезных данных для каждого типа оповещения.

|Предупреждение  |Описание|Схема полезных данных  |
|---------|---------|---------|
|[Общее оповещение](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Общая схема оповещения, которая стандартизирует работу с потреблением уведомлений в Azure сегодня.|Схема общей полезной нагрузки оповещения|
|[Оповещение журнала действий](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Отправляет уведомление, когда какое-либо новое событие в журнале действий Azure соответствует определенным условиям. Например, если в **myProductionResourceGroup** выполняется операция `Delete VM` или возникает новое событие службы "Работоспособность служб Azure" в состоянии Активное.| [Схема полезных данных оповещения журнала действий](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Оповещения на основе метрик практически в реальном времени](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Отправляет уведомление быстрее, чем оповещения о метриках, когда одна или несколько метрик уровня платформы удовлетворяют указанным условиям. Например, когда значение **для процессора %** на VM превышает 90, а значение для Network **In** превышает 500 МБ за последние 5 минут.| [Схема полезны данных оповещения на основе метрик почти в реальном времени](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Так как данные, предоставляемые для каждого типа оповещения, различны, оповещения разных типов обрабатываются по-разному. В следующем разделе вы узнаете, как создать runbook для обработки различных типов оповещений.

## <a name="create-a-runbook-to-handle-alerts"></a>Создание модуля Runbook для обработки оповещений

Чтобы применять автоматизацию к оповещениям, нужен runbook с логикой для управления полезными данными JSON оповещения, которые передаются в runbook. Приведенный ниже пример модуля Runbook должен быть вызван из оповещения Azure.

Как описано в предыдущем разделе, каждый тип оповещения имеет свою схему. Скрипт принимает данные webhook из `WebhookData` оповещений в параметре ввода runbook. Затем скрипт оценивает полезную нагрузку JSON, чтобы определить, какой тип оповещения используется.

В этом примере используется оповещение из виртуальной машины. Он извлекает данные виртуальной машины из полезных данных и использует эту информацию для остановки виртуальной машины. Подключение должно быть настроено в учетной записи службы автоматизации, в которой выполняется runbook. При использовании оповещений для запуска runbooks важно проверить состояние оповещения в срабатывании runbook. Запуск запускает сярвы каждый раз, когда состояние оповещения изменяется. Оповещения имеют несколько состояний, при этом два наиболее распространенных являются активированными и решенными. Проверьте состояние в логике runbook, чтобы убедиться, что runbook не выполняется более одного раза. Пример в этой статье показывает, как искать оповещения только при активированном состоянии.

В runbook используется `AzureRunAsConnection` актив подключения [Run As для](automation-create-runas-account.md) проверки подлинности с помощью Azure для выполнения действий по управлению против VM.

Используйте этот пример для создания runbook **Stop-AzureVmInResponsetoVMAlert**. Можно изменить сценарий PowerShell и использовать его для множества разных ресурсов.

1. Перейдите к своей учетной записи службы автоматизации Azure.
2. В разделе **Автоматизация процессов** щелкните **Модули Runbook**.
3. В верхней части списка модулей runbook выберите **+ Создать Runbook**.
4. На странице **Add Runbook** введите **Stop-AzureVmInResponsetoVMAlert** для названия runbook. Для типа runbook выберите значение **PowerShell**. Затем выберите **Создать**.  
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
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
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

Оповещения используют группы действий, которые представляют собой коллекции действий, которые запускаются предупреждением. Модули runbook — это лишь одно из многих действий, доступных для групп действий.

1. В учетной записи автоматизации выберите **оповещения** под **мониторингом.**
1. Выберите **+ Новое правило генерации оповещений**.
1. Нажмите **Выберите** под **ресурсом**. На странице **Выберите ресурс,** выберите VM, чтобы предупредить, и нажмите **Готово.**
1. Нажмите **Добавить условие** в соответствии с **условием**. Выберите сигнал, который вы хотите использовать, **например, процентный процессор** и нажмите **Done.**
1. На странице **логики сигнала Настройка** введите **значение Порога** в **логике Alert**и нажмите **Done**.
1. В разделе **Группы действий** выберите **Создать**.
1. На странице **группы действий Добавить** присвоите группе действий имя и короткое имя.
1. Дайте действию имя. Для типа действия выберите **Automation Runbook.**
1. Выберите **детали edit**. На странице **Настройка Runbook** в разделе **Источник модуля Runbook** выберите **Пользователь**.  
1. Выберите свои **подписку** и **учетную запись службы автоматизации**, затем выберите модуль runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Выберите **«Да»** для **включения общей схемы оповещения.**
1. Нажмите кнопку **ОК**, чтобы создать группу действий.

    ![Страница добавления группы действий](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Можно использовать эту группу действий в создаваемых [оповещениях журнала действий](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) и [оповещениях почти в реальном времени](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json).

1. Под **сведениям оповещении**добавьте имя и описание правила оповещения и нажмите **«Создать оповещение».**

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о запуске runbook службы автоматизации с помощью веб-перехватчика см. в разделе [Запуск Runbook службы автоматизации Azure с помощью объекта webhook](automation-webhooks.md).
* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Дополнительные сведения о том, как создать оповещение журнала действий, см. в разделе [Создание оповещений журнала действий](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Дополнительные сведения о том, как создать оповещение почти в реальном времени, см. в разделе [Создание правила оповещения с помощью портала Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* Для справки PowerShell cmdlet [см.](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)
