---
title: Часто задаваемые вопросы о Службе Azure SignalR
description: Вопросы и ответы о Службе Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 12/23/2018
ms.author: zhshang
ms.openlocfilehash: 6b3ddf7d8069e689231b9dcb6f0f074e84052511
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663270"
---
# <a name="azure-signalr-service-faq"></a>Вопросы и ответы о Службе SignalR Azure

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Готова ли Служба Azure SignalR для использования в рабочей среде?

Да.
Объявление об общедоступной версии см. в статье [Azure SignalR Service now generally available](https://azure.microsoft.com/en-us/blog/azure-signalr-service-now-generally-available/) (Общедоступная версия Службы Azure SignalR). 

Обеспечивается полная поддержка [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction).

Поддержка ASP.NET SignalR все еще предоставляется в *общедоступной предварительной версии*. Здесь приведен [пример кода](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Клиентское подключение завершается сообщением об ошибке "Нет доступных серверов". Что это означает?

Эта ошибка возникает, только когда клиенты отправляют сообщения в Службу SignalR.

Если у вас нет сервера приложений и вы используете только REST API Службы SignalR, это **нормальное** поведение.
В бессерверной архитектуре клиентские подключения осуществляются в режиме **ожидания передачи данных** и не отправляют сообщения в Службу SignalR.
Узнайте больше о [REST API](./signalr-quickstart-rest-api.md).

Если у вас есть серверы приложений, это сообщение об ошибке означает, что сервер не подключен к вашему экземпляру Службы SignalR.

Возможные причины:
- Сервер приложений не подключен к Службе SignalR. Проверьте журналы сервера приложений на наличие возможных ошибок подключения. Этот случай является редким при использовании конфигурации с несколькими серверами приложений с высокой доступностью.
- Существуют проблемы с подключением экземпляров Службы SignalR. Эта проблема является временной и будет устранена автоматически.
Если такое состояние продолжается более часа, [сообщите о проблеме на GitHub](https://github.com/Azure/azure-signalr/issues/new) или [создайте запрос в службу поддержки Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>При наличии нескольких серверов приложений клиентские сообщения отправляются на все серверы или только на один из них?

Это сопоставление "один к одному" между клиентом и сервером приложений. Сообщения от одного клиента всегда отправляются на один и тот же сервер приложений.

Сопоставление между клиентом и сервером приложений будет поддерживаться, пока клиент или сервер приложений не отключится.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Если один из моих серверов приложений отключается, как узнать об этом и получить уведомление?

Служба SignalR отслеживает пакеты пульса серверов приложений.
Если пакеты пульса не получены в течение заданного времени, сервер приложений считается отключенным. Все подключения клиентов, которые сопоставляются с этим сервером приложений, будут прерваны.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Почему мой пользовательский `IUserIdProvider` создает исключение при переключении с пакета SDK ASP.NET Core Службы SignalR на пакет SDK Службы Azure SignalR?

Параметр `HubConnectionContext context` отличается для пакета SDK ASP.NET Core Службы SignalR и пакета SDK Службы Azure SignalR при вызове `IUserIdProvider`.

В ASP.NET Core `HubConnectionContext context` — это контекст физического клиентского подключения с допустимыми значениями для всех свойств.

В пакете SDK Службы Azure SignalR `HubConnectionContext context` — это контекст из логического подключения клиента. Физическое подключение выполняется к экземпляру Службы SignalR, поэтому предоставляется только ограниченное число свойств.

На данный момент доступны только `HubConnectionContext.GetHttpContext()` и `HubConnectionContext.User`.
Исходный код можно найти [здесь](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Можно ли настроить транспортировки, доступные в Службе SignalR, так же, как на стороне сервера с помощью ASP.NET Core SignalR? Например, отключить транспортировку WebSocket?

№

Служба Azure SignalR предоставляет все три типа транспортировки, которые поддерживает ASP.NET Core SignalR по умолчанию. Этот параметр не настраивается. Служба SignalR будет обрабатывать подключения и транспортировки для всех клиентских подключений.

Настройка транспортировок на стороне клиента описана в [этой](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports) статье.
