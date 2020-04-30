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
ms.openlocfilehash: e6158938d01b6e5da74ed046d2a74e0dfd827f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417918"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Выполнение действия Wait в фабрике данных Azure
Если в конвейере используется действие Wait, он приостанавливает обработку на указанный период, прежде чем возобновить выполнение последующих действий. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


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

Свойство | Описание | Допустимые значения | Обязательный
-------- | ----------- | -------------- | --------
name | Имя действия `Wait`. | Строка | Да
type | Для этого свойства необходимо задать значение **Wait**. | Строка | Да
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

## <a name="next-steps"></a>Дальнейшие шаги
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие For Each](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
