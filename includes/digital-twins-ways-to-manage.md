---
author: baanders
description: файл включаемых файлов для Azure Digital двойников — способы управления экземпляром
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 887d185249f96b5d3be4aab6a96aa3c6c4a85690
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231418"
---
В этой статье объясняется, как выполнить различные операции управления с помощью [ **пакета SDK** Azure Digital двойников .NET (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). Вы также можете создавать те же вызовы управления с помощью пакетов SDK для других языков, описанных в разделе [*практические руководства. Использование API и пакетов SDK для цифровых двойников Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Помните, что все методы пакета SDK имеют синхронные и асинхронные версии. Для вызовов подкачки асинхронные методы возвращают, `AsyncPageable<T>` когда синхронные версии возвращают `Pageable<T>` .

Другой вариант управления заключается в прямом вызове API- [**интерфейсов**](/rest/api/azure-digitaltwins/) двойников для Azure Digital в этой области с помощью клиента RESTful, например POST. Инструкции о том, как это сделать, см. в разделе [*инструкции. Создание запросов с помощью POST*](../articles/digital-twins/how-to-use-postman.md).

Наконец, можно выполнить те же операции управления с помощью **интерфейса командной строки** Azure Digital двойников. Дополнительные сведения об использовании интерфейса командной строки см. в разделе [*практические рекомендации. Использование интерфейса командной строки Azure Digital двойников*](../articles/digital-twins/how-to-use-cli.md).