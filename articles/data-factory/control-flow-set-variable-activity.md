---
title: Установка действия переменной в фабрике данных Azure
description: Узнайте, как использовать установку действия переменной, чтобы задать значение существующей переменной, определенной в конвейере Фабрики данных
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879277"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Установка переменной активности на фабрике данных Azure

Используйте установку действия переменной, чтобы задать значение существующей переменной с типом "Строка", "Bool" или "Массив", определенной в конвейере Фабрики данных.

## <a name="type-properties"></a>Свойства типа

Свойство | Описание | Обязательно
-------- | ----------- | --------
name | Имя действия в конвейере | да
description | Текст, описывающий действия | Нет
type | Должна быть установлена установка **SetVariable** | да
value | Строка буквального или выражения объекта значение, которое переменная будет назначена | да
variableName | Имя переменной, которое будет задано этим действием | да

## <a name="incrementing-a-variable"></a>Приравливание переменной

Распространенный сценарий, включающий переменные в Azure Data Factory, использует переменную в качестве итератора в рамках действия до или форах. В установленной переменной не удается ссылаться на переменную, установленную в `value` поле. Чтобы обойти это ограничение, установите временную переменную, а затем создайте переменную второго набора. Действие переменной второго набора устанавливает значение итератора к временной переменной. 

Ниже приведен пример такого шаблона:

![Увеличение переменной](media/control-flow-set-variable-activity/increment-variable.png "Увеличение переменной")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о деятельности связанного потока управления, который поддерживается Фабрикой данных: 

- [Добавление действия переменной](control-flow-append-variable-activity.md)
