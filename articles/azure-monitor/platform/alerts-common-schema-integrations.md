---
title: Как интегрировать в общей схеме оповещений с помощью Logic Apps
description: Узнайте, как создать приложение логики, которое использует общую схему оповещения для обработки всех предупреждений.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734888"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Как интегрировать в общей схеме оповещений с помощью Logic Apps

В этой статье показано, как создать приложение логики, которое использует общую схему оповещения для обработки всех предупреждений.

## <a name="overview"></a>Обзор

[Общей схеме оповещений](https://aka.ms/commonAlertSchemaDocs) предоставляет стандартизированный и расширяемую схему JSON для всех разных типов оповещений. Общая схема оповещения наиболее полезна в тех случаях, когда управлять программными средствами — с помощью веб-перехватчиков, модулей Runbook и logic apps. В этой статье мы покажем, как единого приложения логики могут разрабатываться для обработки всех предупреждений. Те же принципы могут применяться к другим программными методами. Приложение логики, описанные в этой статье мы создадим четко определенных переменных для [«важных» полей](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields), а также описывает, как обработать [тип оповещений](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) определенной логики.


## <a name="prerequisites"></a>Технические условия 

В этой статье предполагается, что читатель знаком с 
* Настройка правила генерации оповещений ([метрика](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [журнала](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [журнал действий](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Настройка [группы действий](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Включение [общей схеме оповещений](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) из внутри группы действий

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Создание приложения логики, используя в общей схеме оповещений

1. Выполните [шаги, описанные для создания приложения логики](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Выберите триггер **При получении HTTP-запроса**.

    ![Триггеры приложения логики](media/action-groups-logic-app/logic-app-triggers.png "Триггеры приложения логики")

1.  Выберите **Изменить**, чтобы изменить триггер HTTP-запроса.

    ![Триггеры HTTP-запроса](media/action-groups-logic-app/http-request-trigger-shape.png "Триггеры HTTP-запроса")


1.  Скопируйте и вставьте следующую схему:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Выберите **+** **Новый шаг**, а затем щелкните **Добавить действие**.

    ![Добавление действия](media/action-groups-logic-app/add-action.png "Добавление действия")

1. На этом этапе можно добавить широкий набор соединителей (Microsoft Teams, Slack, Salesforce и др.) на основе требований своего бизнеса. Можно использовать «важных полей» out-of--box. 

    ![Важных полей](media/alerts-common-schema-integrations/logic-app-essential-fields.png "важных полей")
    
    Кроме того вы можете создавать условную логику в зависимости от типа оповещения, с помощью параметра «Выражение».

    ![Выражение логики приложения](media/alerts-common-schema-integrations/logic-app-expressions.png "выражение приложения логики")
    
     [Поля «monitoringService»](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) позволяет однозначно идентифицировать тип оповещения на основе, на которой можно создавать условную логику.

    
    Например ниже фрагмент кода проверяет, если предупреждение оповещения журнала на основе Application Insights и если да, выводит результаты поиска. В противном случае он выводит «Н/д».

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Дополнительные сведения о [написания логики приложения выражения](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения о группах действий](../../azure-monitor/platform/action-groups.md).
* [Дополнительные сведения об общей схеме оповещений](https://aka.ms/commonAlertSchemaDocs).

