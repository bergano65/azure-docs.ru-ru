---
title: Выполнение нескольких зависимых служб с использованием .NET Core и Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 66a08ad674477da478ec7037833fe4cb836f9bb0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357036"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Разработка с использованием нескольких служб с помощью Azure Dev Spaces

В этом руководстве вы узнаете, как разрабатывать мультисервисные приложения в Azure Dev Spaces, а также о некоторых дополнительных преимуществах, предоставляемых Dev Spaces.

## <a name="call-another-container"></a>Вызов другого контейнера
В этом разделе вы создадите вторую службу, `mywebapi`, к которой будет обращаться `webfrontend`. Каждая служба будет выполняться в отдельных контейнерах. Затем вы выполните отладку в обоих контейнерах.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Скачайте пример кода для *mywebapi*.
Ради экономии времени давайте загрузим образец кода из репозитория GitHub. Перейдите к https://github.com/Azure/dev-spaces и выберите **Clone or download** (Клонировать или скачать), чтобы скачать репозиторий GitHub. Код для этого раздела находится в папке `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Запуск *mywebapi*
1. Откройте проект `mywebapi` в *отдельном окне Visual Studio*.
1. Выберите **Azure Dev Spaces** в раскрывающемся списке параметров запуска, как вы это делали ранее для проекта `webfrontend`. Вместо того, чтобы создавать новый кластер AKS, в этот раз выберите уже созданный. Как и ранее, для поля "Пространство" оставьте значение по умолчанию (`default`) и нажмите кнопку **ОК**. В окне выходных данных можно заметить, что Visual Studio начинает подготавливать эту новую службу в пространстве разработки, чтобы ускорить процессы при запуске отладки.
1. Нажмите клавишу F5 и подождите, пока выполнится сборка и развертывание службы. Вы узнаете, что служба готова, когда строка состояния Visual Studio станет оранжевой.
1. Обратите внимание на URL-адрес конечной точки, отображаемый на панели **Azure Dev Spaces for AKS** (Azure Dev Spaces для AKS) в окне **Выходные данные**. Он должен иметь примерно следующий вид: `http://localhost:<portnumber>`. Может показаться, что контейнер работает локально, но на самом деле он выполняется в пространстве разработки в Azure.
2. Когда контейнер `mywebapi` готов, откройте в браузере адрес localhost и добавьте `/api/values` к URL-адресу, чтобы вызвать API GET по умолчанию для `ValuesController`. 
3. Если все шаги были успешными, вы должны увидеть ответ от службы `mywebapi`, который выглядит так:

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Запрос из *webfrontend* к *mywebapi*
Давайте теперь напишем код в `webfrontend`, который отправляет запрос к `mywebapi`. Переключитесь в окно Visual Studio, в котором есть проект `webfrontend`. В файле `HomeController.cs` *замените* код метода About следующим кодом:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

В предыдущем примере код перенаправляет заголовок `azds-route-as` из входящего запроса в исходящий. Позже вы увидите, как это повышает эффективность разработки в [сценариях командной работы](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Отладка в нескольких службах
1. На этом этапе служба `mywebapi` по-прежнему должна выполняться с подключенным отладчиком. Если это не так, нажмите клавишу F5 в проекте `mywebapi`.
1. Установите точку останова в методе `Get(int id)` в файле `Controllers/ValuesController.cs`, который обрабатывает запросы GET `api/values/{id}`.
1. В проекте `webfrontend`, куда вы вставили код выше, установите точку останова до отправки запроса GET к `mywebapi/api/values`.
1. Нажмите клавишу F5 в проекте `webfrontend`. Visual Studio снова откроет в браузере соответствующий порт localhost, и появится веб-приложение.
1. Перейдите по ссылке **About** (Сведения) в верхней части страницы, чтобы вызвать точку останова в проекте `webfrontend`. 
1. Нажмите клавишу F10, чтобы продолжить. Точка останова запустится в проекте `mywebapi`.
1. Нажмите клавишу F5, чтобы продолжить и вернуться в код проекта `webfrontend`.
1. Повторное нажатие клавиши F5 завершит запрос и вернет страницу в браузере. В веб-приложении на странице About (Сведения) будет отображаться объединенное сообщение от двух служб: "Hello from webfrontend and Hello from mywebapi".

Все готово! Теперь у вас есть многоконтейнерное приложение, где каждый контейнер можно разрабатывать и развертывать отдельно.

### <a name="automatic-tracing-for-http-messages"></a>Автоматическая трассировка сообщений HTTP
Вы могли заметить, что, хотя *webfrontend* не содержит код для вывода HTTP-вызова, выполняемого к *mywebapi*, можно увидеть сообщения трассировки HTTP в окне вывода:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --gyk-> webfrontend:
   GET /Home/About HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend-668b7ddb9f-n5rhj --pu5-> mywebapi:
   GET /api/values/1 HTTP/1.1

// Response from *mywebapi*
webfrontend-668b7ddb9f-n5rhj <-pu5-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-gyk-- webfrontend:
   HTTP/1.1 200 OK
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-sc...<[TRUNCATED]>
```
Это одно из "бесплатных" преимуществ инструментирования с помощью Dev Spaces. Мы вставляем компоненты для отслеживания HTTP-запросов по мере их прохождения через систему, чтобы вам было легче отслеживать сложные вызовы к нескольким службам во время разработки.

### <a name="well-done"></a>Все готово!
Теперь у вас есть многоконтейнерное приложение, где каждый контейнер можно разрабатывать и развертывать отдельно.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Коллективная разработка с помощью Dev Spaces](team-development-netcore-visualstudio.md)
