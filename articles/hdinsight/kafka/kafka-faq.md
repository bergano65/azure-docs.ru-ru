---
title: Часто задаваемые вопросы о Apache Кафка в Azure HDInsight
description: Получите ответы на распространенные вопросы о Apache Kafka на Azure HDInsight, управляемом облачном сервисе Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: ff4079263fd7afb02e132a798997687fad7e9961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206985"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Frequently asked questions about Apache Kafka in Azure HDInsight (Часто задаваемые вопросы о Apache Kafka в Azure HDInsight)

В этой статье рассматриваются некоторые распространенные вопросы об использовании Apache Kafka в Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Какие версии Kafka поддерживаются HDInsight?

Найти более подробную информацию о HDInsight официально поддерживается компонентверсий в [Каковы компоненты Apache Hadoop и версии доступны с HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). Мы рекомендуем всегда использовать последнюю версию, чтобы обеспечить наилучшую производительность и пользовательский опыт.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Какие ресурсы предоставляются в кластере HDInsight Kafka и за какие ресурсы взимается плата?

Кластер HDInsight Kafka включает в себя следующие ресурсы:

* Головные узлы
* Узлы Zookeeper
* Брокерские (рабочие) узлы 
* Управляемые диски Azure, прикрепленные к узлам брокера
* Узлы шлюза

