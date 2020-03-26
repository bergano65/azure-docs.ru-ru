---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78190885"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Выполните функцию, запустив локальное хост-приложение среды выполнения Функций Azure из папки *LocalFunctionProj*:

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

Ближе к концу выходных данных появятся следующие строки: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Если HttpExample не похож на пример ниже, скорее всего, вы запустили хост-приложение из папки *HttpExample*. В этом случае остановите хост-приложение сочетанием клавиш **Ctrl**+**C**, перейдите в родительскую папку *LocalFunctionProj* и снова запустите указанную выше команду.

Скопируйте URL-адрес функции `HttpExample` из этих выходных данных в браузер и добавьте строку запроса `?name=<your-name>`, сформировав полный URL-адрес, например `http://localhost:7071/api/HttpExample?name=Functions`. В браузере должно отобразиться сообщение, например `Hello Functions`:

![Получившаяся функция выполняется локально в браузере](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Терминал, в котором вы выполнили `func start`, также выводит данные журнала при выполнении запросов.

Когда все будет готово, нажмите клавиши **Ctrl**+**C** и выберите `y`, чтобы отключить хост-приложение функции.