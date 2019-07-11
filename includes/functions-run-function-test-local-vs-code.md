---
title: включение файла
description: включение файла
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88a4fe8b9f0b477ed851a03742a9957c08b7cbf0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455201"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Основные инструменты службы "Функции Azure" позволяют запускать проекты функций Azure на локальном компьютере разработчика.

1. Чтобы протестировать созданную функцию, установите точку останова в коде функции и нажмите клавишу F5 для запуска проекта приложения-функции. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал).

1. На панели **Terminal** (Терминал) скопируйте URL-адрес конечной точки функции, активируемой HTTP-запросом. Этот URL-адрес содержит ключ функции, который передается в параметр запроса `code`.

    ![Локальные выходные данные в Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Вставьте URL-адрес запроса в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. Выполнение функции приостановится при достижении точки останова.

1. При продолжении выполнения функции в браузере отобразится следующий ответ на запрос GET:

    ![Ответ функции localhost в браузере](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Нажмите клавиши SHIFT+F5, чтобы остановить отладку.