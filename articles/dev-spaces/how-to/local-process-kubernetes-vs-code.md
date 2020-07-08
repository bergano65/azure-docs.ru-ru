---
title: Использование Локального процесса с Kubernetes в Visual Studio Code (предварительная версия)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Узнайте, как использовать локальный процесс с Kubernetes для подключения компьютера разработчика к кластеру Kubernetes с Azure Dev Spaces
keywords: Локальный процесс с Kubernetes, Azure Dev Spaces, сферами разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316465"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Использование Локального процесса с Kubernetes в Visual Studio Code (предварительная версия)

Локальный процесс с Kubernetes позволяет запускать и отлаживать код на компьютере разработчика, сохраняя подключение к кластеру Kubernetes с остальной частью вашего приложения или служб. Например, при наличии большой архитектуры микрослужб с множеством взаимозависимых служб и баз данных репликация этих зависимостей на компьютере разработчика может быть сложной. Кроме того, создание и развертывание кода в кластере Kubernetes для каждого изменения кода во время разработки внутреннего цикла может быть медленным, трудоемким и трудным для использования с отладчиком.

Локальный процесс с Kubernetes позволяет избежать необходимости создавать и развертывать код в кластере, создавая подключение напрямую между компьютером разработчика и кластером. Подключение компьютера разработчика к кластеру во время отладки позволяет быстро тестировать и разрабатывать службы в контексте полного приложения без создания конфигурации DOCKER или Kubernetes.

Локальный процесс с Kubernetes перенаправляет трафик между подключенным кластером Kubernetes и компьютером разработки. Это перенаправление трафика позволяет взаимодействовать с кодом на компьютере разработчика и в службах, работающих в кластере Kubernetes, так, как если бы они находящегося в одном кластере Kubernetes. Локальный процесс с Kubernetes также позволяет реплицировать переменные среды и подключенные тома, доступные для модулей Pod в кластере Kubernetes на компьютере разработчика. Предоставление доступа к переменным среды и подключенным томам на компьютере разработчика позволяет быстро работать с кодом без репликации этих зависимостей вручную.

В этом руководство вы узнаете, как использовать локальный процесс с Kubernetes для перенаправления трафика между кластером Kubernetes и кодом, выполняемым на компьютере разработчика. В этом руководством также представлен сценарий для развертывания большого примера приложения с несколькими микрослужбами в кластере Kubernetes.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][preview-terms]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="before-you-begin"></a>Перед началом

В этом руководством для демонстрации подключения компьютера разработчика к кластеру Kubernetes [Azure dev Spaces используется пример приложения для общего доступа к велосипеду][bike-sharing-github] . Если у вас уже есть приложение, работающее в кластере Kubernetes, вы можете выполнить приведенные ниже действия и использовать имена собственных служб.

### <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI][azure-cli].
* [Visual Studio Code][vs-code] , работающий в MacOS или Windows 10.
* Расширение [Azure dev Spaces][azds-vs-code] версии 2.0.220200601 или более поздней, установленное в Visual Studio Code.
* [Установлен Azure dev SPACES CLI][azds-cli].

## <a name="create-a-kubernetes-cluster"></a>Создание кластера Kubernetes

Создайте кластер AKS в [поддерживаемом регионе][supported-regions]. Следующие команды создают группу ресурсов *MyResourceGroup* и кластер AKS *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>Установите пример приложения

Установите пример приложения в кластере с помощью предоставленного скрипта. Этот скрипт можно выполнить на компьютере разработчика или с помощью [Azure Cloud Shell][azure-cloud-shell].

> [!IMPORTANT]
> Для выполнения скрипта необходимо иметь доступ *владельца* или *участника* к кластеру.

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Перейдите к образцу приложения, в котором выполняется кластер, открыв его общедоступный URL-адрес, который отображается в выходных данных скрипта установки.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

