---
title: Сценарий PowerShell для создания ресурса Application Insights | Документация Майкрософт
description: Автоматизация создания ресурсов Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2016
ms.openlocfilehash: 11245d0f9d6e6b86a5d0249df65b33f851bee9d7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820690"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Сценарий PowerShell для создания ресурса Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Если вам требуется отслеживать новое приложение или новую версию приложения с помощью [Application Insights](https://azure.microsoft.com/services/application-insights/), настройте новый ресурс в Microsoft Azure. В этом ресурсе будут анализироваться и отображаться данные телеметрии из вашего приложения. 

Вы можете автоматизировать создание нового ресурса с помощью PowerShell.

Например, если вы разрабатываете приложение для мобильных устройств, вполне вероятно, что в одно и то же время клиенты будут использовать несколько опубликованных версий вашего приложения. Вам, наверняка, не нужно получать смешанные результаты телеметрии по разным версиям. Поэтому вам доступен процесс сборки, позволяющий создавать новый ресурс для каждой сборки.

> [!NOTE]
> Если вы хотите сразу создать набор ресурсов, рассмотрите возможность [использования шаблона Azure Resource Manager](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Сценарий для создания ресурса Application Insights
См. спецификацию соответствующего командлета:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Сценарий PowerShell*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Что делать с iKey
Каждый ресурс определяется по ключу инструментирования (iKey). Этот ключ iKey выводит сценарий создания ресурса. Сценарий сборки должен предоставлять iKey для пакета SDK для Application Insights, внедренного в приложение.

Есть два способа сделать iKey доступным для пакета SDK:

* В файле [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Или в [коде инициализации](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Дополнительные материалы
* [Создание ресурсов Application Insights и веб-тестов на основе шаблонов](powershell.md)
* [Настройка мониторинга диагностики Azure с помощью PowerShell](powershell-azure-diagnostics.md) 
* [Настройка оповещений с помощью PowerShell](powershell-alerts.md)

