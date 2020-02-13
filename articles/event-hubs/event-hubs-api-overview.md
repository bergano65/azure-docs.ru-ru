---
title: Обзор API Центров событий Azure | Документация Майкрософт
description: В этой статье представлен обзор доступных интерфейсов API (среда выполнения и управление) для использования службы концентраторов событий Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162231"
---
# <a name="available-event-hubs-apis"></a>Доступные интерфейсы API Центров событий

В этой статье описывается набор доступных клиентов API, которые можно использовать для управления ресурсами Центров событий.

## <a name="runtime-apis"></a>API среды выполнения

В этом разделе описываются все доступные сейчас клиенты в среде выполнения для Центров событий Azure. Хотя некоторые из этих библиотек включают в себя ограниченные функции управления, также существуют [специальные библиотеки](#management-apis), предназначенные для операций управления. Основной задачей этих библиотек является отправка сообщений в концентратор событий и получение их из него.

Чтобы узнать больше о текущем состоянии каждой библиотеки среды выполнения, см. раздел [Дополнительная информация](#additional-information).

| Язык или платформа | Пакет клиента | Пакет EventProcessorHost | Хранилище |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Недоступно |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Узел | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Недоступно | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | Недоступно | Недоступно | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Дополнительные сведения

#### <a name="net"></a>.NET

В экосистеме .NET существует несколько сред выполнения, поэтому для Центров событий используется несколько библиотек .NET. Библиотеку .NET Standard можно запустить с помощью .NET Core или .NET Framework, а библиотеку .NET Framework можно запустить только в среде .NET Framework. Дополнительные сведения о версиях платформы .NET Framework см. в статье [Требуемые версии .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Узел

В настоящее время [Библиотека JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) находится на этапе предварительной версии и поддерживается как побочный проект сотрудниками Майкрософт и внешними участниками. Все добавляемые материалы, включая исходный код, принимаются и проверяются.

## <a name="management-apis"></a>API управления

В приведенной ниже таблице перечислены все доступные библиотеки, предназначенные для операций управления. Ни одна из этих библиотек не содержит операций среды выполнения. Они предназначены только для управления сущностями Центров событий.

| Язык или платформа | Пакет управления | Хранилище |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о Центрах событий см. в следующих источниках:

* [Общие сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)
