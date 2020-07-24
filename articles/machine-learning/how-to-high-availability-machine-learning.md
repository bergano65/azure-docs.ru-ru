---
title: Как повысить устойчивость
titleSuffix: Azure Machine Learning
description: Узнайте, как повысить устойчивость Машинное обучение Azure связанных ресурсов к простоям с помощью конфигурации высокой доступности.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098904"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Повышение устойчивости Машинное обучение Azure

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как сделать ресурсы, связанные с Машинное обучение Azure, более устойчивыми с помощью конфигураций высокого уровня доступности. Службы Azure, которые Машинное обучение Azure зависит от, могут быть настроены для обеспечения высокой доступности. В этой статье содержатся сведения о том, какие службы можно настроить для обеспечения высокой доступности, а также приведены ссылки на сведения о настройке этих ресурсов.

> [!NOTE]
> Машинное обучение Azure сама по себе не предлагает вариант аварийного восстановления.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Общие сведения о службах Azure для Машинное обучение Azure

Машинное обучение Azure зависит от нескольких служб Azure и имеет несколько уровней. Некоторые из них подготавливаются в подписке (клиент). Вы несете ответственность за настройку высокого уровня доступности этих служб. Некоторые из них создаются в подписке Майкрософт и управляются корпорацией Майкрософт.

* **Инфраструктура машинное обучение Azure**: среда, управляемая Майкрософт, для машинное обучение Azure рабочей области.

* **Связанные ресурсы**: ресурсы, подготовленные в подписке во время создания рабочей области машинное обучение Azure. К ним относятся служба хранилища Azure, Azure Key Vault, реестр контейнеров Azure (запись контроля доступа) и App Insights. Вы несете ответственность за параметры высокого уровня доступности для этих ресурсов.
  * Хранилище по умолчанию содержит такие данные, как модель, данные журнала обучения и набор данных.
  * Key Vault имеет учетные данные для хранилища, записи контроля доступа, хранилища данных.
  * Запись контроля доступа содержит образ DOCKER для обучения и окружения.
  * App Insights предназначена для мониторинга Машинное обучение Azure.

* **Ресурсы вычислений**: ресурсы, создаваемые после развертывания рабочей области. Например, вы можете создать вычислительный экземпляр или вычислительный кластер для обучения модели машинного обучения.
  * Вычислительный экземпляр и вычислительный кластер: среда разработки моделей, управляемая корпорацией Майкрософт.
  * Другие ресурсы. это вычислительные ресурсы, которые можно подключить к Машинное обучение Azure, например Azure Kubernetes Service (AKS), Azure Databricks, службы "экземпляры контейнеров Azure" (ACI) и HDInsight. Вы несете ответственность за настройку высокого уровня доступности.

* **Дополнительные хранилища данных**. машинное обучение Azure можете подключать дополнительные хранилища данных, такие как служба хранилища azure, Azure Data Lake Storage и база данных SQL Azure для обучающих данных.  Они находятся в вашей подписке, и вы несете ответственность за настройку высокого уровня доступности.

В следующей таблице показано, какие службы управляются корпорацией Майкрософт, которыми вы управляете, и что по умолчанию имеет высокий уровень доступности:

| Служба | Управляется | HA по умолчанию |
| ----- | ----- | ----- |
| **Инфраструктура Машинное обучение Azure** | Майкрософт | |
| **Связанные ресурсы** |
| Служба хранилища Azure | Вы | |
| Azure Key Vault | Вы | ✓ |
| Реестр контейнеров Azure | Вы | |
| Application Insights | Вы | Н/Д |
| **Ресурсы для вычислений** |
| Вычислительный экземпляр | Майкрософт |  |
| Кластер вычислений | Майкрософт |  |
| Другие ресурсы, такие как служба Kubernetes Azure, <br>Azure Databricks, экземпляр контейнера Azure, Azure HDInsight | Вы |  |
| **Дополнительные хранилища данных** , такие как служба хранилища Azure, база данных SQL Azure,<br> База данных Azure для PostgreSQL, база данных Azure для MySQL, <br>Azure Databricks файловая система | Вы | |

Используйте сведения, приведенные в оставшейся части этого документа, чтобы узнать, какие действия необходимо предпринять для обеспечения высокой доступности каждой из этих служб.

## <a name="associated-resources"></a>Связанные ресурсы

> [!IMPORTANT]
> Машинное обучение Azure не поддерживает отработку отказа учетной записи хранения по умолчанию с помощью геоизбыточного хранилища (GRS) или геоизбыточного хранилища (ГЗРС) с доступом только для чтения (RA-GRS) или геоизбыточного хранилища с доступом на чтение (RA-ГЗРС).

Убедитесь, что для каждого ресурса задан параметр высокой доступности со следующей информацией.

* Служба **хранилища Azure**. Чтобы настроить высокий уровень доступности, см. статью [избыточность хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault**. он обеспечивает службу высокой доступности по умолчанию и не требует вмешательства пользователя.  См. раздел [доступность Azure Key Vault и избыточность](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Реестр контейнеров Azure**: выберите SKU Premium для георепликации. См. [раздел Георепликация в реестре контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: этот параметр не обеспечивает высокую доступность. Вы можете настроить срок хранения данных и сведения о [сборе, хранении и хранилище данных в Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Ресурсы для вычислений

Убедитесь, что для каждого ресурса задана Высокая доступность с помощью следующей документации.

* **Служба Kubernetes Azure**. Ознакомьтесь с [рекомендациями по обеспечению непрерывности бизнес-процессов и аварийному восстановлению в службе KUBERNETES Azure (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) и [Создайте кластер Azure Kubernetes Service (AKS), использующий зоны доступности](https://docs.microsoft.com/azure/aks/availability-zones). Если кластер AKS был создан Машинное обучение Azure (с помощью студии, пакета SDK или ML CLI), высокий уровень доступности в разных регионах не поддерживается.
* **Azure Databricks**: см. раздел [региональные аварийное восстановление для Azure Databricks кластеров](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Экземпляр контейнера Azure**: ACI Orchestrator отвечает за отработку отказа. См. раздел [экземпляры контейнеров Azure и оркестрации контейнеров](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure hdinsight**. см. статью [службы высокого уровня доступности, поддерживаемые Azure hdinsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Дополнительные хранилища данных

Убедитесь, что для каждого ресурса задана Высокая доступность с помощью следующей документации.

* **Контейнер больших двоичных объектов Azure/файловый ресурс Azure/Azure Data Lake Gen2**: то же, что и хранилище по умолчанию.
* **Azure Data Lake Gen1**: см. раздел[руководство по аварийному восстановлению данных в Azure Data Lake Storage 1-го поколения](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **База данных SQL Azure**: см. раздел [Высокая доступность и база данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **База данных Azure для PostgreSQL**: см. раздел [Основные понятия высокой доступности в базе данных Azure для PostgreSQL-Single Server](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **База данных Azure для MySQL**. см. статью [сведения о непрерывности бизнес-процессов в базе данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Файловая система кирпичей**: см. раздел [региональные аварийное восстановление для кластеров Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Если вы предоставляете собственный ключ (ключ, управляемый клиентом) для развертывания Машинное обучение Azure рабочей области, Cosmos DB также подготавливается в рамках вашей подписки. В этом случае вы несете ответственность за высокий уровень доступности. См. статью [высокий уровень доступности с Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы развернуть Машинное обучение Azure со связанными ресурсами с параметрами высокой доступности, используйте [шаблон Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).