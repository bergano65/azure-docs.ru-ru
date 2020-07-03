---
title: 'Отладка и итерация в Kubernetes: Visual Studio Code и Node.js'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: В этом кратком руководстве описано, как использовать Azure Dev Spaces и Visual Studio Code для отладки и быстрого выполнения итерации приложения Node.js в службе Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 18171a2f8d13bfcf3df76b1453c39c59cab89d7c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80240197"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Краткое руководство. Отладка и итерация с помощью Visual Studio Code, Node.js и Azure Dev Spaces в Kubernetes

В этом кратком руководстве вы настроите Azure Dev Spaces с управляемой средой кластера Kubernetes и используете приложение Node.js в Visual Studio Code для итеративной разработки и отладки кода в контейнерах. Azure Dev Spaces позволяет выполнять отладку и тестирование всех компонентов приложения в службе Azure Kubernetes (AKS) с минимальной настройкой компьютера для разработки. 

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно. 
- [Последняя версия Node.js](https://nodejs.org/download/).
- [Visual Studio Code](https://code.visualstudio.com/download).
- Расширение [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) для Visual Studio Code.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Вам нужно создать кластер AKS в [поддерживаемом регионе][supported-regions]. Следующие команды создают группу ресурсов *MyResourceGroup* и кластер AKS *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Включение Azure Dev Spaces в кластере AKS

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране. Следующая команда включает Dev Spaces в кластере *MyAKS* в группе *MyResourceGroup* и создает пространство разработки *по умолчанию*.

> [!NOTE]
> Команда `use-dev-spaces` также установит интерфейс командной строки Azure Dev Spaces, если он еще не установлен. Интерфейс командной строки Azure Dev Spaces невозможно установить в Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
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

Откройте Visual Studio Code, выберите **Файл**, **Открыть**, а затем перейдите в каталог *dev-spaces/samples/nodejs/getting-started/webfrontend* и выберите **Открыть**.

Теперь проект *webfrontend* открыт в Visual Studio Code. Для запуска приложения в области разработки создайте ресурсы диаграмм Docker и Helm, используя расширение Azure Dev Spaces в палитре команд.

Чтобы открыть палитру команд в Visual Studio Code, выберите **Представление**, а затем — **Палитра команд**. Начните вводить `Azure Dev Spaces` и выберите **Azure Dev Spaces. Подготовка файлов конфигурации для Azure Dev Spaces**.

![Подготовка файлов конфигурации для Azure Dev Spaces](./media/common/command-palette.png)

При появлении в Visual Studio Code запроса на настройку общедоступной конечной точки выберите `Yes`, чтобы включить общедоступную конечную точку.

![Выбор общедоступной конечной точки](media/common/select-public-endpoint.png)

Эта команда подготавливает проект для запуска в Azure Dev Spaces, создавая диаграмму Helm и Dockerfile. Она также создает каталог *.vscode* с конфигурацией отладки в корне проекта.

> [!TIP]
> Azure Dev Spaces использует [Dockerfile и диаграмму Helm](how-dev-spaces-works-prep.md#prepare-your-code) проекта для сборки и выполнения кода. Но вы можете изменить эти файлы, если нужно определить другой способ сборки и запуска проекта.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Сборка и запуск кода в Kubernetes из Visual Studio Code

Выберите значок **Отладка** слева, а затем **Launch Server (AZDS)** (Запустить программу на Java (AZDS)) сверху.

![Launch Server (Запустить сервер)](media/get-started-node/debug-configuration-nodejs.png)

Эта команда создает службы в Azure Dev Spaces. Окно **Терминал** внизу показывает выходные данные сборки и URL-адрес для службы, запущенной в Azure Dev Spaces. В **консоли отладки** показаны выходные данные журнала.

> [!Note]
> Если вы не видите никаких команд Azure Dev Spaces в **палитре команд**, убедитесь, что вы установили расширение [Visual Studio Code для Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Кроме того, убедитесь, что вы открыли каталог *dev-spaces/samples/nodejs/getting-started/webfrontend* в Visual Studio Code.

Вы увидите, что служба запущена, перейдя по общедоступному URL-адресу.

Выберите **Отладка**, а затем выберите **Остановить отладку**, чтобы остановить отладчик.

## <a name="update-code"></a>Обновление кода

Чтобы развернуть обновленную версию службы, обновите любой файл в проекте и повторно выполните команду **Launch Server** (Запустить сервер). Пример:

1. Если ваше приложение по-прежнему работает, выберите пункт **Отладка**, а затем — **Остановить отладку**, чтобы остановить его.
1. Измените [строку 13 в `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) следующим образом:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Сохраните изменения.
1. Повторите команду **Launch Server** (Запустить сервер).
1. Перейдите к запущенной службе и просмотрите внесенные изменения.
1. Выберите пункт **Отладка**, а затем **Остановить отладку**, чтобы остановить приложение.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Настройка и использование точек останова для отладки

Запуск службы осуществляется с помощью пункта **Запустить сервер (AZDS)** .

Вернитесь в представление Explorer, выбрав **Представление**, а затем — **Explorer**. Откройте *server.js* и щелкните строку 13, чтобы расположить в ней курсор. Чтобы задать точку останова, нажмите клавишу **F9** или выберите пункт **Отладка** и **Переключить точку останова**.

Откройте службу в браузере. Вы увидите, что сообщение не отображается. Вернитесь в Visual Studio Code. Вы увидите, что строка 13 выделена. Заданная вами точка останова приостановила выполнение службы на строке 13. Чтобы возобновить работу службы, нажмите клавишу **F5** или выберите пункт **Отладка**, а затем **Продолжить**. Вернитесь в браузер. Вы увидите, что сообщение теперь отображается.

Во время выполнения службы в Kubernetes с присоединенным отладчиком у вас есть полный доступ к отладочным сведениям, включая стек вызовов, локальные переменные и данные об исключениях.

Удалите точку останова, поместив курсор в строке 13 в *server.js* и нажав клавишу **F9**.

Выберите **Отладка**, а затем выберите **Остановить отладку**, чтобы остановить отладчик.

## <a name="update-code-from-visual-studio-code"></a>Обновление кода из Visual Studio Code

Измените режим отладки на **Подключить к серверу (AZDS)** и запустите службу:

![](media/get-started-node/attach-nodejs.png)

Эта команда создает службы в Azure Dev Spaces. Он также запускает процесс [nodemon](https://nodemon.io) в контейнере службы и присоединяет VS Code. Процесс *nodemon* осуществляет автоматический перезапуск при внесении изменений в исходный код, ускоряя внутренний цикл разработки так, как это происходит на локальном компьютере.

Перейдите к запущенной службе с помощью браузера и поработайте с ней.

Пока служба выполняется, вернитесь в VS Code и обновите строку 13 в *server.js*. Пример:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Сохраните файл и вернитесь к службе в браузере. Поработайте со службой и проверьте, отображается ли обновленное сообщение.

Во время выполнения *nodemon* процесс Node автоматически перезапускается сразу же после обнаружения изменений в коде. Этот автоматический процесс перезапуска аналогичен действиям редактирования и перезапуска службы на локальном компьютере. При этом предоставляются возможности внутреннего цикла разработки.

## <a name="clean-up-your-azure-resources"></a>Очистка ресурсов Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Работа с несколькими контейнерами и командной разработкой](multi-service-nodejs.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
