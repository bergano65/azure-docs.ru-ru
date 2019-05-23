---
title: включение файла
description: включение файла
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66128261"
---
## <a name="run-the-web-application"></a>Запуск веб-приложения

1. На платформе GitHub размещен пример одностраничного веб-приложения. Откройте в браузере [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/).

    > [!NOTE]
    > Путь к исходному HTML-файлу: [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Когда появится запрос на ввод базового URL-адреса приложения-функции, введите `http://localhost:7071`.

1. Введите имя пользователя при появлении запроса.

1. Веб-приложение вызывает функцию *GetSignalRInfo* в приложении-функции для получения сведений о подключении, чтобы подключиться к службе Azure SignalR. После установки подключения появится поле ввода сообщений чата.

1. Введите сообщение и нажмите клавишу ВВОД. Приложение отправляет сообщение в функцию *SendMessage* приложения-функции Azure, которое использует привязку для вывода SignalR, чтобы транслировать сообщение всем подключенным клиентам. Если все работает правильно, сообщение должно появиться в приложении.

    ![Выполнение приложения](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Откройте другой экземпляр веб-приложения в новом окне браузера. Вы увидите, что все отправляемые сообщения отображаются во всех экземплярах приложения.

> [!IMPORTANT]
> Так как HTML-страница обслуживается по протоколу HTTPS, но в локальной среде выполнения Функций Azure по умолчанию используется протокол HTTP, браузер (например, Firefox) может принудительно применить политику смешанного содержимого, блокирующую запросы от веб-страницы к функциям. Чтобы решить эту проблему, используйте браузер без таких ограничений или запустите локальный сервер HTTP, например [http-server](https://www.npmjs.com/package/http-server) в каталоге */docs/demo/chat-v2*. Обязательно добавьте источник в параметр `CORS` в файле *local.settings.json*.