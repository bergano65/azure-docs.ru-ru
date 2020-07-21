---
title: Заметки о выпуске Azure HDInsight
description: Последние заметки о выпуске Azure HDInsight. Ознакомьтесь с советами по разработке и подробными сведениями о Hadoop, Spark, R Server, Hive и т. д.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 0481f92666c8ca547dc427e117f9917afb257629
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511880"
---
# <a name="azure-hdinsight-release-notes"></a>Заметки о выпуске Azure HDInsight

Эта статья содержит сведения о **последних** обновлениях выпуска Azure HDInsight. Дополнительные сведения о предыдущих выпусках см. в статье [Заметки о выпуске для компонентов Hadoop в Azure HDInsight (архив)](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Сводка

Azure HDInsight является одной из самых популярных среди корпоративных клиентов служб для аналитики с открытым кодом в Azure.

## <a name="release-date-07132020"></a>Дата выпуска: 07/13/2020

Этот выпуск применим к HDInsight версий 3.6 и 4.0. Выпуск HDInsight предоставляется для разных регионов на протяжении нескольких дней. Указанная здесь дата выпуска обозначает дату выпуска для первого из регионов. Если вы не видите приведенных ниже изменений, дождитесь выхода выпуска в вашем регионе в течение нескольких дней.

## <a name="new-features"></a>новые функции;
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Поддержка защищенное хранилище для Microsoft Azure
Azure HDInsight теперь поддерживает защищенное хранилище Azure. Он предоставляет клиентам интерфейс для просмотра и утверждения или отклонения запросов на доступ к данным клиентов. Он используется, когда инженеру Майкрософт требуется получить доступ к данным клиента во время запроса на поддержку. Дополнительные сведения см. в разделе [защищенное хранилище for Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Политики конечной точки службы для хранилища
Теперь клиенты могут использовать политики конечных точек службы (SEP) в подсети кластера HDInsight. Дополнительные сведения о [политике конечных точек службы Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Устаревшее
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Прекращение поддержки Spark 2.1 и 2.2 для кластера Spark в HDInsight 3.6
Начиная с июля 1 2020 клиенты не могут создавать новые кластеры Spark с Spark 2,1 и 2,2 в HDInsight 3,6. Существующие кластеры будут продолжать работу без изменений, но без поддержки со стороны корпорации Майкрософт. Постарайтесь перейти к версии Spark 2.3 в HDInsight 3.6 не позднее 30 июня 2020 г., чтобы избежать потенциальных проблем или перерывов в работе.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Устаревание Spark 2.3 для кластера Spark в HDInsight 4.0
Начиная с июля 1 2020 клиенты не могут создавать новые кластеры Spark с помощью Spark 2,3 в HDInsight 4,0. Существующие кластеры будут продолжать работу без изменений, но без поддержки со стороны корпорации Майкрософт. Постарайтесь перейти к версии Spark 2.4 в HDInsight 4.0 не позднее 30 июня 2020 г., чтобы избежать потенциальных проблем или перерывов в работе.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Прекращение поддержки Kafka 1.1 для кластера Kafka в HDInsight 4.0
Начиная с 1 июля 2020 г. клиенты не смогут создавать в HDInsight 4.0 новые кластеры Kafka с поддержкой Kafka 1.1. Существующие кластеры будут продолжать работу без изменений, но без поддержки со стороны корпорации Майкрософт. Постарайтесь перейти к версии Kafka 2.1 в HDInsight 4.0 не позднее 30 июня 2020 г., чтобы избежать потенциальных проблем или перерывов в работе.

## <a name="behavior-changes"></a>Изменения в работе
Нет изменений в поведении, к которым нужно обратить внимание.

## <a name="upcoming-changes"></a>Предстоящие изменения
В предстоящих выпусках будут внесены описанные ниже изменения. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Возможность выбора различных номеров SKU Zookeeper для служб Spark, Hadoop и ML
Сейчас HDInsight не поддерживает изменение номера SKU Zookeeper для типов кластера служб Spark, Hadoop и ML. Он использует A2_v2 номер SKU/a2 для узлов Zookeeper, и клиенты не наплатите за них. В предстоящем выпуске клиенты смогут изменять SKU Zookeeper для служб Spark, Hadoop и ML по мере необходимости. Zookeeper узлам с номером SKU, отличным от A2_v2/a2, будет начисляться счет. Номер SKU по умолчанию по-прежнему будет A2_V2/a2 и без оплаты.

## <a name="bug-fixes"></a>Исправления ошибок
HDInsight постоянно повышает надежность и производительность кластеров. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Исправлена проблема с соединителем хранилища Hive
В предыдущем выпуске возникла ошибка использования соединителя хранилища Hive. Проблема исправлена. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Исправленная Записная книжка Zeppelin усекает начальные нули.
Zeppelin неправильно усекает начальные нули в выходных данных таблицы для формата строки. Мы устранили эту ошибку в этом выпуске.

## <a name="component-version-change"></a>Изменение версий компонентов
В этом выпуска не вносилось изменений в версии компонентов. Текущие версии компонентов для HDInsight 4,0 AD HDInsight 3,6 можно найти в [этом документе](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).
