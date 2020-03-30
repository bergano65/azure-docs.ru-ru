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
ms.openlocfilehash: 2321918e9eae63a71d136753657bd7259862c2d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272660"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Что представляют собой компоненты и версии Apache Hadoop, доступные в HDInsight?

Узнайте о компонентах и версиях экосистем [Apache Hadoop](https://hadoop.apache.org/) в Microsoft Azure HDInsight, а также о пакете корпоративной безопасности. Кроме того, узнайте, как проверить версии компонентов Hadoop в HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Компоненты Apache Hadoop, доступные в разных версиях HDInsight

Azure HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. По состоянию на 4 апреля 2017 года кластерная версия по умолчанию, используемая Azure HDInsight, растарела 3,6.

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

Версии компонентов экосистемы Hadoop, связанных с версиями кластера HDInsight, могут быть изменены в обновлениях для HDInsight. Чтобы проверить компоненты Hadoop и версии, которые должны использоваться для кластера, используйте Ambari REST API. Для извлечения сведений о компонентах службы можно воспользоваться командой **GetComponentInformation**. Дополнительные сведения см. в [документации по Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Заметки о выпуске

Дополнительные заметки о выпусках последних версий HDInsight см. [здесь](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Поддерживаемые версии HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Поддержка истечения срока действия и выхода на пенсию для версий HDInsight

**Истечение срока действия поддержки** означает, что корпорация Майкрософт больше не будет предоставлять поддержку указанной версии HDInsight и больше не будет предоставляться через портал Azure для создания кластера. Тем не менее, эти версии все еще могут быть созданы с помощью Azure CLI или различных SDK. 

**Выход из** версии HDInsight означает, что существующие кластеры будут продолжать работать как есть. Однако новые кластеры этой версии не могут быть созданы с помощью любых средств (включая CLI и SDK). Другие функции плоскости управления (например, ручное масштабирование и автоматическое масштабирование) также могут не работать после выхода на пенсию версии. Поддержка недоступна для отставных версий.

В приведенных ниже таблицах перечислены версии HDInsight. Даты прекращения поддержки и прекращения использования также предоставляются, когда они известны.

### <a name="available-versions"></a>Доступные версии

В этой таблице перечислены версии HDInsight, доступные на портале Azure, а также другие методы развертывания, например с помощью PowerShell и SDK .NET.

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

    Раньше можно было создавать кластеры HDInsight только с правами локального администратора и локального пользователя SSH. Локальный администратор может получить доступ ко всем файлам, папкам, таблицам и столбцам.  Пакет безопасности предприятия позволяет включить управление доступом на основе ролей. Для этого кластеры HDInsight интегрируются с вашей собственной службой Active Directory, которая включает локальную версию Active Directory, доменные службы Azure AD или Active Directory на виртуальной машине IaaS. Администратор домена в кластере может предоставить пользователям доступ к кластеру с помощью их собственных корпоративных (принадлежащих домену) имен пользователя и паролей.

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

### <a name="pricing-and-service-level-agreement"></a>Соглашение о ценах и уровне обслуживания

Сведения о ценах и соглашение об уровне обслуживания для пакета безопасности предприятия см. на странице [Цены на Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Соглашение об уровне обслуживания (SLA) для версий кластера HDInsight

Соглашение об уровне обслуживания определяется в терминах _окна поддержки_. Под окном поддержки понимается период времени, в течение которого определенная версия кластера HDInsight поддерживается службой поддержки клиентов корпорации Майкрософт. Если версия достигла _даты прекращения поддержки_, кластер HDInsight находится за пределами окна поддержки. Дата истечения срока действия технической поддержки для конкретной версии HDInsight (обозначается как версия X) рассчитывается как наиболее поздняя из следующих дат (после доступности более новой версии Х+1):  

- Формула 1. Добавьте 180 дней к дате выхода версии X кластера HDInsight.
- Формула 2. Добавьте 90 дней к дате, когда версия кластера HDInsight X + 1 стала доступной на портале Azure.

_Дата выхода на пенсию_ — это дата, после которой кластерная версия не может быть создана на HDInsight. Начиная с 31 июля 2017 года, вы не можете изменить размер кластера HDInsight после даты выхода на пенсию.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Заметки о выпуске Hortonworks, связанные с версиями HDInsight

Раздел содержит ссылки на заметки о выпуске для дистрибутивов Hortonworks Data Platform и компонентов Apache, которые используются с HDInsight.
* Кластер HDInsight версии 4.0 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html).
* Кластер HDInsight версии 3.6 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Кластер HDInsight версии 3.5 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Кластер HDInsight версии 3.5 является кластером Hadoop _по умолчанию_, который создается на портале Azure.
* Кластер HDInsight версии 3.4 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Кластер HDInsight версии 3.3 использует пакет Hadoop, построенный на платформе [Нortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Заметки о выпуске Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) доступны на веб-сайте Apache.
  * [Заметки о выпуске Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) доступны на веб-сайте Apache.
* Кластер HDInsight версии 3.2 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.2][hdp-2-2].

  * Заметки о выпуске для отдельных компонентов Apache: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) и [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Кластер HDInsight версии 3.1 использует дистрибутив Hadoop, основанный на платформе [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Кластеры HDInsight версии 3.1, созданные до 7 ноября 2014 г., основаны на платформе [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Кластер HDInsight версии 3.0 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 2.0][hdp-2-0-8].
* Кластер HDInsight версии 2.1 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 1.3][hdp-1-3-0].
* Кластер HDInsight версии 1.6 использует дистрибутив Hadoop, основанный на платформе [Нortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Конфигурация узлов и размеры виртуальных машин по умолчанию для кластеров

Для получения дополнительной [информации](hdinsight-supported-node-configuration.md)о том, какую виртуальную машину SKUs выбрать для кластера, см.

## <a name="next-steps"></a>Дальнейшие действия

- [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md)
- [Работа в экосистеме Hadoop в HDInsight на компьютере с Windows](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
