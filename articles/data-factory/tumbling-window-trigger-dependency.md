---
title: Создание зависимостей триггера "переворачивающегося" окна в Фабрике данных Azure | Документация Майкрософт
description: Узнайте, как создать зависимость в триггере "переворачивающегося" окна в Фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: c51c1056869cbd7741fae2ed1a554a7c794d1a39
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966855"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Создание зависимости триггера, который запускает конвейер в "переворачивающемся" окне

В этой статье описано, как создать зависимость в триггере "переворачивающегося" окна. Общие сведения об этом типе триггеров см. в статье [Создание триггера, который запускает конвейер в "переворачивающемся" окне](how-to-create-tumbling-window-trigger.md).

Чтобы создать цепочку зависимостей и убедиться в том, что триггер в фабрике данных выполняется только после успешного выполнения другого триггера, используйте этот дополнительный компонент для создания зависимости "переворачивающегося" окна.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Создание зависимости в пользовательском интерфейсе Фабрики данных Azure

Чтобы создать зависимость в триггере, последовательно выберите **Trigger > Advanced > New** (Триггер > Дополнительно > Создать), а затем выберите триггер, для которого будет создана зависимость, с соответствующими смещением и размером. Нажмите кнопку **Finish** (Готово) и опубликуйте изменения фабрики данных, чтобы зависимости вступили в силу.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Свойства зависимостей "переворачивающегося" окна

Зависимость триггера "переворачивающегося" окна имеет следующие свойства:

```json
{
    "name": "DemoTrigger",
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
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

В следующей таблице приведен список атрибутов, необходимых для определения зависимости "переворачивающегося" окна.

| **Имя свойства** | **Описание**  | **Тип** | **Обязательный** |
|---|---|---|---|
| Триггер  | В этом раскрывающемся меню отображаются все существующие триггеры "переворачивающегося" окна. Выберите триггер, для которого будет настроена зависимость.  | TumblingWindowTrigger | Yes |
| Offset | Смещение триггера зависимости. Укажите значение в формате диапазона времени. Разрешено вводить положительные и отрицательные смещения. Этот параметр является обязательным, если триггер имеет зависимость от самого себя. Во всех остальных случаях это необязательный параметр. Самозависимости всегда должны иметь отрицательные смещения. | Timespan | Самозависимость: Да Другое: Нет  |
| Размер окна | Размер "переворачивающегося" окна зависимости. Укажите значение в формате диапазона времени. Это необязательный параметр. | Timespan | Нет   |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Свойства самозависимостей "переворачивающегося" окна

В сценариях, где триггер не должен переходить к следующему окну до успешного завершения предыдущего окна, следует создать самозависимость. Триггер самозависимости будет иметь следующие свойства:

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

Ниже приведены примеры сценариев и использование свойств зависимостей "переворачивающегося" окна.

## <a name="dependency-offset"></a>Смещение зависимости

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Размер зависимости

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Самозависимость

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Сценарии использования

### <a name="dependency-on-another-tumbling-window-trigger"></a>Зависимость от триггера другого "переворачивающегося" окна

Например, ежедневное задание обработки телеметрии с зависимостью от другого ежедневного задания статистической обработки выходных данных за последние семь дней, которое создает скользящие окно потоков за семь дней:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Зависимость от самого себя

Ежедневное задание без разрывов в потоках вывода задания:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Мониторинг зависимостей

Вы можете отслеживать цепочку зависимости и соответствующего окна на странице мониторинга выполнения триггера. Последовательно выберите **Monitoring > Trigger Runs** (Мониторинг > Выполнение триггеров).

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Щелкните значок очков, чтобы просмотреть все запуски зависимого триггера выбранного окна.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь со статьей [Создание триггера, который запускает конвейер в "переворачивающемся" окне](how-to-create-tumbling-window-trigger.md).