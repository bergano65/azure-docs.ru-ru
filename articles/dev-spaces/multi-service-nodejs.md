---
title: Выполнение нескольких зависимых служб с использованием Node.js и Visual Studio Code | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 8d305e051bd6708977926e3a4de47c15a784c7b6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818822"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Разработка с использованием нескольких служб с помощью Azure Dev Spaces

Из этого руководства вы узнаете, как разрабатывать приложения на базе нескольких служб с помощью Azure Dev Spaces. Также здесь описываются некоторые дополнительные преимущества использования Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Вызов службы, запущенной в отдельном контейнере

В этом разделе вы создадите вторую службу, `mywebapi`, к которой будет обращаться `webfrontend`. Каждая служба будет выполняться в отдельных контейнерах. Затем вы выполните отладку в обоих контейнерах.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Открытие примера кода для *mywebapi*.
У вас уже должен быть пример кода для `mywebapi` в рамках этого руководства. Он находится в папке `samples` (в противном случае перейдите по адресу https://github.com/Azure/dev-spaces и выберите **Clone or Download** (Клонировать или загрузить), чтобы загрузить репозиторий GitHub). Код для этого раздела находится в папке `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Запуск *mywebapi*
1. Откройте папку `mywebapi` в *отдельном окне VS Code*.
1. Откройте **палитру команд** (с помощью меню **Вид | Палитра команд**), включите автоматическое завершение ввода и выберите эту команду: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Не следует путать эту команду с командой `azds prep`, которая позволяет настроить проект для развертывания.
1. Нажмите клавишу F5 и подождите, пока выполнится сборка и развертывание службы. Когда все будет готово, откроется панель отладки VS Code.
1. Запишите URL-адрес конечной точки. Он будет иметь приблизительно такой вид: http://localhost:\<portnumber\>. **Совет. В строке состояния VS Code отобразится URL-адрес, щелкнув по которому, можно перейти на соответствующий ресурс**. Может показаться, что контейнер выполняется локально, но фактически он выполняется в вашей среде разработки в Azure. В адресе указано localhost, потому что для `mywebapi` не определены общедоступные конечные точки и доступ осуществляется только в пределах экземпляра Kubernetes. Для вашего удобства и упрощения взаимодействия с закрытой службой на локальном компьютере служба Azure Dev Spaces создает временный туннель SSH для контейнера, запущенного в Azure.
1. Когда `mywebapi` будет готово, откройте в браузере адрес localhost. Вы увидите ответ от службы `mywebapi` ("Hello from mywebapi").


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Запрос из *webfrontend* к *mywebapi*
Давайте теперь напишем код в `webfrontend`, который отправляет запрос к `mywebapi`.
1. Перейдите в окно VS Code для `webfrontend`.
1. Добавьте следующие строки кода в верхней части `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Замените* код для обработчика GET `/api`. При обработке запроса он, в свою очередь, обращается к `mywebapi`, а затем возвращает результаты из обеих служб.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
 4. *Удалите* строку `server.close()` в конце `server.js`

В предыдущем примере код перенаправляет заголовок `azds-route-as` из входящего запроса в исходящий. Позже вы увидите, как это помогает повысить производительность при коллективной разработке.

### <a name="debug-across-multiple-services"></a>Отладка в нескольких службах
1. На этом этапе служба `mywebapi` по-прежнему должна выполняться с подключенным отладчиком. Если это не так, нажмите клавишу F5 в проекте `mywebapi`.
1. Установите точку останова в обработчике GET `/` по умолчанию.
1. В проекте `webfrontend` установите точку останова до отправки запроса GET к `http://mywebapi`.
1. Нажмите клавишу F5 в проекте `webfrontend`.
1. Откройте веб-приложение и выполните пошаговое прохождение кода в обеих службах. В веб-приложении должно отобразиться объединенное сообщение от двух служб: "Hello from webfrontend and Hello from mywebapi".

### <a name="automatic-tracing-for-http-messages"></a>Автоматическая трассировка сообщений HTTP
Вы могли заметить, что, хотя *webfrontend* не содержит код для вывода HTTP-вызова, выполняемого к *mywebapi*, можно увидеть сообщения трассировки HTTP в окне вывода:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --hyh-> webfrontend:
   GET /api?_=1544485357627 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --1b1-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-1b1-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-hyh-- webfrontend:
   HTTP/1.1 200 OK
   Hello from webfrontend and Hello from mywebapi
```
Это одно из "бесплатных" преимуществ инструментирования с помощью Dev Spaces. Мы вставляем компоненты для отслеживания HTTP-запросов по мере их прохождения через систему, чтобы вам было легче отслеживать сложные вызовы к нескольким службам во время разработки.

### <a name="well-done"></a>Все готово!
Теперь у вас есть многоконтейнерное приложение, где каждый контейнер можно разрабатывать и развертывать отдельно.


## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Коллективная разработка с помощью Dev Spaces](team-development-nodejs.md)