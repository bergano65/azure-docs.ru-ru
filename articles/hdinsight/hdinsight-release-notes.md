---
title: Заметки о выпуске Azure HDInsight
description: Последние заметки о выпуске Azure HDInsight. Ознакомьтесь с советами по разработке и подробными сведениями о Hadoop, Spark, R Server, Hive и т. д.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535616"
---
# <a name="azure-hdinsight-release-notes"></a>Заметки о выпуске Azure HDInsight

Эта статья содержит сведения о **последних** обновлениях выпуска Azure HDInsight. Дополнительные сведения о предыдущих выпусках см. в статье [Заметки о выпуске для компонентов Hadoop в Azure HDInsight (архив)](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Сводка

Azure HDInsight является одной из самых популярных среди корпоративных клиентов служб для аналитики с открытым кодом в Azure.

## <a name="release-date-09282020"></a>Дата выпуска: 09/28/2020

Этот выпуск относится как к HDInsight 3,6, так и к HDInsight 4,0. Выпуск HDInsight предоставляется для разных регионов на протяжении нескольких дней. Указанная здесь дата выпуска обозначает дату выпуска для первого из регионов. Если вы не видите приведенных ниже изменений, дождитесь выхода выпуска в вашем регионе в течение нескольких дней.

## <a name="new-features"></a>Новые функции
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>Автомасштабирование для интерактивного запроса с помощью HDInsight 4,0 теперь общедоступно
Тип кластера для интерактивного запроса "автоматическое масштабирование" теперь является общим доступным (общедоступным) для HDInsight 4,0. Все кластеры интерактивных запросов 4,0, созданные после 27 августа 2020, будут иметь общедоступную поддержку автоматического масштабирования.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>Кластер HBase поддерживает ADLS 2-го поколения Premium
HDInsight теперь поддерживает ADLS 2-го поколения "Премиум" в качестве первичной учетной записи хранения для кластеров HDInsight HBase 3,6 и 4,0. Вместе с [ускоренными](./hbase/apache-hbase-accelerated-writes.md)операциями записи можно повысить производительность кластеров HBase.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Распределение секций Kafka в доменах сбоя Azure
Домен сбоя — это логическое объединение базового оборудования в центре обработки данных Azure. Все домены сбоя используют общий источник питания и сетевой коммутатор. Перед тем как HDInsight Kafka может хранить все реплики секций в одном домене сбоя. Начиная с этого выпуска HDInsight теперь поддерживает автоматическое распространение секций Kafka на основе доменов сбоя Azure. 

### <a name="encryption-in-transit"></a>Шифрование при передаче
Клиенты могут включить шифрование при передаче между узлами кластера, используя шифрование IPSec с ключами, управляемыми платформой. Этот параметр можно включить во время создания кластера. См. Дополнительные сведения о [включении шифрования при передаче](./domain-joined/encryption-in-transit.md).

### <a name="encryption-at-host"></a>Шифрование на узле
При включении шифрования на узле данные, хранящиеся на узле виртуальной машины, шифруются в неактивных потоках и передаются в зашифрованном виде в службу хранилища. В этом выпуске можно **включить шифрование на диске с временными данными** при создании кластера. Шифрование на узле поддерживается только для [определенных номеров SKU виртуальных машин в ограниченных регионах](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal). HDInsight поддерживает [следующие конфигурации узла и номера SKU](./hdinsight-supported-node-configuration.md). См. Дополнительные сведения о [том, как включить шифрование на узле](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Переход на масштабируемые наборы виртуальных машин Azure
Сейчас HDInsight использует для подготовки кластера виртуальные машины Azure. Начиная с этого выпуска, служба постепенно переносится в [масштабируемые наборы виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Весь процесс может занять несколько месяцев. После переноса регионов и подписок вновь созданные кластеры HDInsight будут работать в масштабируемых наборах виртуальных машин без действий клиента. Критические изменения не требуются.

## <a name="deprecation"></a>Устаревшее
В этом выпуске нет устаревших версий.

## <a name="behavior-changes"></a>Изменения в работе
Для этого выпуска изменение поведения не выполняется.

## <a name="upcoming-changes"></a>Предстоящие изменения
В предстоящих выпусках будут внесены описанные ниже изменения.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Возможность выбора различных номеров SKU Zookeeper для служб Spark, Hadoop и ML
Сейчас HDInsight не поддерживает изменение номера SKU Zookeeper для типов кластера служб Spark, Hadoop и ML. Он использует A2_v2 номер SKU/a2 для узлов Zookeeper, и клиенты не наплатите за них. В следующем выпуске клиенты могут изменить SKU Zookeeper для служб Spark, Hadoop и ML по мере необходимости. Zookeeper узлам с номером SKU, отличным от A2_v2/a2, будет начисляться счет. Номер SKU по умолчанию по-прежнему будет A2_V2/a2 и без оплаты.

## <a name="bug-fixes"></a>Исправления ошибок
HDInsight постоянно повышает надежность и производительность кластеров. 

## <a name="component-version-change"></a>Изменение версий компонентов
В этом выпуска не вносилось изменений в версии компонентов. Текущие версии компонентов для HDInsight 4,0 и HDInsight 3,6 можно найти в [этом документе](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).