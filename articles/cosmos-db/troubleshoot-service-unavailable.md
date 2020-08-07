---
title: Устранение неполадок при Azure Cosmos DB недоступности службы
description: Как диагностировать и устранить исключение "недоступность службы Cosmos DB"
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987381"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Диагностика и устранение неполадок Azure Cosmos DB служба недоступна
Пакету SDK не удалось подключиться к службе Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок
Следующий список содержит известные причины и решения для исключений, недоступных для службы.

### <a name="1-the-required-ports-are-being-blocked"></a>1. требуемые порты блокируются
Убедитесь, что включены все [необходимые порты](performance-tips-dotnet-sdk-v3-sql.md#networking) .

### <a name="2-client-side-transient-connectivity-issues"></a>2. временные проблемы подключения на стороне клиента
При наличии временных проблем с подключением, которые вызывают тайм-ауты, может возникнуть недоступность службы. Обычно трассировка стека, относящаяся к этому сценарию, будет содержать `TransportException` , например:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Чтобы устранить ошибку [времени ожидания](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) , выполните запрос.

### <a name="3-service-outage"></a>3. сбой службы
Проверьте [состояние Azure](https://status.azure.com/status) , чтобы узнать, существует ли текущая ошибка.


## <a name="next-steps"></a>Дальнейшие действия
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET
* Сведения о рекомендациях по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md)