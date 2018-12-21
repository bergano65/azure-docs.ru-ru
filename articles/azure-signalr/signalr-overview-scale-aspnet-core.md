---
title: Масштабирование ASP.NET Core SignalR с помощью Azure SignalR
description: Узнайте, как использовать службу Azure SignalR для масштабирования приложений ASP.NET Core SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: e739f3905ff2bbb60669b1f1e214a4630b896db4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255471"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Масштабирование приложений ASP.NET Core SignalR с помощью службы Azure SignalR.

## <a name="developing-signalr-apps"></a>Разработка приложений SignalR

Сейчас есть [две версии](https://docs.microsoft.com/aspnet/core/signalr/version-differences) SignalR, которые можно использовать с веб-приложениями: SignalR для ASP.NET и SignalR для ASP.NET Core (более поздняя версия). Служба Azure SignalR — это служба, управляемая Azure и созданная на основе Core SignalR ASP.NET.

ASP.NET Core SignalR — это переписанная предыдущая версия. В результате решение Core SignalR ASP.NET не является обратно совместимым с более ранней версией SignalR. Их API-интерфейсы и поведение отличаются. Пакет SDK для ASP.NET Core SignalR предназначен для .NET Standard, но вы можете использовать его с .NET Framework. Однако вы должны использовать новые API, а не старые. Если вы используете SignalR и хотите перейти на ASP.NET Core SignalR или службу Azure SignalR, вам нужно будет изменить код, чтобы обрабатывать различия в API-интерфейсах.

В службе Azure SignalR серверный компонент ASP.NET Core SignalR размещен в Azure. Однако, так как технология создана поверх ASP.NET Core, у вас есть возможность запускать свое фактическое веб-приложение на нескольких платформах (Windows, Linux и MacOS) при размещении с помощью [службы приложений Azure](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) и [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Вы также можете использовать самостоятельное размещение в собственном процессе.

Если задачей вашего приложения является поддержка новейших функций для обновления веб-клиентов с обновлением содержимого в режиме реального времени, работа на нескольких платформах (Azure, Windows, Linux и MacOS) и размещение в разных средах, использование службы Azure SignalR будет лучшим выбором.

## <a name="why-not-deploy-signalr-myself"></a>Могу ли я развернуть SignalR самостоятельно?

Вы можете развернуть собственное веб-приложение Azure, поддерживающее Core SignalR ASP.NET в качестве серверного компонента для общего веб-приложения.

Одной из основных причин использования службы Azure SignalR является ее простота. С помощью службы Azure SignalR вам не нужно учитывать производительность, масштабируемость, доступность. Все это учитывается в соглашении об уровне обслуживания с показателем доступности 99,9 %.

Кроме того, WebSocket — обычно предпочтительный метод поддержки обновлений содержимого в реальном времени. Однако балансировка нагрузки большого количества постоянных соединений WebSocket становится серьезной проблемой для решения по мере масштабирования. Использование общих решений: балансировка нагрузки DNS, аппаратные и программные подсистемы балансировки нагрузки. Служба Azure SignalR автоматически устраняет эти проблемы.

Другая причина может заключаться в том, что у вас нет требований к фактическому размещению веб-приложения. Логика вашего веб-приложения может использовать [независимые от сервера вычисления](https://azure.microsoft.com/overview/serverless-computing/). Например, возможно, ваш код размещается и выполняется только по запросу с помощью [триггеров функций Azure](https://docs.microsoft.com/azure/azure-functions/). Этот сценарий может быть сложным, потому что ваш код работает только по требованию и не поддерживает длительные соединения с клиентами. Служба Azure SignalR может справиться с этой ситуацией, так как она автоматически управляет подключениями. См. дополнительные сведения об [использовании службы SignalR с помощью Функций Azure](signalr-overview-azure-functions.md).

## <a name="how-does-it-scale"></a>Как выполняется масштабирование?

Как правило, масштабирование SignalR выполняется с помощью SQL Server, Служебной шины Azure или кэша Azure для Redis. Служба Azure SignalR автоматически выбирает метод масштабирования. Производительность и стоимость сопоставимы с этими подходами без сложной работы с этими службами. Вам нужно только обновить количество единиц для своей службы. Каждая единица поддерживает до 1000 клиентских подключений.

## <a name="next-steps"></a>Дополнительная информация

* [Краткое руководство по созданию чата с помощью службы SignalR](signalr-quickstart-dotnet-core.md)