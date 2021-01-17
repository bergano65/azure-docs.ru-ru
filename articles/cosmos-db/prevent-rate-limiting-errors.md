---
title: Предотвращение ошибок, ограничивающих частоту, для Azure Cosmos DB API для операций MongoDB.
description: Узнайте, как запретить Azure Cosmos DB API для операций ограничения частоты обращения с помощью функции SSR (Повтор на стороне сервера).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540735"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Предотвращение ошибок, ограничивающих частоту, для Azure Cosmos DB API для операций MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB API для операций MongoDB может завершиться с ошибкой ограничения скорости (16500/429), если они превышают ограничение пропускной способности коллекции (RUs). 

Можно включить функцию повтора на стороне сервера (SSR) и позволить серверу автоматически повторять эти операции. Повторные запросы выполняются после небольшой задержки для всех коллекций в вашей учетной записи. Эта функция является удобной альтернативой для обработки ошибок ограничения частоты в клиентском приложении.


## <a name="use-the-azure-portal"></a>Использование портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Перейдите к Azure Cosmos DB API для учетной записи MongoDB.

1. Перейдите в область **компоненты** под разделом **Параметры** .

1. Выберите пункт **повторить попытку на стороне сервера**.

1. Нажмите кнопку **включить** , чтобы включить эту функцию для всех коллекций в вашей учетной записи.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Снимок экрана с функцией повтора на стороне сервера для Azure Cosmos DB API для MongoDB":::


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об устранении распространенных ошибок см. в этой статье:

* [Устранение распространенных неполадок в API Azure Cosmos DB для MongoDB](mongodb-troubleshoot.md)
