---
title: Использование оповещения для активации runbook службы автоматизации Azure
description: Узнайте, как настроить запуск runbook при появлении оповещения Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8a1ae906a72d781f638fb171a409b860ffa6d501
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517708"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Использование оповещения для активации runbook службы автоматизации Azure

С помощью [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) можно отслеживать метрики и журналы базового уровня большинства служб Microsoft Azure. Модули runbook службы автоматизации Azure можно вызывать с помощью [групп действий](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) или с помощью классических оповещений, чтобы автоматизировать задачи на основе оповещений. В этой статье показано, как настроить и запустить runbook с помощью оповещений.

## <a name="alert-types"></a>Типы оповещений

Модули runbook службы автоматизации можно использовать с тремя типами оповещений:

* Распространенные предупреждения
* Оповещения журнала действий
* оповещения на основе метрик почти в реальном времени.

> [!NOTE]
> Общей схеме оповещений Стандартизирует взаимодействие потребления для уведомления об оповещениях в Azure уже сегодня. Исторически сложилось так, что трех типов оповещений в Azure сегодня (метрики журнала и журнала действий) имели собственные шаблоны сообщений электронной почты, веб-перехватчика схемы и т. д. Дополнительные сведения см. в разделе [общей схеме оповещений](../azure-monitor/platform/alerts-common-schema.md)

Когда оповещение вызывает runbook, фактически осуществляется вызов веб-перехватчика с помощью HTTP-запроса POST. Текст запроса POST содержит объект в формате JSON с полезными свойствами, связанными с оповещением. В следующей таблице приведены ссылки на схему полезных данных для каждого типа оповещения.

|Оповещение  |Описание|Схема полезных данных  |
|---------|---------|---------|
|[Распространенные предупреждения](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Распространенные предупреждения схема, которая стандартизирует взаимодействие потребления для уведомления об оповещениях в Azure уже сегодня.|[Общая схема полезны данных оповещения](../azure-monitor/platform/alerts-common-schema-definitions.md?toc=%2fazure%2fautomation%2ftoc.json#sample-alert-payload)|
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
2. В разделе **автоматизации процессов**выберите **ранбуки**.
3. В верхней части списка модулей Runbook, выберите **+ Создание модуля runbook**.
4. На **добавления модуля Runbook** введите **Stop-AzureVmInResponsetoVMAlert** имя runbook. Для типа runbook выберите значение **PowerShell**. Затем выберите **Создать**.  
5. Скопируйте следующий пример PowerShell в **изменить** страницы.

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

Оповещения используют группы действий, которые являются коллекциями действия, которые запускаются в оповещении. Модули runbook — это лишь одно из многих действий, доступных для групп действий.

1. В учетной записи службы автоматизации выберите **оповещения** под **мониторинг**.
1. Выберите **+ новое правило генерации оповещений**.
1. Нажмите кнопку **выберите** под **ресурсов**. На **выберите ресурс** странице, выберите свою виртуальную Машину, чтобы предупреждение за пределами класса, а затем выберите **сделать**.
1. Нажмите кнопку **добавить условие** под **условие**. Выберите сигнал, который вы хотите использовать, например **загрузка ЦП** и нажмите кнопку **сделать**.
1. На **настройка логики сигнала** введите ваш **пороговое значение** под **логику оповещений**и нажмите кнопку **сделать**.
1. В разделе **Группы действий** выберите **Создать**.
1. На **добавить группу действий** странице, укажите группу действий имя и краткое имя.
1. Присвойте имя действия. Тип действия, выберите **Runbook службы автоматизации**.
1. Выберите **изменения сведений о**. На странице **Настройка Runbook** в разделе **Источник модуля Runbook** выберите **Пользователь**.  
1. Выберите свои **подписку** и **учетную запись службы автоматизации**, затем выберите модуль runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Выберите **Да** для **включить общую схему оповещения**.
1. Нажмите кнопку **ОК**, чтобы создать группу действий.

    ![Страница добавления группы действий](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Можно использовать эту группу действий в создаваемых [оповещениях журнала действий](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) и [оповещениях почти в реальном времени](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json).

1. В разделе **сведения о предупреждении**, добавьте имя правила генерации оповещений и описание и нажмите кнопку **создать правило генерации оповещений**.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о запуске runbook службы автоматизации с помощью веб-перехватчика см. в разделе [Запуск Runbook службы автоматизации Azure с помощью объекта webhook](automation-webhooks.md).
* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Дополнительные сведения о том, как создать оповещение журнала действий, см. в разделе [Создание оповещений журнала действий](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Дополнительные сведения о том, как создать оповещение почти в реальном времени, см. в разделе [Создание правила оповещения с помощью портала Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
