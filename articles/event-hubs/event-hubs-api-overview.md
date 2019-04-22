---
title: Обзор API Центров событий Azure | Документация Майкрософт
description: Обзор доступных интерфейсов API Центров событий Azure.
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
ms.openlocfilehash: 80566b0246179064d2a479b8c9bf3c79a2a93aac
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681290"
---
# <a name="available-event-hubs-apis"></a>Доступные интерфейсы API Центров событий

В этой статье описывается набор доступных клиентов API, которые можно использовать для управления ресурсами Центров событий.

## <a name="runtime-apis"></a>API среды выполнения

В этом разделе описываются все доступные сейчас клиенты в среде выполнения для Центров событий Azure. Хотя некоторые из этих библиотек включают в себя ограниченные функции управления, также существуют [специальные библиотеки](#management-apis), предназначенные для операций управления. Основной задачей этих библиотек является отправка сообщений в концентратор событий и получение их из него.

Чтобы узнать больше о текущем состоянии каждой библиотеки среды выполнения, см. раздел [Дополнительная информация](#additional-information).

| Язык или платформа | Пакет клиента | Пакет EventProcessorHost | Репозиторий |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Н/Д |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Узел | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Н/Д | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | Н/Д | Н/Д | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Дополнительная информация

#### <a name="net"></a>.NET

В экосистеме .NET существует несколько сред выполнения, поэтому для Центров событий используется несколько библиотек .NET. Библиотеку .NET Standard можно запустить с помощью .NET Core или .NET Framework, а библиотеку .NET Framework можно запустить только в среде .NET Framework. Дополнительные сведения о версиях платформы .NET Framework см. в статье [Требуемые версии .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Узел

Сейчас доступна предварительная версия [библиотеки Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs). Она поддерживается как параллельный проект сотрудниками корпорации Майкрософт и внешними соавторами. Все добавляемые материалы, включая исходный код, принимаются и проверяются.

## <a name="management-apis"></a>API управления

В приведенной ниже таблице перечислены все доступные библиотеки, предназначенные для операций управления. Ни одна из этих библиотек не содержит операций среды выполнения. Они предназначены только для управления сущностями Центров событий.

| Язык или платформа | Пакет управления | Репозиторий |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о Центрах событий см. в следующих источниках:

* [Общие сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)
