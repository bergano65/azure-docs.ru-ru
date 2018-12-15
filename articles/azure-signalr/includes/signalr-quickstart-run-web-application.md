---
title: включение файла
description: включение файла
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d40bfb5a7e691cead5a84be70398e9cbf6656a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262793"
---
## <a name="run-the-web-application"></a>Запуск веб-приложения

1. На платформе GitHub размещен пример одностраничного веб-приложения. Откройте в браузере [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/).

    > [!NOTE]
    > Путь к исходному HTML-файлу: [/docs/demo/chat/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat/index.html).

1. При запросе базового URL-адреса приложения-функции введите *http://localhost:7071*.

1. Введите имя пользователя при появлении запроса.

1. Веб-приложение вызывает функцию *GetSignalRInfo* в приложении-функции для получения сведений о подключении, чтобы подключиться к службе Azure SignalR. После установки подключения появится поле ввода сообщений чата.

1. Введите сообщение и нажмите клавишу ВВОД. Приложение отправляет сообщение в функцию *SendMessage* приложения-функции Azure, которое использует привязку для вывода SignalR, чтобы транслировать сообщение всем подключенным клиентам. Если все работает правильно, сообщение должно появиться в приложении.

    ![Выполнение приложения](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Откройте другой экземпляр веб-приложения в новом окне браузера. Вы увидите, что все отправляемые сообщения отображаются во всех экземплярах приложения.