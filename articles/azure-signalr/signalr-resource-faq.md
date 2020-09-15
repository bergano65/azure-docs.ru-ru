---
title: Часто задаваемые вопросы о Службе Azure SignalR
description: Получите ответы на часто задаваемые вопросы о Службе Azure SignalR, включая сведения об устранении неполадок и типичных сценариях использования.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489567"
---
# <a name="azure-signalr-service-faq"></a>Вопросы и ответы о Службе SignalR Azure

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Готова ли Служба Azure SignalR для использования в рабочей среде?

Да.
Объявление об общедоступной версии см. в статье [Общедоступная версия Службы Azure SignalR](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

Обеспечивается полная поддержка [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction).

Поддержка ASP.NET SignalR все еще предоставляется в *общедоступной предварительной версии*. [Здесь приведен пример кода](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Клиентское подключение завершается сообщением об ошибке "Нет доступных серверов". Что это означает?

Эта ошибка возникает, только когда клиенты отправляют сообщения в Службу Azure SignalR.

Если у вас нет сервера приложений и вы используете только REST API Службы Azure SignalR, это *нормальное* поведение.
В бессерверной архитектуре клиентские подключения осуществляются в режиме *ожидания передачи данных* и не отправляют сообщения в Службу Azure SignalR.
[Ознакомьтесь с дополнительными сведениями о REST API](./signalr-quickstart-rest-api.md).

Если у вас есть серверы приложений, это сообщение об ошибке означает, что сервер не подключен к вашему экземпляру Службы Azure SignalR.

Возможные причины:
- Сервер приложений не подключен к Службе Azure SignalR. Проверьте журналы сервера приложений на наличие возможных ошибок подключения. Этот случай является редким при использовании конфигурации с несколькими серверами приложений с высокой доступностью.
- Существуют проблемы с подключением экземпляров Службы Azure SignalR. Эта проблема является временной и экземпляры будут автоматически восстановлены.
Если такое состояние продолжается более часа, [сообщите о проблеме на GitHub](https://github.com/Azure/azure-signalr/issues/new) или [создайте запрос в службу поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>При наличии нескольких серверов приложений клиентские сообщения отправляются на все серверы или только на один из них?

Между клиентом и сервером приложений происходит сопоставление "один к одному". Сообщения от одного клиента всегда отправляются на один и тот же сервер приложений.

Указанное сопоставление поддерживается, пока клиент или сервер не отключатся.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Если один из моих серверов приложений отключается, как узнать об этом и получить уведомление?

Служба Azure SignalR отслеживает пакеты пульса серверов приложений.
Если пакеты пульса не получены в течение заданного времени, сервер приложений считается отключенным. Все подключения клиентов, которые сопоставляются с этим сервером приложений, будут прерваны.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Почему мой пользовательский `IUserIdProvider` создает исключение, если я переключаюсь с пакета SDK ASP.NET Core Службы SignalR на пакет SDK Службы Azure SignalR?

Параметр `HubConnectionContext context` отличается для пакета SDK ASP.NET Core Службы SignalR и пакета SDK Службы Azure SignalR при вызове `IUserIdProvider`.

В ASP.NET Core `HubConnectionContext context` — это контекст физического клиентского подключения с допустимыми значениями для всех свойств.

В пакете SDK Службы Azure SignalR `HubConnectionContext context` — это контекст из логического подключения клиента. Физический клиент подключен к экземпляру Службы Azure SignalR, поэтому предоставляется только ограниченное число свойств.

На данный момент доступны только `HubConnectionContext.GetHttpContext()` и `HubConnectionContext.User`.
Вы можете [проверить исходный код](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Можно ли настроить транспортировки, доступные в Службе Azure SignalR, на стороне сервера с помощью ASP.NET Core SignalR? Например, можно ли отключить транспортировку WebSocket?

Нет.

Служба Azure SignalR предоставляет все три типа транспортировки, которые поддерживает ASP.NET Core SignalR по умолчанию. Этот параметр не настраивается. Служба Azure SignalR будет обрабатывать подключения и транспортировки для всех клиентских подключений.

Настройка транспортировок на стороне клиента описана в статье [Конфигурация SignalR для ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Что означают такие метрики, как количество сообщений или количество подключений, отображаемые на портале Azure? Какой тип агрегирования мне выбрать?

Сведения о вычислениях этих метрик можно найти в статье [Сообщения и подключения в Службе Azure SignalR](signalr-concept-messages-and-connections.md).

В области обзора ресурсов Службы Azure SignalR мы уже выбрали для вас подходящий тип агрегирования. Если вы перейдете к области "Метрики", вы можете взять тип агрегирования из статьи [Сообщения и подключения в Службе Azure SignalR](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) в качестве ориентира.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Что означают режимы службы `Default`, `Serverless` и `Classic`? Как его выбрать?

Дополнительные сведения о режимах:
* Для режима `Default` *требуется* сервер-концентратор. В этом режиме Служба Azure SignalR направляет клиентский трафик на подключения сервера-концентратора. Служба Azure SignalR проверяет наличие подключенного сервера-концентратора. Если служба не может найти подключенный сервер-концентратор, она отклоняет входящие клиентские подключения. Вы также можете использовать *API управления* в этом режиме для управления подключенными клиентами непосредственно через Службу Azure SignalR.
* В режиме `Serverless` *не* разрешаются какие-либо подключения к серверу. То есть он отклоняет все подключения к серверу. Все клиенты должны работать в бессерверном режиме. Клиенты подключаются к Службе Azure SignalR, а пользователи обычно используют бессерверные технологии, такие как *Функции Azure*, для обработки логики концентратора. [См. простой пример](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu), в котором используется бессерверный режим в Службе Azure SignalR.
* Режим `Classic` — смешанное состояние. Если в концентраторе имеется подключение к серверу, новый клиент будет перенаправлен на сервер-концентратор. В противном случае клиент перейдет в бессерверный режим. 

  Это может привести к проблеме. Например, если все подключения к серверу на мгновение будут потеряны, некоторые клиенты перейдут в бессерверный режим вместо маршрутизации к серверу-концентратору.

Вот некоторые рекомендации по выбору режима:
- При отсутствии сервера-концентратора выберите `Serverless`.
- Если у всех центров есть серверы-концентраторы, выберите `Default`.
- Если у некоторых центров есть серверы-концентраторы, а у других — нет, выберите `Classic`. Однако такое действие может привести к некоторым проблемам. Лучше всего создать два экземпляра: один — `Serverless`, а второй — `Default`.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Различаются ли функции при использовании Службы Azure SignalR с ASP.NET SignalR?
При использовании Службы Azure SignalR некоторые API-интерфейсы и функции ASP.NET SignalR не поддерживаются:
- Возможность передачи произвольного состояния между клиентами и концентратором (часто называемого `HubState`) не поддерживается.
- Класс `PersistentConnection` не поддерживается.
- *Транспортировка Forever Frame* не поддерживается.
- Служба Azure SignalR больше не воспроизводит сообщения, отправленные клиенту, когда клиент находится в автономном режиме.
- Если вы используете Службу Azure SignalR, трафик для одного клиентского подключения всегда маршрутизируется (т. е. *закрепляется*) к одному экземпляру сервера приложений на протяжении всего соединения.

Поддержка ASP.NET SignalR ориентирована на совместимость, поэтому поддерживаются не все новые функции ASP.NET Core SignalR. Например, *MessagePack* и *Streaming* доступны только для приложений ASP.NET Core SignalR.

Вы можете настроить разные режимы Службы Azure SignalR: `Classic`, `Default` и `Serverless`. Режим `Serverless` не поддерживается для ASP.NET. REST API плоскости данных также не поддерживается.

## <a name="where-does-my-data-reside"></a>Где находятся мои данные?

Служба Azure SignalR функционирует как служба обработки данных. В ней не будет храниться какое-либо содержимое клиента, а место расположения данных предусмотрено дизайном. При использовании Службы Azure SignalR вместе с другими службами Azure, например службой хранилища Azure для диагностики, ознакомьтесь с инструкциями в [этом техническом документе](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) о том, как сохранить место расположения данных в регионах Azure.
