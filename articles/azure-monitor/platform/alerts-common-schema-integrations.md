---
title: Как интегрировать общую схему оповещения с приложениями логики
description: Узнайте, как создать логическое приложение, которое использует общую схему оповещения для обработки всех ваших оповещений.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668236"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Как интегрировать общую схему оповещения с приложениями логики

В этой статье показано, как создать логическое приложение, которое использует общую схему оповещения для обработки всех ваших оповещений.

## <a name="overview"></a>Обзор

[Общая схема оповещения](https://aka.ms/commonAlertSchemaDocs) обеспечивает стандартизированную и расширяемую схему JSON во всех различных типах оповещений. Схема общего оповещения наиболее полезна, когда заемные программы — через веб-крючки, сборники и логические приложения. В этой статье мы демонстрируем, как одно логическое приложение может быть нарастать для обработки всех ваших оповещений. Те же принципы могут быть применены и к другим программным методам. Логическое приложение, описанное в этой статье, создает четко определенные переменные для ["основных" полей,](alerts-common-schema-definitions.md#essentials)а также описывает, как можно обрабатывать конкретную логику [типа оповещения.](alerts-common-schema-definitions.md#alert-context)


## <a name="prerequisites"></a>Предварительные требования 

Эта статья предполагает, что читатель знаком с 
* Настройка правил оповещения[(метрика,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) [журнал,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) [журнал активности)](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)
* Настройка [групп действий](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Включение [общей схемы оповещения](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) внутри групп действий

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Создание приложения логики, используя общую схему оповещения

1. Выполните [шаги, изложенные для создания приложения логики.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app) 

1.  Выберите триггер **При получении HTTP-запроса**.

    ![Триггеры приложений логики](media/action-groups-logic-app/logic-app-triggers.png "Триггеры приложений логики")

1.  Выберите **Изменить**, чтобы изменить триггер HTTP-запроса.

    ![Триггеры запроса HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Триггеры запроса HTTP")


1.  Копировать и вставить следующую схему:

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

1. Выберите **+** **новый шаг,** а затем выберите **Добавить действие.**

    ![Добавление действия](media/action-groups-logic-app/add-action.png "Добавление действия")

1. На этом этапе можно добавлять различные разъемы (команды Microsoft, Slack, Salesforce и т.д.) в зависимости от ваших конкретных бизнес-требований. Вы можете использовать "основные поля" из коробки. 

    ![Основные поля](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Основные поля")
    
    Кроме того, можно авторить условную логику, основанную на типе оповещения, используя опцию «Выражение».

    ![Выражение приложения логики](media/alerts-common-schema-integrations/logic-app-expressions.png "Выражение приложения логики")
    
     [Поле "MonitoringService"](alerts-common-schema-definitions.md#alert-context) позволяет однозначно идентифицировать тип оповещения, на основе которого можно создать условную логику.

    
    Например, ниже фрагмент проверяет, является ли оповещение на основе оповещения о поиске Application Insights, и если это так, то результаты поиска печатается. В противном- то и дело, он печатает 'NA'.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Подробнее о [написании логических выражений приложения.](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)

    


## <a name="next-steps"></a>Дальнейшие действия

* [Подробнее о группах действий.](../../azure-monitor/platform/action-groups.md)
* [Подробнее о схеме общего оповещения.](https://aka.ms/commonAlertSchemaDocs)

