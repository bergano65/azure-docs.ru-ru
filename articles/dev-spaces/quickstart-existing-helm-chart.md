---
title: Разработка приложения с использованием существующего чарта Helm на Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: В этом кратком руководстве показано, как использовать Azure Dev Spaces и командную строку для разработки приложения с использованием существующего чарта Helm в службе Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 7d1d782203401607d3909d79d1f0f3a499712c32
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414315"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Краткое руководство. Разработка приложения с использованием существующего чарта Helm на Kubernetes — Azure Dev Spaces
Из этого руководства вы узнаете, как выполнить следующие задачи:

- Настройка Azure Dev Spaces с помощью управляемого кластера Kubernetes в Azure.
- Запуск приложения с использованием существующего чарта Helm в AKS с помощью Azure Dev Spaces в командной строке.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
- [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Вам нужно создать кластер AKS в [поддерживаемом регионе][supported-regions]. Следующие команды создают группу ресурсов *MyResourceGroup* и кластер AKS *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Включение Azure Dev Spaces в кластере AKS

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране. Следующая команда включает Dev Spaces для кластера *MyAKS* в группе *MyResourceGroup* и создает пространство разработки с именем *dev*.

> [!NOTE]
> Команда `use-dev-spaces` также установит интерфейс командной строки Azure Dev Spaces, если он еще не установлен. Интерфейс командной строки Azure Dev Spaces невозможно установить в Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Получение примера кода приложения

В этой статье описано, как использовать [пример приложения Azure Dev Spaces](https://github.com/Azure/dev-spaces), чтобы продемонстрировать применение Azure Dev Spaces.

Клонируйте приложение из GitHub и перейдите в каталог *dev-spaces/samples/python/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Подготовка приложения

Чтобы запустить приложение в Azure Dev Spaces, вам потребуется документ Dockerfile и диаграмма Helm. Клиентские средства Azure Dev Spaces могут создавать все требуемые ресурсы на таких языках, как [Java][java-quickstart], [.NET Core][netcore-quickstart] и [Node.js][nodejs-quickstart]. Во многих языках, например Go, PHP и Python, клиентские средства могут создавать диаграмму Helm, если предоставляется допустимый документ Dockerfile. В этом случае пример приложения имеет существующий файл Dockerfile и чарт Helm.

Создайте конфигурацию для запуска приложения с использованием Azure Dev Spaces и существующего чарта Helm и Dockerfile с помощью команды `azds prep`:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Чтобы правильно создать ресурсы чарт Helm, нужно выполнить команду `prep` в каталоге *dev-spaces/samples/python/getting-started/webfrontend* и указать местоположение чарта Helm с помощью `--chart`.

> [!NOTE]
> Вы можете увидеть следующее предупреждение: *ПРЕДУПРЕЖДЕНИЕ: не удалось создать файла Docker из-за неподдерживаемого языка.* при запуске `azds prep`. Команда `azds prep` попытается создать [Dockerfile и чарт Helm](how-dev-spaces-works-prep.md#prepare-your-code) для проекта, но не перезапишет существующие файлы Dockerfile и диаграммы Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Сборка и запуск кода в Kubernetes

Создайте код в AKS с помощью команды `azds up` и выполните его:

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

Вы можете увидеть выполнение службы, открыв общедоступный URL-адрес, который отображается в выходных данных команды `azds up`. В этом примере используется такой общедоступный URL-адрес: `http://dev.service.1234567890abcdef1234.eus.azds.io/`.

> [!NOTE]
> При переходе к службе во время выполнения `azds up` сведения о трассировке HTTP-запросов также отображаются в выходных данных команды `azds up`. Такая трассировка поможет вам в устранении проблем и отладке службы. Ее можно отключить с помощью команды `--disable-http-traces` при запуске `azds up`.

Если остановить команду `azds up` с помощью сочетания клавиш *CTRL+C*, служба продолжит выполнятся в AKS, а общедоступный URL-адрес останется доступным.

## <a name="update-code"></a>Обновление кода

Чтобы развернуть обновленную версию службы, обновите любой файл в проекте и повторно выполните команду `azds up`. Пример:

1. Если `azds up` по-прежнему выполняется, нажмите клавиши *CTRL+C*.
1. Измените [строку 13 в `webfrontend.py`](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) следующим образом:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Сохраните изменения.
1. Повторно выполните команду `azds up`:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Перейдите к запущенной службе и просмотрите внесенные изменения.
1. Чтобы остановить команду `azds up`, нажмите клавиши *CTRL+C*.

## <a name="clean-up-your-azure-resources"></a>Очистка ресурсов Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service