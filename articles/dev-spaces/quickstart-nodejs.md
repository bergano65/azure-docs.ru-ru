---
title: 'Отладка и итерация в Kubernetes: Visual Studio Code и Node.js'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Быстрая разработка в Kubernetes с использованием контейнеров, микрослужб и Node.js в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8f3ffb6a539937b6d4a03b8fdc808c033c371697
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325581"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Краткое руководство. Отладка и итерация с помощью Visual Studio Code, Node.js и Azure Dev Spaces в Kubernetes

Из этого руководства вы узнаете, как выполнить следующие задачи:

- Настройка Azure Dev Spaces с помощью управляемого кластера Kubernetes в Azure.
- Итеративная разработка кода в контейнерах с помощью Visual Studio Code.
- Отладка кода в среде разработки с помощью Visual Studio Code.

Azure Dev Spaces также позволяет выполнять отладку и итерацию с помощью:
- [Java и Visual Studio Code](quickstart-java.md);
- [.NET Core и Visual Studio Code](quickstart-netcore.md);
- [.NET Core и Visual Studio](quickstart-netcore-visualstudio.md).

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
- [Средство Visual Studio Code](https://code.visualstudio.com/download).
- Расширение [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) для Visual Studio Code.
- [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Вам нужно создать кластер AKS в [поддерживаемом регионе][supported-regions]. Следующие команды создают группу ресурсов *MyResourceGroup* и кластер AKS *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Включение Azure Dev Spaces в кластере AKS

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране. Следующая команда включает Dev Spaces в кластере *MyAKS* в группе *MyResourceGroup* и создает пространство разработки *по умолчанию*.

> [!NOTE]
> Команда `use-dev-spaces` также установит интерфейс командной строки Azure Dev Spaces, если он еще не установлен. Интерфейс командной строки Azure Dev Spaces невозможно установить в Azure Cloud Shell.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Получение примера кода приложения

В этой статье описано, как использовать [пример приложения Azure Dev Spaces](https://github.com/Azure/dev-spaces), чтобы продемонстрировать применение Azure Dev Spaces.

Клонируйте приложение из GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Подготовка примера приложения в Visual Studio Code

Откройте Visual Studio Code, выберите *Файл*, *Открыть*, а затем перейдите в каталог *dev-spaces/samples/nodejs/getting-started/webfrontend* и щелкните *Открыть*.

Теперь проект *webfrontend* открыт в Visual Studio Code. Для запуска приложения в области разработки создайте ресурсы диаграмм Docker и Helm, используя расширение Azure Dev Spaces в палитре команд.

Чтобы открыть палитру команд в Visual Studio Code, щелкните *Представление*, а затем — *Палитра команд*. Начните вводить `Azure Dev Spaces` и щелкните `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Подготовка файлов конфигурации для Azure Dev Spaces](./media/common/command-palette.png)

При появлении в Visual Studio Code запроса на настройку общедоступной конечной точки выберите `Yes`, чтобы включить общедоступную конечную точку.

![Выбор общедоступной конечной точки](media/common/select-public-endpoint.png)

Эта команда подготавливает проект для запуска в Azure Dev Spaces, создавая диаграмму Helm и Dockerfile. Она также создает каталог *.vscode* с конфигурацией отладки в корне проекта.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Сборка и запуск кода в Kubernetes из Visual Studio Code

Щелкните значок *Отладка* слева, а затем *Запустить сервер (AZDS)* вверху.

![Launch Server (Запустить сервер)](media/get-started-node/debug-configuration-nodejs.png)

Эта команда создает службы в Azure Dev Spaces. Окно *Терминал* внизу показывает выходные данные сборки и URL-адрес для службы, запущенной в Azure Dev Spaces. В *консоли отладки* показаны выходные данные журнала.

> [!Note]
> Если вы не видите никаких команд Azure Dev Spaces в *палитре команд*, убедитесь, что вы установили расширение [Visual Studio Code для Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Кроме того, убедитесь, что вы открыли каталог *dev-spaces/samples/nodejs/getting-started/webfrontend* в Visual Studio Code.

Вы увидите, что служба запущена, перейдя по общедоступному URL-адресу.

Щелкните *Отладка*, а затем выберите *Остановить отладку*, чтобы остановить отладчик.

## <a name="update-code"></a>Обновление кода

Чтобы развернуть обновленную версию службы, обновите любой файл в проекте и повторно выполните команду *Launch Server* (Запустить сервер). Например:

1. Если ваше приложение по-прежнему работает, нажмите кнопку *Debug* (Отладка), а затем — *Stop Debugging* (Остановить отладку), чтобы остановить ее.
1. Измените [строку 13 в `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) следующим образом:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Сохраните изменения.
1. Повторите команду *Launch Server* (Запустить сервер).
1. Перейдите к запущенной службе и просмотрите внесенные изменения.
1. Нажмите кнопку *Debug* (Отладка), а затем — *Stop Debugging* (Остановить отладку), чтобы остановить приложение.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Настройка и использование точек останова для отладки

Запуск службы осуществляется с помощью пункта *Запустить сервер (AZDS)* .

Вернитесь в представление *Explorer*, щелкнув *Представление*, а затем — *Explorer*. Откройте `server.js` и щелкните строку 13, чтобы расположить в ней курсор. Чтобы задать точку останова, нажмите клавишу *F9* или щелкните *Отладка* и *Переключить точку останова*.

Откройте службу в браузере. Вы увидите, что сообщение не отображается. Вернитесь в Visual Studio Code. Вы увидите, что строка 13 выделена. Заданная вами точка останова приостановила выполнение службы на строке 13. Чтобы возобновить работу службы, нажмите клавишу *F5* или щелкните *Отладка*, а затем *Продолжить*. Вернитесь в браузер. Вы увидите, что сообщение теперь отображается.

Во время выполнения службы в Kubernetes с присоединенным отладчиком у вас есть полный доступ к отладочным сведениям, включая стек вызовов, локальные переменные и данные об исключениях.

Удалите точку останова, поместив курсор в строке 13 в `server.js` и нажав клавишу *F9*.

Щелкните *Отладка*, а затем выберите *Остановить отладку*, чтобы остановить отладчик.

## <a name="update-code-from-visual-studio-code"></a>Обновление кода из Visual Studio Code

Измените режим отладки на *Подключить к серверу (AZDS)* и запустите службу:

![](media/get-started-node/attach-nodejs.png)

Эта команда создает службы в Azure Dev Spaces. Он также запускает процесс [nodemon](https://nodemon.io) в контейнере службы и присоединяет VS Code. Процесс *nodemon* осуществляет автоматический перезапуск при внесении изменений в исходный код, ускоряя внутренний цикл разработки так, как это происходит на локальном компьютере.

Перейдите к запущенной службе с помощью браузера и поработайте с ней.

Пока служба выполняется, вернитесь в VS Code и обновите строку 13 в `server.js`. Например:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Сохраните файл и вернитесь к службе в браузере. Поработайте со службой и проверьте, отображается ли обновленное сообщение.

Во время выполнения *nodemon* процесс Node автоматически перезапускается сразу же после обнаружения изменений в коде. Этот автоматический процесс перезапуска аналогичен действиям редактирования и перезапуска службы на локальном компьютере. При этом предоставляются возможности внутреннего цикла разработки.

## <a name="clean-up-your-azure-resources"></a>Очистка ресурсов Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Работа с несколькими контейнерами и командной разработкой](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations
