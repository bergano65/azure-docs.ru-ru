---
title: Создание акробатических зависимостей триггера окна
description: Узнайте, как создать зависимость в триггере "переворачивающегося" окна в Фабрике данных Azure.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 39ea8dda0fd823d3061b2cb29e1c548f99281c82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418802"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Создание зависимости триггера, который запускает конвейер в "переворачивающемся" окне
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описано, как создать зависимость в триггере "переворачивающегося" окна. Общие сведения об этом типе триггеров см. в статье [Создание триггера, который запускает конвейер в "переворачивающемся" окне](how-to-create-tumbling-window-trigger.md).

Чтобы создать цепочку зависимостей и убедиться в том, что триггер в фабрике данных выполняется только после успешного выполнения другого триггера, используйте этот дополнительный компонент для создания зависимости "переворачивающегося" окна.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Создание зависимости в пользовательском интерфейсе Фабрики данных Azure

Чтобы создать зависимость в триггере, последовательно выберите **Trigger > Advanced > New** (Триггер > Дополнительно > Создать), а затем выберите триггер, для которого будет создана зависимость, с соответствующими смещением и размером. Нажмите кнопку **Finish** (Готово) и опубликуйте изменения фабрики данных, чтобы зависимости вступили в силу.

![Создание зависимости](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Создание зависимости")

## <a name="tumbling-window-dependency-properties"></a>Свойства зависимостей "переворачивающегося" окна

Акробатика триггера окна с зависимостью имеет следующие свойства:

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

| **Имя свойства** | **Описание**  | **Тип** | **Обязательно** |
|---|---|---|---|
| type  | В этом раскрывающемся меню отображаются все существующие триггеры "переворачивающегося" окна. Выберите триггер, для которого будет настроена зависимость.  | TumblingWindowTriggerСсылка или SelfDependencyTumblingWindowОкноСправка | Да |
| offset | Смещение триггера зависимости. Обеспечить значение в формате промежутка времени и как отрицательные, так и положительные смещения разрешены. Это свойство является обязательным, если триггер зависит от себя и во всех других случаях это необязательно. Самозависимости всегда должны иметь отрицательные смещения. Если значение не указано, окно такое же, как и сам триггер. | Timespan<br/>(чч:мм:сс) | Самозависимость: Да<br/>Другое: Нет |
| size | Размер "переворачивающегося" окна зависимости. Обеспечить положительное значение временной продолжительности. Это необязательное свойство. | Timespan<br/>(чч:мм:сс) | нет  |

> [!NOTE]
> Акробатика триггера окна может зависеть от максимум двух других триггеров.

## <a name="tumbling-window-self-dependency-properties"></a>Свойства самозависимостей "переворачивающегося" окна

В сценариях, где триггер не должен переходить к следующему окну до тех пор, пока предыдущее окно не будет успешно завершено, создайте самозависимость. Триггер самозависимости, зависящий от успеха предыдущих запусков в предыдущем hr, будет иметь нижее свойства:

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

Ниже приведены иллюстрации сценариев и использования свойств зависимости окона акробатика.

### <a name="dependency-offset"></a>Смещение зависимости

![Смотный пример](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Смотный пример")

### <a name="dependency-size"></a>Размер зависимости

![Пример размера](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Пример размера")

### <a name="self-dependency"></a>Самозависимость

![Самозависимость](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Самозависимость")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Зависимость от триггера другого "переворачивающегося" окна

Ежедневная работа по обработке телеметрии в зависимости от другой ежедневной работы, агрегирующей выход за последние семь дней и генерирующей семидневные потоков оконного потока:

![Пример зависимости](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Пример зависимости")

### <a name="dependency-on-itself"></a>Зависимость от самого себя

Ежедневное задание без разрывов в потоках вывода задания:

![Пример самозависимости](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Пример самозависимости")

Для демонстрации того, как создавать зависимые конвейеры на фабрике данных Azure с помощью акробатического триггера окна, смотрите следующее видео:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Мониторинг зависимостей

Можно отслеживать цепочку зависимостей и соответствующие окна со страницы мониторинга триггерного запуска. Последовательно выберите **Monitoring > Trigger Runs** (Мониторинг > Выполнение триггеров). Под столбцом действий можно повторно запустить триггер или просмотреть его зависимости.

![Мониторинг запусков триггеров](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Мониторинг запусков триггеров")

При нажатии на кнопку "Просмотр зависимостей триггеров" можно увидеть состояние зависимостей. Если один из триггеров зависимости выходит из строя, необходимо успешно запустить ее, чтобы выполнить зависимый триггер. Акробатика триггер акробатика будет ждать на зависимостей в течение семи дней до времени.

![Мониторинг зависимостей](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Мониторинг зависимостей")

Для более визуального просмотра графика зависимости триггера выберите представление Gantt.

![Мониторинг зависимостей](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Мониторинг зависимостей")

## <a name="next-steps"></a>Дальнейшие действия

* Обзор [Как создать акробатика триггера окна](how-to-create-tumbling-window-trigger.md)
