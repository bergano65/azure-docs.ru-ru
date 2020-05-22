---
title: Добавление API в Статические веб-приложения Azure с помощью Функций Azure
description: Руководство по началу работы со Статическими веб-приложениями Azure с добавлением бессерверного API в Статическое веб-приложение с помощью Функций Azure.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596153"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Добавление API в предварительную версию Статистического веб-приложения Azure с помощью Функций Azure

Вы можете добавить бессерверные API в Статические веб-приложения Azure с помощью интеграции с Функциями Azure. В этой статье показано, как добавить и развернуть API на сайте Статических веб-приложений Azure.

Сведения о защите маршрутов API см. в [руководстве по маршрутизации](routes.md).

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free) бесплатно.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Расширение "Функции Azure"](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) для Visual Studio Code.
- Расширение [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer).

## <a name="create-a-git-repository"></a>Создание репозитория Git 

В следующих шагах показано, как создать репозиторий и клонировать файлы на компьютер.

1. Перейдите к https://github.com/staticwebdev/vanilla-basic/generate, чтобы создать репозиторий.
1. В поле _Имя репозитория_ введите **my-vanilla-api**.
1. Щелкните **Create repository from template** (Создание репозитория из шаблона).

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Создание репозитория из vanilla-basic":::

После создания проекта можно использовать Visual Studio Code для клонирования репозитория Git.

1. Нажмите клавишу **F1**, чтобы открыть команду в палитре команд.
1. Вставьте URL-адрес в запрос _Git: Clone_ и нажмите клавишу **ВВОД**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Клонирование проекта GitHub с помощью Visual Studio Code":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Клонирование проекта GitHub с помощью Visual Studio Code":::


## <a name="create-your-local-project"></a>Создание локального проекта

В этом разделе вы используете Visual Studio Code. чтобы создать локальный проект Функций Azure. Позже вы опубликуете приложение-функцию в Azure.

1. В проекте _my-vanilla-api_ создайте вложенную папку с именем **api**.

   > [!NOTE]
   > Вы можете присвоить этой папке любое имя. В этом примере используется `api`. 

2. Нажмите клавишу **F1**, чтобы отобразить палитру команд.
3. Введите **Azure Functions: Create New Project…** (Функции Azure: Создать новый проект…).
4. Нажмите клавишу **ВВОД**.
5. Щелкните **Обзор**.
6. Выберите папку **api** в качестве каталога для рабочей области проекта.
7. Щелкните **Выбрать**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Создание Функций Azure с помощью Visual Studio Code":::

