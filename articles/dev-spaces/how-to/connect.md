---
title: Подключение Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Узнайте, как использовать Azure Dev Spaces Connect
keywords: Azure Dev Spaces, пространства разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры, подключение
ms.openlocfilehash: 2e1984b838e961239e0533179c34eccb118abd8e
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065877"
---
# <a name="azure-dev-spaces-connect-preview"></a>Azure Dev Spaces подключение (Предварительная версия)

Azure Dev Spaces Connect позволяет запускать и отлаживать код с помощью контейнера на компьютере разработчика или без него, сохраняя подключение к кластеру Kubernetes с остальными компонентами вашего приложения или служб. Подключение компьютера разработки к кластеру поможет быстро разработать приложение и выполнить сквозное тестирование без необходимости создания какой-либо конфигурации DOCKER или Kubernetes. Вы также можете подключиться к кластеру AKS, не затрагивая другие рабочие нагрузки или пользователей, которые могут использовать один и тот же кластер.

Azure Dev Spaces Connect перенаправляет трафик между подключенным кластером AKS и компьютером разработки. Это перенаправление трафика позволяет коду на компьютере разработки и в службах, работающих в кластере AKS, взаимодействовать так, как если бы они находящегося в одном кластере AKS. Так как код выполняется на компьютере разработки, вы также обладаете гибкостью в средствах разработки, которые используются для запуска и отладки этого кода. Azure Dev Spaces Connect также предоставляет способ репликации переменных среды и подключенных файлов, доступных для модулей Pod в кластере AKS на компьютере разработки.

В этом руководстве вы узнаете как:

- настройка Azure Dev Spaces в управляемом кластере Kubernetes в Azure;
- развертывание большого приложения с несколькими микрослужбами в пространстве разработки;
- Используйте Azure Dev Spaces подключения, чтобы перенаправить трафик между кластером AKS и кодом, выполняемым на компьютере разработки.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="before-you-begin"></a>Перед началом работы

