---
title: Azure Service Fabric CLI-sfctl Chaos Schedule
description: Сведения о sfctl, интерфейсе командной строки Azure Service Fabric. Содержит список команд для планирования Chaos.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: bff83e1d25d04f91611f5bea6c69dfcd299af04c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639179"
---
# <a name="sfctl-chaos-schedule"></a>Расписание sfctl chaos
Получение и настройка расписания Chaos.

## <a name="commands"></a>Команды

|Get-Help|Description|
| --- | --- |
| get | Получение расписания Chaos, определяющего, когда и как выполнять Chaos. |
| set | Настройка используемого Chaos расписания. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Получение расписания Chaos, определяющего, когда и как выполнять Chaos.

Получение версии используемого расписания Chaos, и расписания Chaos, которое определяет, когда и как запускать Chaos.

### <a name="arguments"></a>Аргументы

|Аргумент|Description|
| --- | --- |
| --timeout -t | Время ожидания сервера для выполнения операции в секундах. Это время ожидания указывает период времени, в течение которого клиент желает ожидать завершения запрошенной операции. Значение по умолчанию для этого параметра — 60 секунд.  Значение по умолчанию\: 60. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Description|
| --- | --- |
| --debug | Повышение уровня детализации журнала для включения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат вывода.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации журнала. Чтобы включить полные журналы отладки, используйте параметр --debug. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Настройка используемого Chaos расписания.

Chaos будет автоматически планировать запуски, основанные на расписании Chaos. Расписание Chaos будет Обновлено, если указанная версия соответствует версии на сервере. При обновлении расписания Chaos версия на сервере увеличивается на 1. Версия на сервере будет переноситься обратно в 0 после достижения большого числа. Если Chaos выполняется, когда этот вызов сделан, вызов завершится ошибкой.

### <a name="arguments"></a>Аргументы

|Аргумент|Description|
| --- | --- |
| --chaos-parameters-dictionary | Список в формате JSON, представляющий сопоставление имен строк для ChaosParameters, который будет использоваться для заданий. |
| --expiry-date-utc | Дата и время, когда следует прекратить использование расписания для планирования Chaos.  Значение по умолчанию\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Список ChaosScheduleJobs в формате JSON, в котором указано время и параметры запуска Chaos. |
| --start-date-utc | Дата и время, когда следует начать использование расписания для планирования Chaos.  По умолчанию\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Значение по умолчанию\: 60. |
| --version | Номер версии расписания. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Description|
| --- | --- |
| --debug | Повышение уровня детализации журнала для включения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат вывода.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации журнала. Чтобы включить полные журналы отладки, используйте параметр --debug. |

### <a name="examples"></a>Примеры

Следующая команда настраивает расписание (при условии, что текущее расписание имеет версию 0), которое начинается с 2016-01-01, завершается 2038-01-01 и запускает Chaos 24 часа в сутки, 7 дней в неделю. На этот период времени в кластере запланирован Chaos.

    sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]


## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).
