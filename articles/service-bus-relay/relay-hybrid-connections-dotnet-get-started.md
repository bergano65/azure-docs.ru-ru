---
title: Начало работы с гибридными подключениями WebSocket к Azure Relay в .NET | Документация Майкрософт
description: 'Написание консольного приложения #C для гибридных подключений WebSocket к Azure Relay.'
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 88d540f274d8b07bfbdbb566c670adf6ddfefd74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749740"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Начало работы с гибридными подключениями Azure Relay с использованием WebSocket и .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

В этом кратком руководстве описано, как создать приложения .NET, которые отправляют и получают сообщения с использованием гибридных подключений по протоколу WebSocket в Azure Relay. См. дополнительные сведения о [службе Azure Relay](relay-what-is-it.md). 

При работе с этим кратким руководством вы выполните следующие задачи:

1. Создайте пространство имен ретранслятора с помощью портала Azure.
2. Создайте гибридное подключение в этом пространстве имен с помощью портала Azure.
3. Создайте серверное консольное приложение (прослушиватель) для получения сообщений.
4. Создайте клиентское консольное приложение (отправитель) для отправки сообщений.
5. Запустите приложения. 

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

* [Visual Studio 2015 или более поздней версии](https://www.visualstudio.com). В описанных в этом руководстве примерах используется Visual Studio 2017.
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.

## <a name="create-a-namespace"></a>Создание пространства имен
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Создание гибридного подключения
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Создание серверного приложения (прослушивателя)
Создайте в Visual Studio консольное приложение C# для прослушивания и получения сообщений, отправленных ретранслятором.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Создание клиентского приложения (отправителя)
Создайте в Visual Studio консольное приложение C# для отправки сообщений в ретранслятор.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Запуск приложений
1. Запустите серверное приложение.
2. Запустите клиентское приложение и введите любой текст.
3. Убедитесь, что в серверном консольном приложении отображается текст, введенный в клиентском приложении.

    ![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Поздравляем, вы создали приложение для гибридных подключений.

## <a name="next-steps"></a>Дальнейшие действия
В этом кратком руководстве описано, как создать клиентские и серверные приложения .NET, которые отправляют и получают сообщения по протоколу WebSocket. Функция гибридных подключений службы Azure Relay также поддерживает использование протокола HTTP для отправки и получения сообщений. См. дополнительные сведения об [использовании протокола HTTP с гибридными подключениями Azure Relay](relay-hybrid-connections-http-requests-dotnet-get-started.md).

В этом кратком руководстве описано, как использовать .NET Framework для создания клиентских и серверных приложений. Сведения о том, как создавать клиентские и серверные приложения с помощью Node.js, см. в руководствах по использованию [Node.js и WebSocket](relay-hybrid-connections-node-get-started.md) или [Node.js и HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

