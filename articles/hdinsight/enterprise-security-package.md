---
title: Корпоративный пакет безопасности для Azure HDInsight
description: Сведения о компонентах и версиях Корпоративный пакет безопасности в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: d77d9877fbc49fbb147965b30e299b75b60ccce3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546659"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Корпоративный пакет безопасности для Azure HDInsight

Корпоративный пакет безопасности — это дополнительный пакет, который можно добавить в кластер HDInsight во время рабочего процесса создания кластера. Пакет безопасности предприятия поддерживает следующие возможности:

* Интеграция с Active Directory для аутентификации.

    В прошлом вы создали кластеры HDInsight с локальным пользователем-администратором и локальным пользователем SSH. Локальный администратор может получить доступ ко всем файлам, папкам, таблицам и столбцам.  С Корпоративный пакет безопасности вы включаете управление доступом на основе ролей Azure, интегрируя HDInsight с вашими доменными службами Azure Active Directory.

    Дополнительные сведения см. на странице

    * [Общие сведения об обеспечении безопасности Apache Hadoop с помощью Корпоративного пакета безопасности](./domain-joined/hdinsight-security-overview.md)

    * [Корпоративный пакет безопасности для HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Настройка среды с присоединенной к домену песочницей](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

    * [Настройка присоединенных к домену кластеров HDInsight с помощью доменных служб Azure Active Directory](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Авторизация данных.

  * Интеграция с Apache Ranger для авторизации очередей Hive, Spark SQL и Yarn.
  * Можно задать управление доступом к файлам и папкам.

    Дополнительные сведения см. [в статье Настройка политик Apache Hive в присоединенном к домену кластере HDInsight](./domain-joined/apache-domain-joined-run-hive.md) .

* Просмотр журналов аудита для отслеживания доступа и настроенных политик.

## <a name="supported-cluster-types"></a>Поддерживаемые типы кластеров

Сейчас только следующие типы кластеров поддерживают пакет безопасности предприятия:

* Hadoop (только HDInsight 3.6);
* Spark
* Kafka
* HBase
* Интерактивный запрос

## <a name="support-for-azure-data-lake-storage"></a>Поддержка Azure Data Lake Storage

Корпоративный пакет безопасности поддерживает использование Azure Data Lake Storage в качестве основного и дополнительного хранилища.

## <a name="pricing-and-service-level-agreement-sla"></a>Цены и соглашение об уровне обслуживания (SLA)

Сведения о ценах и соглашение об уровне обслуживания для пакета безопасности предприятия см. на странице [Цены на Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Дальнейшие действия

* [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md)
* [Работа в экосистеме Hadoop в HDInsight на компьютере с Windows](hdinsight-hadoop-windows-tools.md)
* [Заметки о выпуске Hortonworks, связанные с версиями Azure HDInsight](./hortonworks-release-notes.md)
* [Компоненты Apache в HDInsight](./hdinsight-component-versioning.md)