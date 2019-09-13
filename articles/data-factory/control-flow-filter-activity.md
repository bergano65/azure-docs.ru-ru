---
title: Действие Filter в фабрике данных Azure | Документация Майкрософт
description: Действие Filter фильтрует входные данные.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: c0f5d3264d953498af61c6e8d36dadee7dd61931
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915516"
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

Свойство | Описание | Допустимые значения | Обязательное значение
-------- | ----------- | -------------- | --------
name | Имя действия `Filter`. | Строковое | Да
type | Должно быть задано значение **filter**. | Строковое | Да
условие | Условие, которое можно использовать для фильтрации входных данных. | Выражение | Да
items | Входной массив, к которому должен применяться фильтр. | Выражение | Да

## <a name="example"></a>Пример

В этом примере конвейер содержит два действия: **Filter** и **ForEach**. Действие Filter настроено для фильтрации входного массива на наличие элементов со значением больше 3. Затем действие ForEach выполняет перебор отфильтрованных значений и **присваивает переменной значение** текущему значению.

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
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>Следующие шаги
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
