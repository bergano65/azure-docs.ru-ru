---
title: Подключение компьютера разработки к кластеру AKS (предварительный просмотр)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Узнайте, как подключить компьютер разработки к кластеру AKS с Azure Dev Spaces
keywords: Пространства Azure Dev, Dev Spaces, Докер, Кубернетес, Лазурный берег, AKS, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235007"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Подключение компьютера разработки к кластеру AKS (предварительный просмотр)

Пространства Azure Dev позволяют запускать и отлаживать код с контейнером или без его на компьютере разработки, при этом подключенном к кластеру Kubernetes с остальной частью приложения или служб. Подключение компьютера разработки к кластеру позволяет быстро разрабатывать приложение и выполнять сквозное тестирование без создания конфигурации Docker или Kubernetes. Вы также можете подключиться к кластеру AKS, не затрагивая другие рабочие нагрузки или пользователей, которые могут использовать тот же кластер.

Пространства Azure Dev перенаправляют трафик между подключенным кластером AKS и компьютером разработки. Это перенаправление трафика позволяет коду на компьютере разработки и службам, работающим в кластере AKS, общаться так, как будто они находятся в одном кластере AKS. Поскольку ваш код работает на компьютере разработки, вы также имеете гибкость в инструментах разработки, которые вы используете для запуска и отладки этого кода. Пространства Azure Dev также предоставляют способ репликации переменных среды и установленных файлов, доступных для стручков в кластере AKS в компьютере разработки.

Из этого руководства вы узнаете, как выполнить следующие задачи:

* настройка Azure Dev Spaces в управляемом кластере Kubernetes в Azure;
* развертывание большого приложения с несколькими микрослужбами в пространстве разработки;
* Используйте пространства Azure Dev для перенаправления трафика между кластером AKS и кодом, работающим на компьютере разработки.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="before-you-begin"></a>Перед началом

В этом руководстве используется [примерное приложение Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) для демонстрации подключения компьютера разработки к кластеру AKS. Следуйте инструкциям в [azure Dev Spaces Bike Sharing примерприложения README](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) для запуска образца приложения. Кроме того, если у вас есть собственное приложение в кластере AKS, вы все равно можете выполнить приведенные ниже действия и использовать имена собственных служб и стручков.

### <a name="limitations"></a>Ограничения

* В настоящее время UDP не поддерживается.

### <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI][azure-cli].
* [Визуальный студийный код][vs-code] с расширением [Azure Dev Spaces,][azds-vs-code] установленным и работающим на MacOS или Windows 10.
* [Приложение Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) или собственное приложение, работая на кластере AKS.

## <a name="connect-your-development-computer"></a>Подключите компьютер разработки

Откройте *dev-spaces/samples/BikeSharingApp/Bikes* в коде Visual Studio и используйте расширение Azure Dev Spaces для подключения компьютера разработки к кластеру AKS.

Чтобы использовать расширение Azure Dev Spaces, откройте палитру команд в коде Visual Studio, нажав *на View,* затем *нажмите На команду palette.* Начните `Azure Dev Spaces: Redirect` печатать на `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`вводе и нажмите на либо , `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, или `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Команды](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Выберите опцию перенаправления

Если вы `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`работаете, вас просят выбрать существующий сервис Kubernetes:

![Выберите услугу](../media/how-to-connect/connect-choose-service.png)

Эта опция перенаправляет весь трафик в кластере AKS для этой службы на версию приложения, работаемого на компьютере разработки. Если в кластере AKS работает несколько стручков, весь трафик для этой службы направляется только на компьютер разработки. Azure Dev Spaces также направляет весь исходящий трафик из приложения обратно в кластер AKS.

Если вы `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`бежите, вас просят выбрать конкретный стручок:

![Выберите Pod](../media/how-to-connect/connect-choose-pod.png)

Эта опция подключается к определенному стручок. Эта опция полезна для взаимодействия с стручками, которые не отправляют и не получают трафик и реплицируют завершенные стручки. Если стручок действительно отправляет и получает трафик, эта `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` опция ведет себя аналогичным образом и перенаправит весь трафик в кластере AKS для всех стручков, связанных со службой выбранного стручка.

Если вы `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`работаете, вам не предлагается выбрать существующий модуль или услугу. Эта опция перенаправляет весь исходящий трафик из приложения, работаемого на компьютере разработки, в кластер AKS.

Для этого примера выберите `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` и выберите услугу *велосипедов.*

### <a name="select-a-connection-mode"></a>Выберите режим соединения

После выбора опции перенаправления вам предлагается выбрать режим подключения *заменить* или *клона.*

![Заменить или клонировать](../media/how-to-connect/connect-replace-clone.png)

Опция *«Замена»* заменяет текущий модуль или службу в кластере AKS и перенаправляет весь трафик для этой службы на компьютер разработки. Эта опция может нанести непоправимый характер для других служб в кластере AKS, которые взаимодействуют с перенаправлением службы, может не функционировать до тех пор, пока вы не запустите приложение на компьютере разработки. Опция *Клон* позволяет выбрать существующее пространство для разработки ребенка или создать новое пространство для поиска трафика для стручка или службы на компьютер разработки. Эта опция позволяет работать изолированно и не нарушать другие службы, так как только трафик в это пространство для разработчиков ребенка будет перенаправлен на компьютер разработки. Опция *клона* требует включения кластера AKS в кластер AkS.

В этом примере выберите *Заменить*.

> [!NOTE]
> Если в капсуле существующей службы имеется несколько контейнеров, вам также предлагается выбрать контейнер приложения.

### <a name="select-a-port-for-your-application"></a>Выберите порт для приложения

После выбора режима подключения вам будет предложено войти в порт TCP вашего локального приложения. Если приложение открывает несколько портов, разделите их на запятую, *например, 80,81.* Если ваше приложение не принимает сетевых запросов, введите *0*. Для этого примера введите *3000*.

![Подключите выбрать порт](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Подтвердите, что вы подключены

После выбора порта TCP приложения Azure Dev Spaces установит подключение к кластеру AKS. Пространства Azure Dev вводят агент в кластер AKS для перенаправления трафика между кластером AKS и компьютером разработки. Создание этого соединения может занять несколько минут. Azure Dev Spaces также запрашивает доступ администратора для изменения файла *хоста* в компьютере разработки.

> [!IMPORTANT]
> После того, как Azure Dev Spaces установит подключение к кластеру AKS, другие службы в кластере AKS могут работать неправильно, пока вы не запустите службу в компьютере разработки, если вы выберете режим *подключения Replace.* Вместо этого можно выбрать режим соединения *клонов,* чтобы создать пространство для детского разработчика для перенаправления и избежать каких-либо нарушений родительского пространства. Кроме того, если в службе есть зависимость, которая недоступна в компьютере разработки, возможно, потребуется изменить приложение или предоставить [дополнительную конфигурацию](#additional-configuration)

Пространства Azure Dev открывает окно терминала под названием *A'DS Connect - Велосипеды* после того, как оно устанавливает подключение к кластеру AKS. Это окно терминала имеет все переменные среды и записи DNS, настроенные из кластера AKS. Любой код, запущенный в этом окне терминала или с помощью отладчика Visual Studio, подключен к кластеру AKS.

![Терминал](../media/how-to-connect/connect-terminal.png)

Кроме того, Azure Dev Spaces создает окно под названием *Dev Spaces Connect* со всеми его выходными.

![Выходные данные](../media/how-to-connect/connect-output.png)

Пространства Azure Dev также имеют элемент панели статуса, показывающий состояние соединения.

![Состояние](../media/how-to-connect/connect-status.png)

Проверка панели статуса показывает *Dev Spaces: Подключен к dev/bikes на локальном порту 3000.*

### <a name="configure-your-application-on-your-development-computer"></a>Настройка приложения на компьютере разработки

Откройте окно терминала *AzDS Connect - Велосипеды* терминала и запустить: `npm install`

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Нажмите *Отожобье,* а затем *открыть конфигурации*. Если пожелаетвыбрать среду, выберите *Node.js*. Это создает `.vscode/launch.json` файл. Замените содержимое этого файла следующим:

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Откройте [package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) и добавьте отладку:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Запустите приложение на компьютере разработки

Нажмите на *значок Debug* слева и нажмите на кнопку "Пуск" рядом *с запуском через NPM* в верхней части.

![Запуск через NPM](../media/how-to-connect/launch-npm.png)

Приложение запустится, и Azure Dev Spaces перенаправляет трафик между кластером AKS и компьютером разработки. Вы увидите сообщения, похожие на приведенные ниже в *консоли Debug:*

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Перейдите к службе *bikesharingweb,* нажав на панель статуса Azure Dev Spaces и выбрав общедоступный URL приложения. Вы также можете найти общедоступный URL из команды, `azds list-uris` высвенена ранее. Если вы не используете пространства Azure Dev в кластере, используйте IP или URL-адрес приложения для использовавого пространства имен. В примере выше для службы *bikesharingweb* используется общедоступный URL-адрес `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Выберите *Аурелия Бриггс (клиент) в* качестве пользователя, а затем выберите велосипед в аренду.

### <a name="set-a-break-point"></a>Установить точку разрыва

Откройте [server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) и нажмите где-нибудь на линии 233, чтобы положить курсор там. Установите точку разрыва, нажав *F9* или нажав *Debug* затем *Toggle Breakpoint.*

Перейдите к сервису *bikesharingweb,* открыв общедоступный URL. Выберите *Аурелия Бриггс (клиент) в* качестве пользователя, а затем выберите велосипед в аренду. Обратите внимание, что изображение для велосипеда не загружается. Возврат к Visual Studio Code и соблюдать линию 233 выделено. Точка разрыва, установленная вами, приостановила обслуживание на строке 233. Чтобы возобновить работу службы, нажмите клавишу *F5* или щелкните *Отладка*, а затем *Продолжить*. Вернитесь в браузер и убедитесь, что вы видите изображение заполнителя для велосипеда.

Удалите точку разрыва, поставив курсор на `server.js` линию 233 и попав *F9.*

### <a name="update-your-application"></a>Обновление приложения

Отменить, `server.js` чтобы удалить строки 232 и 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Раздел должен теперь выглядеть следующим образом:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Сохраните изменения и нажмите *Debug* затем *перезапустите отладку.* Освежите свой браузер и убедитесь, что вы больше не видите изображение заполнителя для велосипеда.

Щелкните *Отладка*, а затем выберите *Остановить отладку*, чтобы остановить отладчик. Нажмите на панель статуса Azure Dev Spaces, чтобы отключиться от кластера AKS.

## <a name="additional-configuration"></a>Дополнительная настройка

Пространства Azure Dev могут обрабатывать изменения трафика и репликации переменных среды без дополнительной конфигурации. Если вам нужно загрузить любые файлы, которые установлены в контейнере в кластере AKS, такие как файл ConfigMap, вы можете создать `azds-local.env` для загрузки этих файлов на компьютер разработки.

Вот пример: `azds-local.env`

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Использование журналов и диагностики

Вывод ы записи записывается в окно *Dev Spaces Connect* после подключения компьютера разработки к кластеру AKS.

![Выходные данные](../media/how-to-connect/connect-output.png)

Нажмите на панель статуса Azure Dev Spaces и выберите *информацию о диагностике Show.* Эта команда печатает текущие переменные среды и DNS entires в выходе журнала.

![Выход с диагностикой](../media/how-to-connect/connect-output-diagnostics.png)

Кроме того, в `Azure Dev Spaces` [каталоге *TEMP-каталоге* ][azds-tmp-dir]компьютера разработки можно найти журналы диагностики.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как использовать Azure Dev Spaces и GitHub Actions для тестирования изменений запроса на вытягивание непосредственно в AKS до того, как запрос на вытягивание будет объединен в основную ветвь репозитория.

> [!div class="nextstepaction"]
> [Действия GitHub & сервисazре Kubernetes][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download