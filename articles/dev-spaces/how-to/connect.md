---
title: Подключение компьютера для разработки к кластеру AKS (предварительная версия)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Узнайте, как подключить компьютер разработчика к кластеру AKS с помощью Azure Dev Spaces
keywords: Azure Dev Spaces, пространства разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235007"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Подключение компьютера для разработки к кластеру AKS (предварительная версия)

Azure Dev Spaces позволяет запускать и отлаживать код с контейнером на компьютере разработчика или без него, сохраняя подключение к кластеру Kubernetes с остальными компонентами вашего приложения или служб. Подключение компьютера разработчика к кластеру поможет быстро разработать приложение и выполнить сквозное тестирование без необходимости создания какой-либо конфигурации DOCKER или Kubernetes. Вы также можете подключиться к кластеру AKS, не затрагивая другие рабочие нагрузки или пользователей, которые могут использовать один и тот же кластер.

Azure Dev Spaces перенаправляет трафик между подключенным кластером AKS и компьютером разработки. Это перенаправление трафика позволяет взаимодействовать с кодом на компьютере разработчика и в службах, работающих в кластере AKS, так, как если бы они находящегося в одном кластере AKS. Поскольку код выполняется на компьютере разработчика, вы также обладаете гибкостью в средствах разработки, которые используются для запуска и отладки этого кода. Azure Dev Spaces также предоставляет способ репликации переменных среды и подключенных файлов, доступных для модулей Pod в кластере AKS на компьютере разработчика.

Из этого руководства вы узнаете, как выполнить следующие задачи:

* настройка Azure Dev Spaces в управляемом кластере Kubernetes в Azure;
* развертывание большого приложения с несколькими микрослужбами в пространстве разработки;
* Используйте Azure Dev Spaces для перенаправления трафика между кластером AKS и кодом, выполняемым на компьютере разработчика.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="before-you-begin"></a>Подготовка к работе

В этом руководством для демонстрации подключения компьютера разработчика к кластеру AKS [Azure dev Spaces используется пример приложения для общего доступа к велосипеду](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) . Чтобы запустить пример приложения, следуйте инструкциям в [файле readme примера приложения для общего доступа к велосипеду Azure dev Spaces](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) . Кроме того, если вы используете собственное приложение в кластере AKS, вы по-прежнему можете выполнить приведенные ниже действия и использовать имена собственных служб и модулей Pod.

### <a name="limitations"></a>Ограничения

* В настоящее время UDP не поддерживается.

### <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI][azure-cli].
* [Visual Studio Code][vs-code] с установленным и выполняемым на MacOS или Windows 10 расширением [Azure dev Spaces][azds-vs-code] .
* [Azure dev Spacesный пример приложения для общего доступа к велосипеду](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) или ваше собственное приложение, работающее в кластере AKS.

## <a name="connect-your-development-computer"></a>Подключение компьютера разработчика

Откройте модуль *dev-Spaces/Samples/бикешарингапп/велосипеды* в Visual Studio Code и используйте расширение Azure dev Spaces, чтобы подключить компьютер разработчика к кластеру AKS.

Чтобы использовать расширение Azure Dev Spaces, откройте палитру команд в Visual Studio Code, щелкнув *вид* , а затем *Палитра команд*. Начните вводить `Azure Dev Spaces: Redirect` и щелкните либо `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`либо. `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`

