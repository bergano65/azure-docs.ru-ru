---
title: Часто задаваемые вопросы об Azure Synapse Link для Azure Cosmos DB
description: Получите ответы на часто задаваемые вопросы об Synapse Link для Azure Cosmos DB в таких областях, как выставление счетов, аналитическое хранилище, безопасность и срок жизни в аналитическом хранилище.
author: srchi
ms.author: srchi
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: bead905a3bd4b1cdd46c4cd27775f9d7e03040d5
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921199"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Часто задаваемые вопросы об Azure Synapse Link для Azure Cosmos DB

Решение Azure Synapse Link для Azure Cosmos DB обеспечивает тесную интеграцию между Azure Cosmos DB и Azure Synapse Analytics. Оно позволяет клиентам выполнять аналитику операционных данных почти в реальном времени с полной изоляцией выполнения от транзакционных рабочих нагрузок и без применения конвейера извлечения, преобразования и загрузки. В этой статье даны ответы на часто задаваемые вопросы об Azure Synapse Link для Azure Cosmos DB.

## <a name="general-faq"></a>Общие вопросы и ответы

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Для всех ли API-интерфейсов Azure Cosmos DB поддерживается Synapse Link?
В общедоступном предварительном выпуске Synapse Link поддерживается только для API Azure Cosmos DB SQL (Core). Поддержка API Cosmos DB для Mongo DB и API Cassandra в настоящее предоставляется в селективной предварительной версии.  Чтобы запросить доступ к селективной предварительной версии, обратитесь к [команде Azure Cosmos DB ](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>Поддерживается ли Synapse Link для учетных записей Azure Cosmos в нескольких регионах?
Да, для учетных записей Azure Cosmos в нескольких регионах данные, хранящиеся в аналитическом хранилище, также распределены глобально. Независимо от количества регионов записи (один или несколько источников) аналитические запросы, выполненные из Azure Synapse Analytics, могут обрабатываться из ближайших локальных регионов.

При планировании настройки учетной записи Azure Cosmos для нескольких регионов с поддержкой аналитических хранилищ рекомендуется добавить все необходимые регионы при создании учетной записи.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Можно ли включить Synapse Link только для определенного региона, а не всех регионов в учетной записи с несколькими регионами?
Если в предварительном выпуске для учетной записи с несколькими регионами включена поддержка Synapse Link, аналитическое хранилище создается во всех регионах. Базовые данные оптимизированы для обеспечения согласованности пропускной способности и транзакций в хранилище транзакций.

### <a name="is-backup-and-restore-supported-for-synapse-link-enabled-accounts"></a>Поддерживается ли резервное копирование и восстановление для учетных записей с поддержкой ссылок синапсе?
В предварительной версии для учетных записей базы данных с включенной связью синапсе резервное копирование и восстановление контейнеров не поддерживается. Если у вас есть производственные рабочие нагрузки, для которых требуются функции резервного копирования и восстановления, мы рекомендуем не включать ссылку синапсе для этих учетных записей базы данных. 

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>Можно ли отключить компонент Synapse Link для моей учетной записи Azure Cosmos?
В настоящее время после включения возможности Synapse Link на уровне учетной записи ее отключить нельзя.  Если вы хотите отключить эту возможность, необходимо удалить и повторно создать новую учетную запись Azure Cosmos.

Обратите внимание: если возможность Synapse Link включена на уровне учетной записи, однако контейнеры с поддержкой аналитического хранилища отсутствуют, ее включение никак **не** влияет на выставляемые счета.

## <a name="azure-cosmos-db-analytical-store"></a>Аналитическое хранилище Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Можно ли включить аналитическое хранилище для имеющихся контейнеров?
В настоящее время аналитическое хранилище можно включить только для новых контейнеров (как в новых, так и в имеющихся учетных записях).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>Можно ли отключить аналитическое хранилище для контейнеров Azure Cosmos после его включения при их создании?
В настоящее время аналитическое хранилище нельзя отключить для контейнера Azure Cosmos после его включения во время создания контейнера.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>Поддерживается ли аналитическое хранилище для контейнеров Azure Cosmos с подготовленной пропускной способностью с возможностью автомасштабирования?
Да, аналитическое хранилище можно включить для контейнеров с подготовленной пропускной способностью с возможностью автомасштабирования.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Влияет ли эта возможность на количество подготовленных единиц запросов для хранилища транзакций Azure Cosmos DB?
Azure Cosmos DB гарантирует изолированное выполнение транзакционных и аналитических рабочих нагрузок. Включение аналитического хранилища для контейнера не повлияет на показатель ЕЗ/с, подготовленный для хранилища транзакций Azure Cosmos DB. Плата за транзакции (чтение и запись) и плата за хранение для аналитического хранилища будет начисляться отдельно. Дополнительные сведения см. в [разделе цен статьи об аналитическом хранилище Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing).

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Отражаются ли в аналитическом хранилище операции удаления и обновления в хранилище транзакций? 
Да, операции удаления и обновления в хранилище транзакций будут отражены в аналитическом хранилище. Настроив для контейнера срок жизни, можно включить в него данные за предыдущие периоды, чтобы в аналитическом хранилище содержались все версии элементов, удовлетворяющие критериям аналитического срока жизни. Дополнительные сведения см. в [обзор аналитического срока жизни](analytical-store-introduction.md#analytical-ttl).

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Можно ли подключиться к аналитическому хранилищу из модулей аналитики, отличных от Azure Synapse Analytics?
Вы можете получать доступ к аналитическому хранилищу и отправлять в него запросы только с помощью различных сред выполнения, предоставляемых Azure Synapse Analytics. Отправлять запросы в аналитическое хранилище и анализировать его можно с помощью следующих средств:

* Synapse Spark с полной поддержкой Scala, Python, SparkSQL и C# (Synapse Spark — это основное средство для инжиниринга, обработки и анализа данных);
* бессерверная среда SQL с поддержкой языка T-SQL и знакомых средств бизнес-аналитики (например, Power BI Premium и т. п.).

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Можно ли подключиться к аналитическому хранилищу из подготовленной системы Synapse SQL?
В настоящее время доступ к аналитическому хранилищу нельзя получить из подготовленной системы Synapse SQL.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Можно ли записать результаты агрегирования запросов из Synapse в аналитическое хранилище?
Аналитическое хранилище — это доступное только для чтения хранилище в контейнере Azure Cosmos. Поэтому вы не можете напрямую записывать в него результаты агрегирования, но можете записать их в хранилище транзакций Azure Cosmos DB другого контейнера, который впоследствии можно использовать в качестве уровня обслуживания.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Как выполняется автосинхронизируемая репликация из хранилища транзакций в аналитическое хранилище (асинхронно или синхронно) и каковы при этом задержки? 
Репликация выполняется асинхронно, а ожидаемая задержка в настоящее время составляет около 2 минут.

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Существуют ли сценарии, в которых элементы из хранилища транзакций не распространяются автоматически в аналитическое хранилище?
Если определенные элементы в контейнере нарушают [четко определенную схему аналитики](analytical-store-introduction.md#analytical-schema), они не будут включаться в аналитическое хранилище. Если у вас есть сценарии, блокируемые четко определенной схемой для аналитики, обратитесь за справкой к [команде Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) по электронной почте.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Можно ли по-разному секционировать данные в аналитическом хранилище и хранилища транзакций?
Данные в аналитическом хранилище секционируются на основе горизонтального секционирования сегментов в хранилище транзакций. В настоящее время для аналитического хранилища нельзя выбрать другую стратегию секционирования.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Можно ли настроить или переопределить способ преобразования данных о транзакциях в формат столбцов в аналитическом хранилище?
В настоящее время элементы данных, которые автоматически распространяются из хранилища транзакций в аналитическое хранилище, преобразовать невозможно. При наличии сценариев, блокируемых этим ограничением, отправьте сообщение электронной почты [команде Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Аналитический срок жизни

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Поддерживается ли срок жизни для аналитических данных на уровне контейнеров и элементов одновременно?
В настоящее время срок жизни для аналитических данных можно настроить только на уровне контейнера, а задание аналитического срока жизни на уровне элемента не поддерживается.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Можно изменить значение аналитического срока жизни на уровне контейнера в контейнере Azure Cosmos DB после его настройки?
Да, аналитический срок жизни можно обновить любым допустимым значением. Дополнительные сведения см. в разделе об [аналитическом сроке жизни](analytical-store-introduction.md#analytical-ttl).

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Можно ли обновить или удалить элемент из аналитического хранилища после истечения их срока жизни в хранилище транзакций?
Все операции обновления и удаления транзакций копируются в аналитическое хранилище, но если элемент был удален из хранилища транзакций, то его нельзя обновить в аналитическом хранилище. Дополнительные сведения см. в разделе об [аналитическом сроке жизни](analytical-store-introduction.md#analytical-ttl).

## <a name="billing"></a>Выставление счетов

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Какова модель выставления счетов за Synapse Link для Azure Cosmos DB?
[Аналитическое хранилище Azure Cosmos DB](analytical-store-introduction.md) доступно в общедоступной предварительной версии без оплаты за аналитическое хранилище до 30 августа 2020 года. Счета для Synapse Spark и Synapse SQL выставляются за [потребление услуги Synapse](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Безопасность

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Какие способы проверки подлинности применяются в аналитическом хранилище?
Проверка подлинности в аналитическом хранилище такая же, как и в хранилище транзакций. Для данной базы данных можно выполнить проверку подлинности с помощью главного или доступного только для чтения ключа. Чтобы не вставлять ключи Azure Cosmos DB в записные книжки Spark, можно использовать связанную службу в Synapse Studio. Доступ к этой связанной службе предоставляется всем, у кого есть доступ к рабочей области.

## <a name="synapse-run-times"></a>Среды выполнения Synapse

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Использование каких сред выполнения Synapse для доступа к аналитическому хранилищу Azure Cosmos DB поддерживается в настоящее время?

|Среда выполнения Synapse |Текущая поддержка |
|---------|---------|
|Пулы Synapse Spark | Чтение, запись (с помощью хранилища транзакций), таблица, временное представление |
|Синапсе SQL Server    | Чтение, просмотр (селективная предварительная версия)  |
|Подготовленная среда Synapse SQL   |  Недоступно |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Выполняется ли синхронизация таблиц синапсе Spark с моими синапсе таблицами без SQL Server так же, как с Azure Data Lake?
Сейчас такая возможность недоступна.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Можно ли осуществлять структурированную потоковую передачу Spark из аналитического хранилища?
В настоящее время поддержка структурированной потоковой передачи Spark для Azure Cosmos DB реализуется с помощью функциональности канала изменений в хранилище транзакций, однако в аналитическом хранилище она еще не поддерживается.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Как в Synapse Studio узнать, подключен ли контейнер Azure Cosmos DB с поддержкой аналитического хранилища?
Контейнер Azure Cosmos DB с поддержкой аналитического хранилища обозначается следующим значком:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Значок контейнера Azure Cosmos DB с поддержкой аналитического хранилища":::

Контейнер хранилища транзакций будет представлен следующим значком:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Значок контейнера Azure Cosmos DB с поддержкой хранилища транзакций":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Как передаются учетные данные Azure Cosmos DB из Synapse Studio?
В настоящее время учетные данные Azure Cosmos DB передаются при создании связанной службы пользователем, имеющим доступ к базам данных Azure Cosmos DB. Доступ к этому хранилищу предоставляется другим пользователям, имеющим доступ к рабочей области.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с [преимуществами Synapse Link](synapse-link.md#synapse-link-benefits).

* Узнайте об [интеграции Synapse Link и Azure Cosmos DB](synapse-link.md#synapse-link-integration).
