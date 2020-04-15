---
title: Сбор и анализ деятельности Azure в Azure Monitor
description: Соберите журнал azure Activity Log in Azure Monitor Logs и используйте решение мониторинга для анализа и поиска журнала деятельности Azure во всех подписях Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382868"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Сбор и анализ деятельности Azure в Azure Monitor
[Журнал деятельности Azure](platform-logs-overview.md) — это [журнал платформы,](platform-logs-overview.md) который позволяет получить представление о событиях уровня подписки, произошедших в Azure. Во время просмотра журнала activity на портале Azure следует настроить его для отправки в рабочее пространство analytics для включения дополнительных функций Azure Monitor. В этой статье описывается, как выполнять эту конфигурацию и как отправить журнал активности в хранилища и концентраторы событий Azure.

Сбор журнала активности в рабочей области Log Analytics обеспечивает следующие преимущества:

- Отсутствие платы за проглатывание данных или хранение данных для журналов activity, хранящихся в рабочей области Log Analytics.
- Соотнесите данные журнала активности с другими данными мониторинга, собранными Azure Monitor.
- Используйте журналные запросы для выполнения сложного анализа и получения глубоких сведений о записях в журнале activity.
- Используйте оповещения о журнале с записями активности, позволяющими использовать более сложную логику оповещения.
- Храните записи журналов активности более 90 дней.
- Консолидируйте записи журналов из нескольких подписок Azure и арендаторов в одном месте для совместного анализа.

