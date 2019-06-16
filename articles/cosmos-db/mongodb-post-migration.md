---
title: Действия после миграции оптимизации при использовании API Azure Cosmos DB для MongoDB
description: Этот документ содержит методы оптимизации после миграции из MongoDB в APi Azure Cosmos DB для Mongo DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61331220"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Действия после миграции оптимизации при использовании API Azure Cosmos DB для MongoDB 

Для управления данными, перенесенными из базы данных MongoDB в API Azure Cosmos DB для MongoDB, можно подключиться к Azure Cosmos DB. Это руководство содержит действия, которые следует учитывать после миграции. См. в разделе [перенос MongoDB для Azure Cosmos DB API для MongoDB руководства](../dms/tutorial-mongodb-cosmos-db.md) действия миграции.

Из этого руководства вы узнаете, как выполнить следующие задачи:
- [Подключить приложение](#connect-account)
- [Оптимизация политики индексирования](#indexing)
- [Настроили Глобальное распределение для API Azure Cosmos DB для MongoDB](#distribute-data)
- [Установили уровень согласованности](#consistency)

> [!NOTE]
> Единственная обязательная шаг после миграции на уровень приложения является изменение строки подключения в приложении, чтобы она указывала на учетную запись Azure Cosmos DB. Все остальные шаги миграции рекомендуется использовать оптимизацию.
>

## <a id="connect-account"></a>Подключить приложение 

1. В новый вход окно в [портала Azure](https://www.portal.azure.com/)
2. Из [портала Azure](https://www.portal.azure.com/), в левой области откройте **все ресурсы** меню и найдите учетную запись Azure Cosmos DB, к которому вы перенесли данные.
3. Откройте **строку подключения** колонке. На правой панели содержатся все сведения, необходимые для успешного подключения к учетной записи.
4. Используйте сведения о соединении в конфигурацию приложения (или других соответствующих местах) в соответствии с API Azure Cosmos DB для подключения MongoDB в вашем приложении. 
![Строки подключения](./media/mongodb-post-migration/connection-string.png)

Дополнительные сведения см. в разделе [подключение приложения MongoDB к Azure Cosmos DB](connect-mongodb-account.md) страницы.

## <a id="indexing"></a>Оптимизация политики индексирования

Все поля данных автоматически индексируются, по умолчанию во время переноса данных в Azure Cosmos DB. Во многих случаях это значение по умолчанию политика индексирования приемлемо. В общем случае удаление индексов позволяет оптимизировать запросы на запись и необходимости (т. е. Автоматическое индексирование) политика индексирования по умолчанию позволяет оптимизировать запросы на чтение.

Дополнительные сведения об индексировании см. в разделе [данных индексирования в API Azure Cosmos DB для MongoDB](mongodb-indexing.md) , а также [индексирование в Azure Cosmos DB](index-overview.md) статей.

## <a id="distribute-data"></a>Глобальное распределение данных

Azure Cosmos DB доступна во всех [регионов Azure](https://azure.microsoft.com/regions/#services) по всему миру. После выбора уровня согласованности по умолчанию для учетной записи Azure Cosmos DB, вы можете связать один или несколько регионов Azure (в зависимости от потребностей глобального распространения). Высокий уровень доступности и непрерывности бизнес-процессов мы рекомендуем всегда выполняется по крайней мере 2 регионов. Вы можете просмотреть советы по [оптимизации стоимости развертываний в Azure Cosmos DB в нескольких регионах](optimize-cost-regions.md).

Глобальное распределение данных, см. в разделе [распределение данных глобально на API Azure Cosmos DB для MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Установили уровень согласованности
Azure Cosmos DB предлагает четко определенную 5 [уровней согласованности](consistency-levels.md). Чтобы прочитать о сопоставление между MongoDB и Azure Cosmos DB уровней согласованности, прочитайте [уровни согласованности и интерфейсов API Azure Cosmos DB](consistency-levels-across-apis.md). Уровень согласованности по умолчанию является уровень согласованности сеанса. Изменение уровня согласованности является необязательным, и вы можете оптимизировать его для своего приложения. Чтобы изменить уровень согласованности, с помощью портала Azure:

1. Перейдите к **согласованность по умолчанию** колонке в разделе параметров.
2. Выберите ваш [уровень согласованности](consistency-levels.md)

Большинство пользователей оставить уровень согласованности по умолчанию согласованности сеанса. Тем не менее, есть [компромиссы доступности и производительности для различных уровней согласованности](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение приложения MongoDB к Azure Cosmos DB](connect-mongodb-account.md)
* [Подключиться к учетной записи Azure Cosmos DB с помощью Studio 3T](mongodb-mongochef.md)
* [Как глобально распространить считывает с помощью API Azure Cosmos DB для MongoDB](mongodb-readpreference.md)
* [Срок действия данных с помощью API Azure Cosmos DB для MongoDB](mongodb-time-to-live.md)
* [Настраиваемые уровни согласованности данных в Azure Cosmos DB](consistency-levels.md)
* [Индексирование в Azure Cosmos DB](index-overview.md)
* [Единицы запросов в Azure Cosmos DB](request-units.md)





