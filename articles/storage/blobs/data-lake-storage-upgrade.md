---
title: Обновление решений для аналитики больших данных с Azure Data Lake Storage 1-го поколения до Azure Data Lake Storage 2-го поколения
description: Обновление решения для использования Azure Data Lake Storage 2-го поколения
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 4c01cd36d489d8e7128bed645c8cb1127c6c0c25
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130408"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Обновление решений для аналитики больших данных с Azure Data Lake Storage 1-го поколения до Azure Data Lake Storage 2-го поколения

Если вы используете Azure Data Lake Storage 1-го поколения в решениях для аналитики больших данных, это руководство поможет вам обновить эти решения, чтобы использовать Azure Data Lake Storage 2-го поколения. С помощью этого документа можно оценить зависимости вашего решения от Azure Data Lake Storage 1-го поколения. Также в этом руководстве показано, как запланировать и выполнить обновление.

Мы поможем вам выполнить следующие задачи:

:heavy_check_mark: оценка готовности к обновлению;

:heavy_check_mark: планирование обновления;

:heavy_check_mark: выполнение обновления.

## <a name="assess-your-upgrade-readiness"></a>Оценка готовности к обновлению

Наша цель заключается в том, чтобы все возможности Data Lake Storage 1-го поколения стали доступны в Data Lake Storage 2-го поколения. Способ предоставления этих возможностей, например с помощью SDK, CLI и т. д., в 1-м и 2-м поколении Data Lake Storage может различаться. Приложениям и службам, которые работают с Data Lake Storage 1-го поколения, требуется возможность работать аналогичным образом с Data Lake Storage 2-го поколения. Наконец, некоторые возможности не будут доступны в Data Lake Storage 2-го поколения прямо сейчас. Мы будем объявлять об их добавлении в этом документе.

Следующие разделы помогут вам выбрать оптимальный способ обновления до Data Lake Storage 2-го поколения и наиболее подходящий момент для этого.

### <a name="data-lake-storage-gen1-solution-components"></a>Компоненты решения Data Lake Storage 1-го поколения

Скорее всего, при использовании Data Lake Storage 1-го поколения в конвейерах или решениях для аналитики, для достижения общей комплексной функциональности вы задействуете множество дополнительных технологий. В этой [статье](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) описаны различные компоненты потока данных, к которым относятся прием, обработка и использование данных.

Кроме того, существуют сквозные компоненты для подготовки, контроля и отслеживания названных выше компонентов. Каждый из компонентов работает с Data Lake Storage 1-го поколения через интерфейс, который лучше всего приспособлен для этого. Планируя обновление решения до Data Lake Storage 2-го поколения, необходимо помнить об интерфейсах, которые используются. Вам потребуется обновить как интерфейсы управления, так и интерфейсы данных, так как у каждого интерфейса отдельные требования.

![Компоненты решения Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

На **рис. 1** выше показаны функциональные компоненты, которые встречаются в большинстве решений для аналитики.

На **рис. 2** показан пример реализации этих компонентов с использованием определенных технологий.

Функциональные возможности хранения на **рис. 1** обеспечивает Data Lake Storage 1-го поколения (**рис. 2**). Обратите внимание на то, как различные компоненты потока данных взаимодействуют с Data Lake Storage 1-го поколения с помощью REST API или пакета SDK для Java. Также обратите внимание, как сквозные функциональные компоненты взаимодействуют с Data Lake Storage 1-го поколения. Компонент подготовки использует шаблоны ресурсов Azure, тогда как компонент мониторинга, использующий журналы Azure Monitor, использует операционные данные, поступающие от Data Lake Storage 1-го поколения.

Чтобы обновить решение с Data Lake Storage 1-го поколения до Data Lake Storage 2-го поколения, потребуется скопировать данные и метаданные, повторно подключить потоки данных, после чего все компоненты должны успешно работать с Data Lake Storage 2-го поколения.

В следующих разделах содержатся сведения, которые помогут вам принимать оптимальные решения:

:heavy_check_mark: "Возможности платформы"

:heavy_check_mark: "Программные интерфейсы"

:heavy_check_mark: "Экосистема Azure"

:heavy_check_mark: "Партнерская экосистема"

:heavy_check_mark: "Эксплуатационные данные"

В каждом из разделов вы сможете определить необходимые компоненты для обновления. Когда вы будете уверены, что все возможности доступны или существуют приемлемые обходные пути, перейдите к разделу [Планирование обновления](#planning-for-an-upgrade) этого руководства.

### <a name="platform-capabilities"></a>Возможности платформы

В этом разделе описано, какие возможности платформы Data Lake Storage 1-го поколения сейчас доступны в Data Lake Storage 2-го поколения.

| | Data Lake Storage 1-го поколения | Data Lake Storage 2-го поколения — цель | Data Lake Storage 2-го поколения — состояние доступности  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Упорядочение данных| Поддерживает данные, хранящиеся в виде папок и файлов | Поддерживает данные, хранящиеся в виде объектов и больших двоичных объектов, а также папок и файлов — [ссылка](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Поддерживает данные, хранящиеся в виде папок и файлов. *Доступно сейчас*. <br><br> Поддерживает данные, хранящиеся в виде объектов и больших двоичных объектов. *Пока недоступно*. |
| Пространство имен| Иерархическая сущность | Иерархическая сущность |  *Доступно сейчас*  |
| API  | REST API по протоколу HTTPS | REST API по протоколу HTTP/HTTPS| *Доступно сейчас* |
| API серверной части| [REST API, совместимый с WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | REST API службы BLOB-объектов Azure. [REST API Data Lake Storage 2-го поколения](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | REST API Data Lake Storage 2-го поколения. *Доступно сейчас*. <br><br> REST API службы BLOB-объектов Azure. *Пока недоступно*.       |
| Клиент файловой системы Hadoop | Да ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Да ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Доступно сейчас*  |  
| Операции с данными — авторизация  | Списки управления доступом (ACL) POSIX на уровне файлов и папок на основе удостоверений Azure Active Directory  | Списки ACL POSIX на уровне файлов и папок на основе удостоверений Azure Active Directory. [Общий ключ](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) для авторизации на уровне учетных записей. Управление доступом на основе ролей ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) для доступа к контейнерам | *Доступно сейчас* |
| Операции с данными — журналы  | Да | Одноразовые запросы на получение журналов с заданной длительностью с использованием запросов в службу поддержки. Интеграция со службой мониторинга Azure | Одноразовые запросы на получение журналов с заданной длительностью с помощью запросов в службу поддержки. *Доступно сейчас*.<br><br> Интеграция со службой мониторинга Azure. *Пока недоступно*. |
| Шифрование неактивных данных | Прозрачное, на стороне сервера с использованием управляемых службой ключей и управляемых пользователем ключей в хранилище ключей Azure | Прозрачное, на стороне сервера с использованием управляемых службой ключей и управляемых пользователем ключей в хранилище ключей Azure | Ключи, управляемые службой. *Доступно сейчас*.<br><br> Ключи, управляемые клиентом. *Доступно сейчас*.  |
| Операции управления (например, создание учетной записи) | [Контроль доступа на основе ролей](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC), предоставляемый Azure для управления учетными записями | [Контроль доступа на основе ролей](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC), предоставляемый Azure для управления учетными записями | *Доступно сейчас*|
| Пакеты SDK для разработчиков | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| *Пока недоступно* |
| |Оптимизированная производительность для параллельных рабочих нагрузок аналитики. Высокие показатели пропускной способности и операций ввода-вывода в секунду. | Оптимизированная производительность для параллельных рабочих нагрузок аналитики. Высокие показатели пропускной способности и операций ввода-вывода в секунду. | *Доступно сейчас* |
| Поддержка виртуальной сети  | [Интеграция с виртуальной сетью](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Использование конечной точки службы для службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Доступно сейчас* |
| Ограничения размера | Нет ограничений на размер учетных записей, размер и количество файлов | Нет ограничений на размер учетных записей и количество файлов. Максимальный размер файла ограничен 5 ТБ. | *Доступно сейчас*|
| Геоизбыточность| Локально избыточное хранилище (LRS) | Локально избыточная (LRS) зона избыточная (ZRS) геоизбыточное (GRS) геоизбыточное хранилище с доступом на чтение (RA-GRS) Дополнительные сведения см. [здесь](https://docs.microsoft.com/azure/storage/common/storage-redundancy) .| *Доступно сейчас* |
| Доступность в регионах | Дополнительные сведения см. [здесь](https://azure.microsoft.com/regions/). | Все [регионы Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Доступно сейчас*                                                                                                                           |
| Цена                                       | Дополнительные сведения см. на [этой странице](https://azure.microsoft.com/pricing/details/data-lake-store/).                                                                            | Дополнительные сведения см. на [этой странице](https://azure.microsoft.com/pricing/details/storage/data-lake/).                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Соглашение об уровне обслуживания относительно доступности                            | [См. соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [См. соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Доступно сейчас*                                                                                                                           |
| Управление данными                             | Срок действия файлов                                                                                                                                        | Политики жизненного цикла                                                                                                                                                                                                                                                                                                                                                                                                          | *Пока недоступно*                                                                                                                       |

### <a name="programming-interfaces"></a>Программные интерфейсы

В этой таблице описаны наборы API, доступные для пользовательских приложений. Чтобы сделать немного более понятным, мы разделили эти наборы API на 2 типа: API-интерфейсы управления и API-интерфейсы файловой системы.

Интерфейсы API управления помогают управлять учетными записями, а API-интерфейсы файловой системы помогают работать с файлами и папками.

|  Набор API                           |  Data Lake Storage 1-го поколения                                                                                                                                                                                                                                                                                                   | Доступность для Data Lake Storage 2-го поколения — с проверкой подлинности на основе общего ключа | Доступность для Data Lake Storage 2-го поколения — с проверкой подлинности на основе OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Пакет SDK для .NET — управление                  | [Ссылка](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Не поддерживаются*                                                      | *Доступно сейчас —* [ссылка](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| Пакет SDK для .NET — файловая система                  | [Ссылка](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Пока недоступно*                                                | *Пока недоступно*                                                                                                                                             |
| Пакет SDK для Java — управление                  | [Ссылка](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Не поддерживаются*                                                      | *Доступно сейчас —* [ссылка](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Пакет SDK для Java — файловая система                  | [Ссылка](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *Пока недоступно*                                                | *Пока недоступно*                                                                                                                                             |
| Node.js — управление                   | [Ссылка](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Не поддерживается                                                      | *Доступно сейчас —* [ссылка](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node. js — файловая система                   | [Ссылка](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Пока недоступно*                                                | *Пока недоступно*                                                                                                                                             |
| Python — управление                    | [Ссылка](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Не поддерживаются*                                                      | *Доступно сейчас —* [ссылка](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python — файловая система                    | [Ссылка](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Пока недоступно*                                                | *Пока недоступно*                                                                                                                                             |
| REST API — управление                  | [Ссылка](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Не поддерживаются*                                                      | *Доступно сейчас —*                                                                                                                                               |
| REST API файловая система                  | [Ссылка](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Доступно сейчас*                                                    | *Доступно сейчас —* [ссылка](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| Управление и файловая система PowerShell | [Ссылка](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Управление — неподдерживаемая файловая система, *пока недоступна*        | Управление. *Доступно сейчас*. [Ссылка](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Файловая система — *пока недоступна* |
| CLI — управление                       | [Ссылка](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Не поддерживаются*                                                      | *Доступно сейчас —* [ссылка](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI — файловая система                       | [Ссылка](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Пока недоступно*                                                | *Пока недоступно*                                                                                                                                             |
| Шаблоны Azure Resource Manager — управление             | [Шаблон1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Шаблон2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Шаблон3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Не поддерживаются*                                                      | *Доступно сейчас —* [ссылка](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Экосистема Azure

При использовании Data Lake Storage 1-го поколения можно задействовать широкий набор продуктов и служб корпорации Майкрософт в сквозных конвейерах. Эти продукты и службы работают с Data Lake Storage 1-го поколения прямо или опосредованно. В этой таблице показан список служб, которые мы изменили для работы с Data Lake Storage 1-го поколения, а также указано, какие из них сейчас совместимы с Data Lake Storage 2-го поколения.

| **Область**             | **Доступность для Data Lake Storage 1-го поколения**                                                                                                                                    | **Доступность для Data Lake Storage 2-го поколения — с проверкой подлинности на основе общего ключа**                                                                                                           | **Доступность для Data Lake Storage 2-го поколения — с проверкой подлинности на основе OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Платформа аналитики  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Доступно сейчас*                                                                                                                                                              | *Доступно сейчас*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 — *доступно сейчас*. HDInsight 4.0 — *пока недоступно*      | HDInsight 3.6 ESP. *Доступно сейчас*. <br><br>  HDInsight 4.0 ESP. *Пока недоступно*.                                                                 |
|                      | Databricks Runtime 3.1 и выше                                                                                                                                               | [Databricks Runtime 5.1 и более поздней версии](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) — *доступно сейчас* | [Databricks Runtime 5.1 и более поздней версии](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) — *доступно сейчас*                                                                                              |
|                      | [Хранилище данных SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Не поддерживаются*                                                                                                                                                              | *Доступно сейчас*: [ссылка](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Интеграция данных     | [Фабрика данных](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Версия 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) — *доступно сейчас*. Версия 1 — *не поддерживается*                               | [Версия 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). *Доступно сейчас*. <br><br> Версия 1. *Не поддерживается*.  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Не поддерживаются*                                                                                                                                                              | *Не поддерживаются*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
|                      | [Каталог данных](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
| Интернет вещей                  | [Центры событий — функция "Сбор"](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
| Потребление          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
|                      | [Службы Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
| Продуктивность         | [портал Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Не поддерживаются*                                                                                                                                                              | Управление учетными записями. *Доступно сейчас*. <br><br>Операции с данными *–* . *Пока недоступно*.                                                                   |
|                      | [средства Data Lake для Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install);                                   | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |
|                      | [Azure Storage Explorer;](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Доступно сейчас*                                                                                                                                                              | *Доступно сейчас*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Пока недоступно*                                                                                                                                                          | *Пока недоступно*                                                                                                                             |

### <a name="partner-ecosystem"></a>Партнерская экосистема

В этой таблице показан список сторонних служб и продуктов, которые были изменены для работы с Data Lake Storage 1-го поколения. Также в нем показано, какие из них сейчас совместимы с Data Lake Storage 2-го поколения.

| Область            | Партнер  | Продукт или служба  | Доступность для Data Lake Storage 1-го поколения                                                                                                                                               | Доступность для Data Lake Storage 2-го поколения — с проверкой подлинности на основе общего ключа                                                   | Доступность для Data Lake Storage 2-го поколения — с проверкой подлинности на основе OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Платформа аналитики | Cloudera     | CDH                  | [Ссылка](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Пока недоступно*                                                                                                  | [Ссылка](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Ссылка](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Не поддерживаются*                                                                                                                  | *Пока недоступно*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Ссылка](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Пока недоступно*                                                                                                  | *Пока недоступно*                                                                                                  |
|                     | Qubole       |                      | [Ссылка](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Пока недоступно*                                                                                                  | *Пока недоступно*                                                                                                  |
| Извлечение, преобразование и загрузка                 | Наборы потоков   |                      | [Ссылка](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Пока недоступно*                                                                                                  | *Пока недоступно*                                                                                                  |
|                     | Informatica  |                      | [Ссылка](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Пока недоступно*                                                                                                  | *Пока недоступно*                                                                                                  |
|                     | Attunity     |                      | [Ссылка](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Пока недоступно*                                                                                                  | *Пока недоступно*                                                                                                  |
|                     | Alteryx      |                      | [Ссылка](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Пока недоступно*                                                                                                  | *Пока недоступно*                                                                                                  |
|                     | ImanisData   |                      | [Ссылка](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Пока недоступно*                                                                                                  | *Пока недоступно*                                                                                                  |
|                     | WANdisco     |                      | [Ссылка](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Ссылка](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Ссылка](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Эксплуатационные данные

Data Lake Storage 1-го поколения передает определенную информацию и данные в другие службы, что помогает вводить в эксплуатацию конвейеры. В этой таблице показана доступность соответствующей поддержки в Data Lake Storage 2-го поколения.

| Тип данных                                                                                       | Доступность для Data Lake Storage 1-го поколения                                                                 | Доступность для Data Lake Storage 2-го поколения                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Данные о выставлении счетов — единицы измерения, которые отправляются в команду по вопросам коммерции для выставления счетов, а затем становятся доступными для клиентов  | *Доступно сейчас*                                                                                             | *Доступно сейчас*                                                                                                                           |
| Журналы действий                                                                                          | [Ссылка](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Одноразовые запросы на получение журналов с заданной длительностью с использованием запросов в службу поддержки — *доступно сейчас*. Интеграция со службой мониторинга Azure — *пока недоступно* |
| Журналы диагностики                                                                                        | [Ссылка](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Одноразовые запросы на получение журналов с заданной длительностью с использованием запросов в службу поддержки — *доступно сейчас*. Интеграция со службой мониторинга Azure — *пока недоступно* |
| metrics                                                                                                | *Не поддерживаются*                                                                                               | *Доступно сейчас —* [ссылка](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Планирование обновления

В этом разделе предполагается, что вы ознакомились с разделом [Оценка готовности к обновлению](#assess-your-upgrade-readiness) этого руководства и что все необходимые зависимости соблюдены. Если есть возможности, которые все еще недоступны в Data Lake Storage 2-го поколения, продолжайте только в том случае, если вы знаете соответствующие обходные пути. В следующих разделах содержатся рекомендации о том, как запланировать обновление конвейеров. Выполнение фактического обновления будет описано в разделе [Выполнение обновления](#performing-the-upgrade) этого руководства.

### <a name="upgrade-strategy"></a>Стратегия обновления

Важнейшим элементом обновления является выбор стратегии. Это решение определяет доступные для выбора варианты.

В следующей таблице приведен ряд хорошо известных стратегий, которые использовались для переноса баз данных, кластеров Hadoop и т. д. Мы внедрим аналогичные стратегии в нашем руководстве и адаптируем их к соответствующему контексту.

| Стратегия                   | Преимущества                                                                                  | Недостатки                                                           | Сценарии использования                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Перенос по методу lift-and-shift                 | Максимальна простота.                                                                                 | Приводит к простою при копировании данных, переносе заданий и перемещении входящего и исходящего трафика                                             | Для простых решений, когда доступ к одной и той же учетной записи 1-го поколения есть у небольшого количества решений, для которых можно одновременно выполнить быстрый контролируемый перенос.                                                  |
| Однократное и добавочное копирование | Сокращение времени простоя за счет копирования в фоновом режиме, пока исходная система остается активной. | Приводит к простою при перемещении входящего и исходящего трафика.                   | Копируется большой объем данных, и простои, с которыми сопряжено использование метода lift-and-shift, недопустимы. Может потребоваться тестирование со значительным объемом рабочих данных в целевой системе до выполнения переноса. |
| Параллельное внедрение              | Минимальное время простоя. Предоставляет время для переноса приложений по собственному усмотрению.           | Наиболее сложный, так как требуется двунаправленная синхронизация между двумя системами. | Для сложных сценариев, когда приложения, созданные на основе Data Lake Storage 1-го поколения, нельзя перенести все за один раз и их необходимо переносить в пошаговом режиме.                                                      |

Ниже приведены дополнительные сведения о порядке действий для каждой стратегии. Эти действия указывают, что следует делать с компонентами, задействованными в обновлении. К ним относятся вся исходная система, вся целевая система, источники входящего трафика для исходной системы, пункты назначения исходящего трафика для исходной системы и задания, выполняемые в исходной системе.

Эти действия не являются предписанием. Они приводятся для того, чтобы задать общий план для каждой стратегии. Мы предоставим примеры внедрения каждой из стратегий по мере их реализации.

#### <a name="lift-and-shift"></a>Перенос по методу lift-and-shift

1. Приостановите исходную систему — источники входящего трафика, задания, пункты назначения исходящего трафика.
2. Скопируйте все данные из исходной системы в целевую.
3. Укажите для всех источников входящего трафика целевую систему. Укажите пункт назначения исходящего трафика в целевой системе.
4. Переместите все задания в целевую систему, измените и запустите их в ней.
5. Отключите исходную систему.

#### <a name="copy-once-and-copy-incremental"></a>Однократное и добавочное копирование

1. Скопируйте данные из исходной системы в целевую.
2. Копируйте добавочные данные из исходной системы в целевую с регулярными интервалами.
3. Укажите пункт назначения исходящего трафика в целевой системе.
4. Переместите все задания в целевую систему, измените и запустите их в ней.
5. Пошагово укажите для источников входящего трафика целевую систему удобным для вас способом.
6. Когда все источники входящего трафика будут указывать на целевую систему, сделайте следующее:
    1. Отключите добавочное копирование.
    2. Отключите исходную систему.

#### <a name="parallel-adoption"></a>Параллельное внедрение

1. Настройте целевую систему.
2. Настройте двунаправленную репликацию между исходной и целевой системами.
3. Пошагово укажите для источников входящего трафика целевую систему.
4. Пошагово переместите все задания в целевую систему, измените и запустите их в ней.
5. Пошагово укажите пункты назначения исходящего трафика для целевой системы.
6. Когда все исходные источники входящего трафика, задания и пункты назначения исходящего трафика будут работать с целевой системой, отключите исходную систему.

### <a name="data-upgrade"></a>Обновление данных

Общая стратегия, применяемая для выполнения обновления (описанного в разделе [Стратегия обновления](#upgrade-strategy) этого руководства), определяет средства, с помощью которых можно выполнить обновление данных. Перечисленные ниже инструменты основаны на текущих сведениях и являются рекомендуемыми. 

#### <a name="tools-guidance"></a>Рекомендации по инструментам

| Стратегия                       | Сервис                                                                                                             | Преимущества                                                                                                                             | Рекомендации                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Перенос по методу lift-and-shift**                 | [Фабрика данных Azure](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Управляемая облачная служба                                                                                                                | Как данные, так и списки ACL можно копировать в настоящее время.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Хорошо известное средство от Hadoop. С его помощью можно копировать разрешения, то есть списки ACL                                                   | Требуется кластер с возможностью одновременного подключения к Data Lake Storage как 1-го, так и 2-го поколения.                                                                                                                                                                                   |
| **Однократное и добавочное копирование** | Фабрика данных Azure                                                                                                    | Управляемая облачная служба                                                                                                                | Как данные, так и списки ACL можно копировать в настоящее время. Для поддержки добавочного копирования в ADF данные должны быть упорядочены в виде временных рядов. Кратчайший интервал между добавочными копиями составляет [15 минут](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Параллельное внедрение**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Поддержка согласованной репликации. При использовании чистой среды Hadoop, подключенной к Azure Data Lake Storage, — поддержка двухсторонней репликации | Если чистая среда Hadoop не используется, при репликации может возникнуть задержка.                                                                                                                                                                                                                                                  |

Обратите внимание, что существуют сторонние поставщики, которые могут обрабатывать обновление с Data Lake Storage 1-го поколения до Data Lake Storage 2-го поколения, не применяя указанные выше средства копирования данных и метаданных (например, [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Они предоставляют единый интерфейс для переноса данных и рабочих нагрузок. Возможно вам потребуется выполнить отдельное обновление любых средств, находящихся за пределами их экосистемы.

#### <a name="considerations"></a>Рекомендации

* Прежде чем начинать любой этап обновления, необходимо вручную создать учетную запись Data Lake Storage 2-го поколения, так как текущие рекомендации не включают в себя автоматический процесс обработки учетной записи 2-го поколения, основанный на данных учетной записи 1-го поколения. Убедитесь, что сравниваются процессы создания учетных записей [1-го](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) и [2-го](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account) поколения.

* Data Lake Storage 2-го поколения поддерживает файлы размером до 5 ТБ. Для обновления до Data Lake Storage 2-го поколения может потребоваться изменить размер файлов в Data Lake Storage 1-го поколения, чтобы их размер не превышал 5 ТБ.

* Если вы используете средство, которое не копирует списки ACL, или не намерены их копировать, вам придется настроить списки ACL в системе назначения вручную на соответствующем верхнем уровне. Это можно сделать с помощью Обозревателя службы хранилища. Убедитесь, что эти списки ACL являются списками, установленными по умолчанию, чтобы копируемые файлы и папки наследовали их.

* В Data Lake Storage 1-го поколения наиболее высокий уровень, на котором можно задать списки ACL, — корень учетной записи. Однако в Data Lake Storage 2-го поколения самый высокий уровень ACL находится в корневой папке в контейнере, а не в целой учетной записи. Таким образом, если нужно задать списки ACL по умолчанию на уровне учетной записи, необходимо продублировать их по всем файловым системам в учетной записи Data Lake Storage 2-го поколения.

* Ограничения на именование файлов в двух системах хранения различаются. Эти различия особенно важны при копировании из Data Lake Storage 2-го поколения в Data Lake Storage 1-го поколения, так как в этом случае в целевой системе ограничения более жесткие.

### <a name="application-upgrade"></a>Обновление приложения

Если вам необходимо создавать приложения в Data Lake Storage 1-го поколения или Data Lake Storage 2-го поколения, следует сначала выбрать соответствующий программный интерфейс. При вызове API в этом интерфейсе необходимо будет указать соответствующий URI и учетные данные. Представление этих трех элементов — API, URI и способа предоставления учетных данных — различаются в Data Lake Storage 1-го поколения и Data Lake Storage 2-го поколения. Поэтому в процессе обновления приложений необходимо будет соответствующим образом сопоставить эти три компонента.

#### <a name="uri-changes"></a>Изменения URI

Основная задача заключается в преобразовании универсального кода ресурса (URI) с `adl://` префиксом в URI, `abfss://` имеющим префикс.

Схема URI для Data Lake Storage 1-го поколения подробно описана [здесь](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store), но в целом она имеет вид *adl://mydatalakestore.azuredatalakestore.net/\<путь_к_файлу\>.*

Схема универсального кода ресурса (URI) для доступа к Data Lake Storage 2-го поколенияным файлам подробно описана [здесь](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) , но `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`в общем смысле это.

Вам потребуется просмотреть существующие приложения и убедиться, что URI соответствующим образом изменены и ориентированы на Data Lake Storage 2-го поколения. Кроме того, потребуется добавить соответствующие учетные данные. Наконец, способ снятия с учета исходных приложений и замены их новыми должен быть согласован с общей стратегией обновления.

#### <a name="custom-applications"></a>Пользовательские приложения

В зависимости от интерфейса, используемого приложением с Data Lake Storage 1-го поколения, его потребуется изменить для адаптации к Data Lake Storage 2-го поколения.

##### <a name="rest-apis"></a>REST API

Если в приложении используются интерфейсы REST API Data Lake Storage, необходимо изменить приложение для использования REST API Data Lake Storage 2-го поколения. Ссылки содержатся в разделе *Программные интерфейсы*.

##### <a name="sdks"></a>Пакеты SDK

Как указано в разделе *Оценка готовности к обновлению*, пакеты SDK сейчас недоступны. Если нужно перенести приложения в Data Lake Storage 2-го поколения, рекомендуем дождаться выпуска поддерживаемых пакетов SDK.

##### <a name="powershell"></a>PowerShell

Как указано в разделе "Оценка готовности к обновлению", поддержка PowerShell сейчас недоступна для плоскости данных.

Командлеты плоскости управления можно заменить соответствующими командлетами в Data Lake Storage 2-го поколения. Ссылки содержатся в разделе *Программные интерфейсы*.

##### <a name="cli"></a>CLI

Как указано в разделе *Оценка готовности к обновлению*, поддержка интерфейса командной строки (CLI) сейчас недоступна для плоскости данных.

Команды плоскости управления можно заменить соответствующими командами в Data Lake Storage 2-го поколения. Ссылки содержатся в разделе *Программные интерфейсы*.

### <a name="analytics-frameworks-upgrade"></a>Обновление платформ аналитики

Если ваше приложение создает метаданные на основе сведений, содержащихся в хранилище, например явные пути к файлам и папкам, после обновления данных и метаданных хранилища вам потребуется выполнить дополнительные действия. Это особенно актуально для платформ аналитики, таких как Azure HDInsight, Databricks и т. д., которые обычно создают данные каталога на основе данных хранилища.

Платформы аналитики работают с данными и метаданными, содержащимися в удаленных хранилищах, таких как Data Lake Storage 1-го и 2-го поколения. Таким образом, теоретически подсистемы могут быть временными и вызываться только тогда, когда необходимо выполнить задания с хранимыми данными.

Однако в целях оптимизации производительности платформы могут создавать явные ссылки на файлы и папки, содержащиеся в удаленном хранилище, а затем создать кэш для их размещения. В случае изменения URI удаленных данных, например, если данные кластера хранились в Data Lake Storage 1-го поколения, а теперь их нужно хранить в Data Lake Storage 2-го поколения, URI для одного и того же скопированного содержимого будет отличаться. Таким образом, после обновления данных и метаданных также потребуется обновить или повторно инициализировать кэши для этих подсистем.

В рамках планирования необходимо будет идентифицировать приложение и выяснить, как сведения о метаданных можно повторно инициализировать, ориентировав их на данные, которые теперь хранятся в Data Lake Storage 2-го поколения. Ниже приведено руководство по распространенным платформам аналитики, которое поможет выполнить шаги по их обновлению.

#### <a name="azure-databricks"></a>Azure Databricks

В зависимости от выбранной стратегии обновления шаги будут отличаться. В текущем разделе предполагается, что вы выбрали стратегию lift-and-shift. Кроме того, от существующей рабочей области Databricks, которая раньше получала доступ к данным в учетной записи Data Lake Storage 1-го поколения, теперь ожидается работа с данными, скопированными в учетную запись Data Lake Storage 2-го поколения.

Во-первых, убедитесь, что вы создали учетную запись 2-го поколения, а затем скопировали данные и метаданные из системы 1-го поколения в систему 2-го поколения с помощью соответствующего инструмента. Эти инструменты указаны в разделе [Обновление данных](#data-upgrade) этого руководства.

Затем [обновите](https://docs.azuredatabricks.net/user-guide/clusters/index.html) существующий кластер Databricks, чтобы начать использовать среду выполнения Databricks 5.1 или более поздней версии, которая должна поддерживать Data Lake Storage 2-го поколения.

Последующие шаги зависят от того, как существующая рабочая область Databricks получает доступ к данным в учетной записи Data Lake Storage 1-го поколения. Это можно сделать либо путем вызова URI с префиксом adl:// [непосредственно](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) из записных книжек, либо через [точки подключения](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

При непосредственном доступе из записных книжек путем указания полных URI с префиксом adl:// потребуется просмотреть все записные книжки и изменить конфигурацию так, чтобы доступ осуществлялся к соответствующим URI Data Lake Storage 2-го поколения.

Забегая наперед, необходимо будет перенастроить их так, чтобы они указывали на учетную запись Data Lake Storage 2-го поколения. Дополнительные изменения не требуются, и записные книжки должны работать так же, как раньше.

При использовании любой другой стратегии обновления можно создать разновидность описанных выше действий в соответствии с вашими требованиями.

### <a name="azure-ecosystem-upgrade"></a>Обновление экосистемы Azure

Все инструменты и службы, указанные в разделе [Экосистема Azure](#azure-ecosystem) этого руководства, необходимо будет настроить для работы с Data Lake Storage 2-го поколения.

Во-первых, убедитесь в доступности интеграции с Data Lake Storage 2-го поколения.

Затем необходимо будет изменить указанные выше элементы (например, URI и учетные данные). Можно изменить существующий экземпляр, который работает с Data Lake Storage 1-го поколения, или создать новый экземпляр, который будет работать с Data Lake Storage 2-го поколения.

### <a name="partner-ecosystem-upgrade"></a>Обновление экосистем партнеров

Обратитесь к партнеру, предоставляющему тот или иной компонент или инструмент, чтобы убедиться в том, что они могут работать с Data Lake Storage 2-го поколения. 

## <a name="performing-the-upgrade"></a>Выполнение обновления

### <a name="pre-upgrade"></a>Перед обновлением

В рамках этого процесса вы изучили разделы *Оценка готовности к обновлению* и [Планирование обновления](#planning-for-an-upgrade) этого руководства, получили все необходимые сведения и создали план, соответствующий вашим потребностям. Вероятно, на этом этапе у вас будет предусмотрена задача тестирования.

### <a name="in-upgrade"></a>В ходе обновления

В зависимости от выбранной стратегии и сложности решения этот этап может быть коротким или расширенным при наличии множества рабочих нагрузок, ожидающих пошагового переноса в Data Lake Storage 2-го поколения. Это будет наиболее важной частью процесса обновления.

### <a name="post-upgrade"></a>После обновления

После завершения переноса заключительные действия включают в себя тщательную проверку. Это может включать и не ограничиваться проверкой правильности копирования данных, а проверка списков ACL была настроена верно. Проверка правильности правильной работы конвейеров и т. д. После выполнения проверок можно отключить старые конвейеры, удалить исходные учетные записи Data Lake Storage 1-го поколения и полностью перейти к работе с решениями на основе Data Lake Storage 2-го поколения.

## <a name="conclusion"></a>Заключение

Рекомендации, приведенные в этом документе, должны помочь вам обновить свое решение для использования Data Lake Storage 2-го поколения. 

Если у вас есть дополнительные вопросы или вы хотите оставить отзыв, напишите комментарий ниже или добавьте отзыв на [форуме отзывов и предложений Azure](https://feedback.azure.com/forums/327234-data-lake).
