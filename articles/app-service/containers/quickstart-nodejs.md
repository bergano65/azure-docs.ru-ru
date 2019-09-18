---
title: Создание веб-приложений Node.js в Службе приложений Azure
description: Развертывание приложений Node.js в Службе приложений Azure
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: 8d679a95cc89c2ae7774b7f7b51b9d0aadd89d12
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390979"
---
# <a name="create-a-nodejs-app-in-azure"></a>Создание приложения Node.js в Azure

Служба приложений Azure — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом кратком руководстве объясняется, как развертывать приложения Node.js в Службе приложений Azure.

## <a name="prerequisites"></a>Предварительные требования

Если у вас нет учетной записи Azure [, зарегистрируйтесь сегодня](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) и получите бесплатную учетную запись с кредитами Azure на сумму $200, которые позволят проверить любое сочетание служб.

Вам нужно установить [Visual Studio Code](https://code.visualstudio.com/), а также [Node.js и npm](https://nodejs.org/en/download), диспетчер пакетов для Node.js.

Кроме того, потребуется [расширение Службы приложений Azure](vscode:extension/ms-azuretools.vscode-azureappservice), которое позволяет создавать, администрировать и развертывать веб-приложения Linux в Azure в режиме PaaS (платформа как услуга).

### <a name="sign-in"></a>Вход

После установки расширения войдите в учетную запись Azure. На панели действий щелкните эмблему Azure, чтобы отобразить обозреватель **Службы приложений Azure**. Щелкните ссылку **Вход в Azure...** и следуйте инструкциям.

![Вход в Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Устранение неполадок

Если отображается сообщение об ошибке **"Не удается найти подписку с именем [идентификатор подписки]"** , возможно вы находитесь за прокси-сервером и не можете связаться с API Azure. Укажите в переменных среды `HTTP_PROXY` и `HTTPS_PROXY` параметры прокси-сервера, используя команду терминала `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Если настройка переменных среды не устранит проблему, свяжитесь с нами, нажав кнопку **У меня есть проблема** ниже.

### <a name="prerequisite-check"></a>Проверка предварительных условий

Прежде чем продолжить, проверьте наличие и правильность настройки всех обязательных компонентов.

В VS Code должна отображаться следующая информация: адрес электронной почты Azure в строке состояния и подписка в обозревателе **Службы приложений Azure**.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Создание приложения Node.js

Теперь создайте приложение Node. js, которое можно развернуть в облаке. В этом кратком руководстве используется генератор приложений для быстрого создания шаблона приложения с помощью терминала.

> [!TIP]
> Если вы уже выполнили [учебник по Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), можно сразу перейти к [развертыванию веб-сайта](#deploy-the-website).

### <a name="install-the-express-generator"></a>Установка генератора Express

[Express](https://www.expressjs.com) — это популярная платформа для создания и запуска приложений Node. js. Вы можете создать новое приложение Express с помощью средства [Генератор Express](https://expressjs.com/en/starter/generator.html). Генератор Express поставляется как модуль npm и устанавливается средством `npm` из командной строки npm.

```bash
npm install -g express-generator
```

Параметр `-g` устанавливает Генератор Express на компьютере и делает его глобально доступным, то есть вы сможете запускать его из любого места.

### <a name="scaffold-a-new-application"></a>Подготовка нового приложения

Теперь создайте шаблон для нового приложения Express с именем `myExpressApp`, выполнив следующую команду:

```bash
express myExpressApp --view pug --git
```

Параметры `--view pug --git` сообщают генератору, что он должен использовать обработчик шаблонов [pug](https://pugjs.org/api/getting-started.html) (ранее известный под именем `jade`) и создать файл с именем `.gitignore`.

Чтобы установить все зависимости приложения, перейдите в новую папку и выполните команду `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Выполнение приложения

Теперь убедитесь, что приложение успешно выполняется. Запустите приложение из терминала с помощью команды `npm start`, чтобы начать работу сервера.

```bash
npm start
```

Теперь откройте в браузере страницу [http://localhost:3000](http://localhost:3000), которая должна выглядеть примерно так:

![Выполнение приложения Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>Развертывание веб-сайта

В этом разделе вы развернете веб-сайт Node.js с помощью VS Code и расширения Службы приложений Azure. В этом кратком руководстве используется самая простая модель развертывания: приложение в сжатом виде развертывается как веб-приложение Azure на платформе Linux.

### <a name="deploy-using-azure-app-service"></a>Развертывание с помощью Службы приложений Azure

Сначала откройте папку приложения в VS Code.

```bash
code .
```

В обозревателе **Службы приложений Azure** щелкните значок с направленной вверх синей стрелкой, чтобы развернуть приложение в Azure.

![Развертывание в виде веб-приложения](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Также развертывание можно выполнить из **Палитры команд** (Ctrl+Shift+P), введя строку "Deploy to Web App" (развернуть в веб-приложение) и выполнив команду Службы приложений Azure **Azure App Service: Deploy to Web App** (Служба приложений Azure. Развертывание в виде веб-приложения).

1. Выберите каталог `myExpressApp`, который у вас уже открыт.

2. Щелкните **Create new Web App** (Создать веб-приложение).

3. Введите глобально уникальное имя веб-приложения и нажмите клавишу ВВОД. В имени приложения допускаются символы 'a-z', '0-9' и '-'.

4. Выберите **версию Node.js**. Мы рекомендуем использовать LTS.

    В канале уведомлений отображаются ресурсы Azure, которые создаются для вашего приложения.

Нажмите кнопку **Да**, когда появится запрос на обновление конфигурации, чтобы выполнить `npm install` на целевом сервере. Затем приложение развертывается.

![Настроенное развертывание](./media/quickstart-nodejs/server-build.png)

При запуске развертывания вам будет предложено обновить рабочую область, чтобы последующие развертывания автоматически нацеливались на то же веб-приложение Службы приложений. Выберите **Да**, чтобы все изменения правильно применялись к приложению.

![Настроенное развертывание](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Убедитесь, что приложение прослушивает порт, указанный в переменной среды PORT: `process.env.PORT`.

### <a name="browse-the-website"></a>Открытие веб-сайта в браузере

После завершения развертывания щелкните **Обзор веб-сайта** в диалоге с предложением просмотреть только что развернутый веб-сайт.

### <a name="troubleshooting"></a>Устранение неполадок

Если отображается сообщение об ошибке **Вы не имеете разрешения на просмотр этого каталога пли страницы.** , значит приложение не может нормально запуститься. Перейдите к следующему разделу и проверьте выходные данные журнала, чтобы найти и исправить ошибку. Если устранить ее не удается, свяжитесь с нами, нажав кнопку **У меня есть проблема** ниже. Мы всегда рады помочь.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>Обновление веб-сайта

Вы можете развернуть изменения в это приложение, повторив тот же процесс и выбрав существующее приложение вместо создания нового.

## <a name="viewing-logs"></a>Просмотр журналов

В этом разделе вы узнаете, как просматривать журналы работающего веб-сайта. Все вызовы `console.log` на этом сайте отображаются в окне выходных данных в Visual Studio Code.

Найдите приложение в обозревателе **Службы приложений Azure**, щелкните его правой кнопкой мыши и выберите действие **Просмотреть журналы потоковой передачи**.

В запросе подтвердите намерение включить ведение журнала и перезапустить приложение. После перезапуска приложения откроется окно выходных данных VS Code, подключенное к потоку журнала.

![Просмотр журналов потоковой передачи](./media/quickstart-nodejs/view-logs.png)

![Включение ведения журнала и перезапуск](./media/quickstart-nodejs/enable-restart.png)

Через несколько секунд появится сообщение о том, что вы подключены к службе потоковой передачи журналов.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Несколько раз обновите страницу в браузере и убедитесь, что данные об этом поступают в журнал.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
```

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Дополнительная информация

Поздравляем, вы успешно завершили работу с этим руководством!

Теперь ознакомьтесь с другими расширениями Azure.

* [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Функции Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Инструменты Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Средства интерфейса командной строки Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Вы можете установить их все сразу в составе [пакета расширений для узла Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).
