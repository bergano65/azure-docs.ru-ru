---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 6944d2e6a8f762e62c14f6f3fa3f600a9b3c333e
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195024"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Выполните функцию, запустив локальное хост-приложение среды выполнения Функций Azure из папки *LocalFunctionProj*:

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func host start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
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
> Если результат HttpExample не похож на пример ниже, скорее всего, вы запустили хост-приложение из папки, отличной от корневой папки проекта. В этом случае остановите хост-приложение клавишами **CTRL**+**C**, перейдите в корневую папку проекта и снова выполните указанную выше команду.

Скопируйте URL-адрес функции `HttpExample` из этих выходных данных в браузер и добавьте строку запроса `?name=<your-name>`, сформировав полный URL-адрес, например `http://localhost:7071/api/HttpExample?name=Functions`. В браузере должно отобразиться сообщение, например `Hello Functions`:

![Получившаяся функция выполняется локально в браузере](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Терминал, в котором вы запустили проект, также выводит данные журнала при выполнении запросов.

Когда все будет готово, нажмите клавиши **Ctrl**+**C** и выберите `y`, чтобы отключить хост-приложение функции.
