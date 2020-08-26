---
title: Устранение неполадок в Azure Cosmos DB недоступности службы
description: Узнайте, как диагностировать и исправлять исключения, недоступные для службы Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870873"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Диагностика и устранение неполадок, связанных с Azure Cosmos DB недоступности службы
Пакету SDK не удалось подключиться к Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок
Следующий список содержит известные причины и решения для исключений, недоступных для службы.

### <a name="the-required-ports-are-being-blocked"></a>Требуемые порты блокируются
Убедитесь, что включены все [необходимые порты](performance-tips-dotnet-sdk-v3-sql.md#networking) .

### <a name="client-side-transient-connectivity-issues"></a>Временные проблемы подключения на стороне клиента
Исключения, недоступные для службы, могут быть вызваны временными проблемами подключения, вызывающими тайм-ауты. Как правило, трассировка стека, связанная с этим сценарием, будет содержать `TransportException` ошибку. Пример.

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Чтобы устранить [неполадки, выполните действия по устранению неполадок с запросом времени ожидания](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) .

### <a name="service-outage"></a>Простой службы
Проверьте [состояние Azure](https://status.azure.com/status) , чтобы узнать, существует ли текущая ошибка.


## <a name="next-steps"></a>Дальнейшие шаги
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET.
* Ознакомьтесь с рекомендациями по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md).