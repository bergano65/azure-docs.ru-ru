---
title: Устранение сбоев при создании кластера с помощью Azure HDInsight
description: Узнайте, как устранять неполадки при создании кластера для Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/06/2019
ms.openlocfilehash: c7092b2cbcef01ef71261b6f5498cde56a40c358
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857252"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Устранение сбоев при создании кластера с помощью Azure HDInsight

Ниже перечислены основные причины сбоев при создании кластера.

- Проблемы с разрешениями
- Ограничения политики ресурсов
- Брандмауэры
- Блокировки ресурсов
- Неподдерживаемые версии компонентов
- Ограничения имен учетных записей хранения
- Простои службы

## <a name="permissions-issues"></a>Проблемы с разрешениями

Если вы используете Data Lake Storage Gen 2, убедитесь, что назначенное пользователем управляемое удостоверение, назначенное вашему кластеру HDInsight, находится в роли **участника данных BLOB-объекта хранилища** или в **роли владельца данных большого двоичного объекта хранилища**. Полные инструкции по установке см. [в статье использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) .

Если вы используете Data Lake Storage Gen 1, см. инструкции по настройке и настройке [здесь](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage Gen 1 не поддерживается для кластеров HBase и не поддерживается в HDInsight версии 4,0.

При использовании службы хранилища Azure убедитесь, что имя учетной записи хранения допустимо во время создания кластера.

## <a name="resource-policy-restrictions"></a>Ограничения политики ресурсов

Политики Azure на основе подписки могут запрещать создание общедоступных IP-адресов. Для создания кластера HDInsight требуется два общедоступных IP-адреса.  

Как правило, следующие политики могут повлиять на создание кластера:

* Политики, препятствующие созданию IP-адресов & подсистемы балансировки нагрузки в рамках подписки.
* Политика, препятствующая созданию учетной записи хранения.
* Политика, препятствующая удалению сетевых ресурсов (подсистемы балансировки IP-адресов/лоад).

## <a name="firewalls"></a>Брандмауэры

Брандмауэры в виртуальной сети или учетной записи хранения могут запретить обмен данными с IP-адресами управления HDInsight.

Разрешить трафик с IP-адресов, указанных в таблице ниже.

| Исходный IP-адрес | Назначение | Direction |
|---|---|---|
| 168.61.49.99 | *: 443 | Входящие |
| 23.99.5.239 | *: 443 | Входящие |
| 168.61.48.131 | *: 443 | Входящие |
| 138.91.141.162 | *: 443 | Входящие |

Также добавьте IP-адреса, относящиеся к региону, в котором создается кластер. Список адресов для каждого региона Azure см. в разделе [IP-адреса управления HDInsight](../hdinsight-management-ip-addresses.md) .

Если вы используете Express Route или собственный DNS-сервер, см. статью [планирование виртуальной сети для Azure HDInsight: подключение нескольких сетей](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Блокировки ресурсов  

Убедитесь, что [в виртуальной сети и группе ресурсов нет блокировок](../../azure-resource-manager/resource-group-lock-resources.md).  

## <a name="unsupported-component-versions"></a>Неподдерживаемые версии компонентов

Убедитесь, что вы используете [поддерживаемую версию Azure HDInsight](../hdinsight-component-versioning.md) и все [компоненты Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) в решении.  

## <a name="storage-account-name-restrictions"></a>Ограничения имен учетных записей хранения

Длина имен учетных записей хранения не может превышать 24 символов и не может содержать специальный символ. Эти ограничения также касаются имени контейнера по умолчанию в учетной записи хранения.

## <a name="service-outages"></a>Простои службы

Проверьте [состояние Azure](https://status.azure.com/status) на наличие потенциальных сбоев или проблем со службой.

## <a name="next-steps"></a>Следующие шаги

* [Расширение возможностей HDInsight с помощью виртуальной сети Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Использование Azure Data Lake Storage Gen2 с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Использование службы хранилища Azure с кластерами Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Установка кластеров в HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других технологий](../hdinsight-hadoop-provision-linux-clusters.md)