В этом руководством для демонстрации Azure Dev Spaces Connect используется [пример приложения для совместного использования велосипеда Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) . Если у вас есть собственное приложение в кластере AKS, которое вы хотите использовать вместо этого, вы можете начать [здесь](#use-azure-dev-spaces-connect).

### <a name="limitations"></a>Ограничения

* В данный момент протокол UDP не поддерживается в Azure Dev Spaces Connect.

### <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI][azure-cli].
* [Установленный Helm 2.13 или более поздней версии][helm-installed].
* [Visual Studio Code][vs-code] с установленным и выполняемым на MacOS или Windows 10 расширением [Azure dev Spaces][azds-vs-code] .

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Вам нужно создать кластер AKS в [поддерживаемом регионе][supported-regions]. Следующие команды создают группу ресурсов *MyResourceGroup* и кластер AKS *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Включение Azure Dev Spaces в кластере AKS

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране. Следующая команда включает Dev Spaces для кластера *MyAKS* в группе *MyResourceGroup* и создает пространство разработки с именем *dev*.

> [!NOTE]
> Команда `use-dev-spaces` также установит интерфейс командной строки Azure Dev Spaces, если он еще не установлен. Интерфейс командной строки Azure Dev Spaces невозможно установить в Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Получение примера кода приложения

В этой статье на примере [приложения Azure Dev Spaces для аренды велосипедов](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) вы изучите применение Azure Dev Spaces.

Клонируйте приложение с GitHub и перейдите в его каталог:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Получение HostSuffix для *dev*

Используйте команду `azds show-context`, чтобы показать HostSuffix для *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Внесение данных о HostSuffix в чарт Helm

Откройте файл [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) и замените все вхождения `<REPLACE_ME_WITH_HOST_SUFFIX>` полученным ранее значением HostSuffix. Сохраните изменения и закройте файл.

## <a name="run-the-sample-application-in-kubernetes"></a>Запуск примера приложения в Kubernetes

Команды, используемые для запуска примера приложения в Kubernetes, являются частью существующего процесса и не зависят от средств Azure Dev Spaces. В нашем примере для запуска примера приложения используется Helm, но вы можете выбрать и другие средства для выполнения всего приложения в пространстве имен в кластере. Команды Helm используют пространство разработки с именем *dev*, которое вы создали ранее. Это пространство разработки одновременно является и пространством имен Kubernetes. Это означает, что любые средства могут использовать пространство разработки точно так же, как любые другие пространства имен.

Azure Dev Spaces можно использовать для разработки после запуска приложения в кластере независимо от средств, используемых для его развертывания.

### <a name="use-helm-to-install-the-sample-application"></a>Установка примера приложения с помощью Helm

Используйте команды `helm init` и `helm install` для настройки и установки примера приложения в кластере.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```

> [!Note]
> **Если вы используете кластер с поддержкой RBAC**, обязательно настройте [учетную запись службы для Tiller](https://helm.sh/docs/using_helm/#role-based-access-control). В противном случае выполнение команды `helm` завершится ошибкой.

Выполнение команды `helm install` может занять несколько минут. Выходные данные команды отобразят состояние всех служб, которые она развернула в кластере:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

### <a name="navigate-to-your-sample-application"></a>Перейдите к вашему примеру приложения

По завершении установки примера приложения в кластере, для которого включена служба Dev Spaces, вы можете с помощью команды `azds list-uris` отобразить URL-адреса примера приложения в пространстве разработки *dev*, которое выбрано в данный момент.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Перейдите к службе *bikesharingweb* по общедоступному URL-адресу, который вам предоставила команда `azds list-uris`. В примере выше для службы *bikesharingweb* используется общедоступный URL-адрес `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды. Убедитесь, что вы видите изображение заполнителя для велосипеда.

## <a name="use-azure-dev-spaces-connect"></a>Использование Azure Dev Spaces Connect

Откройте модуль *dev-Spaces/Samples/бикешарингапп/велосипеды* в Visual Studio Code и используйте расширение Azure dev Spaces Connect, чтобы подключить компьютер разработки к кластеру AKS.

Чтобы использовать расширение Azure Dev Spaces Connect, откройте палитру команд в Visual Studio Code, щелкнув *вид* , а затем *Палитра команд*. Начните вводить `Azure Dev Spaces: Redirect` и щелкните `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`или `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Команды подключения](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Выбор параметра перенаправления

При запуске `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`вам будет предложено выбрать существующую службу Kubernetes:

![Подключение выбор службы](../media/how-to-connect/connect-choose-service.png)

Этот параметр перенаправляет весь трафик в кластере AKS для этой службы в версию приложения, работающего на компьютере разработки. Если в кластере AKS работает несколько модулей Pod, весь трафик для этой службы перенаправляется только на компьютер разработки. Azure Dev Spaces Connect также направляет весь исходящий трафик из приложения обратно в кластер AKS.

При запуске `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`вам будет предложено выбрать конкретный модуль:

![Подключение выбрать Pod](../media/how-to-connect/connect-choose-pod.png)

Этот параметр подключается к определенному Pod. Этот параметр полезен для взаимодействия с модулями Pod, которые не отправляют и не получают трафик и не выполняют репликацию прерванных модулей. Если модуль-получатель отправляет и получает трафик, этот параметр ведет себя так же, как `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` и будет перенаправлять весь трафик в кластере AKS для всех модулей Pod, связанных со службой выбранного модуля.

При запуске `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`вам не будет предложено выбрать существующий Pod или службу. Этот параметр перенаправляет весь исходящий трафик из приложения, работающего на компьютере разработки, в кластер AKS.

В этом примере выберите `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` и выберите службу *велосипедов* .

### <a name="select-a-connection-mode"></a>Выберите режим подключения

После выбора параметра перенаправления вам будет предложено выбрать режим подключения *заменить* или *клонировать* .

![Connect Replace или Clone](../media/how-to-connect/connect-replace-clone.png)

Параметр *redirect* заменяет текущий модуль Pod или службу в кластере AKS и перенаправляет весь трафик для этой службы на компьютер разработки. Этот параметр может нарушить работу других служб в кластере AKS, которые взаимодействуют с перенаправляемой службой, могут не работать до тех пор, пока приложение не будет запущено на компьютере разработки. Параметр *clone* позволяет выбрать существующее Дочернее пространство разработки или создать новое дочернее пространство разработки для перенаправления трафика для Pod или службы на компьютер разработки. Этот параметр позволяет работать изолированно, не нарушая работу других служб, так как только трафик к этому дочернему пространству разработки будет перенаправлен на компьютер разработки. Для параметра *clone* требуется, чтобы в кластере AKS был включен Azure dev Spaces.

В этом примере выберите *заменить*.

> [!NOTE]
> Если у имеющейся в наличии модуля Pod есть несколько контейнеров, вам будет предложено выбрать контейнер приложения.

### <a name="select-a-port-for-your-application"></a>Выберите порт для своего приложения

После выбора режима подключения Вам будет предложено ввести TCP-порт для локального приложения. Если приложение открывает несколько портов, разделите их запятыми, например *80, 81*. Если приложение не принимает никаких сетевых запросов, введите *0*. В этом примере введите *8080*.

![Подключение выбор порта](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Убедитесь, что вы подключены

После выбора TCP-порта приложения Azure Dev Spaces Connect установит подключение к кластеру AKS. Azure Dev Spaces Connect внедряет агент в кластер AKS для перенаправления трафика между кластером AKS и компьютером разработки. Установка этого подключения может занять несколько минут. Azure Dev Spaces Connect также запрашивает доступ администратора, чтобы изменить файл *hosts* на компьютере разработки.

> [!IMPORTANT]
> Когда Azure Dev Spaces Connect устанавливает подключение к кластеру AKS, другие службы в кластере AKS могут работать неправильно, пока служба не будет запущена на компьютере разработки. Кроме того, если у службы есть зависимость, недоступная на компьютере разработки, может потребоваться изменить приложение или предоставить [дополнительную конфигурацию](#additional-configuration) .

Azure Dev Spaces подключить открывает окно терминала под названием *Аздс Connect-велосипеды* после установления подключения к кластеру AKS. В этом окне терминала находятся все переменные среды и записи DNS, настроенные из кластера AKS. Любой код, запускаемый в этом окне терминала или с помощью отладчика Visual Studio Code, подключается к кластеру AKS.

![Подключить терминал](../media/how-to-connect/connect-terminal.png)

Кроме того, Azure Dev Spaces Connect создает окно с именем *dev Spaces Connect* со всеми его выходными данными.

![Подключение к выходным данным](../media/how-to-connect/connect-output.png)

Azure Dev Spaces Connect также имеет элемент строки состояния, отображающий состояние подключения.

![Состояние подключения](../media/how-to-connect/connect-status.png)

Убедитесь, что в строке состояния отображаются *пространства разработки: подключено к dev/велосипеды по локальному порту 8080*.

### <a name="configure-your-application-on-your-development-machine"></a>Настройка приложения на компьютере разработки

Откройте окно терминала *Аздс Connect-велосипеды* и запустите `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Откройте [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L352) и в строке 352 *порт* обновления на *8080*:

```javascript
var port = 8080;
var server = null;
```

Щелкните *Отладка* , а затем *откройте конфигурации*. При этом создается файл `.vscode/launch.json`. Замените содержимое этого файла следующим:

```json
{
    "configurations": [
        ...
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

### <a name="start-your-application-on-your-development-machine"></a>Запуск приложения на компьютере разработки

Щелкните значок " *Отладка* " слева и нажмите кнопку "Пуск" рядом с пунктом *запустить через NPM* в верхней части страницы.

![Запуск через NPM](../media/how-to-connect/launch-npm.png)

Приложение запустится, и Azure Dev Spaces подключения перенаправит трафик между кластером AKS и компьютером разработки. Вы увидите сообщения, аналогичные приведенным ниже, в *консоль отладки*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 8080
```

Перейдите к службе *бикешарингвеб* , щелкнув строку состояния подключения Azure dev Spaces и выбрав общедоступный URL-адрес приложения. Вы также можете найти общедоступный URL-адрес из команды `azds list-uris`, которая была выполнена ранее. Если вы не используете Azure Dev Spaces в кластере, используйте IP или URL-адрес приложения для используемого пространства имен. В примере выше для службы *bikesharingweb* используется общедоступный URL-адрес `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды.

### <a name="set-a-break-point"></a>Установка точки останова

Откройте [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) и щелкните где-нибудь в строке 233, чтобы поместить в него курсор. Установите точку останова, нажав клавишу *F9* или щелкнув *отладку* , а затем *переключите точку останова*.

Перейдите к службе *бикешарингвеб* , открыв общедоступный URL-адрес. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды. Обратите внимание, что изображение велосипеда не загружается. Вернитесь к Visual Studio Code и обратите внимание на строку 233. Заданная точка останова приостановила службу в строке 233. Чтобы возобновить работу службы, нажмите клавишу *F5* или щелкните *Отладка*, а затем *Продолжить*. Вернитесь в браузер и убедитесь, что вы видите изображение заполнителя для велосипеда.

Удалите точку останова, поместив курсор на строку 233 в `server.js` и нажав клавишу *F9*.

### <a name="update-your-application"></a>Обновление приложения

Измените `server.js`, чтобы удалить строки 232 и 233:

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

Щелкните *Отладка*, а затем выберите *Остановить отладку*, чтобы остановить отладчик. Щелкните строку состояния Azure Dev Spaces подключить, чтобы отключиться от кластера AKS.

## <a name="additional-configuration"></a>Дополнительная конфигурация

Azure Dev Spaces Connect может управлять трафиком маршрутизации и реплицировать переменные среды без дополнительной настройки. Если необходимо загрузить файлы, подключенные к контейнеру в кластере AKS, например файл ConfigMap, можно создать `azds-local.env`, чтобы скачать эти файлы на компьютер разработки.

Ниже приведен пример `azds-local.env`.

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как использовать действия Azure Dev Spaces и GitHub для тестирования изменений из запроса на вытягивание непосредственно в AKS перед слиянием запроса на включение внесенных изменений в основную ветвь репозитория.

> [!div class="nextstepaction"]
> [Действия GitHub & службы Azure Kubernetes][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download