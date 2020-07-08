---
title: Пользовательская база данных Apache Ambari в Azure HDInsight
description: Узнайте, как создавать кластеры HDInsight с помощью собственной пользовательской базы данных Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: e92b0679111a6d5c6173da04c5061c95956125b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322957"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Настройка кластеров HDInsight с помощью настраиваемой базы данных Ambari DB

Apache Ambari упрощает управление и мониторинг кластера Apache Hadoop. Ambari предоставляет простой в использовании пользовательский веб-интерфейс и REST API. Ambari входит в кластеры HDInsight и используется для мониторинга кластера и внесения изменений в конфигурацию.

При нормальном создании кластера, как описано в других статьях, таких как [Настройка кластеров в hdinsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari развертывается в [базе данных SQL Azure S0](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) , управляемой HDInsight и недоступной пользователям.

Функция Custom Ambari DB позволяет развернуть новый кластер и настроить Ambari во внешней управляемой базе данных. Развертывание выполняется с помощью шаблона Azure Resource Manager. Эта функция имеет следующие преимущества.

- Настройка — вы выбираете размер и производительность обработки базы данных. Если большие кластеры интенсивно обрабатывают ресурсоемкие рабочие нагрузки, то база данных Ambari с более низкими спецификациями может стать узким местом для операций управления.
- Гибкость. Вы можете масштабировать базу данных по мере необходимости в соответствии с вашими требованиями.
- Управление. Вы можете управлять резервным копированием и безопасностью базы данных способом, который соответствует требованиям вашей организации.

В оставшейся части этой статьи обсуждаются следующие моменты.

- требования для использования пользовательской функции Ambari DB
- действия, необходимые для подготовки кластеров HDInsight с помощью собственной внешней базы данных для Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Пользовательские требования к базе данных Ambari DB

Пользовательскую базу данных Ambari DB можно развернуть со всеми типами и версиями кластера. Несколько кластеров не могут использовать одну и ту же Ambari DB.

Пользовательская база данных Ambari DB имеет следующие другие требования:

- Имя базы данных не может содержать дефисы и пробелы
- Необходимо иметь существующий сервер и базу данных SQL Azure.
- База данных, предоставляемая для установки Ambari, должна быть пустой. В схеме dbo по умолчанию не должно быть таблиц.
- Пользователь, используемый для подключения к базе данных, должен иметь разрешения SELECT, CREATE TABLE и INSERT для базы данных.
- Включите параметр, чтобы [Разрешить доступ к службам Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) на сервере, где будет размещаться Ambari.
- IP-адреса управления из службы HDInsight должны быть разрешены в правиле брандмауэра. Список IP-адресов, которые необходимо добавить в правило брандмауэра уровня сервера, см. в разделе [IP-адреса управления HDInsight](hdinsight-management-ip-addresses.md) .

При размещении Apache Ambari DB во внешней базе данных учитывайте следующие моменты.

- Вы несете ответственность за дополнительные затраты на базу данных SQL Azure, которая содержит Ambari.
- Периодически создавайте резервную копию пользовательской базы данных Ambari DB. База данных SQL Azure автоматически создает резервные копии, но срок хранения резервной копии изменяется. Дополнительные сведения см. в статье [Подробнее об автоматическом резервном копировании базы данных SQL](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Развертывание кластеров с помощью настраиваемой базы данных Ambari DB

Чтобы создать кластер HDInsight, использующий собственную внешнюю базу данных Ambari, используйте [шаблон быстрого запуска AMBARI DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Измените параметры в, `azuredeploy.parameters.json` чтобы указать сведения о новом кластере и базе данных, в которой будет храниться Ambari.

Вы можете начать развертывание с помощью Azure CLI. Замените `<RESOURCEGROUPNAME>` группой ресурсов, в которой требуется развернуть кластер.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Дальнейшие шаги

- [Использование внешних хранилищ метаданных в Azure HDInsight](hdinsight-use-external-metadata-stores.md)
