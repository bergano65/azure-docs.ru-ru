---
title: Интерфейс командной строки Azure Service Fabric. sfctl chaos schedule | Документация Майкрософт
description: Описание команд sfctl chaos schedule интерфейса командной строки Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837510"
---
# <a name="sfctl-chaos-schedule"></a>Расписание sfctl chaos
Получение и настройка расписания Chaos.

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
| --timeout -t | Время ожидания сервера в секундах.  Значение по умолчанию\: 60. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Настройка используемого Chaos расписания.

Chaos будет автоматически планировать запуски, основанные на расписании Chaos. Версия в предоставленном расписании ввода должна соответствовать версии расписания Chaos на сервере. Если предоставленная версия не соответствует версии на сервере, расписание Chaos не обновляется. Если предоставленная версия соответствует версии на сервере, то расписание Chaos обновляется, а версия расписания на сервере увеличивается на единицу и сбрасывается до 0 после 2, 147, 483, 647. Если Chaos выполняется, когда этот вызов сделан, вызов завершится ошибкой.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --chaos-parameters-dictionary | Список в формате JSON, представляющий сопоставление имен строк для ChaosParameters, который будет использоваться для заданий. |
| --expiry-date-utc | Дата и время, когда следует прекратить использование расписания для планирования Chaos.  Значение по умолчанию\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Список ChaosScheduleJobs в формате JSON, в котором указано время и параметры запуска Chaos. |
| --start-date-utc | Дата и время, когда следует начать использование расписания для планирования Chaos.  По умолчанию\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Время ожидания сервера в секундах.  Значение по умолчанию\: 60. |
| --version | Номер версии расписания. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
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