> [!IMPORTANT]
> Сбор журналов между арендаторами требует [Azure Маяк](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Сбор журнала активности
Регистрация активности собирается автоматически для [просмотра на портале Azure.](activity-log-view.md) Чтобы собрать его в рабочей области Log Analytics или отправить его в хранилище или концентраторы событий Azure, создайте [диагностическую настройку.](diagnostic-settings.md) Это тот же метод, используемый журналами ресурсов, что делает его последовательным для всех [журналов платформы.](platform-logs-overview.md)  

Чтобы создать диагностическую настройку для журнала Activity, выберите **параметры диагностики** из меню **журнала Activity** в Azure Monitor. Смотрите [Создать диагностическую настройку для сбора журналов платформ и метрик в Azure](diagnostic-settings.md) для получения подробной информации о создании настройки. См [Категории в журнале активности](activity-log-view.md#categories-in-the-activity-log) для описания категорий, которые можно отфильтровать. Если у вас есть какие-либо устаревшие настройки, убедитесь, что вы отпустите их перед созданием диагностической настройки. Наличие обоих включенных данных может привести к дублированию данных.

![Параметры диагностики](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> В настоящее время можно создать диагностическую настройку уровня подписки только с помощью портала Azure и шаблона менеджера ресурсов. 


## <a name="legacy-settings"></a>Настройки наследия 
В то время как диагностические настройки являются предпочтительным методом отправки журнала activity в различные пункты назначения, устаревшие методы будут продолжать работать, если вы не решите заменить диагностическую настройку. Диагностические настройки имеют следующие преимущества по сравнению с устаревшими методами, и рекомендуется обновить конфигурацию:

- Последовательный метод сбора всех журналов платформ.
- Сбор журнала активности через несколько подписок и арендаторов.
- Коллекция фильтров для сбора журналов только для определенных категорий.
- Соберите все категории журналов активности. Некоторые категории не собираются с помощью устаревшего метода.
- Более быстрая задержка для проглатывания журнала. Предыдущий метод имеет около 15 минут задержки в то время как диагностические настройки добавляет только около 1 минуты.



### <a name="log-profiles"></a>Профили журнала
Профили журнала — это устаревший метод отправки журнала активности в хранилища Azure или концентраторы событий. Используйте следующую процедуру, чтобы продолжить работу с профилем журнала или отключить его в рамках подготовки к переходу в диагностическую настройку.

1. Из меню **Azure Monitor** на портале Azure выберите **журнал activity.**
3. Нажмите **На настройки диагностики**.

   ![Параметры диагностики](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Нажмите фиолетовый баннер для наследия опыт.

    ![Наследие опыта](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Рабочая область Log Analytics
Устаревший метод сбора журнала Activity в рабочее пространство Log Analytics соединяет журнал в конфигурации рабочего пространства. 

1. Из меню **рабочих областей анализа журналов** на портале Azure выберите рабочее пространство для сбора журнала активности.
1. В разделе **«Источники данных рабочего пространства»** меню рабочего пространства выберите **журнал деятельности Azure.**
1. Нажмите на подписку, подключившуюся к ней.

    ![Рабочие области](media/activity-log-collect/workspaces.png)

1. Нажмите **Connect,** чтобы подключить журнал активности в подписке к выбранной рабочей области. Если подписка уже подключена к другому рабочему пространству, нажмите **"Отключьте"** сначала, чтобы отключить ее.

    ![Подключение рабочих пространств](media/activity-log-collect/connect-workspace.png)


Чтобы отключить настройку, выполните ту же процедуру и нажмите **Disconnect,** чтобы удалить подписку из рабочего пространства.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Анализ входа в журнал активности в рабочей области Log Analytics
При подключении журнала активности к рабочей области Log Analytics записи будут записаться в рабочее пространство в таблицу под названием *AzureActivity,* которую можно получить с [помощью запроса журнала.](../log-query/log-query-overview.md) Структура этой таблицы варьируется в зависимости от [категории входа в журнал.](activity-log-view.md#categories-in-the-activity-log) Ознакомьтесь с [схемой событий Azure Activity Log](activity-log-schema.md) для описания каждой категории.


### <a name="data-structure-changes"></a>Изменения структуры данных
Диагностические настройки собирают те же данные, что и устаревший метод, используемый для сбора журнала activity с некоторыми изменениями в структуре таблицы *AzureActivity.*

Столбцы в следующей таблице были унижены в обновленной схеме. Они все еще существуют в *AzureActivity,* но у них не будет данных. Замена этих столбцов не нова, но они содержат те же данные, что и и уеренный столбец. Они находятся в другом формате, поэтому может потребоваться изменить журнал запросов, которые их используют. 

| Обезображенный столбец | Колонка замены |
|:---|:---|
| АктивностьСтатус    | ActivityStatusValue    |
| АктивностьСубстатус | ActivitySubstatusValue |
| OperationName     | ОперацияНаи-Стоимость     |
| ResourceProvider  | РесурсПровайдерСтоимость  |

> [!IMPORTANT]
> В некоторых случаях значения в этих столбцах могут быть во всех верхних сеяных столбцах. Если у вас есть запрос, который включает эти столбцы, вы должны использовать [оператора](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) для сравнения случае в нечувствительном.

В обновленной схеме добавлена следующая колонка *AzureActivity:*

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Решение мониторинга журналов активности
Решение мониторинга Azure Log Analytics будет вскоре униточено и заменено рабочей книгой с использованием обновленной схемы в рабочей области Log Analytics. Вы все еще можете использовать решение, если оно уже включено, но оно может быть использовано только при сборе журнала activity с использованием устаревших настроек. 



### <a name="use-the-solution"></a>Использование решения
Решения мониторинга доступны из меню **Monitor** на портале Azure. Выберите **больше** в разделе **Исследования,** чтобы открыть страницу **Обзор** с плиткой решения. Плитка **журналов активности Azure** отображает количество записей **AzureActivity** в рабочем пространстве.

![Плитка "Журналы действий Azure"](media/collect-activity-logs/azure-activity-logs-tile.png)


Нажмите на плитку **журналов активности Azure,** чтобы открыть представление **журналов активности Azure.** Представление включает в себя части визуализации в следующей таблице. Каждая часть перечисляет до 10 элементов, соответствующих критериям, установленным этими частями для указанного диапазона времени. Вы можете запустить запрос журнала, который возвращает все соответствующие записи, нажав **Смотрите все** в нижней части.

![Панель мониторинга "Журналы действий Azure"](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Включить решение для новых подписок
В скором времени вы больше не сможете добавлять решение Activity Logs Analytics в подписку с помощью портала Azure. Вы можете добавить его с помощью следующей процедуры с шаблоном менеджера ресурсов. 

1. Копируйте следующий json в файл под названием *ActivityLogTemplate*.json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Развертывание шаблона с помощью следующих команд PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о [журнале активности](platform-logs-overview.md).
- Узнайте больше о [платформе данных Azure Monitor.](data-platform.md)
- Используйте [запросы журналов](../log-query/log-query-overview.md) для просмотра подробной информации из журнала активности.
