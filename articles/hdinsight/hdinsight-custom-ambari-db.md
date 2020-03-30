---
title: Пользовательская база данных Apache Ambari на Azure HDInsight
description: Узнайте, как создавать кластеры HDInsight с помощью собственной базы данных Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240154"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Настройка кластеров HDInsight с помощью пользовательского Ambari DB

Apache Ambari упрощает управление и мониторинг кластера Apache Hadoop. Ambari предоставляет простой в использовании веб-uI и REST API. Ambari включен в кластеры HDInsight и используется для мониторинга кластера и внесения изменений конфигурации.

При нормальном создании кластеров, как описано в других статьях, таких как [Настройка кластеров в HDInsight,](hdinsight-hadoop-provision-linux-clusters.md)Ambari развертывается в [базе данных S0 Azure S'L,](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) которая управляется HDInsight и недоступна для пользователей.

Пользовательская функция Ambari DB позволяет развертывать новый кластер и настроить Ambari во внешней базе данных, которая вам управляет. Развертывание выполняется с помощью шаблона управления ресурсами Azure. Эта функция имеет следующие преимущества:

- Настройка - вы выбираете размер и емкость обработки базы данных. Если у вас есть большие кластеры, обрабатывающие интенсивные рабочие нагрузки, база данных Ambari с более низкими спецификациями может стать узким местом для управления операциями.
- Гибкость - вы можете масштабировать базу данных по мере необходимости в соответствии с вашими требованиями.
- Управление - вы можете управлять резервными копированиями и безопасностью для вашей базы данных таким образом, чтобы соответствовать требованиям организаций.

В остальной части этой статьи рассматриваются следующие моменты:

- требования к использованию пользовательской функции Ambari DB
- шаги, необходимые для предоставления кластеров HDInsight с использованием собственной внешней базы данных для Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Пользовательские требования Ambari DB

Можно развернуть пользовательский Ambari DB со всеми типами кластеров и версиями. Несколько кластеров не могут использовать один и тот же Ambari DB.

Пользовательский Ambari DB имеет следующие другие требования:

- У вас должен быть существующий сервер и база данных Azure S'L DB.
- База данных, которую вы предоставляете для установки Ambari, должна быть пустой. В схеме dbo по умолчанию не должно быть таблиц.
- Пользователь, используемый для подключения к базе данных, должен иметь в базе данных разрешения SELECT, CREATE TABLE и INSERT.
- Включите опцию [разрешить доступ к службам Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) на сервере Azure S'L, где вы будете размещать Ambari.
- IP-адреса управления службой HDInsight должны быть разрешены в сервере S'L. Просматривайте [IP-адреса управления HDInsight](hdinsight-management-ip-addresses.md) для получения списка IP-адресов, которые должны быть добавлены в брандмауэр сервера S'L.

При размещении Apache Ambari DB во внешней базе данных, помните следующие моменты:

- Вы несете ответственность за дополнительные расходы, связанные с Azure S'L DB, который удерживает Ambari.
- Резервное копирование пользовательских Ambari DB периодически. База данных Azure S'L генерирует резервные данные автоматически, но сроки хранения резервного копирования различаются. Дополнительные сведения см. в статье [Подробнее об автоматическом резервном копировании базы данных SQL](../sql-database/sql-database-automated-backups.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Развертывание кластеров с пользовательским Ambari DB

Для создания кластера HDInsight, используюго собственную внешнюю базу данных Ambari, используйте [пользовательский шаблон Ambari DB quickstart.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)

Отспособьте параметры в `azuredeploy.parameters.json` для указания информации о новом кластере и базе данных, в которых будет храниться Ambari.

Развертывание можно начать с помощью Azure CLI. Замените `<RESOURCEGROUPNAME>` группу ресурсов, в которой требуется развернуть кластер.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Дальнейшие действия

- [Использование внешних хранилищ метаданных в Azure HDInsight](hdinsight-use-external-metadata-stores.md)