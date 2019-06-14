---
title: Начало работы с функциями WebSocket гибридных подключений к Azure Relay в Node | Документация Майкрософт
description: Написание консольного приложения Node.js для функций Websocket гибридных Azure Relay Azure.
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: b4864673e25ba4f5a1f2e8629e0889863051bc07
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60553921"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Начало работы с гибридными подключениями Azure Relay с использованием WebSocket и Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

В этом кратком руководстве описано, как создать приложения Node.js, которые отправляют и получают сообщения с использованием гибридных подключений по протоколу WebSocket в Azure Relay. См. дополнительные сведения о [службе Azure Relay](relay-what-is-it.md). 

При работе с этим кратким руководством вы выполните следующие задачи: 

1. Создайте пространство имен ретранслятора с помощью портала Azure.
2. Создайте гибридное подключение в этом пространстве имен с помощью портала Azure.
3. Создайте серверное консольное приложение (прослушиватель) для получения сообщений.
4. Создайте клиентское консольное приложение (отправитель) для отправки сообщений.
5. Запустите приложения. 

## <a name="prerequisites"></a>Технические условия

- [Node.js](https://nodejs.org/en/).
- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.

## <a name="create-a-namespace"></a>Создание пространства имен
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Создание гибридного подключения
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Создание серверного приложения (прослушивателя)
Для ожидания передачи данных и получения сообщений, отправленных ретранслятором, создайте консольное приложение Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Создание клиентского приложения (отправителя)
Для отправки сообщений в ретранслятор создайте консольное приложение Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Запуск приложений

1. Запустите серверное приложение. Для этого в командной строке Node.js введите `node listener.js`.
2. Запустите клиентское приложение. Для этого в командной строке Node.js введите `node sender.js`, а затем любой текст.
3. Убедитесь, что серверное консольное приложение выводит текст, введенный в клиентском приложении.

    ![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Поздравляем, вы создали приложение для гибридных подключений с помощью Node.js!

## <a name="next-steps"></a>Дальнейшие действия
В этом кратком руководстве описано, как создать клиентские и серверные приложения Node.js, которые отправляют и получают сообщения по протоколу WebSocket. Функция гибридных подключений службы Azure Relay также поддерживает использование протокола HTTP для отправки и получения сообщений. См. дополнительные сведения об [использовании протокола HTTP с Node.js и гибридными подключениями Azure Relay](relay-hybrid-connections-http-requests-node-get-started.md).

В этом кратком руководстве описано, как использовать Node.js для создания клиентских и серверных приложений. Сведения о том, как создавать клиентские и серверные приложения с помощью .NET Framework, см. в руководствах по использованию [.NET и WebSocket](relay-hybrid-connections-dotnet-get-started.md) или [.NET и HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).