8. Введите следующие сведения по соответствующим запросам:

    - _Выберите язык для проекта приложения-функции_: выберите **JavaScript**.
    - _Выберите шаблон для первой функции вашего проекта_. Выберите **триггер HTTP**.
    - _Укажите имя функции_. Введите **GetMessage**.
    - _Уровень авторизации_: выберите **Анонимный**, что позволит любому пользователю вызывать конечную точку функции.
        - Дополнительные сведения об уровнях авторизации см. в разделе [Ключи авторизации](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

9. Используя эти сведения, Visual Studio Code создает проект функций Azure с триггером HTTP.
    - Файлы локального проекта можно просмотреть в окне проводника Visual Studio Code.
    - Дополнительные сведения см. в разделе [Generated project files](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files) (Созданные файлы проекта).

10. Теперь приложение должно иметь структуру проекта, аналогичную приведенному ниже примеру.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Затем обновите функцию `GetMessage` в разделе _api/GetMessage/index.js_ с помощью следующего кода.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Обновите конфигурацию `GetMessage` в разделе `api/GetMessage/function.json` с помощью следующих параметров.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
В указанных выше параметрах конечная точка API:

- запускается с помощью HTTP-запроса к функции;
- доступна для всех запросов независимо от состояния проверки подлинности;
- предоставляется через маршрут _/api/message_.

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Visual Studio Code интегрируется с [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local), чтобы перед публикацией в Azure можно было запустить этот проект на локальном компьютере разработки.

1. Выполните функцию, нажав клавишу **F5**, чтобы запустить приложение-функцию. Выходные данные Core Tools отобразятся на панели _Терминал_.

2. Если вы еще не установили Azure Functions Core Tools, выберите в запросе **Установить**.

    При установке Core Tools приложение запускается на панели _Терминал_. В качестве части выходных данных можно увидеть конечную точку URL-адреса функции, активированной с помощью HTTP, которая выполняется локально.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Создание Функций Azure с помощью Visual Studio Code":::

3. Запустив Core Tools, перейдите по указанному далее URL-адресу, чтобы выполнить запрос `GET`.

   <http://localhost:7071/api/message>

   Возвращается ответ, который в браузере выглядит следующим образом:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Создание Функций Azure с помощью Visual Studio Code":::

Убедившись, что функция выполняется правильно, можно вызвать API из приложения JavaScript.

### <a name="call-the-api-from-the-application"></a>Вызов API из приложения

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Обновление файлов для доступа к API

1. Теперь обновите содержимое файла _index.html_ следующим кодом, чтобы получить текст из функции API и отобразить его на экране:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   Запустив Core Tools, используйте расширение [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) Visual Studio Code, чтобы обслуживать файл _index.html_ и открыть его в браузере. 

2. Нажмите клавишу **F1** и выберите **Live Server: Open with Live Server** (Live Server: открыть с помощью Live Server).

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Создание Функций Azure с помощью Visual Studio Code":::

   > [!NOTE]
   > Для обслуживания файла `index.html` можно использовать другие серверы HTTP или прокси-серверы. Доступ к `index.html` из `file:///` не будет работать.

### <a name="commit-and-push-your-changes-to-github"></a>Фиксация и отправка изменений в GitHub

С помощью Visual Studio Code зафиксируйте и отправьте изменения в удаленный репозиторий Git.

1. Нажмите клавишу **F1**, чтобы отобразить палитру команд.
1. Введите **Git: Commit All** (Git: зафиксировать все).
1. Добавьте сообщение фиксации.
1. Введите **Git: push** (Git: отправка).

## <a name="create-a-static-web-app"></a>Создание Статического веб-приложения

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Создание статического приложения на портале Azure — экран 1":::

1. Перейдите на [портал Azure](https://portal.azure.com).
1. Щелкните **Создать ресурс**.
1. Выполните поиск по запросу **Статические веб-приложения**.
1. Выберите **Статические веб-приложения (предварительная версия)** .
1. Нажмите кнопку **Создать**.
1. Выберите _подписку Azure_.
1. Выберите или создайте _группу ресурсов_.
1. Присвойте приложению имя **my-vanilla-api**.
1. Выберите ближайший к вам _регион_.
1. Выберите **Бесплатный** в поле _Номер SKU_.
1. Нажмите кнопку **Войти по учетным данным GitHub** и выполните проверку подлинности с помощью GitHub.
1. Выберите предпочитаемую _Организацию_.
1. Выберите **my-vanilla-api** из раскрывающегося списка _Репозиторий_.
1. Выберите **master** в раскрывающемся списке _Ветвь_.
1. Нажмите кнопку **Next: Build >** (Далее: сборка >) для изменения конфигурации сборки.

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Создание статического приложения на портале Azure — экран 2":::

Теперь добавьте следующие сведения о сборке.

1. Введите **./** для _расположения приложения_.

1. Введите **api** в поле _Расположение API_.

   Это имя папки API, созданной на предыдущем шаге.
   
1. Удалите значение по умолчанию в _расположении артефакта приложения_, оставив поле пустым.

1. Щелкните **Review + create** (Просмотреть и создать).

| Параметр | Описание             | Обязательно |
| -------- | ----------------------- |
|  Расположение приложения | Расположение исходного кода статического приложения | Да |
|  Расположение API | Расположение серверной части API. Указывает на корневую папку проекта приложения Функций Azure. | нет |
|  Расположение артефакта приложения | Расположение выходной папки сборки. Некоторые интерфейсные платформы JavaScript имеют шаг сборки, в котором рабочие файлы помещены в папку. Этот параметр указывает на выходную папку сборки. | нет |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Создание статического приложения на портале Azure — экран 3":::

1. Нажмите кнопку **Создать**.
1. Дождитесь завершения развертывания (это может занять пару минут).
1. Перейдите на страницу `https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`.
1. Убедитесь, что сборка выполнена успешно.

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="Рабочий процесс GitHub":::

Развернутый API будет доступен по адресу `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>`.

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="Рабочий процесс GitHub":::

URL-адрес приложения можно также найти на портале Azure:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Доступ к URL-адресу статического приложения на портале Azure":::

Кроме того, вы можете напрямую получить доступ к Статическому веб-приложению Azure по адресу `https://<STATIC_APP_NAME>.azurestaticapps.net` и проверить результат в браузере.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не хотите, чтобы это приложение можно было использовать, выполните следующие действия, чтобы удалить Статическое веб-приложение Azure и связанные с ним ресурсы.

1. Перейдите на [портал Azure](https://portal.azure.com).
1. В верхней строке поиска введите **Группы ресурсов**.
1. Щелкните **Группы ресурсов**. 
1. Выберите **MyResourceGroup**.
1. На странице _myResourceGroup_ убедитесь, что перечислены те ресурсы, которые нужно удалить.
1. Выберите **Удалить**.
1. Введите **myResourceGroup** в текстовое поле.
1. Выберите команду **Удалить**.


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Настройка параметров приложения](./application-settings.md)
