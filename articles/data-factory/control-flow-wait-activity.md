---
title: Действие Wait в фабрике данных Azure | Документация Майкрософт
description: Действие Wait приостанавливает обработку в конвейере на указанный период.
services: data-factory
documentationcenter: ''
author: shlo
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/12/2018
ms.author: shlo
ms.openlocfilehash: 66d79bc1597cd8f3c7e01eb8227eb7c91ba04d1d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764757"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Выполнить действие wait в фабрике данных Azure
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

Свойство | ОПИСАНИЕ | Допустимые значения | Обязательно для заполнения
-------- | ----------- | -------------- | --------
name | Имя действия `Wait`. | String | Да
тип | Для этого свойства необходимо задать значение **Wait**. | String | Да
waitTimeInSeconds | Период ожидания в секундах перед возобновлением обработки в конвейере. | Целое число  | Да

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

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
