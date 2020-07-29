---
title: Создание ресурса рабочей области Azure Monitor Application Insights | Документация Майкрософт
description: Узнайте, что нужно сделать, чтобы использовать новые ресурсы на основе рабочих областей Azure Monitor Application Insights.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: ef3c2161e5a032983a2cbc9e4ccdf60af6920a7d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323117"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Ресурсы рабочей области Application Insights (предварительная версия)

Ресурсы рабочей области поддерживают полную интеграцию Application Insights и Log Analytics. Теперь вы можете отправлять данные телеметрии Application Insights в общую рабочую область Log Analytics, которая обеспечивает полный доступ ко всем функциям Log Analytics, сохраняя журналы приложений, инфраструктуры и платформы в централизованном расположении.

Это также позволяет использовать общее управление доступом на основе ролей (RBAC) для ресурсов и устраняет необходимость в перекрестных запросах между приложениями и рабочими областями.

> [!NOTE]
> Прием и хранение данных ресурсов рабочей области Application Insights оплачиваются по тарифам рабочей области Log Analytics, в которой находятся данные. [Узнайте больше]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) о выставлении счетов за ресурсы рабочей области Application Insights.

Чтобы проверить новые возможности, войдите на [портал Azure](https://portal.azure.com) и создайте ресурс Application Insights.

![Ресурс рабочей области Application Insights](./media/create-workspace-resource/create-workspace-based.png)

Если у вас еще нет рабочей области Log Analytics, [обратитесь к документации по созданию рабочей области Log Analytics](../learn/quick-create-workspace.md).

Для общедоступной предварительной версии **ресурсы рабочей области в настоящее время доступны только в западной части США 2, восточной части США и центрально-южной части США.**

После создания ресурса информация о соответствующей рабочей области отобразится в области **Обзор**.

![Имя рабочей области](./media/create-workspace-resource/workspace-name.png)

Щелкнув синий текст ссылки, вы перейдете в соответствующую рабочую область Log Analytics, где сможете воспользоваться преимуществами новой унифицированной среды обработки запросов рабочей области.

> [!NOTE]
> Мы по-прежнему предоставляем полную обратную совместимость с классическими запросами, ресурсами, книгами и оповещениями на основе журналов Application Insights. Для запроса или просмотра [структуры или схемы новой таблицы рабочей области](apm-tables.md), необходимо сначала перейти в рабочую область Log Analytics. На этапе предварительной версии можно выбрать **Журналы** в областях Application Insights, чтобы перейти к классическому интерфейсу запросов Application Insights.

## <a name="copy-the-connection-string"></a>Копирование строки подключения

[Строка подключения](./sdk-connection-string.md?tabs=net) определяет ресурс, с которым необходимо связать данные телеметрии. Она также позволяет изменить конечные точки, которые ресурс будет использовать в качестве назначения для данных телеметрии. Необходимо скопировать строку подключения и добавить ее в код приложения или переменную среды.

## <a name="monitoring-configuration"></a>Конфигурация мониторинга

После создания ресурса рабочей области Application Insights настроить мониторинг относительно просто.

### <a name="code-based-application-monitoring"></a>Мониторинг приложений на основе кода

Чтобы реализовать мониторинг приложений на основе кода, нужно просто установить соответствующий пакет SDK для Application Insights и указать ключ инструментирования или строку подключения к только что созданному ресурсу.  

Подробную информацию по настройке пакета SDK для Application Insights для мониторинга на основе кода можно найти в соответствующей документации для языка или платформы.

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [Фоновые задачи и современные консольные приложения (.NET и .NET Core)](./worker-service.md)
- [Классические консольные приложения (.NET)](./console.md) 
- [Java ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Мониторинг без написания кода и создание ресурсов Visual Studio

Чтобы использовать мониторинг без написания кода для таких служб, как Функции Azure и службы приложений Azure, необходимо сначала создать ресурс рабочей области Application Insights, а затем указать этот ресурс на этапе настройки мониторинга.

Хотя эти службы дают возможность создать ресурс Application Insights в рамках собственного процесса создания ресурсов, ресурсы, созданные с помощью этих параметров пользовательского интерфейса, в настоящее время ограничены классическим интерфейсом Application Insights.

То же самое относится к интерфейсу создания ресурсов Application Insights в Visual Studio для ASP.NET и ASP.NET Core. Необходимо выбрать существующий ресурс рабочей области с помощью пользовательского интерфейса включения мониторинга Visual Studio. Если выбрать создание ресурса в Visual Studio, то можно будет создать только классический ресурс Application Insights.

## <a name="creating-a-resource-automatically"></a>Автоматическое создание ресурса

### <a name="azure-cli"></a>Azure CLI

Чтобы получить доступ к командам Azure CLI для предварительной версии Application Insights, сначала необходимо выполнить следующее.

```azurecli
 az extension add -n application-insights
```

Если не выполнить команду `az extension add`, появится сообщение об ошибке, в котором будет указано следующее: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Теперь можно выполнить следующие действия, чтобы создать ресурс Application Insights.

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Пример

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Полную документацию Azure CLI по этой команде можно найти [здесь](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

В настоящее время команда PowerShell `New-AzApplicationInsights` не поддерживает создание ресурса рабочей области Application Insights. Чтобы создать ресурс рабочей области с помощью PowerShell, можно использовать приведенные ниже шаблоны Azure Resource Manager и выполнить развертывание с помощью PowerShell.

### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

#### <a name="template-file"></a>Файл шаблона

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Файл параметров

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Изменение связанной рабочей области

После создания ресурса рабочей области Application Insights можно изменить связанную с ним рабочую область Log Analytics.

В области ресурсов Application Insights выберите **Свойства** > **Изменить рабочую область** > **Рабочие области Log Analytics**.

## <a name="export-telemetry"></a>Экспорт данных телеметрии

Устаревшие функции непрерывного экспорта не поддерживаются для ресурсов рабочей области. Вместо этого в ресурсе Application Insights выберите **Параметры диагностики** > **Добавить параметр диагностики**. Можно выбрать все таблицы или подмножество таблиц для архивации в учетную запись хранения или использовать потоковую передачу в концентратор событий Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Изучение метрик](../platform/metrics-charts.md)
* [Написание запросов аналитики](../log-query/log-query-overview.md)

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md