В приведенном выше примере общедоступный URL-адрес — `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Подключение к кластеру и Отладка службы

На компьютере разработчика Скачайте и настройте интерфейс командной строки Kubernetes для подключения к кластеру Kubernetes с помощью команды [AZ AKS Get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Откройте модуль *dev-Spaces/Samples/бикешарингапп/велосипеды* из [примера приложения Azure dev Spaces для совместного использования велосипедов][bike-sharing-github] в Visual Studio Code. Откройте расширение службы Azure Kubernetes и выберите пространство имен *dev* в кластере *мякс* .

![Выбор пространства имен](../media/local-process-kubernetes-vs-code/select-namespace.png)

Используйте `npm install` команду, чтобы установить зависимости для приложения.

```console
npm install
```

Щелкните значок *отладки* слева и выберите *локальный процесс с Kubernetes (Предварительная версия)* вверху.

![Выбор локального процесса с помощью Kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Нажмите кнопку Пуск рядом с пунктом *локальный процесс с Kubernetes (Предварительная версия)*. При первом запуске этой конфигурации запуска вам будет предложено настроить службу, которую требуется заменить, порт, пересылаемый с компьютера разработки, и выполняемую задачу запуска.

Выберите службу *велосипедов* .

![Выбор службы](../media/local-process-kubernetes-vs-code/choose-service.png)

Весь трафик в кластере Kubernetes перенаправляется для службы *велосипедов* в версию приложения, работающего на компьютере разработчика. Локальный процесс с Kubernetes также направляет весь исходящий трафик из приложения обратно в кластер Kubernetes.

> [!IMPORTANT]
> Перенаправление можно выполнять только для служб с одним модулем Pod.

После выбора службы вам будет предложено ввести TCP-порт для локального приложения. В этом примере введите *3000*.

![Подключение выбор порта](../media/local-process-kubernetes-vs-code/choose-port.png)

В качестве задачи запуска выберите *запустить через NPM* .

![Подключение выбор задачи запуска](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> Вам будет предложено разрешить *кубернетесднсманажер* запуск с повышенными правами и изменить файл hosts.

Компьютер разработчика подключен, когда строка состояния становится оранжевой, а расширение пространства разработки показывает, что вы подключены.

![Компьютер разработки подключен](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> При запуске субескуент вам не будет предложено ввести имя службы, порт или задачу запуска. Эти значения сохраняются в *. vscode/tasks.json*.

После подключения компьютера разработчика трафик начнет перенаправление на компьютер разработки для заменяемой службы.

## <a name="set-a-break-point"></a>Установка точки останова

Откройте [server.js][server-js-breakpoint] и щелкните где-нибудь в строке 233, чтобы поместить в него курсор. Установите точку останова, нажав клавишу *F9* , или выберите команду *выполнить* и *Переключить точку останова*.

Перейдите к образцу приложения, открыв общедоступный URL-адрес. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды. Обратите внимание, что изображение велосипеда не загружается. Вернитесь к Visual Studio Code и обратите внимание на строку 233. Заданная точка останова приостановила службу в строке 233. Чтобы возобновить работу службы, нажмите *клавишу F5* или кнопку *запустить* , а затем *продолжить*. Вернитесь в браузер и убедитесь, что вы видите изображение заполнителя для велосипеда.

Удалите точку останова, поместив курсор в строку 233 в `server.js` и нажав клавишу *F9*.

### <a name="update-your-application"></a>Обновление приложения

Измените `server.js` , чтобы удалить строки 234 и 235:

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

Сохраните изменения и нажмите кнопку *выполнить* , а затем *перезапустите отладку*. После повторного подключения обновите браузер и убедитесь, что вы больше не видите местозаполнитель для велосипеда.

Щелкните *запустить* , а затем — *Отключить отладку* , чтобы прерывать работу отладчика.

> [!NOTE]
> По умолчанию при остановке задачи отладки также отключается компьютер разработчика из кластера Kubernetes. Это поведение можно изменить, выполнив поиск *локального процесса с помощью Kubernetes: Disconnect после отладки* в параметрах Visual Studio Code и удалив флажок *Отключить автоматическое отключение при отладке.* После обновления этого параметра компьютер разработчика останется подключенным при выключении и запуске отладки. Чтобы отключить компьютер разработчика от кластера, щелкните расширение Azure Dev Spaces в строке состояния, а затем выберите *отключить текущий сеанс*.
>
> Если Visual Studio Code внезапно завершает подключение к кластеру или завершается, то перенаправляемая служба может быть не восстановлена до исходного состояния перед подключением к локальному процессу с помощью Kubernetes. Чтобы устранить эту проблему, см. [руководство по устранению неполадок][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Использование ведения журналов и диагностики

После подключения компьютера разработчика к кластеру Kubernetes выходные данные журнала записываются в окно *пространства* разработки.

![Выходные данные](../media/local-process-kubernetes-vs-code/output.png)

Щелкните строку состояния Azure Dev Spaces и выберите пункт *отобразить сведения о диагностике подключения*. Эта команда выводит текущие переменные среды и DNS целиком в выходные данные ведения журнала.

![Выходные данные с диагностикой](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

Кроме того, журналы диагностики можно найти в `Azure Dev Spaces` каталоге во [ *временном* каталоге компьютера разработчика][azds-tmp-dir].

## <a name="remove-the-sample-application-from-your-cluster"></a>Удаление примера приложения из кластера

Используйте предоставленный скрипт для удаления примера приложения из кластера.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте, как использовать действия Azure Dev Spaces и GitHub для тестирования изменений из запроса на вытягивание непосредственно в AKS перед слиянием запроса на включение внесенных изменений в основную ветвь репозитория.

> [!div class="nextstepaction"]
> [Действия GitHub & службы Azure Kubernetes][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download