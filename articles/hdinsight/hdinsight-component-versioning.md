---
title: Компоненты и версии Apache Hadoop в Azure HDInsight
description: Сведения о компонентах и версиях Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/08/2020
ms.openlocfilehash: 9ce6df8491bf4ce7cbb0e12a08816e198f665beb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124658"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Компоненты и версии Apache, доступные для Azure HDInsight

В этой статье вы узнаете о компонентах и версиях [Apache Hadoopной](https://hadoop.apache.org/) среды в Azure HDInsight и корпоративный пакет безопасности. Вы также узнаете, как проверить версии компонентов Hadoop в HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Компоненты Apache, доступные в разных версиях HDInsight

Azure HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. 4 апреля 2017 версия кластера по умолчанию, используемая Azure HDInsight, была 3,6.

Версии компонентов, связанные с версиями кластера HDInsight, приведены в указанной ниже таблице.

> [!NOTE]
> Версия по умолчанию службы HDInsight может измениться без предварительного уведомления. При наличии зависимости версий укажите версию HDInsight при создании кластеров с помощью пакета SDK для .NET с Azure PowerShell и классической интерфейсом командной строки Azure.

| Компонент              | HDInsight 4.0 | HDInsight 3.6 (по умолчанию)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop и YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 в интерактивном запросе ESP) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (см. Примечание).   |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Поддержка Kafka версии 0,10 была ограничена в марте 2019 из-за соображений производительности системы.

## <a name="check-for-current-apache-component-version-information"></a>Проверить сведения о текущей версии компонента Apache

Версии компонентов среды Hadoop, связанные с версиями кластера HDInsight, можно изменить с помощью обновлений для HDInsight. Чтобы проверить компоненты Hadoop и версии, которые должны использоваться для кластера, используйте Ambari REST API. Для извлечения сведений о компонентах службы можно воспользоваться командой **GetComponentInformation**. Дополнительные сведения см. в [документации по Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Заметки о выпуске

Дополнительные заметки о выпуске для последних версий HDInsight см. в [заметках о выпуске hdinsight](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Поддерживаемые версии HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Поддержка истечения срока действия и прекращения использования версий HDInsight

**Срок поддержания поддержки** означает, что корпорация Майкрософт больше не предоставляет поддержку для конкретной версии HDInsight. Он больше не доступен в портал Azure для создания кластера. Эти версии по-прежнему можно создавать с помощью Azure CLI или различных пакетов SDK.

Прекращение **использования означает, что существующие** кластеры версии HDInsight продолжают работать как есть. Новые кластеры этой версии не могут быть созданы с помощью средств, включая CLI и пакеты SDK. Другие функции плоскости управления, такие как ручное масштабирование и автоматическое масштабирование, также могут не работать после выхода версии. Поддержка в снятых версиях недоступна.

В приведенных ниже таблицах перечислены версии HDInsight. Кроме того, при их наличии предоставляются сроки поддержки и прекращения использования.

### <a name="available-versions"></a>Доступные версии

В этой таблице перечислены версии HDInsight, доступные в портал Azure и с помощью других методов развертывания, таких как PowerShell и пакет SDK для .NET.

| Версия HDInsight | ОС виртуальной машины | Дата выпуска | Дата прекращения поддержки | Дата вывода | Высокий уровень доступности |  Доступность в портал Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 сентября 2018 г. | | |Да |Да |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 апреля 2017 г. | 31 декабря 2020 г. |31 декабря 2020 г. |Да |Да |

Срок поддержки Spark 2,1, 2,2 и Kafka 1,0 истекает 30 июня 2020 г.

> [!NOTE]
> После истечения срока поддержки версии она может быть недоступна через портал Azure. Версии кластера по-прежнему доступны с помощью параметра **Version** в команде Windows PowerShell [New-аздинсигхтклустер](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) и пакета SDK для .NET до даты выхода версии.

### <a name="retired-versions"></a>Устаревшие версии

В этой таблице перечислены версии HDInsight, которые недоступны в портал Azure.

| Версия HDInsight | Версия HDP | ОС виртуальной машины | Дата выпуска | Дата прекращения поддержки | Дата вывода | Высокий уровень доступности |  Доступность в портал Azure |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 сентября 2016 г. |5 сентября 2017 г. |28 июня 2018 года |Да |Нет |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 марта 2016 г. |29 декабря 2016 г. |9 января 2018 г. |Да |Нет |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 декабря 2015 г. |27 июня 2016 г. |31 июля 2018 г. |Да |Нет |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 декабря 2015 г. |27 июня 2016 г. |31 июля 2017 г. |Да |Нет |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS или Windows Server 2012 R2 |18 февраля 2015 г. |1 марта 2016 г. |1 апреля 2017 г. |Да |Нет |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24 июня 2014 г. |18 мая 2015 г. |30 июня 2016 г. |Да |Нет |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11 февраля 2014 г. |17 сентября 2014 г. |30 июня 2015 г. |Да |Нет |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28 октября 2013 г. |12 мая 2014 г. |31 мая 2015 г. |Да |Нет |
| HDInsight 1.6 |HDP 1.1 | |28 октября 2013 г. |26 апреля 2014 г. |31 мая 2015 г. |Нет |Нет |

> [!NOTE]
> Высокодоступные кластеры с двумя головными узлами развертываются по умолчанию для HDInsight версии 2.1 и более поздних. Они недоступны для кластеров HDInsight версии 1,6.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Соглашение об уровне обслуживания для версий кластера HDInsight

Соглашение об уровне обслуживания определяется как _окно поддержки_. Окно поддержки — это период времени, в течение которого версия HDInsight поддерживается службой поддержки клиентов Майкрософт. Если версия прошла _до истечения срока действия поддержки_, кластер HDInsight находится за пределами окна поддержки. Срок действия поддержки для HDInsight версии X (после того, как доступна более новая версия X + 1) ниже:

- **Формула 1:** Добавьте 180 дней к дате выпуска кластера HDInsight версии X.
- **Формула 2:** Добавьте 90 дней к дате, когда версия кластера HDInsight X + 1 станет доступной в портал Azure.

_Дата выбытия_ — это дата, после которой версия кластера не может быть создана в HDInsight. По состоянию на 31 июля 2017 нельзя изменить размер кластера HDInsight после его даты выбытия.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Конфигурация узлов и размеры виртуальных машин по умолчанию для кластеров

Дополнительные сведения о том, какие номера SKU виртуальных машин следует выбрать для кластера, см. в разделе [сведения о конфигурации кластера Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Дальнейшие действия

- [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md)
- [Работа в экосистеме Hadoop в HDInsight на компьютере с Windows](hdinsight-hadoop-windows-tools.md)
- [Заметки о выпуске Hortonworks, связанные с версиями Azure HDInsight](./hortonworks-release-notes.md)
- [Корпоративный пакет безопасности](./enterprise-security-package.md)