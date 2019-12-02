---
title: Выполнение нескольких зависимых служб с использованием .NET Core и Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 9fb6993c913454d67e2118cd3ff5a4b8fea6464b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325698"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>Выполнение нескольких зависимых служб с использованием .NET Core и Visual Studio с Azure Dev Spaces

Из этого руководства вы узнаете, как разрабатывать приложения на базе нескольких служб с помощью Azure Dev Spaces. Также здесь описываются некоторые дополнительные преимущества использования Dev Spaces.

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

### <a name="well-done"></a>Все готово!
Теперь у вас есть многоконтейнерное приложение, где каждый контейнер можно разрабатывать и развертывать отдельно.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Коллективная разработка с помощью Dev Spaces](team-development-netcore-visualstudio.md)
