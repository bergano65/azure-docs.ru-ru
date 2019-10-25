---
title: Подключение Управляемый экземпляр Azure SQL для индексирования поиска
titleSuffix: Azure Cognitive Search
description: Включите общедоступную конечную точку, чтобы разрешить подключения к управляемым экземплярам SQL из индексатора в Azure Когнитивный поиск.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 16daf4a79252134703715ccd88f0b10dda7f4fa6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792155"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Настройка подключения из индексатора Когнитивный поиск Azure к SQL Управляемый экземпляр

Как указано в [подсоединении базы данных SQL Azure к azure когнитивный Поиск с помощью индексаторов](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), Azure когнитивный Поиск поддерживает создание индексаторов для **управляемых экземпляров SQL** .

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Создание Управляемый экземпляр Azure SQL с помощью общедоступной конечной точки
Создайте Управляемый экземпляр SQL с выбранным параметром **включить общедоступную конечную точку** .

   ![Включение общедоступной конечной точки](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Включить общедоступную конечную точку")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Включение общедоступной конечной точки Azure SQL Управляемый экземпляр
Можно также включить общедоступную конечную точку на существующем Управляемый экземпляр SQL в разделе **безопасность** > **виртуальная сеть** > **общедоступная конечная точка** > **включить**.

   ![Включение общедоступной конечной точки](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Включить общедоступную конечную точку")

## <a name="verify-nsg-rules"></a>Проверка правил NSG
Убедитесь, что группа безопасности сети имеет правильные **правила безопасности для входящего трафика** , которые разрешают подключения из служб Azure.

   ![Правило безопасности для входящего трафика NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Правило безопасности для входящего трафика NSG")

## <a name="get-public-endpoint-connection-string"></a>Получение строки подключения общедоступной конечной точки
Убедитесь, что используется строка подключения для **общедоступной конечной точки** (порт 3342, а не порт 1433).

   ![Строка подключения общедоступной конечной точки](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Строка подключения общедоступной конечной точки")

## <a name="next-steps"></a>Дальнейшие действия
После настройки вы можете указать Управляемый экземпляр SQL в качестве источника данных для индексатора Azure Когнитивный поиск с помощью портала или REST API. Дополнительные сведения см. [в статье подключение базы данных SQL Azure к azure когнитивный Поиск с помощью индексаторов](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
