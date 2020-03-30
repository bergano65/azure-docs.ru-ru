---
title: Автоматизация Azure Application Insights с помощью PowerShell | Документация Майкрософт
description: Автоматизация создания и управления ресурсами, оповещениями и тестами доступности в PowerShell с помощью шаблона управления ресурсами Azure.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275884"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Управление ресурсами Insights приложений с помощью PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье показано, как автоматизировать создание и обновление ресурсов [Application Insights](../../azure-monitor/app/app-insights-overview.md) с помощью управления ресурсами Azure. Эту функцию можно использовать, например, в процессе сборки. Наряду с базовым ресурсом Application Insights можно создавать [веб-тесты доступности](../../azure-monitor/app/monitor-web-app-availability.md) и другие ресурсы Azure, а также настраивать [оповещения](../../azure-monitor/app/alerts.md) и [схему цен](pricing.md).

Ключ к созданию этих ресурсов — шаблоны JSON для [диспетчера ресурсов Azure](../../azure-resource-manager/management/manage-resources-powershell.md). Основная процедура: скачать определения JSON существующих ресурсов; параметры определенных значений, таких как имена; а затем запускать шаблон всякий раз, когда вы хотите создать новый ресурс. Несколько ресурсов можно объединить, чтобы создавать их одновременно, например, объединить монитор приложений с тестами доступности, оповещениями и хранилищем для непрерывного экспорта. С параметризацией некоторых значений связаны определенные тонкости, которые мы рассмотрим позднее.

## <a name="one-time-setup"></a>Однократная настройка
Если вы ранее не использовали PowerShell для подписки Azure:

Установите модуль Azure Powershell на компьютере, где требуется выполнять сценарии.

