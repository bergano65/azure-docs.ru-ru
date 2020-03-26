---
title: Краткое руководство. Создание веб-приложения Node.js
description: Быстрое развертывание первого приложения Hello World на Node.js в Службе приложений Azure. Развертывание выполняется с помощью инструмента Visual Studio Code, который является одним из множества способов развертывания в Службе приложений.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: bcb0ae2457175e7511c3539187d72d48e80baa5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047131"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Создание веб-приложений Node.js в Azure 

Чтобы начать работу со Службой приложений Azure, создайте локально приложение Node.js/Express с помощью Visual Studio Code, а затем разверните его в облаке. Так как вы используете бесплатный уровень Службы приложений, для прохождения этого краткого руководства никакие затраты не требуются.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) бесплатно.
- [Node.js и NPM](https://nodejs.org). Выполнив команду `node --version`, убедитесь, что платформа Node.js установлена.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение Службы приложений Azure](vscode:extension/ms-azuretools.vscode-azureappservice) для Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Клонирование и запуск локального приложения Node.js

1. На локальном компьютере откройте терминал и выполните клонирование примера репозитория:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Перейдите в папку нового приложения:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Запустите приложение, чтобы проверить его локально:

    ```bash
    npm start
    ```
    
1. Откройте веб-браузер и перейдите по адресу [http://localhost:1337](http://localhost:1337). В браузере должно отобразиться сообщение "Hello World!".

1. Нажмите **Ctrl**+**C** в терминале, чтобы остановить работу сервера.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Развертывание приложения в Azure

В этом разделе вы развернете приложение Node.js в Azure с помощью VS Code и расширения Службы приложений Azure.

1. В окне терминала убедитесь, что вы находитесь в папке *nodejs-docs-hello-world*, а затем запустите Visual Studio Code с помощью следующей команды:

    ```bash
    code .
    ```

1. На панели действий VS Code выберите эмблему Azure, чтобы отобразить обозреватель **СЛУЖБА ПРИЛОЖЕНИЙ AZURE**. Выберите **Войти в Azure...** и следуйте инструкциям. (Если произойдут ошибки, см. раздел [Устранение неполадок со входом в Azure](#troubleshooting-azure-sign-in) ниже.) После входа в обозревателе должно отобразиться имя вашей подписки Azure.

    ![Вход в Azure](containers/media/quickstart-nodejs/sign-in.png)

1. В обозревателе **СЛУЖБА ПРИЛОЖЕНИЙ AZURE** в VS Code щелкните значок с направленной вверх синей стрелкой, чтобы развернуть приложение в Azure. (Ту же команду можно также вызвать из **палитры команд** (**Ctrl**+**Shift**+**P**). Для этого следует ввести "развернуть в веб-приложении" и выбрать пункт **Служба приложений Azure: развертывание в веб-приложении**).

    ![Развертывание в виде веб-приложения](containers/media/quickstart-nodejs/deploy.png)
        
1. Выберите папку *nodejs-docs-hello-world*.

1. Выберите вариант создания на основе операционной системы, на которую требуется развернуть:

    - Linux: Щелкните **Создание нового веб-приложения**
    - Windows: Щелкните **Создание нового веб-приложения... Дополнительно**

1. Введите глобально уникальное имя веб-приложения и нажмите клавишу **ВВОД**. Имя должно быть уникальным во всех системах Azure и состоять только из буквенно-цифровых символов ("A-Z", "a-z" и "0-9") и дефисов ("-").

1. Если вы намерены использовать Linux, выберите версию Node.js при появлении соответствующего запроса. Рекомендуется использовать версию **LTS**.

1. Если вы намерены использовать Windows, следуйте дополнительным запросам:
    1. Выберите **Создать группу ресурсов**, а затем введите имя для группы ресурсов, например `AppServiceQS-rg`.
    1. Выберите **Windows** в поле операционной системы.
    1. Выберите **Создать новый план службы приложений**, введите имя плана (например, `AppServiceQS-plan`), а затем выберите **F1 Free** для ценовой категории.
    1. Выберите **Пропустить** при появлении запроса о Application Insights.
    1. Выберите регион, расположенный рядом с вами или рядом с ресурсами, к которым вы хотите получить доступ.

1. После ответа на все запросы, в VS Code отображаются ресурсы Azure, созданные для вашего приложения.

    При развертывании в Linux, нажмите **Да**, когда появится запрос на обновление конфигурации, чтобы выполнить `npm install` на целевом сервере Linux.

    ![Запрос на обновление конфигурации на целевом сервере Linux](containers/media/quickstart-nodejs/server-build.png)

1. Выберите **Да** при появлении запроса **Always deploy the workspace "nodejs-docs-hello-world" to (app name)"** (Всегда развертывайте рабочую область "nodejs-docs-hello-world" в (имя приложения)). Если выбрать **Да**, VS Code будет в дальнейшем автоматически выполнять развертывания в то же веб-приложение Службы приложений.

1. При развертывании в Linux выберите **Обзор веб-сайта** в командной строке, чтобы просмотреть новое развернутое веб-приложение после завершения развертывания. В браузере должно отобразиться сообщение "Hello World!".

1. При развертывании в Windows необходимо сначала задать номер версии Node.js для веб-приложения:

    1. В VS Code разверните узел для новой службы приложений, щелкните правой кнопкой мыши **Параметры приложения** и выберите **Добавить новый параметр...** :

        ![Команда добавления параметра приложения](containers/media/quickstart-nodejs/add-setting.png)

    1. Введите `WEBSITE_NODE_DEFAULT_VERSION` для ключа параметра.
    1. Введите `10.15.2` для значения параметра.
    1. Щелкните правой кнопкой мыши узел службы приложений и выберите **Перезапустить**.

        ![Команда перезапуска службы приложений](containers/media/quickstart-nodejs/restart.png)

    1. Еще раз щелкните правой кнопкой мыши узел для службы приложений и выберите **Обзор веб-сайта**.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Устранение неполадок со входом в Azure

Если при входе в Azure отображается сообщение об ошибке **"Не удается найти подписку с именем [идентификатор подписки]"** , возможно, вы находитесь за прокси-сервером и не можете связаться с API Azure. Укажите в переменных среды `HTTP_PROXY` и `HTTPS_PROXY` параметры прокси-сервера, используя команду терминала `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Если настройка переменных среды не устранит проблему, свяжитесь с нами, нажав кнопку **I ran into an issue** (У меня есть проблема) выше.

### <a name="update-the-app"></a>Обновление приложения

Вы можете развернуть изменения в это приложение. Для этого внесите изменения в VS Code, сохраните файлы, а затем повторите тот же процесс, но выберите существующее приложение вместо создания нового.

## <a name="viewing-logs"></a>Просмотр журналов

Выходные данные журнала (вызовы `console.log`) из приложения непосредственно в окне вывода VS Code.

1. В обозревателе **СЛУЖБЫ ПРИЛОЖЕНИЙ AZURE** щелкните узел приложения правой кнопкой мыши и выберите **Start Streaming Logs** (Начать потоковую передачу журналов).

    ![Запуск потоковой передачи журналов](containers/media/quickstart-nodejs/view-logs.png)

1. В запросе подтвердите намерение включить ведение журнала и перезапустить приложение. После перезапуска приложения откроется окно выходных данных VS Code, подключенное к потоку журнала. 

    ![Включение ведения журнала и перезапуск](containers/media/quickstart-nodejs/enable-restart.png)

1. Через несколько секунд в окне вывода появится сообщение о том, что вы подключены к службе потоковой передачи журналов. Обновив страницу в браузере, можно создать дополнительные действия вывода.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Дальнейшие действия

Поздравляем, вы успешно завершили работу с этим руководством!

Теперь ознакомьтесь с другими расширениями Azure.

* [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Функции Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Инструменты Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Средства интерфейса командной строки Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Вы можете установить их все сразу в составе [пакета расширений для узла Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).
