---
title: Ошибка Рекуестбодитуларже из приложения Apache Spark — Azure HDInsight
description: Нативеазурефилесистем... Рекуестбодитуларже отображается в журнале для приложения потоковой передачи Apache Spark в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894396"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"Нативеазурефилесистем... Рекуестбодитуларже "отображается в журнале приложений Apache Spark потоковой передачи в HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Ошибка: `NativeAzureFileSystem ... RequestBodyTooLarge` отображается в журнале драйвера для приложения потоковой передачи Apache Spark.

## <a name="cause"></a>Причина:

Возможно, файл журнала событий Spark имеет ограничение длины файла для WASB.

В Spark 2,3 Каждое приложение Spark создает один файл журнала событий Spark. Файл журнала событий Spark для приложения потоковой передачи Spark продолжит расти во время работы приложения. Сегодня файл в WASB имеет ограничение в 50000 блоков, а размер блока по умолчанию — 4 МБ. Поэтому в конфигурации по умолчанию максимальный размер файла составляет 195 ГБ. Однако служба хранилища Azure увеличила максимальный размер блока до 100 МБ, что фактически вывела ограничение одного файла на 4,75 ТБ. Дополнительные сведения см. в статье [Scalability and performance targets for Blob storage](../../storage/blobs/scalability-targets.md) (Целевые показатели масштабируемости и производительности для хранилища BLOB-объектов).

## <a name="resolution"></a>Разрешение

Для этой ошибки доступны три решения:

* Увеличьте размер блока до 100 МБ. В пользовательском интерфейсе Ambari измените свойство `fs.azure.write.request.size` конфигурации HDFS (или создайте его `Custom core-site` в разделе). Задайте для свойства большее значение, например: 33554432. Сохраните обновленную конфигурацию и перезапустите затронутые компоненты.

* Периодически прекращайте и повторно отправляйте задание Spark-Streaming.

* Используйте HDFS для хранения журналов событий Spark. Использование HDFS для хранения может привести к утрате данных событий Spark при масштабировании кластера или обновлении Azure.

    1. Внесите изменения в `spark.eventlog.dir` пользовательский `spark.history.fs.logDirectory` интерфейс Ambari и через него:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Создание каталогов в HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Перезапустите все затронутые службы через пользовательский интерфейс Ambari.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
