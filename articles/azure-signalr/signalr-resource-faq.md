---
title: Часто задаваемые вопросы о Службе Azure SignalR
description: Получите быстрый доступ к часто задаваемым вопросам о Службе Azure SignalR, об устранении неполадок и типичных сценариях использования.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c944ae3a5d647cc457edd20a5d3dd0489e19e286
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192292"
---
# <a name="azure-signalr-service-faq"></a>Вопросы и ответы о Службе SignalR Azure

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Готова ли Служба Azure SignalR для использования в рабочей среде?

Да.
Объявление об общедоступной версии см. в статье [Azure SignalR Service now generally available](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/) (Общедоступная версия Службы Azure SignalR). 

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
Если такое состояние продолжается более часа, [сообщите о проблеме на GitHub](https://github.com/Azure/azure-signalr/issues/new) или [создайте запрос в службу поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

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
Исходный код можно найти [здесь](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Можно ли настроить транспортировки, доступные в Службе SignalR, так же, как на стороне сервера с помощью ASP.NET Core SignalR? Например, отключить транспортировку WebSocket?

Нет.

Служба Azure SignalR предоставляет все три типа транспортировки, которые поддерживает ASP.NET Core SignalR по умолчанию. Этот параметр не настраивается. Служба SignalR будет обрабатывать подключения и транспортировки для всех клиентских подключений.

Настройка транспортировок на стороне клиента описана в [этой](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2) статье.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Что означают такие метрики, как количество сообщений или количество подключений, отображаемые на портале Azure? Какой тип агрегирования мне выбрать?

Подробные сведения о вычислении метрик см. на [этой странице](signalr-concept-messages-and-connections.md).

В колонке обзора ресурсов Службы Azure SignalR мы уже выбрали для вас подходящий тип агрегирования. Если вы перейдете к колонке "Метрики", вы можете взять тип агрегирования [здесь](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) в качестве ссылки.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Что означает режим службы `Default`/`Serverless`/`Classic`? Как его выбрать?

Режимы:
* Для режима `Default` **требуется** сервер-концентратор. Если подключение к серверу для концентратора будет недоступно, клиент попытается подключиться к этому концентратору, но этот процесс закончится ошибкой.
* В режиме `Serverless` **НЕ** разрешается любое подключение к серверу, т. е. он отклоняет все подключения к серверу, а все клиенты должны работать в бессерверном режиме.
* Режим `Classic` — смешанное состояние. Если в концентраторе имеется подключение к серверу, новый клиент будет перенаправлен на сервер-концентратор. В противном случае клиент перейдет в бессерверный режим.

  Это может привести к некоторым проблемам, например все подключения к серверу на мгновение будут потеряны, некоторые клиенты перейдут в бессерверный режим вместо маршрута к серверу-концентратору.

Выбор:
1. При отсутствии сервера-концентратора выберите `Serverless`.
1. Если у всех центров имеются серверы-концентраторы, выберите `Default`.
1. Если у некоторых центров имеются серверы-концентраторы, а у других — нет, выберите `Classic`, но такой выбор может привести к некоторым проблемам. Лучше создать два экземпляра: `Serverless` и `Default`.

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Различаются ли функции при использовании Azure SignalR для ASP.NET SignalR?
При использовании Azure SignalR некоторые API-интерфейсы и функции ASP.NET SignalR больше не поддерживаются:
- Возможность передачи произвольного состояния между клиентами и концентратором (часто называемого `HubState`) не поддерживается при использовании Azure SignalR.
- Класс `PersistentConnection` пока не поддерживается при использовании Azure SignalR.
- **Транспортировка Forever Frame** не поддерживается при использовании Azure SignalR.
- Azure SignalR больше не воспроизводит сообщения, отправленные клиенту, когда клиент находится в автономном режиме.
- При использовании Azure SignalR трафик для одного клиентского подключения всегда маршрутизируется (т.е. **закрепляется**) к одному экземпляру сервера приложений на протяжении подключения.

Поддержка ASP.NET SignalR ориентирована на совместимость, поэтому поддерживаются не все новые функции ASP.NET Core SignalR. Например, **MessagePack**, **Streaming** и т. д. доступны только для приложений ASP.NET Core SignalR.

Службу SignalR можно настроить для разных режимов службы: `Classic`/`Default`/`Serverles`. В этой поддержке ASP.NET режим `Serverless` не поддерживается. REST API плоскости данных также не поддерживается.

## <a name="where-do-my-data-reside"></a>Где находятся мои данные?

Служба Azure SignalR функционирует как служба обработки данных. В ней не будет храниться какое-либо содержимое клиента, а место расположения данных предусмотрено дизайном. При использовании Службы Azure SignalR вместе с другими службами Azure, например службой хранилища Azure для диагностики, ознакомьтесь с [инструкцией о сохранении места расположения данных в регионах Azure](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/).
