---
title: Подключение Управляемый экземпляр Azure SQL для индексирования поиска с поиском Azure
description: Включите общедоступную конечную точку, чтобы разрешить подключения к управляемым экземплярам SQL из индексатора в службе поиска Azure.
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229132"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Настройка подключения из индексатора поиска Azure к SQL Управляемый экземпляр
Как указано в [подсоединении базы данных SQL Azure к службе поиска Azure с помощью индексаторов, для](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)поиска Azure через общедоступную конечную точку службы поддерживают индексаторы для **управляемых экземпляров SQL** .

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Создание Управляемый экземпляр Azure SQL с помощью общедоступной конечной точки
Создайте Управляемый экземпляр SQL с выбранным параметром **включить общедоступную конечную точку** .

   ![Включить общедоступную конечную точку](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Включить общедоступную конечную точку")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Включение общедоступной конечной точки Azure SQL Управляемый экземпляр
Вы также можете включить общедоступную конечную точку на существующем управляемый экземпляр SQL в разделе**открытая конечная точка** >  **безопасности** > **виртуальной сети** >  **.**

   ![Включить общедоступную конечную точку](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Включить общедоступную конечную точку")

## <a name="verify-nsg-rules"></a>Проверка правил NSG
Убедитесь, что группа безопасности сети имеет правильные **правила безопасности для входящего трафика** , которые разрешают подключения из служб Azure.

   ![Правило безопасности для входящего трафика NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Правило безопасности для входящего трафика NSG")

## <a name="get-public-endpoint-connection-string"></a>Получение строки подключения общедоступной конечной точки
Убедитесь, что используется строка подключения для общедоступной **конечной точки** (порт 3342, а не порт 1433).

   ![Строка подключения общедоступной конечной точки](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Строка подключения общедоступной конечной точки")

## <a name="next-steps"></a>Следующие шаги
После настройки можно указать Управляемый экземпляр SQL в качестве источника данных для индексатора службы поиска Azure с помощью портала или REST API. Дополнительные сведения см. в статье [Подключение базы данных SQL Azure к Поиску Azure с помощью индексаторов](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).
