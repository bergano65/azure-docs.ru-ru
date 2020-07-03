---
title: Собирайте и анализируйте журналы действий Azure в Azure Monitor
description: Собирайте журналы действий Azure в журналах Azure Monitor и используйте решение мониторинга для анализа и поиска в журнале действий Azure во всех подписках Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382868"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Собирайте и анализируйте журналы действий Azure в Azure Monitor
[Журнал действий Azure](platform-logs-overview.md) — это [Журнал платформы](platform-logs-overview.md) , который позволяет получить представление о событиях уровня подписки, произошедших в Azure. Вы можете просмотреть журнал действий в портал Azure, а также настроить его для отправки в Log Analytics рабочую область, чтобы включить дополнительные функции Azure Monitor. В этой статье описывается, как выполнить эту настройку и как отправить журнал действий в службу хранилища Azure и концентраторы событий.

Сбор журнала действий в рабочей области Log Analytics предоставляет следующие преимущества.

- Для данных журнала действий, хранящихся в Log Analytics рабочей области, не взимается нагрузка на данные или хранение данных.
- Сопоставьте данные журнала действий с другими данными мониторинга, собранными Azure Monitor.
- Используйте запросы журналов для выполнения сложного анализа и получения подробных сведений о записях журнала действий.
- Используйте оповещения журнала с записями действий, что позволяет использовать более сложные алгоритмы оповещений.
- Храните записи журнала действий дольше 90 дней.
- Объедините записи журнала из нескольких подписок Azure и клиентов в единое место для анализа вместе.

