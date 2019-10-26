---
title: Интерфейс командной строки Azure Service Fabric. sfctl chaos schedule | Документация Майкрософт
description: Описание команд sfctl chaos schedule интерфейса командной строки Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 443a8186ac6e57360105e59e30f84db997cd2251
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897542"
---
# <a name="sfctl-chaos-schedule"></a>Расписание sfctl chaos
Получение и настройка хаотического расписания.

## <a name="commands"></a>Команды

|Команда|Описание|
| --- | --- |
| get | Получение расписания Chaos, определяющего, когда и как выполнять Chaos. |
| set | Настройка используемого Chaos расписания. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Получение расписания Chaos, определяющего, когда и как выполнять Chaos.

Получение версии используемого расписания Chaos, и расписания Chaos, которое определяет, когда и как запускать Chaos.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --timeout -t | Время ожидания сервера для выполнения операции в секундах. Это время ожидания указывает период времени, в течение которого клиент желает ожидать завершения запрошенной операции. Значение по умолчанию для этого параметра — 60 секунд.  Значение по умолчанию\: 60. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения см. на сайте http\://jmespath.org. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Настройка используемого Chaos расписания.

Chaos будет автоматически планировать запуски, основанные на расписании Chaos. Расписание Chaos будет Обновлено, если указанная версия соответствует версии на сервере. При обновлении расписания Chaos версия на сервере увеличивается на 1. Версия на сервере будет переноситься обратно в 0 после достижения большого числа. Если Chaos выполняется, когда этот вызов сделан, вызов завершится ошибкой.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --chaos-parameters-dictionary | Список в формате JSON, представляющий сопоставление имен строк для ChaosParameters, который будет использоваться для заданий. |
| --expiry-date-utc | Дата и время, когда следует прекратить использование расписания для планирования Chaos.  Значение по умолчанию\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Список ChaosScheduleJobs в формате JSON, в котором указано время и параметры запуска Chaos. |
| --start-date-utc | Дата и время, когда следует начать использование расписания для планирования Chaos.  По умолчанию\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Значение по умолчанию\: 60. |
| --version | Номер версии расписания. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения см. на сайте http\://jmespath.org. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

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
