---
title: Шаблоны Azure Resource Manager
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Использование шаблонов Azure Resource Manager для создания и настройки Базы данных SQL Azure и Управляемого экземпляра SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 0e3688fdbeabd525e54aad1b9e36c82b9c86751f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84023881"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Шаблоны Azure Resource Manager для Базы данных SQL Azure и Управляемого экземпляра SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Шаблоны Azure Resource Manager позволяют определить инфраструктуру как код и развертывать решения в облаке Azure для Базы данных SQL и Управляемого экземпляра SQL Azure.

## <a name="azure-sql-database"></a>[База данных SQL Azure](#tab/single-database)

В следующей таблице представлены ссылки на шаблоны Azure Resource Manager для Базы данных SQL Azure.

| |  |
|---|---|
| [База данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Этот шаблон Azure Resource Manager создает отдельную базу данных в службе "База данных SQL Azure" и настраивает правила брандмауэра для IP-адресов на уровне сервера. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Этот шаблон Azure Resource Manager создает логический сервер Базы данных SQL Azure. |
| [Эластичный пул](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Этот шаблон позволяет развернуть эластичный пул и назначить ему базы данных. |
| [Группы отработки отказа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Этот шаблон создает два сервера, отдельную базу данных и группу отработки отказа в Базе данных SQL Azure.|
| [Обнаружение угроз](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Этот шаблон позволяет развернуть сервер и набор баз данных с включенным обнаружением угроз и адресом электронной почты для оповещений для каждой базы данных. Обнаружение угроз является частью предложения Расширенной защиты от угроз (ATP) SQL и предоставляет слой безопасности, который отвечает на потенциальные угрозы через серверы и базы данных.|
| [Аудит в хранилище BLOB-объектов Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Этот шаблон позволяет развернуть сервер с включенным аудитом для записи журналов аудита в хранилище BLOB-объектов. Аудит базы данных SQL Azure отслеживает события базы данных и записывает их в журнал аудита, который можно разместить в учетной записи хранения Azure, рабочей области OMS или Центрах событий.|
| [Аудит в концентраторе событий Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Этот шаблон позволяет развернуть сервер с включенным аудитом для записи журналов аудита в имеющийся концентратор событий. Чтобы отправить события аудита в концентратор событий, задайте параметры аудита с помощью `Enabled` `State` и задайте `IsAzureMonitorTargetEnabled` значение `true`. Кроме того, настройте параметры диагностики, указав категорию журналов диагностики `SQLSecurityAuditEvents`, в базе данных `master` (для аудита на уровне обслуживания). Аудит отслеживает события базы данных и записывает их в журнал аудита, который можно разместить в учетной записи хранения Azure, рабочей области OMS или концентраторах событий.|
| [Веб-приложение Azure с Базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Этот пример создает бесплатное веб-приложение Azure и базу данных в службе "База данных SQL Azure" с уровнем обслуживания "Базовый".|
| [Веб-приложение Azure и Кэш Redis с Базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Этот шаблон создает веб-приложение, Кэш Redis и Базу данных SQL в той же группе ресурсов и создает две строки подключения в веб-приложении для Базы данных SQL и Кэша Redis.|
| [Импорт данных из хранилища BLOB-объектов с помощью ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Этот шаблон Azure Resource Manager создает Фабрику данных Azure версии 2, копирующую данные из хранилища BLOB-объектов Azure в Базу данных SQL.|
| [Кластер HDInsight с Базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Этот шаблон позволяет создать кластер HDInsight, логический сервер SQL, базу данных и две таблицы. Этот шаблон используется в статье [Использование Apache Sqoop с Hadoop в HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Приложение логики Azure, выполняющее хранимую процедуру SQL по расписанию](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Этот шаблон позволяет создать приложение логики, которое будет запускать хранимую процедуру SQL по расписанию. Все аргументы для процедуры можно поместить в раздел body шаблона.|

## <a name="azure-sql-managed-instance"></a>[Управляемый экземпляр SQL Azure](#tab/managed-instance)

В следующей таблице представлены ссылки на шаблоны Azure Resource Manager для Управляемого экземпляра SQL Azure.

| |  |
|---|---|
| [Управляемый экземпляр SQL в новой виртуальной сети](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Этот шаблон Azure Resource Manager создает настроенную виртуальную сеть Azure и Управляемый экземпляр SQL в виртуальной сети. |
| [Сетевая среда для Управляемого экземпляра SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Это развертывание создаст настроенную виртуальную сеть Azure с двумя подсетями: одна будет выделена для Управляемых экземпляров SQL, а другая — для других ресурсов (например, виртуальных машин, сред Службы приложений и т. д.). Этот шаблон будет создавать правильно настроенные сетевые среды, в которых можно развернуть Управляемые экземпляры SQL. |
| [Управляемый экземпляр SQL с подключением "точка — сеть"](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Это развертывание создаст виртуальную сеть Azure с двумя подсетями: `ManagedInstance` и `GatewaySubnet`. Управляемый экземпляр SQL будет развернут в подсети ManagedInstance. В подсети `GatewaySubnet` будет создан шлюз виртуальной сети, настроенный для VPN-подключения "точка — сеть". |
| [Управляемый экземпляр SQL с виртуальной машиной](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Это развертывание создаст виртуальную сеть Azure с двумя подсетями: `ManagedInstance` и `Management`. Управляемый экземпляр SQL будет развернут в подсети `ManagedInstance`. Виртуальная машина с последней версией SQL Server Management Studio (SSMS) будет развернута в подсети `Management`. |

---
