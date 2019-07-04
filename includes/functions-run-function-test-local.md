---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185061"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Следующая команда запускает приложение-функцию. Приложение выполняется с помощью той же среды выполнения функций Azure, что и в Azure.

```bash
func host start --build
```

Параметр `--build` необходим для компиляции проектов C#. Этот параметр не нужен для проектов JavaScript.

При запуске узел функций записывает примерно следующие выходные данные, усеченные для удобства чтения.

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Скопируйте URL-адрес функции `HttpTrigger` из выходных данных среды выполнения и вставьте его в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. Ниже показан ответ в браузере на запрос GET, возвращаемый локальной функцией.

![Локальное тестирование в браузере](./media/functions-run-function-test-local/functions-test-local-browser.png)

Теперь, когда вы выполнили функцию локально, можно создать приложение-функцию и другие необходимые ресурсы в Azure.