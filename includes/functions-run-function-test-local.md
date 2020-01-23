---
title: включить файл
description: включить файл
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 343321b43d219401f0b494ab3f6a54074fbc36f3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279419"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Следующая команда запускает приложение-функцию. Приложение выполняется с помощью той же среды выполнения функций Azure, что и в Azure. Команда запуска зависит от языка проекта.

### <a name="c"></a>C\#

```command
func start --build
```

### <a name="javascript"></a>JavaScript

```command
func start
```

### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

При запуске узел функций записывает примерно следующие выходные данные, усеченные для удобства чтения:

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
