---
title: Создание зависимостей триггера "переворачивающегося" окна в Фабрике данных Azure | Документация Майкрософт
description: Узнайте, как создать зависимость в триггере "переворачивающегося" окна в Фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: 6e5e293e9759f091b6537d5efab9884e0a20fabc
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725430"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Создание зависимости триггера, который запускает конвейер в "переворачивающемся" окне

В этой статье описано, как создать зависимость в триггере "переворачивающегося" окна. Общие сведения об этом типе триггеров см. в статье [Создание триггера, который запускает конвейер в "переворачивающемся" окне](how-to-create-tumbling-window-trigger.md).

Чтобы создать цепочку зависимостей и убедиться в том, что триггер в фабрике данных выполняется только после успешного выполнения другого триггера, используйте этот дополнительный компонент для создания зависимости "переворачивающегося" окна.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Создание зависимости в пользовательском интерфейсе Фабрики данных Azure

Чтобы создать зависимость в триггере, последовательно выберите **Trigger > Advanced > New** (Триггер > Дополнительно > Создать), а затем выберите триггер, для которого будет создана зависимость, с соответствующими смещением и размером. Нажмите кнопку **Finish** (Готово) и опубликуйте изменения фабрики данных, чтобы зависимости вступили в силу.

![Создание зависимостей](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Создание зависимостей")

## <a name="tumbling-window-dependency-properties"></a>Свойства зависимостей "переворачивающегося" окна

Триггер окна "переворачивающегося" с зависимостью имеет следующие свойства:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

В следующей таблице приведен список атрибутов, необходимых для определения зависимости "переворачивающегося" окна.

| **Имя свойства** | **Описание**  | **Тип** | **Обязательный** |
|---|---|---|---|
| type  | В этом раскрывающемся меню отображаются все существующие триггеры "переворачивающегося" окна. Выберите триггер, для которого будет настроена зависимость.  | Тумблингвиндовтригжердепенденциреференце или Селфдепенденцитумблингвиндовтригжерреференце | Да |
| offset | Смещение триггера зависимости. Укажите значение в формате периода времени и допускаются и отрицательные, и положительные смещения. Это свойство является обязательным, если триггер зависит от самого себя и во всех остальных случаях он является необязательным. Самозависимости всегда должны иметь отрицательные смещения. Если значение не указано, окно совпадает с самим триггером. | Временной диапазон<br/>(чч:мм:сс) | Самозависимость: Да<br/>Другое: Нет |
| размер | Размер "переворачивающегося" окна зависимости. Укажите положительное значение TimeSpan. Это необязательное свойство. | Временной диапазон<br/>(чч:мм:сс) | Нет  |

> [!NOTE]
> Триггер окна "переворачивающегося" может зависеть не более чем от двух других триггеров.

## <a name="tumbling-window-self-dependency-properties"></a>Свойства самозависимостей "переворачивающегося" окна

В сценариях, где триггер не должен переходить к следующему окну до тех пор, пока предыдущее окно не будет успешно завершено, создайте самостоятельную зависимость. Триггер самозависимости, зависящий от успешности предыдущих запусков самого себя в течение предыдущего часа, будет иметь следующие свойства:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Сценарии использования и примеры

Ниже приведены примеры сценариев и использование свойств зависимости окна "переворачивающегося".

### <a name="dependency-offset"></a>Смещение зависимости

![Пример смещения](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Пример смещения")

### <a name="dependency-size"></a>Размер зависимости

![Пример размера](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Пример размера")

### <a name="self-dependency"></a>Самозависимость

![Самостоятельная зависимость](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Самостоятельная зависимость")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Зависимость от триггера другого "переворачивающегося" окна

Ежедневное задание обработки телеметрии в зависимости от задания, агрегированного за последние семь дней, с последующим созданием потоковых окон с пошаговыми интервалами.

![Пример зависимости](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Пример зависимости")

### <a name="dependency-on-itself"></a>Зависимость от самого себя

Ежедневное задание без разрывов в потоках вывода задания:

![Пример с] самозависимостью (media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Пример с") самозависимостью

## <a name="monitor-dependencies"></a>Мониторинг зависимостей

Вы можете отслеживать цепочку зависимостей и соответствующие окна на странице триггер Запуск мониторинга. Последовательно выберите **Monitoring > Trigger Runs** (Мониторинг > Выполнение триггеров).

![Мониторинг запусков триггеров](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Мониторинг запусков триггеров")

Щелкните значок действия, чтобы просмотреть все запуски зависимого триггера выбранного окна.

![Мониторинг зависимостей](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Мониторинг зависимостей")

В приведенном выше примере ежедневный триггер зависит от ежечасного триггера без определенного окна и со смещением 3 часа. В результате триггер запускается после 24 успешных запусков зависимости.

## <a name="next-steps"></a>Следующие шаги

* Узнайте [, как создать триггер окна "переворачивающегося"](how-to-create-tumbling-window-trigger.md)
