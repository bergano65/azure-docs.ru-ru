---
title: Подключение azure S'L Управляемый экземпляр для индексации поиска
titleSuffix: Azure Cognitive Search
description: Позволяет публичная конечная точка позволять соединениям с управляемыми экземплярами S'L от индекса на Azure Cognitive Search.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964895"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Наверскакните соединение с индексатором когнитивного поиска Azure в управляемый экземпляр S'L

Как отмечалось в [подключении базы данных Azure S'L к azure Cognitive Search с помощью индексеров,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)создание индексеров в отношении **управляемых экземпляров S'L** поддерживается Azure Cognitive Search через общедоступную конечную точку.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Создание управляемого экземпляра Azure S'L с общедоступной конечной точкой
Создайте управляемый экземпляр S'L с выбранным **общедоступным вариантом конечных точек.**

   ![Включение общедоступной конечной точки](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Включение общедоступной конечной точки")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Включить публичную конечную точку управления Azure S'L
Вы также можете включить публичную конечную точку на существующей управляемой инстанции S'L в соответствии с **системой Security** > **Virtual Network** > **Public endpoint** > **Enable.**

   ![Включение общедоступной конечной точки](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Включение общедоступной конечной точки")

## <a name="verify-nsg-rules"></a>Проверка правил NSG
Проверьте, что группа сетевой безопасности имеет правильные **правила безопасности входящих,** которые позволяют соединения из служб Azure.

   ![Правило безопасности NSG входящие](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Правило безопасности NSG входящие")

> [!NOTE]
> Индексы по-прежнему требуют, чтобы управляемые экземпляры S'L были настроены с общедоступной конечной точкой для чтения данных.
> Тем не менее, можно ограничить входящий доступ к этой публичной`public_endpoint_inbound`конечной точке, заменив текущее правило () следующими 2 правилами:
>
> * Разрешение входящего доступа `AzureCognitiveSearch` из [тега службы](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("ИСТОЧНИК" - `AzureCognitiveSearch`"NAME" - `cognitive_search_inbound`)
>
> * Разрешение входящего доступа с IP-адреса поисковой службы, которое можно получить путем `<your-search-service-name>.search.windows.net`пингирования его полностью квалифицированного доменного имени (например, ). ("Источник" `IP address`- "ИМЯ" `search_service_inbound`- "
>
> Для каждого из этих 2 правил, `3342`установить "PORT" - "PROTOCOL" - `TCP`"DESTINATION" - `Any`"ACTION"`Allow`

## <a name="get-public-endpoint-connection-string"></a>Получить общедоступную строку соединения конечных точек
Убедитесь, что вы используете строку соединения для **публичной конечной точки** (порт 3342, а не порт 1433).

   ![Строка общедоступной строки соединения конечных точек](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Строка общедоступной строки соединения конечных точек")

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда конфигурация не возможна, теперь можно указать управляемый экземпляр S'L в качестве источника данных для индекса когнитивного поиска Azure, используя либо портал, либо REST API. Для получения дополнительной [информации см.](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
