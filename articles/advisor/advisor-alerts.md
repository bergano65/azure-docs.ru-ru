---
title: Создание предупреждений советника Azure для новых рекомендаций
description: Создание предупреждений советника Azure для новых рекомендаций
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443158"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Создание предупреждений советника Azure о новых рекомендациях 

В этой статье показано, как настроить оповещение о новых рекомендациях от Azure Advisor с помощью портала Azure и шаблонов управления ресурсами Azure. 

Всякий раз, когда Azure Advisor обнаруживает новую рекомендацию для одного из ваших ресурсов, событие хранится в [журнале деятельности Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) Вы можете настроить оповещения для этих событий от Azure Advisor, используя рекомендацию конкретных оповещений создания. Можно выбрать подписку и опциональную группу ресурсов, чтобы указать ресурсы, на которые вы хотите получать оповещения. 

Вы также можете определить типы рекомендаций, используя следующие свойства:

* Категория
* Уровень воздействия
* Тип рекомендации

Вы также можете настроить действие, которое будет происходить при срабатывании оповещения:  

* Выбор существующей группы действий
* Создание новой группы действий

Чтобы узнать больше о группах действий, [см.](../azure-monitor/platform/action-groups.md)

> [!NOTE] 
> Предупреждения советников в настоящее время доступны только для рекомендаций по высокой доступности, производительности и стоимости. Рекомендации по безопасности не поддерживаются. 

## <a name="in-the-azure-portal"></a>На портале Azure
1. На **портале**выберите **Azure Advisor.**

    ![Советник Azure на портале](./media/advisor-alerts/create1.png)

2. В разделе **Мониторинг** левого меню выберите **оповещения**. 

    ![Оповещения в советнике](./media/advisor-alerts/create2.png)

3. Выберите **новое оповещение советника**.

    ![Предупреждение нового советника](./media/advisor-alerts/create3.png)

4. В разделе **Область** действия выберите подписку и опционную группу ресурсов, о которых вы хотите предупредить. 

    ![Область оповещения советника](./media/advisor-alerts/create4.png)

5. В разделе **Состояние** выберите метод, который вы хотите использовать для настройки оповещения. Если вы хотите предупредить все рекомендации по определенной категории и/или уровню воздействия, выберите **Категорию и уровень воздействия.** Если вы хотите предупредить все рекомендации определенного типа, выберите **тип Рекомендации.**

    ![Состояние оповещения советника Azure](./media/advisor-alerts/create5.png)

6. В зависимости от выбранной вами опции Настройка по выбранной опции, вы сможете указать критерии. Если вы хотите все рекомендации, просто оставьте оставшиеся поля пустыми. 

    ![Группа действий по предупреждению консультантов](./media/advisor-alerts/create6.png)

7. В разделе **групп действий** выберите **Добавить существующую,** чтобы использовать группу действий, созданную вами, или выберите **Создать новую** для настройки новой [группы действий.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 

    ![Предупреждение советника добавить существующие](./media/advisor-alerts/create7.png)

8. В разделе «Оповещение» укажите имя и краткое описание. Если вы хотите включить оповещение, оставьте **правило Включить при** наборе выбора создания в **Да.** Затем выберите группу ресурсов, чтобы сохранить оповещение. Это не повлияет на таргетинговую область рекомендации. 

    ![Баннер советника Azure](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>С шаблоном управления ресурсами Azure

Этот шаблон менеджера ресурсов создает оповещение о рекомендациях и новую группу действий.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Настройка рекомендаций для использования веб-крючка
В этом разделе показано, как настроить оповещения советника Azure для отправки рекомендательных данных через веб-крючки в существующие системы. 

Вы можете настроить оповещения для уведомления, когда у вас есть новая рекомендация советника на одном из ваших ресурсов. Эти оповещения могут уведомлять вас по электронной почте или текстовым сообщениям, но они также могут быть использованы для интеграции с существующими системами через веб-крюк. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Использование полезной нагрузки рекомендации советника оповещения
Если вы хотите интегрировать оповещения Советника в свои собственные системы с помощью веб-крючка, вам необходимо будет разобрать полезную нагрузку JSON, которая отправляется из уведомления. 

При настройке группы действий для этого оповещения вы выбираете, хотите ли использовать общую схему оповещения. При выборе общей схемы оповещения ваша полезная нагрузка будет выглядеть следующим образом: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Если вы не используете общую схему, ваша полезная нагрузка выглядит следующим образом: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

В любой схеме можно определить события рекомендации советника, ища `Microsoft.Advisor/recommendations/available/action` **eventSource** есть `Recommendation` и **operationи:**

Некоторые из других важных полей, которые вы можете использовать являются: 

* *alertTargetIDs* (в общей схеме) или *resourceId* (схема наследия)
* *рекомендацияType*
* *рекомендацияИмя*
* *рекомендацияКатегория*
* *рекомендацияImpact*
* *рекомендацияResourceLink*


## <a name="manage-your-alerts"></a>Управление оповещениями 

Из Azure Advisor можно отсечь, удалить или отключить и включить оповещения рекомендаций. 

1. На **портале**выберите **Azure Advisor.**

    ![Баннер советника Azure](./media/advisor-alerts/create1.png)

2. В разделе **Мониторинг** левого меню выберите **оповещения**.

    ![Баннер советника Azure](./media/advisor-alerts/create2.png)

3. Чтобы отсеять оповещение, нажмите на имя оповещения, чтобы открыть оповещение и отсвативать поля, которые вы хотите отсеить.

4. Чтобы удалить, включить или отключить оповещение, нажмите на эллипс в конце строки, а затем выберите действие, выразив щебня.
 

