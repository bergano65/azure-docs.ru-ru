---
title: включить файл
description: включить файл
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2020
ms.locfileid: "85115219"
---
Вы можете применить запросы в обозревателе данных для получения и фильтрации данных.

1. В верхней части вкладки **Элементы** в обозревателе данных просмотрите запрос по умолчанию `SELECT * FROM c`. Этот запрос позволяет извлечь и отобразить все документы из контейнера, упорядоченные по идентификаторам. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Стандартный запрос SELECT * FROM c в обозревателе данных":::
   
1. Чтобы изменить запрос, выберите **Изменить фильтр**, замените запрос по умолчанию на `ORDER BY c._ts DESC`, а затем выберите **Применить фильтр**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Изменение запроса по умолчанию с добавлением предиката ORDER BY c._ts DESC и применение фильтра":::

   Измененный запрос отображает все документы в порядке убывания метки времени, поэтому теперь первым в списке будет второй документ. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Изменение запроса на предикат ORDER BY c._ts DESC и применение фильтра":::

Если вы знакомы с синтаксисом SQL, используйте в этом поле предиката запроса любой поддерживаемый [SQL-запрос](../articles/cosmos-db/sql-api-sql-query.md). Вы также можете использовать обозреватель данных для создания хранимых процедур, определяемых пользователем функций и триггеров для бизнес-логики на стороне сервера. 

Это средство предоставляет легкий доступ на портале Azure ко всем встроенным возможностям программного доступа к данным, доступным в API-интерфейсах. Вы можете также воспользоваться порталом, чтобы масштабировать пропускную способность, получить ключи и строки подключения и просмотреть метрики и Соглашения об уровне обслуживания для своей учетной записи Azure Cosmos DB. 

