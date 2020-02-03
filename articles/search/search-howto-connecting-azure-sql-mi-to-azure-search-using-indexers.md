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
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964895"
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

> [!NOTE]
> Индексаторы по-прежнему потребует настройки SQL Управляемый экземпляр с общедоступной конечной точкой для чтения данных.
> Однако можно ограничить входящий доступ к этой общедоступной конечной точке, заменив текущее правило (`public_endpoint_inbound`) на следующие 2 правила:
>
> * Разрешение входящего доступа из [тега службы](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) `AzureCognitiveSearch` ("Source" = `AzureCognitiveSearch`, "Name" = `cognitive_search_inbound`)
>
> * Разрешение входящего доступа с IP-адреса службы поиска, которую можно получить с помощью проверки связи с полным доменным именем (например, `<your-search-service-name>.search.windows.net`). ("SOURCE" = `IP address`, "NAME" = `search_service_inbound`)
>
> Для каждого из этих двух правил установите «PORT» = `3342`, «PROTOCOL» = `TCP`, «DESTINATION» = `Any`, «ACTION» = `Allow`

## <a name="get-public-endpoint-connection-string"></a>Получение строки подключения общедоступной конечной точки
Убедитесь, что используется строка подключения для **общедоступной конечной точки** (порт 3342, а не порт 1433).

   ![Строка подключения общедоступной конечной точки](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Строка подключения общедоступной конечной точки")

## <a name="next-steps"></a>Дальнейшие действия
После настройки вы можете указать Управляемый экземпляр SQL в качестве источника данных для индексатора Azure Когнитивный поиск с помощью портала или REST API. Дополнительные сведения см. [в статье подключение базы данных SQL Azure к azure когнитивный Поиск с помощью индексаторов](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
