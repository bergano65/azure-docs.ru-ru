---
title: Действие Filter в фабрике данных Azure | Документация Майкрософт
description: Действие Filter фильтрует входные данные.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: shlo
ms.openlocfilehash: 787c9393e2700bd7ed349b501e70abc4a0687b9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60554853"
---
# <a name="filter-activity-in-azure-data-factory"></a>Действие Filter в фабрике данных Azure
Вы можете использовать действие Filter в конвейере для применения выражения фильтра к входному массиву. 

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Свойства типа

Свойство | ОПИСАНИЕ | Допустимые значения | Обязательно для заполнения
-------- | ----------- | -------------- | --------
name | Имя действия `Filter`. | String | Да
type | Должно быть задано значение **filter**. | String | Да
условие | Условие, которое можно использовать для фильтрации входных данных. | Выражение | Да
элементы | Входной массив, к которому должен применяться фильтр. | Выражение | Да

## <a name="example"></a>Пример

В этом примере конвейер содержит два действия: **Filter** и **ForEach**. Действие Filter настроено для фильтрации входного массива на наличие элементов со значением больше 3. Затем действие ForEach выполняет итерацию по отфильтрованным значениям и ожидает в течение времени, заданного текущим значением ( в секундах).

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
                "name": "MyForEach",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
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
