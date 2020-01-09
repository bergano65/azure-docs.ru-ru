---
title: Распространенные исключения FabricClient
description: Описание распространенных исключений и ошибок, которые могут выдаваться интерфейсами API FabricClient при управлении приложениями и кластерами.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457935"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Распространенные исключения и ошибки при работе с интерфейсами API FabricClient
Интерфейсы API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) позволяют администраторам кластеров и приложений выполнять административные задачи в приложении, службе или кластере Service Fabric. К ним могут относиться развертывание приложений, обновление и удаление приложений, проверка работоспособности кластера или тестирование службы. Разработчики приложений и администраторы кластера могут использовать API FabricClient для разработки средств управления кластером и приложениями Service Fabric.

С помощью FabricClient можно выполнять операции различных типов.  Каждый метод может выдавать исключения из-за неправильных входных данных, ошибок времени выполнения или временных проблем инфраструктуры.  Чтобы узнать, какие исключения выдаются определенным методом, обратитесь к справочной документации по API. При этом некоторые исключения могут выдаваться различными интерфейсами API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). В следующей таблице перечислены исключения, которые являются общими для всех интерфейсов API FabricClient.

| Исключение | Вызывается, когда |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |Объект [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) находится в закрытом состоянии. Удалите используемый объект [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) и создайте экземпляр объекта [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Время ожидания операции истекло. [Оператионтимедаут](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) возвращается, если для завершения операции требуется больше MaxOperationTimeout. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Не удалось выполнить проверку доступа для операции. Возвращается E_ACCESSDENIED. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |При выполнении операции произошла ошибка времени выполнения. Любой из методов FabricClient может вызвать исключение [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception). Причина этого исключения определяется [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode). Коды ошибок определяются в перечислении [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode). |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Не удалось выполнить операцию из-за временной ошибки какого-либо вида. Например, операция может завершиться ошибкой, так как кворум реплик временно недоступен. Временные исключения соответствуют невыполненным операциям, которые могут быть выполнены повторно. |

Некоторые самые распространенные ошибки [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode), которые могут быть возвращены в [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), включают:

| Ошибка | Условие |
| --- |:--- |
| CommunicationError |Ошибка связи привела к сбою операции; повторите операцию. |
| InvalidCredentialType |Недопустимый тип учетных данных. |
| InvalidX509FindType |Недопустимый тип поиска X509. |
| InvalidX509StoreLocation |Недопустимое расположение хранилища X509. |
| InvalidX509StoreName |Недопустимое имя хранилища X509. |
| InvalidX509Thumbprint |Недопустимая строка отпечатка сертификата X509. |
| InvalidProtectionLevel |Недопустимый уровень защиты. |
| InvalidX509Store |Не удается открыть хранилище сертификатов X509. |
| InvalidSubjectName |Недопустимое имя субъекта. |
| InvalidAllowedCommonNameList |Недопустимый формат строки списка общих имен. Это должен быть список с разделителями-запятыми. |

