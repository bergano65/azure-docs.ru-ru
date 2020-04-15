---
title: Сбои создания кластеров с Azure HDInsight
description: Узнайте, как устранить проблемы создания кластеров Apache для Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: a95c4d654650276c815c5b23fb2c6f8a6d06bc2e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383452"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Сбои создания кластеров с Azure HDInsight

Следующие проблемы являются наиболее распространенными основными причинами сбоев создания кластеров:

- Вопросы разрешения
- Ограничения политики в отношении ресурсов
- Брандмауэры
- Блокировки ресурсов
- Неподдерживаемые версии компонентов
- Ограничения на хранение имен учетной записи
- Перебои в обслуживании

## <a name="permissions-issues"></a>Проблемы с разрешениями

Если вы используете Azure Data Lake Storage ```AmbariClusterCreationFailedErrorCode```Gen2 и получаете ```Internal server error occurred while processing the request. Please retry the request or contact support.```ошибку, откройте портал Azure, перейдите на свой аккаунт хранения и под контролем доступа (IAM), убедитесь, что **вкладчик у владельца данных Storage Blob** или роль владельца **данных Storage Blob** получили доступ к **назначенному пользователю управляемому удостоверению** для подписки. Подробные инструкции см. в разделе о [настройке разрешений для управляемого удостоверения в учетной записи Data Lake Storage 2-го поколения](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account).

Если вы используете Azure Data Lake Storage Gen1, смотрите инструкции по настройке и конфигурации [здесь.](../hdinsight-hadoop-use-data-lake-store.md) Data Lake Storage Gen1 не поддерживается для кластеров HBase и не поддерживается в версии HDInsight 4.0.

При использовании Azure Storage убедитесь, что имя учетной записи хранилища допустимо во время создания кластера.

## <a name="resource-policy-restrictions"></a>Ограничения политики в отношении ресурсов

Политики Azure, основанные на подписке, могут отказать в создании общедоступных IP-адресов. Для создания кластера HDInsight требуется два общедоступных IP-адреса.  

Как правило, следующие политики могут повлиять на создание кластеров:

* Политики, препятствующие созданию IP-адреса & балансеров нагрузки в рамках подписки.
* Политика, препятствующая созданию учетной записи хранилища.
* Политика, предотвращающая удаление сетевых ресурсов (IP-адрес/балансеры нагрузки).

## <a name="firewalls"></a>Брандмауэры

Брандмауэры в вашей виртуальной сети или учетной записи хранения могут отказать в общении с IP-адресами управления HDInsight.

Разрешить трафик с IP-адресов в таблице ниже.

| Исходный IP-адрес | Назначение | Направление |
|---|---|---|
| 168.61.49.99 | *:443 | Входящий трафик |
| 23.99.5.239 | *:443 | Входящий трафик |
| 168.61.48.131 | *:443 | Входящий трафик |
| 138.91.141.162 | *:443 | Входящий трафик |

Также добавьте IP-адреса, специфичные для региона, где создается кластер. Смотрите [IP-адреса управления HDInsight](../hdinsight-management-ip-addresses.md) для перечисления адресов для каждого региона Azure.

Если вы используете экспресс-маршрут или собственный [Plan a virtual network for Azure HDInsight - connecting multiple networks](../hdinsight-plan-virtual-network-deployment.md#multinet)dNS-сервер, см.

## <a name="resources-locks"></a>Блокировки ресурсов  

Убедитесь, что в [вашей виртуальной сети и группе ресурсов нет блокировок.](../../azure-resource-manager/management/lock-resources.md) Кластеры не могут быть созданы или удалены, если группа ресурсов заблокирована. 

## <a name="unsupported-component-versions"></a>Неподдерживаемые версии компонентов

Убедитесь, что вы используете [поддерживаемую версию Azure HDInsight](../hdinsight-component-versioning.md) и любые [компоненты Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) в вашем решении.  

## <a name="storage-account-name-restrictions"></a>Ограничения на хранение имен учетной записи

Имена учетных записей хранилища не могут быть более чем 24 символами и не могут содержать специальный символ. Эти ограничения также касаются имени контейнера по умолчанию в учетной записи хранения.

Другие ограничения именования также применяются для создания кластера. Для получения дополнительной информации [см.](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)

## <a name="service-outages"></a>Перебои в обслуживании

Проверьте [состояние Azure](https://status.azure.com) на наличие возможных сбоев или проблем с обслуживанием.

## <a name="next-steps"></a>Следующие шаги

* [Расширяйте Azure HDInsight с помощью виртуальной сети Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Используйте хранилище данных Azure Data Lake Gen2 с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Использование службы хранилища Azure с кластерами Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Установка кластеров в HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других технологий](../hdinsight-hadoop-provision-linux-clusters.md)
