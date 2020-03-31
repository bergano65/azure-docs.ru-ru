---
title: Установка кластеров в HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других технологий
description: Сведения об установке кластеров Hadoop, Kafka, Spark, HBase, Microsoft R Server или Storm для HDInsight из браузера с помощью классического Azure CLI, Azure PowerShell, REST или пакета SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 246ec08e9b4edb33fa49318b68cc4364534282b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064649"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Установка кластеров в HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других технологий

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Узнайте, как настроить и настроить Apache Hadoop, Apache Spark, Apache Kafka, Интерактивный запрос, Apache HBase, ML Services или Apache Storm в HDInsight. Кроме того, вы узнаете, как повысить безопасность кластеров путем их присоединения к домену.

Кластер Hadoop включает в себя несколько виртуальных машин (узлов), которые используются для распределенной обработки задач. Azure HDInsight управляет сведениями об установке и настройке отдельных узлов, поэтому вам нужно указать только общие сведения о конфигурации.

> [!IMPORTANT]  
> Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. Узнайте, как [удалить кластер](hdinsight-delete-cluster.md).

## <a name="cluster-setup-methods"></a>Способы установки кластера

В приведенной ниже таблице представлены различные способы установки кластера HDInsight.

| Метод создания кластеров | браузер | Командная строка | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Портал Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Фабрика данных Azure](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Лазурный CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Лазурная силаШелл](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Шаблоны диспетчера ресурсов Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Эта статья проветроки вас через установку в [портале Azure](https://portal.azure.com), где вы можете создать кластер HDInsight.

## <a name="basics"></a>Основы

![hdinsight создать параметры пользовательских быстро](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>сведения о проекте;

[Менеджер ресурсов Azure](../azure-resource-manager/management/overview.md) помогает работать с ресурсами в приложении как с группой, называемой [группой ресурсов](../azure-resource-manager/management/overview.md#resource-groups)Azure. Вы можете развертывать, обновлять, отслеживать или удалять все ресурсы для приложения в рамках одной скоординированной операции.

### <a name="cluster-details"></a>Сведения о кластере

#### <a name="cluster-name"></a>Имя кластера

Имена кластеров HDInsight имеют следующие ограничения:

* Разрешенные символы: a-z, 0-9, A-я
* Максимальная длина: 59
* Зарезервированные имена: приложения
* Область именования кластеров предназначена для всех Azure во всех подписках. Таким образом, название кластера должно быть уникальным во всем мире.
* Первые шесть символов должны быть уникальными в виртуальной сети

#### <a name="region"></a>Регион

Расположение кластера не требуется указывать явно: кластер находится в том же расположении, что и хранилище по умолчанию. Для списка поддерживаемых регионов выберите список выпадающих регионов **региона** по [ценам HDInsight.](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)

#### <a name="cluster-type"></a>Тип кластера

В настоящее время Azure HDInsight предоставляет указанные ниже типы кластеров, каждый из которых имеет набор компонентов для предоставления определенных функциональных возможностей.

> [!IMPORTANT]  
> Доступны различные типы кластеров HDInsight, каждый из которых предназначен для отдельной рабочей нагрузки или технологии. Создать кластер, в котором бы объединились несколько типов, например Storm и HBase, нельзя. Если для решения нужны технологии, распределенные по нескольким типам кластеров HDInsight, [виртуальная сеть Azure](https://docs.microsoft.com/azure/virtual-network) может объединять необходимые типы кластеров.

| Тип кластера | Функциональность |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Пакетный запрос и анализ хранимых данных |
| [HBase](hbase/apache-hbase-overview.md) |Обработка больших объемов данных NoSQL без схемы |
| [Интерактивный запрос](./interactive-query/apache-interactive-query-get-started.md) |Кэширование в памяти для обеспечения интерактивных и ускоренных запросов Hive |
| [Kafka](kafka/apache-kafka-introduction.md) | Распределенная платформа потоковой передачи с открытым кодом, которую можно использовать для создания конвейеров и приложений потоковой передачи данных в режиме реального времени. |
| [Службы машинного обучения](r-server/r-server-overview.md) |Разнообразная статистика больших данных, прогнозное моделирование и возможности машинного обучения |
| [Spark](spark/apache-spark-overview.md) |Обработка в памяти, интерактивные запросы, обработка потоков микро-пакетов |
| [Шторм](storm/apache-storm-overview.md) |Обработка событий в режиме реального времени |

#### <a name="version"></a>Версия

Выберите версию HDInsight для этого кластера. Дополнительные сведения см. в разделе [Поддерживаемые версии HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Учетные данные кластера

Во время создания кластера HDInsight можно настроить две учетные записи пользователя.

* Имя пользователя кластера: Имя пользователя по умолчанию является *админ.* Он использует базовую конфигурацию на портале Azure. Иногда он называется "Кластер пользователя", или "HTTP пользователя".
* Безопасное имя пользователя Shell (SSH): Используется для подключения к кластеру через SSH. Для получения дополнительной информации [см.](hdinsight-hadoop-linux-use-ssh-unix.md)

Имя пользователя HTTP имеет следующие ограничения:

* Разрешено специальные `_` символы: и`@`
* Персонажи не допускаются: «;.»,,,:'!?»...<>)\/{}&-----%-пространство
* Максимальная длина: 20

Имя пользователя SSH имеет следующие ограничения:

* Разрешено специальные`_` символы: и`@`
* Персонажи не допускаются: «;.»,,,:'!?»...<>)\/{}&-----%-пространство
* Максимальная длина: 64
* Зарезервированные имена: hadoop, пользователи, oozie, улей, mapred, ambari-qa, зоозащитник, tez, hdfs, sqoop, пряжа, hcat, ams, hbase, шторм, администратор, администратор, пользователь, user1, тест, user2, test1, user3, admin1, admin1, a, actuser, adm, adm, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, david, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, david, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, david, david, davidnet, davidnet, davidnet, david, david, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, davidnet, david, david, david, david, davidnet, davidnet, davidnet, david гость, Джон, владелец, корень, сервер, sql, поддержка, support_388945a0, sys, test2, test3, user4, user5, искра

## <a name="storage"></a>Хранилище

![Настройки кластерного хранения: hdFS-совместимые конечные точки](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Хотя при локальной установке кластера Hadoop для хранилища используется распределенная файловая система Hadoop (HDFS), в облаке применяются конечные точки хранилища, подключенные к кластеру. Использование облачного хранилища означает, что вы можете безопасно удалять кластеры HDInsight, используемые для вычислений, сохраняя при этом свои данные.

Кластеры HDInsight могут использовать следующие параметры хранения:

* Azure Data Lake Storage 2-го поколения
* Хранилище Azure Data Lake Storage 1-го поколения
* Общая цель хранения azure v2
* Общая цель хранения azure v1
* Blob Azure Storage Block **(поддерживается только в качестве вторичного хранилища)**

Для получения дополнительной информации о вариантах хранения с ПОМОЩЬю HDInsight [см. Сравнить варианты хранения для использования с кластерами Azure HDInsight.](hdinsight-hadoop-compare-storage-options.md)

> [!WARNING]  
> Использование дополнительной учетной записи хранения, местоположение которой отличается от местоположения кластера HDInsight, не поддерживается.

Во время настройки вы указываете контейнер BLOB-объектов в учетной записи хранения Azure или хранилище Data Lake Storage для конечной точки хранилища по умолчанию. Хранилище по умолчанию содержит журналы приложений и системный журнал. При необходимости вы можете указать дополнительные связанные учетные записи хранения Azure и учетные записи Data Lake Storage, к которым кластер может получать доступ. Кластер HDInsight должен находиться в том же расположении Azure, что и зависимые учетные записи хранения.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="metastore-settings"></a>Настройки Metastore

Вы можете создать дополнительные хранилища метаданных Hive или Apache Oozie. Однако не все типы кластеров поддерживают хранилища метаданных, а хранилище данных SQL Azure не совместимо с хранилищами метаданных.

Дополнительную информацию см. в статье [Использование внешних хранилищ метаданных в Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Создавая пользовательское хранилище метаданных, не используйте в имени базы данных тире, дефисы и пробелы. Это может привести к сбою при создании кластера.

#### <a name="sql-database-for-hive"></a>База данных для Hive

Если вы хотите сохранить таблицы Hive после удаления кластера HDInsight, используйте пользовательское хранилище метаданных. Затем можно будет подключить это хранилище к другому кластеру HDInsight.

Метамагазин HDInsight, созданный для одной кластерной версии HDInsight, не может быть общим для различных групповых версий HDInsight. Список версий HDInsight см. в разделе [Поддерживаемые версии HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

#### <a name="sql-database-for-oozie"></a>База данных для Oozie

Для повышения производительности Oozie используйте пользовательское хранилище метаданных. Хранилище метаданных также позволяет осуществлять доступ к данным задания Oozie после удаления кластера.

#### <a name="sql-database-for-ambari"></a>База данных Для Амбари

Ambari используется для мониторинга кластеров HDInsight, внесения изменений конфигурации и хранения информации об управлении кластерами, а также истории работы. Пользовательская функция Ambari DB позволяет развертывать новый кластер и настроить Ambari во внешней базе данных, которая вам управляет. Для получения дополнительной информации, см [пользовательских Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Повторно использовать хранилище метаданных Oozie невозможно. Чтобы использовать пользовательское хранилище метаданных Oozie, при создании кластера HDInsight необходимо предоставить пустую базу данных SQL Azure.

## <a name="security--networking"></a>Безопасность и сети

![hdinsight create options choose enterprise security package](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Пакет безопасности корпоративного уровня

Для кластеров типа Hadoop, Spark, HBase, Kafka и Interactive Query вы можете включить **Корпоративный пакет безопасности**. Этот пакет содержит параметр для более безопасной настройки кластера с помощью Apache Ranger и интеграции с Azure Active Directory. Для получения дополнительной информации смотрите [Обзор безопасности предприятия в Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Пакет безопасности предприятия позволяет интегрировать HDInsight с Active Directory и Apache Ranger. При помощи пакета безопасности корпоративного уровня можно создать нескольких пользователей.

Дополнительные сведения о создании присоединенного к домену кластера HDInsight см. в статье о [создании присоединенной к домену среды песочницы HDInsight](./domain-joined/apache-domain-joined-configure.md).

### <a name="tls"></a>TLS

Для получения дополнительной информации [см.](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)

### <a name="virtual-network"></a>Виртуальная сеть

Если для решения нужны технологии, распределенные по нескольким типам кластеров HDInsight, [виртуальная сеть Azure](https://docs.microsoft.com/azure/virtual-network) может объединять необходимые типы кластеров. Благодаря такой конфигурации кластеры и любой развернутый в них код могут взаимодействовать друг с другом напрямую.

Для получения дополнительной информации об использовании виртуальной сети Azure с HDInsight, [см.](hdinsight-plan-virtual-network-deployment.md)

Пример использования двух типов кластера в виртуальной сети Azure см. в статье об [использовании структурированного потока Apache Spark при помощи Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Для получения дополнительной информации об использовании HDInsight с виртуальной [Plan a virtual network for HDInsight](hdinsight-plan-virtual-network-deployment.md)сетью, включая конкретные требования к конфигурации для виртуальной сети, см.

### <a name="disk-encryption-setting"></a>Настройка шифрования диска

Для получения дополнительной [Customer-managed key disk encryption](./disk-encryption.md)информации см.

### <a name="kafka-rest-proxy"></a>Прокси-сервер REST для Kafka

Эта настройка доступна только для типа кластера Kafka. Для получения дополнительной информации [см.](./kafka/rest-proxy.md)

### <a name="identity"></a>идентификации

Для получения дополнительной информации смотрите [Управляемые идентификаторы в Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Конфигурация и ценообразование

![HDInsight выбирает размер узла](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Выставляете счет за использование узлов до тех пор, пока кластер существует. Начисление оплаты начинается после создания кластера и прекращается только после его удаления. Кластеры не могут быть отсекретаны или отложены.

### <a name="node-configuration"></a>конфигурация узла;

Для каждого типа кластера используется своя терминология. Кроме того, типы отличаются количеством узлов и стандартными размерами виртуальных машин. В следующей таблице число узлов каждого типа указано в скобках.

| Тип | Узлы | Схема |
| --- | --- | --- |
| Hadoop |Головной узла (2), Рабочий узла (1) |![Узлы кластера HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Головной сервер (2), региональный сервер (от 1), основной узел или узел Zookeeper (3) |![Установка кластера HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Узел Nimbus (2), сервер супервизора (от 1), узел Zookeeper (3) |![HDInsight настройки типа штормового кластера](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Головной узл (2), Рабочий узл (1"), узл зоозащитника (3) (бесплатно для размера A1 зоохранителя VM) |![HDInsight искры кластера типа установки](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Дополнительные сведения см. в разделе [Конфигурация узлов и размеры виртуальных машин по умолчанию для кластеров](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) статьи "Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?"

Затраты на кластеры HDInsight определяются числом узлов и размерами виртуальных машин на узлах.

Кластеры разных типов отличаются типами, количеством и размерами узлов.
* Тип кластера Hadoop по умолчанию:
    * два *головных узла*;  
    * Четыре *рабочих узла*
* Тип кластера Storm по умолчанию:
    * два *узла Nimbus*;
    * три *узла ZooKeeper*;
    * четыре *узла супервизора*.

Если вы просто опробуете HDInsight, мы рекомендуем вам использовать один рабочий узла. Подробные сведения о ценах на HDInsight см. на [этой странице](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Максимальный размер кластера зависит от подписки Azure. Чтобы увеличить лимит, обратитесь в [службу поддержки по вопросам выставления счетов Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

При использовании портала Azure для настройки кластера размер узла доступен через вкладку **«Конфигурация и цена».** На портале также можно увидеть стоимость, связанную с различными размерами узлов.

### <a name="virtual-machine-sizes"></a>Размер виртуальных машин

При развертывании кластеров выберите вычислительные ресурсы в зависимости от решения, которое планируется развернуть. Для кластеров HDInsight используются следующие виртуальные машины:

* виртуальные машины серий A и D1–4: [Размеры виртуальных машин Linux общего назначения](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general);
* виртуальные машины серии D11–14: [Размеры виртуальных машин Linux, оптимизированных для памяти](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Сведения о том, какие значения следует указывать для размера виртуальной машины при создании кластера с помощью различных пакетов SDK или Azure PowerShell, см. в разделе [Таблицы размеров](../cloud-services/cloud-services-sizes-specs.md#size-tables). Используйте значение из столбца **Размер** таблиц, приведенных в этой статье.

> [!IMPORTANT]  
> Если в кластере требуется более 32 рабочих узлов, необходимо выбрать размер головного узла с не менее чем 8 ядрами и 14 ГБ оперативной памяти.

Для получения дополнительной [информации см.](../virtual-machines/windows/sizes.md) Сведения о расценках на разные размеры см. [здесь](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Добавить приложение

Пользователи могут устанавливать приложения HDInsight в кластере HDInsight под управлением Linux. Вы можете использовать приложения, предоставляемые корпорацией Майкрософт, сторонними производителями или разработанные самостоятельно. Дополнительные сведения см. в статье [Установка сторонних приложений Apache Hadoop в Azure HDInsight](hdinsight-apps-install-applications.md).

Большинство приложений HDInsight устанавливаются в пустой граничный узел.  Пустой граничный узел — это виртуальная машина Linux, на которой установлены и настроены те же клиентские инструменты, что и на головном узле. Граничный узел можно использовать для доступа к кластеру, а также тестирования и размещения клиентских приложений. Подробные сведения см. в статье [Использование пустых граничных узлов в HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Действия сценария

Можно установить дополнительные компоненты или настроить конфигурацию кластера с помощью сценариев во время создания. Такие скрипты вызываются с помощью **действия скрипта** — параметра конфигурации, который может использоваться с помощью портала Azure, командлетов HDInsight PowerShell или пакета SDK для HDInsight .NET. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

Некоторые собственные компоненты Java, такие как Apache Mahout и Cascading, могут выполняться в кластере как архивные файлы (JAR) Java. Эти JAR-файлы можно распространить в службе хранилища Azure и отправить в кластеры HDInsight с помощью механизмов отправки заданий Hadoop. См. дополнительные сведения об [отправке заданий Apache Hadoop программными средствами](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Если при развертывании или вызове JAR-файлов в кластерах HDInsight возникают проблемы, обратитесь в [службу поддержки Майкрософт](https://azure.microsoft.com/support/options/).
>
> Компонент Cascading не поддерживается службой HDInsight и на него не распространяется техническая поддержка Майкрософт. Списки поддерживаемых компонентов см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)

В некоторых случаях требуется изменить следующие файлы конфигурации в процессе создания:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* Hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Подробные сведения см. в статье [Настройка кластеров HDInsight с помощью начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Сбои создания кластеров с Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Что такое Azure HDInsight и стек технологий Apache Hadoop](hadoop/apache-hadoop-introduction.md)
* [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Работа в экосистеме Hadoop в HDInsight на компьютере с Windows](hdinsight-hadoop-windows-tools.md)