> [!IMPORTANT]
> Для сбора журналов между клиентами требуется [Azure лигхсаусе](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Сбор данных журнала действий
Журнал действий автоматически собирается для [просмотра в портал Azure](activity-log-view.md). Чтобы выполнить сбор данных в Log Analytics рабочей области или отправить хранилище Azure или концентраторы событий, создайте [параметр диагностики](diagnostic-settings.md). Это тот же метод, который используется журналами ресурсов, что делает его единообразным для всех [журналов платформы](platform-logs-overview.md).  

Чтобы создать параметр диагностики для журнала действий, выберите **параметры диагностики** в меню **журнал действий** в Azure Monitor. Дополнительные сведения о создании параметров см. в статье [Создание параметров диагностики для сбора журналов и метрик платформы в Azure](diagnostic-settings.md) . Описание категорий, которые можно фильтровать, см. в разделе [категории в журнале действий](activity-log-view.md#categories-in-the-activity-log) . Если у вас есть устаревшие параметры, убедитесь, что они отключены перед созданием параметра диагностики. Включение обоих значений может привести к дублированию данных.

![Параметры диагностики](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Сейчас параметр диагностики уровня подписки можно создать только с помощью шаблона портал Azure и диспетчер ресурсов. 


## <a name="legacy-settings"></a>Устаревшие параметры 
Хотя параметры диагностики являются предпочтительным способом отправки журнала действий в разные места назначения, устаревшие методы будут продолжать работать, если вы не решите заменить параметром диагностики. Параметры диагностики имеют следующие преимущества по сравнению с устаревшими методами, поэтому рекомендуется обновить конфигурацию.

- Последовательный метод для сбора всех журналов платформы.
- Собирайте журналы действий для нескольких подписок и клиентов.
- Фильтрация коллекции для сбора только журналов для определенных категорий.
- Собирайте все категории журналов действий. Некоторые категории не собираются с помощью устаревшего метода.
- Ускоренная задержка приема журнала. В предыдущем методе задержка составляет около 15 минут, а параметры диагностики добавляются только около 1 минуты.



### <a name="log-profiles"></a>Профили журнала
Профили журналов — это устаревший метод отправки журнала действий в службу хранилища Azure или концентраторы событий. Используйте следующую процедуру, чтобы продолжить работу с профилем журнала или отключить его при подготовке к миграции в параметр диагностики.

1. В меню **Azure Monitor** в портал Azure выберите пункт **Журнал действий**.
3. Щелкните **параметры диагностики**.

   ![Параметры диагностики](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Щелкните сиреневый баннер для устаревшего интерфейса.

    ![Устаревшие возможности](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Рабочая область Log Analytics
Устаревший метод для сбора журнала действий в Log Analytics рабочей области подключается к журналу в конфигурации рабочей области. 

1. В меню **log Analytics рабочие области** в портал Azure выберите рабочую область, чтобы получить журнал действий.
1. В разделе **Источники данных рабочей области** в меню рабочей области выберите **Журнал действий Azure**.
1. Щелкните подписку, которую нужно подключить.

    ![Рабочие области](media/activity-log-collect/workspaces.png)

1. Нажмите кнопку **подключить** , чтобы подключить журнал действий в подписке к выбранной рабочей области. Если подписка уже подключена к другой рабочей области, нажмите кнопку **Отключить** сначала, чтобы отключить ее.

    ![Подключение рабочих областей](media/activity-log-collect/connect-workspace.png)


Чтобы отключить этот параметр, выполните ту же процедуру и нажмите кнопку **Отключить** , чтобы удалить подписку из рабочей области.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Анализ журнала действий в рабочей области Log Analytics
При подключении журнала действий к рабочей области Log Analytics записи будут записываться в рабочую область в таблицу с именем *AzureActivity* , которую можно получить с помощью [запроса журнала](../log-query/log-query-overview.md). Структура этой таблицы зависит от [категории записи журнала](activity-log-view.md#categories-in-the-activity-log). Описание каждой категории см. в статье [схема событий журнала действий Azure](activity-log-schema.md) .


### <a name="data-structure-changes"></a>Изменения структуры данных
Параметры диагностики собираются те же данные, что и устаревший метод, используемый для получения журнала действий с некоторыми изменениями в структуре таблицы *AzureActivity* .

Столбцы в следующей таблице не являются устаревшими в обновленной схеме. Они по-прежнему существуют в *AzureActivity* , но они не будут иметь данных. Замена этих столбцов не является новой, но содержит те же данные, что и устаревший столбец. Они имеют другой формат, поэтому может потребоваться изменить запросы к журналу, которые их используют. 

| Устаревший столбец | Столбец замены |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| активитисубстатус | активитисубстатусвалуе |
| OperationName     | оператионнамевалуе     |
| ResourceProvider  | ресаурцепровидервалуе  |

> [!IMPORTANT]
> В некоторых случаях значения в этих столбцах могут быть в верхнем регистре. При наличии запроса, содержащего эти столбцы, следует использовать [оператор = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) для сравнения без учета регистра.

Следующий столбец был добавлен в *AzureActivity* в обновленной схеме:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Решение для мониторинга журналов действий
Решение для мониторинга Log Analytics Azure будет считаться устаревшим и заменено книгой, использующей обновленную схему в рабочей области Log Analytics. Вы по-прежнему можете использовать решение, если оно уже включено, но его можно использовать только при сборе журнала действий с использованием устаревших параметров. 



### <a name="use-the-solution"></a>Использование решения
Доступ к решениям мониторинга осуществляется из меню " **монитор** " в портал Azure. Выберите **больше** в разделе **Insights** , чтобы открыть страницу **обзора** с плитками решения. На плитке **журналы действий Azure** отображается количество записей **AzureActivity** в рабочей области.

![Плитка "Журналы действий Azure"](media/collect-activity-logs/azure-activity-logs-tile.png)


Щелкните плитку **журналы действий Azure** , чтобы открыть представление **журналы действий Azure** . Представление включает в себя элементы визуализации, приведенные в следующей таблице. Каждая часть содержит до 10 элементов, соответствующих критериям этой части за указанный диапазон времени. Можно выполнить запрос журнала, возвращающий все совпадающие записи, щелкнув **Просмотреть все** в нижней части части.

![Панель мониторинга "Журналы действий Azure"](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Включение решения для новых подписок
Вскоре вы больше не сможете добавить решение "аналитика журналов действий" в подписку с помощью портал Azure. Его можно добавить, используя следующую процедуру с шаблоном Resource Manager. 

1. Скопируйте следующий код JSON в файл с именем *активитилогтемплате*. JSON.

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

2. Разверните шаблон с помощью следующих команд PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о [журнале действий](platform-logs-overview.md).
- Дополнительные сведения о [платформе Azure Monitor Data Platform](data-platform.md).
- Используйте [запросы журналов](../log-query/log-query-overview.md) для просмотра подробных сведений из журнала действий.
