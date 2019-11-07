---
title: Действие Wait в фабрике данных Azure
description: Действие Wait приостанавливает обработку в конвейере на указанный период.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: f9dd53fded06eec169219d00993620a0f2aa2bf0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678243"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Выполнение действия Wait в фабрике данных Azure
Если в конвейере используется действие Wait, он приостанавливает обработку на указанный период, прежде чем возобновить выполнение последующих действий. 

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Свойства типа

Свойство | Description (Описание) | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
name | Имя действия `Wait`. | string | Да
type | Для этого свойства необходимо задать значение **Wait**. | string | Да
waitTimeInSeconds | Период ожидания в секундах перед возобновлением обработки в конвейере. | Целое число | Да

## <a name="example"></a>Пример

> [!NOTE]
> Этот раздел содержит определения JSON и примеры команд PowerShell для выполнения действий в конвейере. Пошаговые инструкции по созданию конвейера фабрики данных с помощью Azure PowerShell и определений JSON см. в [этом руководстве](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Конвейер с действием Wait
В этом примере конвейер содержит два действия: **Until** и **Wait**. Действие Wait настроено для ожидания в течение одной секунды. Конвейер запускает веб-действие в цикле с секундным периодом ожидания между запусками. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [действие условия If](control-flow-if-condition-activity.md);
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