1. Установите [установщик веб-платформы Майкрософт (версии 5 или более поздней)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Используйте его для установки Microsoft Azure PowerShell.

В дополнение к использованию шаблонов управления ресурсами, существует богатый набор [приложений Insights PowerShell cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights), которые позволяют легко настроить ресурсы Application Insights программно. Возможности, включенные cmdlets включают в себя:

* Создание и удаление ресурсов Application Insights
* Получить списки ресурсов Application Insights и их свойства
* Создание и управление непрерывным экспортом
* Создание и управление ключами приложений
* Установить ежедневную крышку
* Установить план ценообразования

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Создание ресурсов Application Insights с помощью cmdlet PowerShell

Вот как создать новый ресурс Application Insights в центре обработки данных Azure East US с помощью cmdlet [New-AzApplicationInsights:](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights)

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Создание ресурсов Application Insights с помощью шаблона менеджера ресурсов

Вот как создать новый ресурс Application Insights с помощью шаблона «Менеджер ресурсов».

### <a name="create-the-azure-resource-manager-template"></a>Создание шаблона управления ресурсами Azure

Создайте новый файл JSON — в данном примере назовем его `template1.json` . Скопируйте в него следующее содержимое:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Используйте шаблон менеджера ресурсов для создания нового ресурса Application Insights

1. В PowerShell вопием для Azure с помощью`$Connect-AzAccount`
2. Установите контекст подпиской с`Set-AzContext "<subscription ID>"`
2. Запустите новое развертывание для создания нового ресурса Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` — группа, в которой необходимо создать ресурсы.
   * Параметр `-TemplateFile` должен предшествовать пользовательским параметрам.
   * `-appName` — имя создаваемого ресурса.

Можно добавить другие параметры. Их описание доступно в разделе параметров шаблона.

## <a name="get-the-instrumentation-key"></a>Получите ключ прибора

После создания ресурса приложения вам понадобится ключ инструментирования. 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Чтобы просмотреть список многих других свойств ресурса Application Insights, используйте:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Дополнительные свойства доступны через cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Ссылайтесь на [подробную документацию](https://docs.microsoft.com/powershell/module/az.applicationinsights) по параметрам для этих cmdlets.  

## <a name="set-the-data-retention"></a>Установить удержание данных 

Чтобы получить текущее хранение данных для ресурса Application Insights, вы можете использовать инструмент OSS [ARMClient.](https://github.com/projectkudu/ARMClient)  (Узнайте больше об ARMClient из статей [Дэвида Эббо](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) и [Дэниела Боуби .)](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)  Вот пример использования, `ARMClient`чтобы получить текущее удержание:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Чтобы установить удержание, команда аналогична PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Чтобы установить сохранение данных до 365 дней с помощью приведенного выше шаблона, запустите:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Следующий скрипт также может быть использован для изменения удержания. Копировать этот скрипт, чтобы сохранить как `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Этот скрипт может быть использован в качестве:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Установите дневную крышку

Чтобы получить свойства дневной крышки, используйте [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) cmdlet: 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Чтобы установить свойства дневной крышки, используйте тот же cmdlet. Например, установить крышку до 300 ГБ/день,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Вы также можете использовать [ARMClient,](https://github.com/projectkudu/ARMClient) чтобы получить и установить параметры дневной крышки.  Чтобы получить текущие значения, используйте:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Установите ежедневное время сбросить крышку

Чтобы установить дневное время сбросить крышку, вы можете использовать [ARMClient.](https://github.com/projectkudu/ARMClient) Вот пример с `ARMClient`использованием, чтобы установить время сбросить на новый час (в этом примере 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Установить план ценообразования 

Чтобы получить текущий план ценообразования, используйте [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) cmdlet:

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Чтобы установить план ценообразования, используйте `-PricingPlan` тот же cmdlet с указанным:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Вы также можете установить план ценообразования на существующем ресурсе Application Insights, используя приведенный выше шаблон `dependsOn` Resource Manager, испустив ресурс "microsoft.insights/components" и узла из ресурса выставления счетов. Например, чтобы установить его в план Per GB (ранее назывался Базовый план), запустите:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

Определяется `priceCode` как:

|priceCode|План|
|---|---|
|1|На GB (ранее названный Основной план)|
|2|За узла (ранее название План предприятия)|

Наконец, вы можете использовать [ARMClient,](https://github.com/projectkudu/ARMClient) чтобы получить и установить планы ценообразования и ежедневные параметры крышки.  Чтобы получить текущие значения, используйте:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

И вы можете установить все эти параметры, используя:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Это установит дневной лимит до 200 ГБ/день, накончает дневное время сбросить крышку до 12:00 UTC, отправит электронные письма как при ударе крышки, так и на уровне предупреждения, и установит порог предупреждения до 90% крышки.  

## <a name="add-a-metric-alert"></a>Добавление оповещения метрики

Чтобы автоматизировать создание метрических оповещений, обратитесь к [статье шаблона метрикар](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Добавление теста доступности

Чтобы автоматизировать тесты доступности, обратитесь к [статье шаблона метрикар.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert)

## <a name="add-more-resources"></a>Добавление дополнительных ресурсов

Чтобы автоматизировать создание любого другого ресурса любого типа, создайте пример вручную, а затем скопируйте его код и выполните его параметризацию из [Azure Resource Manager](https://resources.azure.com/). 

1. Откройте [диспетчер ресурсов Azure](https://resources.azure.com/). Перейдите в `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` к своему ресурсу приложения. 
   
    ![Навигация в обозревателе ресурсов Azure](./media/powershell/01.png)
   
    *Компоненты* — это основные ресурсы Application Insights для отображения приложений. Для соответствующих правил оповещения и веб-тестов доступности имеются отдельные ресурсы.
2. Скопируйте JSON компонента в соответствующее место в файле `template1.json`.
3. Удалите следующие свойства:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Откройте `webtests` `alertrules` и разделы и скопируйте JSON для отдельных элементов в шаблон. (Не копируйте из `webtests` `alertrules` узлов: идите в элементы под ними.)
   
    С каждым веб-тестом связано правило оповещения, поэтому скопировать необходимо оба элемента.
   
    Можно также добавить оповещения для метрик. [Метрические имена](powershell-alerts.md#metric-names).
5. Вставьте в каждый ресурс следующую строку.
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Параметризация шаблона
Теперь отдельные имена необходимо заменить параметрами. Для [параметризации шаблона](../../azure-resource-manager/templates/template-syntax.md) необходимо записать выражения, используя [набор вспомогательных функций](../../azure-resource-manager/templates/template-functions.md). 

Параметризовать только часть строки нельзя, поэтому для формирования строки используйте `concat()` .

Вот примеры возможных замен: Каждая замена используется несколько раз. В вашем шаблоне могут потребоваться другие замены. В данных примерах используются параметры и переменные, определенные в верхней части шаблона.

| поиск | заменить на |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (строчная) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Установка зависимостей между ресурсами
Служба Azure должна настраивать ресурсы в строгом порядке. Чтобы одна процедура настройки не начиналась прежде, чем завершится предыдущая, добавьте строки зависимости:

* В ресурсе теста доступности:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* В ресурсе оповещения для теста доступности:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Дальнейшие действия
Другие статьи об автоматизации:

* [Создание ресурса Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) — быстрый метод без использования шаблона.
* [Настройка оповещений](powershell-alerts.md)
* [Creating an Application Insights Web Test and Alert Programmatically](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Отправка данных системы диагностики Azure в Application Insights](powershell-azure-diagnostics.md)
* [Развертывание в Azure из GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Создание заметок выпуска](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)