![Команды](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Выбор параметра перенаправления

При запуске `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`вам будет предложено выбрать существующую службу Kubernetes:

![Выбор службы](../media/how-to-connect/connect-choose-service.png)

Этот параметр перенаправляет весь трафик в кластере AKS для этой службы в версию приложения, работающего на компьютере разработчика. Если в кластере AKS работает несколько модулей Pod, весь трафик для этой службы направляется только на компьютер разработчика. Azure Dev Spaces также направляет весь исходящий трафик из приложения обратно в кластер AKS.

При запуске `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`вам будет предложено выбрать конкретный модуль:

![Выбор Pod](../media/how-to-connect/connect-choose-pod.png)

Этот параметр подключается к определенному Pod. Этот параметр полезен для взаимодействия с модулями Pod, которые не отправляют и не получают трафик и не выполняют репликацию прерванных модулей. Если модуль-получатель отправляет и получает трафик, этот параметр ведет себя аналогичным образом `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` и перенаправляет весь трафик в кластере AKS для всех модулей Pod, связанных со службой выбранного модуля.

При запуске `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`вы не получите запрос на выбор существующего Pod или службы. Этот параметр перенаправляет весь исходящий трафик из приложения, работающего на компьютере разработчика, в кластер AKS.

В этом примере выберите `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` и выберите службу *велосипедов* .

### <a name="select-a-connection-mode"></a>Выберите режим подключения

После выбора параметра перенаправления вам будет предложено выбрать режим подключения *заменить* или *клонировать* .

![Заменить или клонировать](../media/how-to-connect/connect-replace-clone.png)

Параметр *redirect* заменяет текущий модуль Pod или службу в кластере AKS и перенаправляет весь трафик для этой службы на компьютер разработчика. Этот параметр может нарушить работу других служб в кластере AKS, которые взаимодействуют с перенаправляемой службой, могут не работать до тех пор, пока приложение не будет запущено на компьютере разработчика. Параметр *clone* позволяет выбрать существующее Дочернее пространство разработки или создать новое дочернее пространство разработки для перенаправления трафика для Pod или службы на компьютер разработчика. Этот параметр позволяет работать изолированно, не нарушая работу других служб, так как только трафик к этому дочернему пространству разработки будет перенаправлен на компьютер разработчика. Для параметра *clone* требуется, чтобы в кластере AKS был включен Azure dev Spaces.

В этом примере выберите *заменить*.

> [!NOTE]
> Если у имеющейся в наличии модуля Pod есть несколько контейнеров, вам будет предложено выбрать контейнер приложения.

### <a name="select-a-port-for-your-application"></a>Выберите порт для своего приложения

После выбора режима подключения Вам будет предложено ввести TCP-порт для локального приложения. Если приложение открывает несколько портов, разделите их запятыми, например *80, 81*. Если приложение не принимает никаких сетевых запросов, введите *0*. В этом примере введите *3000*.

![Подключение выбор порта](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Убедитесь, что вы подключены

После выбора TCP-порта приложения Azure Dev Spaces установит подключение к кластеру AKS. Azure Dev Spaces внедряет агент в кластер AKS для перенаправления трафика между кластером AKS и компьютером разработки. Установка этого подключения может занять несколько минут. Azure Dev Spaces также запрашивает права администратора, чтобы изменить файл *hosts* на компьютере разработчика.

> [!IMPORTANT]
> После Azure Dev Spaces устанавливает подключение к кластеру AKS, другие службы в кластере AKS могут работать неправильно, пока служба не будет запущена на компьютере разработчика, если выбрать режим *замены* подключения. Вместо этого можно выбрать режим подключения *клона* , чтобы создать дочернее пространство разработки для перенаправления и избежать сбоев в родительском пространстве. Кроме того, если у службы есть зависимость, недоступная на компьютере разработчика, может потребоваться изменить приложение или предоставить [дополнительную конфигурацию](#additional-configuration) .

Azure Dev Spaces открывает окно терминала под названием *Аздс Connect-велосипеды* после установления подключения к кластеру AKS. В этом окне терминала находятся все переменные среды и записи DNS, настроенные из кластера AKS. Любой код, запускаемый в этом окне терминала или с помощью отладчика Visual Studio Code, подключается к кластеру AKS.

![Терминал](../media/how-to-connect/connect-terminal.png)

Кроме того, Azure Dev Spaces создает окно с именем *dev Spaces Connect* со всеми его выходными данными.

![Выходные данные](../media/how-to-connect/connect-output.png)

Azure Dev Spaces также имеет элемент строки состояния, отображающий состояние соединения.

![Состояние](../media/how-to-connect/connect-status.png)

Убедитесь, что в строке состояния отображаются *пространства разработки: подключено к dev/велосипеды по локальному порту 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Настройка приложения на компьютере разработчика

Откройте окно терминала *Аздс Connect-велосипеды* и выполните `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Щелкните *Отладка* , а затем *откройте конфигурации*. При появлении запроса на выбор среды выберите *node. js*. При этом создается `.vscode/launch.json` файл. Замените содержимое этого файла следующим:

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

Откройте [Package. JSON](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) и добавьте скрипт отладки:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Запуск приложения на компьютере разработчика

Щелкните значок " *Отладка* " слева и нажмите кнопку "Пуск" рядом с пунктом *запустить через NPM* в верхней части страницы.

![Запуск через NPM](../media/how-to-connect/launch-npm.png)

Приложение запустится и Azure Dev Spaces перенаправит трафик между кластером AKS и компьютером разработки. Вы увидите сообщения, аналогичные приведенным ниже, в *консоль отладки*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Перейдите к службе *бикешарингвеб* , щелкнув строку состояния Azure dev Spaces и выбрав общедоступный URL-адрес приложения. Вы также можете найти общедоступный URL- `azds list-uris` адрес из команды, которая была выполнена ранее. Если вы не используете Azure Dev Spaces в кластере, используйте IP или URL-адрес приложения для используемого пространства имен. В примере выше для службы *bikesharingweb* используется общедоступный URL-адрес `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды.

### <a name="set-a-break-point"></a>Установка точки останова

Откройте [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) и щелкните где-нибудь в строке 233, чтобы поместить в него курсор. Установите точку останова, нажав клавишу *F9* или щелкнув *отладку* , а затем *переключите точку останова*.

Перейдите к службе *бикешарингвеб* , открыв общедоступный URL-адрес. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды. Обратите внимание, что изображение велосипеда не загружается. Вернитесь к Visual Studio Code и обратите внимание на строку 233. Заданная точка останова приостановила службу в строке 233. Чтобы возобновить работу службы, нажмите клавишу *F5* или щелкните *Отладка*, а затем *Продолжить*. Вернитесь в браузер и убедитесь, что вы видите изображение заполнителя для велосипеда.

Удалите точку останова, поместив курсор в строку 233 `server.js` в и нажав клавишу *F9*.

### <a name="update-your-application"></a>Обновление приложения

Измените `server.js` , чтобы удалить строки 232 и 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Теперь раздел должен выглядеть следующим образом:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Сохраните изменения и щелкните *Отладка* , а затем *перезапустите отладку*. Обновите браузер и убедитесь, что вы больше не видите местозаполнитель для велосипеда.

Щелкните *Отладка*, а затем выберите *Остановить отладку*, чтобы остановить отладчик. Щелкните строку состояния Azure Dev Spaces, чтобы отключиться от кластера AKS.

## <a name="additional-configuration"></a>Дополнительная настройка

Azure Dev Spaces может управлять трафиком маршрутизации и репликацией переменных среды без дополнительной настройки. Если необходимо загрузить файлы, подключенные к контейнеру в кластере AKS, например файл ConfigMap, можно создать, `azds-local.env` чтобы скачать эти файлы на компьютер разработчика.

Ниже приведен пример `azds-local.env`.

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

## <a name="using-logging-and-diagnostics"></a>Использование ведения журналов и диагностики

После подключения компьютера разработчика к кластеру AKS данные журнала записываются в окно *подключения к пространствам* разработки.

![Выходные данные](../media/how-to-connect/connect-output.png)

Щелкните строку состояния Azure Dev Spaces и выберите пункт *отобразить сведения о диагностике*. Эта команда выводит текущие переменные среды и DNS целиком в выходные данные ведения журнала.

![Выходные данные с диагностикой](../media/how-to-connect/connect-output-diagnostics.png)

Кроме того, журналы диагностики можно найти в `Azure Dev Spaces` каталоге во [ *временном* каталоге компьютера разработчика][azds-tmp-dir].

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте, как использовать действия Azure Dev Spaces и GitHub для тестирования изменений из запроса на вытягивание непосредственно в AKS перед слиянием запроса на включение внесенных изменений в основную ветвь репозитория.

> [!div class="nextstepaction"]
> [Действия GitHub & службы Azure Kubernetes][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download