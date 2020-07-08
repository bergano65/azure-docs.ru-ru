---
title: Использование Локального процесса с Kubernetes в Visual Studio (предварительная версия)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Узнайте, как использовать локальный процесс с Kubernetes в Visual Studio для подключения компьютера разработчика к кластеру Kubernetes с Azure Dev Spaces
keywords: Локальный процесс с Kubernetes, Azure Dev Spaces, сферами разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316555"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Использование Локального процесса с Kubernetes в Visual Studio (предварительная версия)

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
* [Visual Studio 2019][visual-studio] версии 16,7 Предварительная версия 2 или более поздняя на Windows 10 с установленными рабочими нагрузками *ASP.NET и веб-разработка* и *Разработка Azure* .
* [Установлен Azure dev SPACES CLI][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>Включение функции локального процесса с предварительной версией Kubernetes в Visual Studio

Чтобы включить локальный процесс с помощью Kubernetes в Visual Studio, щелкните *Сервис*  >  *Параметры*  >  *Среда*  >  *Предварительная версия функции*. Выберите *включить локальную отладку для Kubernetes Services*.

Кроме того, для консольных приложений .NET установите пакет NuGet *Microsoft. VisualStudio. Azure. Kubernetes. Tools. targets* .

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

Откройте *Дев-спацес/Samples/бикешарингапп/ресерватионенгине/App. csproj* из [примера приложения Azure dev Spaces для общего доступа к велосипеду][bike-sharing-github] в Visual Studio.

В проекте выберите *локальный процесс с Kubernetes* в раскрывающемся списке Параметры запуска, как показано ниже.

![Выбор локального процесса с помощью Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

Нажмите кнопку Пуск рядом с пунктом *локальный процесс с Kubernetes*. В диалоговом окне *локальный процесс с Kubernetes* :

* Выберите свою подписку.
* Выберите *мякс* для кластера.
* Выберите *dev* для пространства имен.
* Выберите *ресерватионенгине* для перенаправления службы.
* Выберите *приложение* для профиля запуска.
* Выберите `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` этот URL-адрес для запуска браузера.

![Выберите локальный процесс с кластером Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> Перенаправление можно выполнять только для служб с одним модулем Pod.

Щелкните *сохранить и начать отладку*.

Весь трафик в кластере Kubernetes перенаправляется для службы *ресерватионенгине* в версию приложения, работающего на компьютере разработчика. Локальный процесс с Kubernetes также направляет весь исходящий трафик из приложения обратно в кластер Kubernetes.

> [!NOTE]
> Вам будет предложено разрешить *кубернетесднсманажер* запуск с повышенными правами и изменить файл hosts.

Компьютер разработчика подключен, когда в строке состояния отображается подключение к службе *ресерватионенгине* .

![Компьютер разработки подключен](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> При запуске субескуент в диалоговом окне *локальный процесс с Kubernetes* не будет выводиться запрос. Эти параметры обновляются в области *Отладка* в свойствах проекта.

После подключения компьютера разработчика трафик начнет перенаправление на компьютер разработки для заменяемой службы.

## <a name="set-a-break-point"></a>Установка точки останова

Откройте [BikesHelper.CS][bikeshelper-cs-breakpoint] и щелкните где-нибудь на строке 26, чтобы поместить курсор туда. Установите точку останова, нажав клавишу *F9* или щелкнув *отладку* , а затем *переключите точку останова*.

Перейдите к образцу приложения, открыв общедоступный URL-адрес. В качестве пользователя выберите *Аурелиа Бриггс (клиент)* , а затем выберите велосипед для аренды. Щелкните *сдается велосипед*. Вернитесь в Visual Studio и обратите внимание на строку 26. Заданная точка останова приостановила службу в строке 26. Чтобы возобновить работу службы, нажмите клавишу *F5* или щелкните *Отладка*, а затем *Продолжить*. Вернитесь в браузер и убедитесь, что на странице отображается арендованный велосипед.

Удалите точку останова, поместив курсор в строку 26 в `BikesHelper.cs` и нажав клавишу *F9*.

> [!NOTE]
> По умолчанию при остановке задачи отладки также отключается компьютер разработчика из кластера Kubernetes. Это поведение можно изменить, изменив параметр *Отключить после отладки* на *false* в разделе *средства отладки Kubernetes* в параметрах отладки. После обновления этого параметра компьютер разработчика останется подключенным при выключении и запуске отладки. Чтобы отключить компьютер разработчика от кластера, нажмите кнопку *Отключить* на панели инструментов.
>
> Если Visual Studio внезапно завершает подключение к кластеру или завершает работу, то перенаправляемая служба может быть не восстановлена до исходного состояния перед подключением к локальному процессу с помощью Kubernetes. Чтобы устранить эту проблему, см. [руководство по устранению неполадок][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Использование ведения журналов и диагностики

Журналы диагностики можно найти в каталоге во `Azure Dev Spaces` [ *временном* каталоге компьютера разработчика][azds-tmp-dir].

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
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/