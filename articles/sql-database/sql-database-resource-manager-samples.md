---
title: Шаблоны Azure Resource Manager для Базы данных SQL | Документация Майкрософт
description: Использование шаблонов Azure Resource Manager для создания и настройки базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: b967dc872529ec8b045df81542eec4c555b17a6c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418432"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Шаблоны Azure Resource Manager для Базы данных SQL Azure

Шаблоны Azure Resource Manager позволяют определить инфраструктуру как код и развертывать решения в облаке Azure.

## <a name="single-database--elastic-pool"></a>Отдельная база данных или эластичный пул

В следующей таблице представлены ссылки на шаблоны Azure Resource Manager для Базы данных SQL Azure.

| |  |
|---|---|
| [Отдельная база данных](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Этот шаблон Azure Resource Manager создает отдельную базу данных SQL Azure с логическим сервером и настраивает правила брандмауэра. |
| [Логический сервер](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Этот шаблон Azure Resource Manager создает логический сервер Базы данных SQL Azure. |
| [Эластичный пул](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Этот шаблон позволяет развернуть новый эластичный пул с новым связанным сервером SQL Server и новой Базой данных SQL, назначенной ему. |
| [Группы отработки отказа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Этот шаблон создает два логических сервера SQL Azure, базу данных SQL и группу отработки отказа.|
| [Обнаружение угроз](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Этот шаблон позволяет развернуть логический сервер SQL Azure и набор баз данных SQL Azure с включенным обнаружением угроз и адресом электронной почты для оповещений для каждой базы данных. Обнаружение угроз является частью предложения Расширенной защиты от угроз (ATP) и предоставляет слой безопасности, который отвечает на потенциальные угрозы через серверы и базы данных SQL.|
| [Аудит в хранилище BLOB-объектов Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Этот шаблон позволяет развернуть логический сервер SQL Azure с включенным аудитом для записи журналов аудита в хранилище BLOB-объектов. Аудит базы данных SQL Azure отслеживает события базы данных и записывает их в журнал аудита, который можно разместить в учетной записи хранения Azure, рабочей области OMS или Центрах событий.|
| [Аудит в концентраторе событий Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Этот шаблон позволяет развернуть сервер SQL Azure с включенным аудитом для записи журналов аудита в имеющийся концентратор событий. Чтобы отправить события аудита в концентратор событий, задайте параметры аудита с помощью `Enabled` `State` и задайте `IsAzureMonitorTargetEnabled` значение `true`. Кроме того, настройте параметры диагностики с помощью категории журналов диагностики `SQLSecurityAuditEvents` в базе данных `master` (для аудита на уровне обслуживания). Аудит базы данных SQL Azure и Хранилища данных SQL отслеживает события базы данных и записывает их в журнал аудита, который можно разместить в учетной записи хранения Azure, в рабочей области OMS или в Центрах событий.|
| [Веб-приложение Azure с Базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Этот пример создает бесплатное веб-приложение Azure и Базу данных SQL с уровнем обслуживания "Базовый".|
| [Веб-приложение Azure и Кэш Redis с Базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Этот шаблон создает веб-приложение, Кэш Redis и Базу данных SQL в той же группе ресурсов и создает две строки подключения в веб-приложении для Базы данных SQL и Кэша Redis.|
| [Импорт данных из хранилища BLOB-объектов с помощью ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Этот шаблон Azure Resource Manager создает Фабрику данных Azure версии 2, копирующую данные из хранилища BLOB-объектов Azure в Базу данных SQL.|
| [Кластер HDInsight с Базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Этот шаблон позволяет создать кластер HDInsight, сервер Базы данных SQL, Базу данных SQL и две таблицы. Этот шаблон используется в статье [Использование Apache Sqoop с Hadoop в HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Приложение логики Azure, выполняющее хранимую процедуру SQL по расписанию](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Этот шаблон позволяет создать приложение логики, которое будет запускать хранимую процедуру SQL по расписанию. Все аргументы для процедуры можно поместить в раздел body шаблона.|

## <a name="managed-instance"></a>Управляемый экземпляр

В следующей таблице представлены ссылки на шаблоны Azure Resource Manager для Управляемого экземпляра Базы данных SQL Azure.

| |  |
|---|---|
| [Управляемый экземпляр в новой виртуальной сети](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Этот шаблон Azure Resource Manager создает настроенную виртуальную сеть Azure и Управляемый экземпляр в виртуальной сети. |
| [Сетевая среда для Управляемого экземпляра](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Это развертывание создаст настроенную виртуальную сеть Azure с двумя подсетями: одна будет выделена для Управляемых экземпляров, а другая — для других ресурсов (например, виртуальных машин, сред Службы приложений и т. д.). Этот шаблон будет создавать правильно настроенные сетевые среды, в которых можно развернуть Управляемые экземпляры. |
| [Управляемый экземпляр с подключением "точка — сеть"](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Это развертывание создаст виртуальную сеть Azure с двумя подсетями: `ManagedInstance` и `GatewaySubnet`. Управляемый экземпляр будет развернут в подсети ManagedInstance. В подсети `GatewaySubnet` будет создан шлюз виртуальной сети, настроенный для VPN-подключения "точка — сеть". |
| [Управляемый экземпляр с виртуальной машиной](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Это развертывание создаст виртуальную сеть Azure с двумя подсетями: `ManagedInstance` и `Management`. Управляемый экземпляр будет развернут в подсети `ManagedInstance`. Виртуальная машина с последней версией SQL Server Management Studio (SSMS) будет развернута в подсети `Management`. |
