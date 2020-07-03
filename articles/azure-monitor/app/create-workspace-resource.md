---
title: Создание нового Azure Monitor Application Insights ресурса на основе рабочей области | Документация Майкрософт
description: Сведения о действиях, необходимых для включения новых Azure Monitor Application Insights ресурсов на основе рабочих областей.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65c2220bd3e9ea93e562b256e84796aed9046d00
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211412"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Ресурсы Application Insights на основе рабочей области (Предварительная версия)

Ресурсы на основе рабочей области поддерживают полную интеграцию между Application Insights и Log Analytics. Теперь вы можете отправить данные телеметрии Application Insights в общую рабочую область Log Analytics, которая обеспечивает полный доступ ко всем функциям Log Analytics, сохраняя журналы приложений, инфраструктуры и платформы в едином консолидированном расположении.

Это также позволяет использовать общий контроль доступа на основе ролей (RBAC) в ресурсах и устраняет необходимость в перекрестных запросах между приложениями и рабочими областями.

> [!NOTE]
> Прием и хранение данных для Application Insights ресурсов на основе рабочей области выставляются в Log Analytics рабочей области, где находятся данные. Дополнительные [сведения]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) о выставлении счетов за ресурсы Application Insights на основе рабочих областей.

Чтобы протестировать новый интерфейс, войдите в [портал Azure](https://portal.azure.com)и создайте ресурс Application Insights:

![Ресурс Application Insights на основе рабочей области](./media/create-workspace-resource/create-workspace-based.png)

Если у вас еще нет существующей рабочей области Log Analytics, [обратитесь к документации по созданию рабочей области log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Для ресурсов на основе рабочей области общедоступной предварительной версии в **настоящее время ограничены Западная часть США 2, Восточная часть США и Юго-Центральный регион.**

После создания ресурса вы увидите соответствующую информацию о рабочей области в области **Обзор** :

![Имя рабочей области](./media/create-workspace-resource/workspace-name.png)

Щелкнув синий текст ссылки, вы перейдете в соответствующую рабочую область Log Analytics, где можно воспользоваться преимуществами новой среды запросов единой рабочей области.

> [!NOTE]
> Мы по-прежнему предоставляем полную обратную совместимость для Application Insightsных запросов к ресурсам, книг и оповещений на основе журналов в Application Insights. Чтобы запросить/просмотреть [новую структуру или схему таблицы на основе рабочей области](apm-tables.md) , сначала необходимо перейти в рабочую область log Analytics. Во время предварительной версии выбор **журналов** из панелей Application Insights предоставит вам доступ к классическому интерфейсу Application Insightsных запросов.

## <a name="copy-the-connection-string"></a>Копирование строки подключения

[Строка подключения]() определяет ресурс, с которым необходимо связать данные телеметрии. Он также позволяет изменять конечные точки, которые ресурс будет использовать в качестве назначения для телеметрии. Необходимо скопировать строку подключения и добавить ее в код приложения или в переменную среды.

![Щелкните и скопируйте ключ инструментирования](./media/create-new-resource/instrumentation-key.png)

## <a name="monitoring-configuration"></a>Конфигурация мониторинга

После создания ресурса Application Insights на основе рабочей области Настройка наблюдения относительно проста.

### <a name="code-based-application-monitoring"></a>Мониторинг приложений на основе кода

Для мониторинга приложений на основе кода нужно просто установить соответствующий пакет SDK Application Insights и указать ключ инструментирования или строку подключения к только что созданному ресурсу.  

Подробную документацию по настройке пакета SDK для Application Insights для мониторинга на основе кода см. в документации, относящейся к языку или платформе.

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [Фоновые задачи & современные консольные приложения (.NET и .NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [Классические консольные приложения (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Отслеживание некодированного кода и создание ресурсов Visual Studio

Для безпрограммного отслеживания таких служб, как функции Azure и службы приложений Azure, необходимо сначала создать ресурс Application Insights на основе рабочей области, а затем указать этот ресурс на этапе настройки мониторинга.

Хотя эти службы предлагают возможность создания нового Application Insights ресурса в рамках собственного процесса создания ресурсов, ресурсы, созданные с помощью этих параметров пользовательского интерфейса, в настоящее время ограничены классической Application Insightsой.

То же самое относится к интерфейсу Application Insights создания ресурсов в Visual Studio для ASP.NET и ASP.NET Core. Необходимо выбрать существующий ресурс на основе рабочей области с помощью пользовательского интерфейса включения мониторинга Visual Studio. Выбор пункта создать ресурс в Visual Studio приведет к ограничению создания классического Application Insights ресурса.

## <a name="creating-a-resource-automatically"></a>Автоматическое создание ресурса

### <a name="azure-cli"></a>Azure CLI

Чтобы получить доступ к командам предварительной версии Application Insights Azure CLI, сначала необходимо выполнить следующие действия.

```azurecli
 az extension add -n application-insights
```

Если не выполнить `az extension add` команду, появится сообщение об ошибке, в котором будет указано следующее:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Теперь можно выполнить следующие действия, чтобы создать ресурс Application Insights:

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

Полный Azure CLI документации по этой команде см. в документации по [Azure CLI](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

В `New-AzApplicationInsights` настоящее время команда PowerShell не поддерживает создание ресурса Application Insights на основе рабочей области. Чтобы создать ресурс на основе рабочей области с помощью PowerShell, можно использовать приведенные ниже шаблоны Azure Resource Manager и выполнить развертывание с помощью PowerShell.

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

После создания ресурса Application Insights на основе рабочей области можно изменить связанную с ним Log Analytics рабочую область.

В области ресурсов Application Insights выберите **Свойства**  >  **изменить рабочую область**  >  **log Analytics рабочие области** .

## <a name="next-steps"></a>Дальнейшие действия

* [Просмотр метрик](../../azure-monitor/platform/metrics-charts.md)
* [Написание запросов аналитики](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
