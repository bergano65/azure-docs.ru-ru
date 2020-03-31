---
title: RequestBodyTooБольшая ошибка от приложения Apache Spark - Azure HDInsight
description: РоднойАзурЕФиадСистема ... RequestBodyTooLarge отображается в журнале для потокового приложения Apache Spark в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894396"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"РоднойАзюреФилСистема... RequestBodyTooLarge" появится в журнале потокового приложения Apache Spark в HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Ошибка: `NativeAzureFileSystem ... RequestBodyTooLarge` отображается в журнале драйверов для потокового приложения Apache Spark.

## <a name="cause"></a>Причина

Ваш файл журнала событий Spark, вероятно, попадет в предел длины файла для WASB.

В Spark 2.3 каждое приложение Spark генерирует один файл журнала событий Spark. Файл журнала событий Spark для потокового приложения Spark продолжает расти во время работы приложения. Сегодня файл на WASB имеет лимит в 50000 блоков, а размер блока по умолчанию составляет 4 МБ. Таким образом, в конфигурации по умолчанию максимальный размер файла составляет 195 ГБ. Тем не менее, Azure Storage увеличилмаксимальный размер блока до 100 МБ, что фактически довело лимит одного файла до 4,75 ТБ. Дополнительные сведения см. в статье [Scalability and performance targets for Blob storage](../../storage/blobs/scalability-targets.md) (Целевые показатели масштабируемости и производительности для хранилища BLOB-объектов).

## <a name="resolution"></a>Решение

Для этой ошибки доступно три решения:

* Увеличьте размер блока до 100 МБ. В UI Ambari измените `fs.azure.write.request.size` свойство конфигурации `Custom core-site` HDFS (или создайте его в разделе). Например, установите свойство на более значительное значение: 33554432. Сохранить обновленную конфигурацию и перезапустить затронутые компоненты.

* Периодически останавливайся и повторно отправляй работу потоковой искры.

* Используйте HDFS для хранения журналов событий Spark. Использование HDFS для хранения может привести к потере данных событий Spark во время масштабирования кластеров или обновления Azure.

    1. Вносить `spark.eventlog.dir` изменения `spark.history.fs.logDirectory` в и через Ambari UI:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Создание каталогов на HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Перезапустите все пострадавшие услуги через UI Ambari.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
