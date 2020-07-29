---
title: Устранение неполадок при Azure Cosmos DB недоступности службы
description: Как диагностировать и устранить исключение "недоступность службы Cosmos DB"
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294521"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Диагностика и устранение неполадок Azure Cosmos DB служба недоступна
Пакету SDK не удалось подключиться к службе Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок
Следующий список содержит известные причины и решения для исключений, недоступных для службы.

### <a name="1-the-required-ports-are-not-enabled"></a>1. требуемые порты не включены.
Убедитесь, что включены все [необходимые порты](performance-tips-dotnet-sdk-v3-sql.md#networking) .

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Если для существующего приложения или службы запущено получение 503

### <a name="1-there-is-an-outage"></a>1. происходит сбой
Проверьте [состояние Azure](https://status.azure.com/status) , чтобы узнать, существует ли текущая ошибка.

### <a name="2-snat-port-exhaustion"></a>2. нехватка портов SNAT
Следуйте указаниям [по исчерпанию портов SNAT](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. требуемые порты блокируются
Убедитесь, что включены все [необходимые порты](performance-tips-dotnet-sdk-v3-sql.md#networking) .

## <a name="next-steps"></a>Дальнейшие действия
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET
* Сведения о рекомендациях по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md)