---
title: Создание веб-приложений Node.js в Службе приложений Azure
description: Развертывание приложений Node.js в Службе приложений Azure
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: 32e141cae98e3fe34c8207f1565a82d2d76bdd25
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72439201"
---
# <a name="create-a-nodejs-app-in-azure"></a>Создание приложения Node.js в Azure

Служба приложений Azure — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом кратком руководстве объясняется, как развертывать приложения Node.js в Службе приложений Azure.

## <a name="prerequisites"></a>Предварительные требования

Если у вас нет учетной записи Azure [, зарегистрируйтесь сегодня](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) и получите бесплатную учетную запись с кредитами Azure на сумму $200, которые позволят проверить любое сочетание служб.

Вам нужно установить [Visual Studio Code](https://code.visualstudio.com/), а также [Node.js и npm](https://nodejs.org/en/download), диспетчер пакетов для Node.js.

Кроме того, потребуется [расширение Службы приложений Azure](vscode:extension/ms-azuretools.vscode-azureappservice), которое позволяет создавать, администрировать и развертывать веб-приложения Linux в Azure в режиме PaaS (платформа как услуга).

### <a name="sign-in"></a>Вход

После установки расширения войдите в учетную запись Azure. На панели действий выберите эмблему Azure, чтобы отобразить обозреватель **Служба приложений Azure**. Выберите **Войти в Azure...** и следуйте инструкциям.

![Вход в Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Устранение неполадок

Если отображается сообщение об ошибке **"Не удается найти подписку с именем [идентификатор подписки]"** , возможно вы находитесь за прокси-сервером и не можете связаться с API Azure. Укажите в переменных среды `HTTP_PROXY` и `HTTPS_PROXY` параметры прокси-сервера, используя команду терминала `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Если настройка переменных среды не устранит проблему, свяжитесь с нами, нажав кнопку **I ran into an issue** (У меня есть проблема) ниже.

### <a name="prerequisite-check"></a>Проверка предварительных условий

Прежде чем продолжить, проверьте наличие и правильность настройки всех обязательных компонентов.

В VS Code должна отображаться следующая информация: адрес электронной почты Azure в строке состояния и подписка в обозревателе **Службы приложений Azure**.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Создание приложения Node.js

Теперь создайте приложение Node. js, которое можно развернуть в облаке. В этом кратком руководстве используется генератор приложений для быстрого создания шаблона приложения с помощью терминала.

> [!TIP]
> Если вы уже завершили работу с [учебником по Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), можно сразу перейти к разделу о [развертывании в Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Подготовка нового приложения с помощью генератора Express

[Express](https://www.expressjs.com) — это популярная платформа для создания и запуска приложений Node. js. Вы можете создать новое приложение Express с помощью средства [Генератор Express](https://expressjs.com/en/starter/generator.html). Генератор Express поставляется как модуль npm, и его можно непосредственно запустить (без установки) с помощью программы командной строки npm `npx`.

```bash
npx express-generator myExpressApp --view pug --git
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

## <a name="deploy-to-azure"></a>Развертывание в Azure

В этом разделе вы развернете приложение Node.js с помощью VS Code и расширения Службы приложений Azure. В этом кратком руководстве используется самая простая модель развертывания: приложение в сжатом виде развертывается как веб-приложение Azure на платформе Linux.

### <a name="deploy-using-azure-app-service"></a>Развертывание с помощью Службы приложений Azure

Сначала откройте папку приложения в VS Code.

```bash
code .
```

В обозревателе **Служба приложений Azure** щелкните значок с направленной вверх синей стрелкой, чтобы развернуть приложение в Azure.

![Развертывание в виде веб-приложения](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Также развертывание можно выполнить из **Палитры команд** (Ctrl+Shift+P), введя строку "Deploy to Web App" (развернуть в веб-приложение) и выполнив команду Службы приложений Azure **Azure App Service: Deploy to Web App** (Служба приложений Azure. Развертывание в виде веб-приложения).

1. Выберите каталог `myExpressApp`, который у вас уже открыт.

1. Выберите **Create new Web App** (Создать веб-приложение), которое по умолчанию развертывается в Службе приложений в Linux.

1. Введите глобально уникальное имя веб-приложения и нажмите клавишу ВВОД. В имени приложения допускаются символы 'a-z', '0-9' и '-'.

1. Выберите **версию Node.js**. Мы рекомендуем использовать LTS.

    В канале уведомлений отображаются ресурсы Azure, которые создаются для вашего приложения.

1. Щелкните **Да**, когда появится запрос на обновление конфигурации, чтобы выполнить `npm install` на целевом сервере. Затем приложение развертывается.

    ![Настроенное развертывание](./media/quickstart-nodejs/server-build.png)

1. При запуске развертывания вам будет предложено обновить рабочую область, чтобы последующие развертывания автоматически нацеливались на то же веб-приложение Службы приложений. Выберите **Да**, чтобы все изменения правильно применялись к приложению.

    ![Настроенное развертывание](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Убедитесь, что приложение прослушивает порт, указанный в переменной среды PORT: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Просмотр приложения в Azure

По завершении развертывания щелкните **Обзор веб-сайта** в диалоговом окне с предложением просмотреть только что развернутое веб-приложение.

### <a name="troubleshooting"></a>Устранение неполадок

Если отображается сообщение об ошибке **Вы не имеете разрешения на просмотр этого каталога пли страницы.** , значит приложение не может нормально запуститься. Перейдите к следующему разделу и проверьте выходные данные журнала, чтобы найти и исправить ошибку. Если устранить ее не удается, свяжитесь с нами, нажав кнопку **I ran into an issue** (У меня есть проблема) ниже. Мы всегда рады помочь.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Обновление приложения

Вы можете развернуть изменения в это приложение, повторив тот же процесс и выбрав существующее приложение вместо создания нового.

## <a name="viewing-logs"></a>Просмотр журналов

В этом разделе вы узнаете, как просматривать журналы (или последние записи журнала) работающего приложения Службы приложений. Все вызовы `console.log` в этом приложении отображаются в окне выходных данных в Visual Studio Code.

Найдите приложение в обозревателе **Службы приложений Azure**, щелкните его правой кнопкой мыши и выберите действие **Просмотреть журналы потоковой передачи**.

В запросе подтвердите намерение включить ведение журнала и перезапустить приложение. После перезапуска приложения откроется окно выходных данных VS Code, подключенное к потоку журнала.

![Просмотр журналов потоковой передачи](./media/quickstart-nodejs/view-logs.png)

![Включение ведения журнала и перезапуск](./media/quickstart-nodejs/enable-restart.png)

Через несколько секунд появится сообщение о том, что вы подключены к службе потоковой передачи журналов. Обновите страницу несколько раз, чтобы отобразить больше действий.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
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