Все эти ресурсы взимаются на основе нашей [модели ценообразования HDInsight,](https://azure.microsoft.com/pricing/details/hdinsight/)за исключением узлов шлюза. С вас не взимается плата за узлы шлюза.

Более подробное описание различных типов узлов можно получить в виртуальной [сетевой архитектуре Azure HDInsight.](../hdinsight-virtual-network-architecture.md) Ценообразование основано на использовании в минуту узла. Цены варьируются в зависимости от размера узла, количества узлов, типа управляемого используемого диска и региона.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Работают ли Apache Kafka AIS с HDInsight?

Да, HDInsight использует родные ApIs Кафки. Код клиентского приложения не нуждается в изменении. [См. Обучатель: Используйте Apache Kafka Producer и потребительские AIS,](./apache-kafka-producer-consumer-api.md) чтобы увидеть, как можно использовать Java-производственные/ потребительские AA с кластером.

## <a name="can-i-change-cluster-configurations"></a>Могу ли я изменить конфигурацию кластеров?

Да, через портал Амбари. Каждый компонент портала имеет **секцию конфигураций,** которая может быть использована для изменения конфигураций компонентов. Некоторые изменения могут потребовать перезапуска брокера.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Какой тип аутентификации поддерживает Apache Kafka?

С помощью [пакета корпоративной безопасности (ESP)](../domain-joined/apache-domain-joined-architecture.md)можно получить безопасность на уровне темы для кластеров Kafka. См. Учебник: Для получения дополнительной информации можно ознакомиться с инструкциями по [политике Apache Kafka в HDInsight с пакетом корпоративной безопасности (Preview) для](../domain-joined/apache-domain-joined-run-kafka.md)получения дополнительной информации.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Зашифрованы ли мои данные? Могу ли я использовать свои собственные ключи?

Все сообщения Kafka на управляемых дисках шифруются с [помощью шифрования службы хранения данных Azure (SSE).](../../storage/common/storage-service-encryption.md) Транзит данных (например, данные, передаваемые от клиентов брокерам и наоборот) не шифруется по умолчанию. Можно шифровать такой трафик, [настраивая SSL самостоятельно.](./apache-kafka-ssl-encryption-authentication.md) Кроме того, HDInsight позволяет управлять своими ключами для шифрования данных в состоянии покоя. Дополнительную информацию можно узнать [из-за шифрования диска с управлением клиентов.](../disk-encryption.md)

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Как подключить клиентов к кластеру?

Для клиентов Kafka, чтобы общаться с брокерами Kafka, они должны быть в состоянии связаться с брокерами по сети. Для кластеров HDInsight виртуальная сеть (VNet) является границей безопасности. Таким образом, самый простой способ подключения клиентов к вашему кластеру HDInsight — это создавать клиентов в рамках того же VNet, что и кластер. Другие сценарии включают в себя:

* Подключение клиентов в другой Azure VNet — исправите кластер VNet и клиент VNet и направите кластер для [IP-рекламы.](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising) При использовании IP-рекламы клиенты Kafka должны использовать IP-адреса брокера для связи с брокерами, а не полностью квалифицированными доменными именами (F-DNs).

* Подключение клиентов - Использование VPN-сети и настройка пользовательских DNS-серверов, как описано в [Плане, виртуальная сеть для Azure HDInsight.](../hdinsight-plan-virtual-network-deployment.md)

* Создание общедоступной точки для службы Kafka — Если это позволяют требования к корпоративной безопасности, вы можете развернуть публичную конечную точку для ваших брокеров Kafka или самоуправляемую конечную точку REST с открытым исходным кодом с общедоступной конечной точкой.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Могу ли я добавить больше дискового пространства на существующем кластере?

Чтобы увеличить количество места, доступного для сообщений Kafka, можно увеличить количество узлов. В настоящее время добавление большего количества дисков в существующий кластер не поддерживается.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Может ли кластер Kafka работать с Databricks? 

Да, кластеры Kafka могут работать с Databricks до тех пор, пока они находятся в том же VNet. Чтобы использовать кластер Kafka с Databricks, создайте VNet с кластером HDInsight Kafka, затем укажите, что VNet при создании рабочего пространства Databricks и использовании впрыска VNet. Дополнительные сведения см. в статье [Deploy Azure Databricks in your Azure Virtual Network (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject) (Развертывание Azure Databricks в виртуальной сети Azure (внедрение виртуальной сети)). При создании рабочего пространства Databricks необходимо предоставить имена брокеров Кафки. Для получения информации о получении имен брокера Kafka, [см.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)

## <a name="how-can-i-have-maximum-data-durability"></a>Как я могу иметь максимальную долговечность данных?

Долговечность данных позволяет достичь самого низкого риска потери сообщений. Для достижения максимальной долговечности данных мы рекомендуем следующие настройки:

* использовать минимальный коэффициент репликации 3 в большинстве регионов
* использовать минимальный коэффициент репликации 4 в регионах с двумя доменами неисправности
* отключить нечистый лидер выборов
* установить **min.insync.replicas** до 2 или более - это изменяет количество реплик, которые должны быть полностью синхронизированы с лидером, прежде чем писать может продолжить
* установить свойство **acks** для **всех** - это свойство требует, чтобы все реплики, чтобы признать все сообщения

Настройка Kafka для более высокой согласованности данных влияет на доступность брокеров для получения запросов.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Могу ли я воспроизвести свои данные на несколько кластеров?

Да, данные могут быть воспроизведены на несколько кластеров с помощью Kafka MirrorMaker. Подробнее о настройке MirrorMaker читайте в [материале Mirror Apache Kafka.](apache-kafka-mirroring.md) Кроме того, существуют и другие самоуправляемые технологии с открытым исходным кодом и поставщики, которые могут помочь достичь репликации для нескольких кластеров, таких как [Brooklin.](https://github.com/linkedin/Brooklin/)

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Могу ли я обновить свой кластер? Как обновить кластер?

В настоящее время мы не поддерживаем обновления кластерных версий на месте. Чтобы обновить кластер до более высокой версии Kafka, создайте новый кластер с нужной версией и переносите клиентов Kafka для использования нового кластера.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Как контролировать кластер Kafka?

Используйте монитор Azure для анализа [журналов Kafka.](./apache-kafka-log-analytics-operations-management.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка шифрования и аутентификации Безопасных розеток (SSL) для Apache Kafka в Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Репликация разделов Apache Kafka с помощью Kafka в HDInsight и MirrorMaker](./apache-kafka-mirroring.md)
