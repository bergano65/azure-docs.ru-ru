---
title: Постмиграционные шаги оптимизации с API API Azure Cosmos DB для MongoDB
description: Этот документ предоставляет методы после миграции оптимизации от MongoDB до APi Azure Cosmos DB для Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063613"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Послемиграционные шаги оптимизации при использовании API-избивательов Azure Cosmos DB для MongoDB

После переноса данных, хранящихся в базе данных MongoDB, в API Azure Cosmos DB для MongoDB можно подключиться к DB Azure Cosmos и управлять данными. В этом руководстве приводятся шаги, которые следует учитывать после миграции. Ознакомьтесь с [aPI-извне MongoDB для Azure Cosmos DB для руководства MongoDB](../dms/tutorial-mongodb-cosmos-db.md) для шагов миграции.

Из этого руководства вы узнаете, как выполнить следующие задачи:

- [Подключите приложение](#connect-your-application)
- [Оптимизация политики индексации](#optimize-the-indexing-policy)
- [Налажить глобальное распределение для API API Azure Cosmos DB для MongoDB](#globally-distribute-your-data)
- [Установите уровень согласованности](#set-consistency-level)

> [!NOTE]
> Единственным обязательным шагом после миграции на уровне приложения является изменение строки соединения в приложении, чтобы указать на новую учетную запись Azure Cosmos DB. Все остальные шаги миграции рекомендуется оптимизировать.
>

## <a name="connect-your-application"></a>Подключите приложение

1. В новом окне ввечна в [портал Azure](https://www.portal.azure.com/)
2. С [портала Azure](https://www.portal.azure.com/)в левом стеле откройте меню **Всех ресурсов** и найдите учетную запись Azure Cosmos DB, на которую вы перенесли свои данные.
3. Откройте лезвие **строки соединения.** На правой панели содержатся все сведения, необходимые для успешного подключения к учетной записи.
4. Используйте информацию о подключении в конфигурации приложения (или других соответствующих местах), чтобы отразить API API Azure Cosmos DB для соединения MongoDB в приложении.
![Соединение-строка](./media/mongodb-post-migration/connection-string.png)

Для получения более подробной информации, пожалуйста, [ознакомьтесь с приложением Connect a MongoDB на странице Azure Cosmos DB.](connect-mongodb-account.md)

## <a name="optimize-the-indexing-policy"></a>Оптимизация политики индексации

Все поля данных автоматически индексируются по умолчанию при переносе данных в Azure Cosmos DB. Во многих случаях эта политика индексации по умолчанию является приемлемой. Как правило, удаление индексов оптимизирует запросы на запись и наличие политики индексирования по умолчанию (т.е. автоматической индексации) оптимизирует запросы на чтение.

Более подробную информацию об индексации можно узнать [в API API Azure Cosmos DB для MongoDB,](mongodb-indexing.md) а также [в статьях Индексирования в статьях Azure Cosmos DB.](index-overview.md)

## <a name="globally-distribute-your-data"></a>Глобально распространять данные

Служба Azure Cosmos DB доступна во всех [регионах Azure](https://azure.microsoft.com/regions/#services) по всему миру. После выбора уровня согласованности по умолчанию для учетной записи Azure Cosmos DB можно связать один или несколько регионов Azure (в зависимости от глобальных потребностей в дистрибуции). Для обеспечения высокой доступности и непрерывности бизнеса мы всегда рекомендуем работать как минимум в 2 регионах. Вы можете ознакомиться с советами по оптимизации затрат на [развертывание мультирегионов в Azure Cosmos DB.](optimize-cost-regions.md)

Для глобального распространения данных, пожалуйста, смотрите [Распространение данных по всему миру на API API Azure Cosmos DB для MongoDB.](tutorial-global-distribution-mongodb.md)

## <a name="set-consistency-level"></a>Установите уровень согласованности

Azure Cosmos DB предлагает 5 четко [определенных уровней согласованности.](consistency-levels.md) Чтобы прочитать о картографии между уровнями согласованности MongoDB и Azure Cosmos DB, прочитайте [уровни согласованности и AA AIs Azure Cosmos DB.](consistency-levels-across-apis.md) Уровень согласованности по умолчанию — это уровень согласованности сеанса. Изменение уровня согласованности является необязательным, и вы можете оптимизировать его для вашего приложения. Чтобы изменить уровень согласованности с помощью портала Azure:

1. Перейдите к **лезвию последовательности по умолчанию** под настройками.
2. Выберите [уровень согласованности](consistency-levels.md)

Большинство пользователей оставляют уровень согласованности в настройках последовательности сеанса по умолчанию. Тем не менее, есть [доступность и производительность компромиссов для различных уровней согласованности.](consistency-levels-tradeoffs.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение приложения MongoDB к Azure Cosmos DB](connect-mongodb-account.md)
* [Подключение к учетной записи Azure Cosmos DB с помощью Studio 3T](mongodb-mongochef.md)
* [Глобальное распространение операций чтения с помощью API Azure Cosmos DB для MongoDB](mongodb-readpreference.md)
* [Завершение срока действия данных с помощью API Azure Cosmos DB для MongoDB](mongodb-time-to-live.md)
* [Настраиваемые уровни согласованности данных в Azure Cosmos DB](consistency-levels.md)
* [Индексирование в Azure Cosmos DB](index-overview.md)
* [Запросные подразделения в Azure Cosmos DB](request-units.md)