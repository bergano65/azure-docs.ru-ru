---
title: Мониторинг действия копирования
description: Узнайте о том, как контролировать выполнение действия копирования на фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 47824095e892ca3c919d2d871feb612758ab2308
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417850"
---
# <a name="monitor-copy-activity"></a>Мониторинг действия копирования

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описывается, как контролировать выполнение действия копирования на фабрике данных Azure. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="monitor-visually"></a>Визуальный мониторинг

После создания и публикации конвейера на фабрике данных Azure можно связать его с триггером или вручную запустить специальный запуск. Вы можете отслеживать все провода, запускаемые на месте в пользовательском интерфейсе Azure Data Factory. Узнайте о мониторинге Azure Data Factory в целом с [сайта Visually monitor Azure Data Factory.](monitor-visually.md)

Чтобы проследить действие Copy, перейдите на ваш завод данных **Author & Monitor** UI. На вкладке **Monitor** вы видите список запусков конвейера, щелкните ссылку **имени конвейера,** чтобы получить доступ к списку действий, запущенных в запуске конвейера.

![Запуск действия копирования](./media/copy-activity-overview/monitor-pipeline-run.png)

На этом уровне можно увидеть ссылки на копирование ввода активности, вывода и ошибок (если выполняется действие Copy), а также статистические данные, такие как продолжительность/статус. Нажатие **кнопки «Подробности»** (очки) рядом с именем активности копирования даст вам подробную информацию о выполнении вашей деятельности копии. 

![Запуск действия копирования](./media/copy-activity-overview/monitor-copy-activity-run.png)

В этом графическом представлении мониторинга Azure Data Factory представляет вам информацию о выполнении действий копирования, включая прочитанный/письменный объем данных, количество файлов/рядов данных, скопированных из источника в раковину, пропускную готовность, конфигурации, применяемые для сценария копирования, шаги, которые действие копирования проходит с соответствующей продолжительностью и деталями, и многое другое. Обратитесь к [этой таблице](#monitor-programmatically) по каждой возможной метрике и ее подробному описанию. 

В некоторых сценариях при запуске действия Copy в Data Factory вы увидите **«Подсказки настройки производительности»** в верхней части представления мониторинга активности копирования, как показано в примере. Советы сказать вам узкое место определены ADF для конкретного запуска копии, а также предложение о том, что изменить, чтобы увеличить пропускную информацию копию. Узнайте больше о советах по [автоматической настройке производительности.](copy-activity-performance-troubleshooting.md#performance-tuning-tips)

Детали нижней **части исполнения и продолжительности** описывают ключевые шаги, через которые проходит действие копий, что особенно полезно для устранения неполадок в производительности копии. Узкое место в копии запуска является одним с самой длинной продолжительностью. Ссылайтесь на [производительность действия копирования Troubleshoot](copy-activity-performance-troubleshooting.md) на то, что представляет собой каждый этап, и подробное руководство по устранению неполадок.

**Пример: Копия от Amazon S3 до Azure Хранения данных озера Gen2**

![Мониторинг действия копирования выполнения деталей](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Мониторинг при помощи программных средств

Детали выполнения копирования и характеристики производительности также возвращаются в разделе > **результата** вывода **результатов выполнения copy Activity,** который используется для визуализации представления мониторинга uI. Ниже приводится полный список свойств, которые могут быть возвращены. Вы увидите только свойства, применимые к сценарию копирования. Для получения информации о том, как контролировать деятельность работает программно в целом, [см.](monitor-programmatically.md)

| Имя свойства  | Описание | Единица в выходе |
|:--- |:--- |:--- |
| dataRead | Фактический объем данных, считаемых из источника. | Значение Int64, в байтах |
| dataWritten | Фактическое монтировать данные, написанные/совершенные в раковину. Размер может отличаться `dataRead` от размера, так как он связан с тем, как каждый хранилище данных хранит данные. | Значение Int64, в байтах |
| filesRead | Количество файлов, считаемых из файло-источника. | Значение Int64 (не единица измерения) |
| filesWritten | Количество файлов, написанных/совершенных в раковину на основе файлов. | Значение Int64 (не единица измерения) |
| источникPeakConnections | Пиковое число одновременных подключений, установленных в хранилище исходных данных во время выполнения действия Copy. | Значение Int64 (не единица измерения) |
| sinkPeakConnections | Пиковое число одновременных подключений, установленных в хранилище данных раковины во время выполнения активности Copy. | Значение Int64 (не единица измерения) |
| строкиЧитаться строкиЧитаться | Количество строк, прочитанный из источника (не применимо для двоичной копии). | Значение Int64 (не единица измерения) |
| rowsCopied | Количество строк, скопированных для погружения (не применимо для двоичной копии). | Значение Int64 (не единица измерения) |
| rowsSkipped | Количество несовместимых строк, которые были пропущены. Можно пропустить несовместимые строки, установив `enableSkipIncompatibleRow` их до истины. | Значение Int64 (не единица измерения) |
| copyDuration | Длительность выполнения копий. | Значение Int32, в секундах |
| throughput | Скорость передачи данных. | Номер плавающей точки, в KBps |
| источникPeakConnections | Пиковое число одновременных подключений, установленных в хранилище исходных данных во время выполнения действия Copy. | Значение Int32 (без единицы) |
| sinkPeakConnections| Пиковое число одновременных подключений, установленных в хранилище данных раковины во время выполнения активности Copy.| Значение Int32 (без единицы) |
| sqlDwPolyBase | Используется ли PolyBase при копировании данных в хранилище данных S'L. | Логическое |
| redshiftUnload | Используется ли UNLOAD при копировании данных из Redshift. | Логическое |
| hdfsDistcp | Используется ли DistCp при копировании данных из HDFS. | Логическое |
| effectiveIntegrationRuntime | Время выполнения интеграции (IR) или время выполнения, используемое `<IR name> (<region if it's Azure IR>)`для питания выполнения действия, в формате . | Текст (string) |
| usedDataIntegrationUnits | Единицы интеграции актуальных данных во время копирования. | Значение Int32 |
| usedParallelCopies | Использованное количество параллельных процессов копирования. | Значение Int32 |
| redirectRowPath | Путь к журналу пропущенных несовместимых строк в хранилище капли, настроенной в свойстве. `redirectIncompatibleRowSettings` Посмотреть [допуск неисправности](copy-activity-overview.md#fault-tolerance). | Текст (string) |
| executionDetails | Более подробная информация о этапах, через которые проходит активность Copy, и соответствующие шаги, продолжительность, конфигурации и так далее. Мы не рекомендуем вам разбирать этот раздел, поскольку он может измениться. Чтобы лучше понять, как это помогает вам понять и устранить производительность копирования, обратитесь к визуальному разделу [Monitor.](#monitor-visually) | Array |
| perfРекомендация | Копирование советы по настройке производительности. Подробнее [о настройках производительности.](copy-activity-performance-troubleshooting.md#performance-tuning-tips) | Array |

**Примере:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
См. другие статьи о действиях копирования:

\- [Общие сведения о действии копирования](copy-activity-overview.md)

\- [Производительность действия копирования](copy-activity-performance.md)