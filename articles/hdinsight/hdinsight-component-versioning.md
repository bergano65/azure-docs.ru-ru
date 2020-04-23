---
title: Компоненты и версии Apache Hadoop в Azure HDInsight
description: Изучите компоненты и версии Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 337d36d8f5e7a7b69b98de2f68ff372d4914f905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084776"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Что представляют собой компоненты и версии Apache Hadoop, доступные в HDInsight?

Узнайте о компонентах и версиях среды [Apache Hadoop](https://hadoop.apache.org/) в Microsoft Azure HDInsight и пакете корпоративной безопасности. Кроме того, узнайте, как проверить версии компонентов Hadoop в HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Компоненты Apache Hadoop, доступные в разных версиях HDInsight

Azure HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. 4 апреля 2017 года кластерная версия по умолчанию, используемая Azure HDInsight, рана 3,6.

Версии компонентов, связанные с версиями кластера HDInsight, указаны в указанной ниже таблице.

> [!NOTE]  
> Версия по умолчанию службы HDInsight может измениться без предварительного уведомления. Если используется зависимость версии, укажите версию HDInsight при создании кластеров с использованием пакета SDK для .NET, Azure PowerShell и классической версии Azure CLI.

| Компонент              | HDInsight 4.0 | HDInsight 3.6 (по умолчанию)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop и YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0 (не-ESP кластеры), 1.2.1 (ESP кластеры)                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0* (см. примечание внизу) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> По соображениям оптимизации производительности поддержка Kafka версии 0.10 прекращается в марте 2019 г.

## <a name="check-for-current-hadoop-component-version-information"></a>Проверка сведений о текущей версии компонентов Hadoop

Версии компонентов среды Hadoop, связанные с кластерными версиями HDInsight, могут изменяться с обновлением HDInsight. Чтобы проверить компоненты Hadoop и версии, которые должны использоваться для кластера, используйте Ambari REST API. Для извлечения сведений о компонентах службы можно воспользоваться командой **GetComponentInformation**. Дополнительные сведения см. в [документации по Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Заметки о выпуске

Дополнительные заметки о выпусках последних версий HDInsight см. [здесь](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Поддерживаемые версии HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Поддержка истечения срока действия и выхода на пенсию для версий HDInsight

**Истечение срока действия поддержки** означает, что корпорация Майкрософт больше не будет предоставлять поддержку указанной версии HDInsight. И он больше не будет доступен через портал Azure для создания кластера. Тем не менее, эти версии все еще могут быть созданы с помощью Azure CLI или различных SDK.

**Выход из** версии HDInsight означает, что существующие кластеры будут продолжать работать как есть. Однако новые кластеры этой версии не могут быть созданы с помощью любых средств (включая CLI и SDK). Другие функции плоскости управления (например, ручное масштабирование и автоматическое масштабирование) также могут не работать после выхода на пенсию версии. Поддержка недоступна для отставных версий.

В приведенных ниже таблицах перечислены версии HDInsight. Даты прекращения поддержки и прекращения использования также предоставляются, когда они известны.

### <a name="available-versions"></a>Доступные версии

В следующей таблице перечислены версии HDInsight, доступные на портале Azure, и другие методы развертывания, такие как PowerShell и .NET SDK.

| Версия HDInsight | ОС виртуальной машины | Дата выпуска | Дата прекращения поддержки | Дата вывода | Высокий уровень доступности |  Доступность на портале Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 сентября 2018 г. | | |Да |Да |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 апреля 2017 г. | 31 декабря 2020 года |31 декабря 2020 года |Да |Да |

Срок действия поддержки Spark 2.1, 2.2 & Kafka 1.0 истекает 30 июня 2020 года.

> [!NOTE]  
> По истечении срока поддержки для версии она может стать недоступной на портале Microsoft Azure. Однако доступ к версиям кластера по-прежнему можно будет получить с помощью параметра `Version` в командлете Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) и пакета SDK для .NET до даты вывода.

### <a name="retired-versions"></a>Устаревшие версии

В следующей таблице перечислены версии HDInsight, которые **недоступны** на портале Azure.

| Версия HDInsight | Версия HDP | ОС виртуальной машины | Дата выпуска | Дата прекращения поддержки | Дата вывода | Высокий уровень доступности |  Доступность на портале Azure |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 сентября 2016 г. |5 сентября 2017 г. |28 июня 2018 года |Да |нет |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 марта 2016 г. |29 декабря 2016 г. |9 января 2018 г. |Да |нет |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 декабря 2015 г. |27 июня 2016 г. |31 июля 2018 г. |Да |нет |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 декабря 2015 г. |27 июня 2016 г. |31 июля 2017 г. |Да |нет |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS или Windows Server 2012 R2 |18 февраля 2015 г. |1 марта 2016 г. |1 апреля 2017 г. |Да |нет |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24 июня 2014 г. |18 мая 2015 г. |30 июня 2016 г. |Да |нет |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11 февраля 2014 г. |17 сентября 2014 г. |30 июня 2015 г. |Да |нет |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28 октября 2013 г. |12 мая 2014 г. |31 мая 2015 г. |Да |нет |
| HDInsight 1.6 |HDP 1.1 | |28 октября 2013 г. |26 апреля 2014 г. |31 мая 2015 г. |нет |нет |

> [!NOTE]  
> Высокодоступные кластеры с двумя головными узлами развертываются по умолчанию для HDInsight версии 2.1 и более поздних. Они недоступны для кластеров HDInsight версии 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Пакет безопасности предприятия для HDInsight

Корпоративный пакет безопасности — это дополнительный пакет, который можно добавить в кластер HDInsight во время рабочего процесса создания кластера. Пакет безопасности предприятия поддерживает следующие возможности:

- Интеграция с Active Directory для аутентификации.

    В прошлом вы создавали кластеры HDInsight с локальным пользователем админ и локальным пользователем SSH. Локальный администратор может получить доступ ко всем файлам, папкам, таблицам и столбцам.  С помощью пакета корпоративной безопасности вы включите элементуправления доступа на основе ролей, интегрируя HDInsight с вашим Active Directory. Включает в себя предпосылок Active Directory, службы домена Active Directory. Или Active Directory на виртуальной машине IaaS. Администратор домена в кластере может предоставить пользователям возможность использовать свое собственное корпоративное (доменовое) имя пользователя и пароль.

    Дополнительные сведения см. в разделе:

    - [Общие сведения об обеспечении безопасности Apache Hadoop с помощью Корпоративного пакета безопасности](./domain-joined/hdinsight-security-overview.md)
    - [Корпоративный пакет безопасности для HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Настройка среды с присоединенной к домену песочницей](./domain-joined/apache-domain-joined-configure.md)
    - [Настройка присоединенных к домену кластеров HDInsight с помощью доменных служб Azure Active Directory](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Авторизация данных.

  - Интеграция с Apache Ranger для авторизации очередей Hive, Spark SQL и Yarn.
  - Можно задать управление доступом к файлам и папкам.

    Дополнительные сведения см. в разделе:

  - [Настройка политик Apache Hive в HDInsight с Корпоративным пакетом безопасности](./domain-joined/apache-domain-joined-run-hive.md)

- Просмотр журналов аудита для отслеживания доступа и настроенных политик.

### <a name="supported-cluster-types"></a>Поддерживаемые типы кластеров

Сейчас только следующие типы кластеров поддерживают пакет безопасности предприятия:

- Hadoop (только HDInsight 3.6);
- Spark
- Kafka
- HBase
- Интерактивный запрос

### <a name="support-for-azure-data-lake-storage"></a>Поддержка Azure Data Lake Storage

Корпоративный пакет безопасности поддерживает использование Azure Data Lake Storage в качестве основного и дополнительного хранилища.

### <a name="pricing-and-service-level-agreement-sla"></a>Соглашение о ценах и уровне обслуживания (SLA)

Сведения о ценах и соглашение об уровне обслуживания для пакета безопасности предприятия см. на странице [Цены на Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Соглашение об уровне обслуживания (SLA) для версий кластера HDInsight

Соглашение об уровне обслуживания (SLA) определяется как _окно поддержки._ Окно поддержки — это период времени, `Microsoft Customer Service and Support`который поддерживает сяртовая версия HDInsight. Если у версии _истек срок действия поддержки,_ кластер HDInsight находится за окном поддержки. Поддержка истечения срока для версии HDInsight X (после того, как доступна более новая версия X'1) является более поздним:  

- Формула 1. Добавьте 180 дней к дате выхода версии X кластера HDInsight.
- Формула 2. Добавьте 90 дней к дате, когда версия кластера HDInsight X + 1 стала доступной на портале Azure.

_Дата выхода на пенсию_ — это дата, после которой кластерная версия не может быть создана на HDInsight. Начиная с 31 июля 2017 года, вы не можете изменить размер кластера HDInsight после даты выхода на пенсию.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Конфигурация узлов и размеры виртуальных машин по умолчанию для кластеров

Для получения дополнительной [информации](hdinsight-supported-node-configuration.md)о том, какую виртуальную машину SKUs выбрать для кластера, см.

## <a name="next-steps"></a>Дальнейшие действия

- [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md)
- [Работа в экосистеме Hadoop в HDInsight на компьютере с Windows](hdinsight-hadoop-windows-tools.md)
- [Заметки Hortonworks, связанные с версиями Azure HDInsight](./hortonworks-release-notes.md)
