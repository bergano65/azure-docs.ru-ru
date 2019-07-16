---
title: Развертывание приложения с помощью Dockerfile в Kubernetes используя Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Развертывание микрослужбы с помощью Dockerfile в AKS используя Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: a272047955564ba745d4cf521e560af9d24cfb9e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710697"
---
# <a name="quickstart-develop-an-application-with-a-dockerfile-on-kubernetes-using-azure-dev-spaces"></a>Краткое руководство. Разработка приложения с помощью Dockerfile в Kubernetes используя Azure Dev Spaces
Из этого руководства вы узнаете, как выполнить следующие задачи:

- Настройка Azure Dev Spaces с помощью управляемого кластера Kubernetes в Azure.
- Разработка и выполнение кода в контейнерах с помощью командной строки.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
- [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Вам нужно создать кластер AKS в [поддерживаемом регионе][supported-regions]. Следующие команды создают группу ресурсов *MyResourceGroup* и кластер AKS *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Включение Azure Dev Spaces в кластере AKS

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране. Следующая команда включает Dev Spaces в кластере *MyAKS* в группе *MyResourceGroup* и создает пространство разработки *по умолчанию*.

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

В этой статье описано, как использовать [пример приложения Azure Dev Spaces](https://github.com/Azure/dev-spaces), чтобы продемонстрировать применение Azure Dev Spaces. Образец приложения написан на Go и до тех пор, пока вы предоставляете допустимый Dockerfile для контейнеризации приложения и его запуска в AKS, вы можете использовать почти любой язык.

Клонируйте приложение из GitHub и перейдите в каталог *dev-spaces/samples/golang/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/golang/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Подготовка приложения

Чтобы запустить приложение в Azure Dev Spaces, вам потребуется документ Dockerfile и диаграмма Helm. Клиентские средства Azure Dev Spaces могут создавать все требуемые ресурсы на таких языках, как [Java][java-quickstart], [.NET core][netcore-quickstart] и [Node.js][nodejs-quickstart]. Во многих языках, например Go, PHP и Python, клиентские средства могут создавать диаграмму Helm, если предоставляется допустимый документ Dockerfile.

Образец приложения содержит допустимый Dockerfile. Чтобы создать ресурсы диаграмм Helm для выполнения приложения в Kubernetes, используйте команду `azds prep`.

```cmd
azds prep --public
```

Чтобы правильно создать ресурсы диаграмм Helm, нужно выполнить команду `prep` в каталоге *dev-spaces/samples/golang/getting-started/webfrontend*.

# <a name="build-and-run-code-in-kubernetes"></a>Сборка и запуск кода в Kubernetes

Создайте код в AKS с помощью команды `azds up` и выполните его:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...3s
Installing Helm chart...2s
Waiting for container image build...39s
Building container image...
Step 1/7 : FROM golang:1.10
Step 2/7 : EXPOSE 80
Step 3/7 : WORKDIR /go/src
Step 4/7 : COPY src .
Step 5/7 : WORKDIR /go/src/app
Step 6/7 : RUN go install app
Step 7/7 : ENTRYPOINT /go/bin/app
Built container image in 41s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:50199
...
```

Открыв общедоступный URL-адрес и перейдя по пути `/api` вы увидите, что служба запущена. Общедоступный URL-адрес отображается в выходных данных команды `azds up`. В этом примере общедоступным URL-адресом является `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/` и вам нужно будет перейти по адресу `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/api`.

Если остановить команду `azds up` с помощью сочетания клавиш *CTRL+C*, служба продолжит выполнятся в AKS, а общедоступный URL-адрес останется доступным.

## <a name="update-code"></a>Обновление кода

Чтобы развернуть обновленную версию службы, обновите любой файл в проекте и повторно выполните команду `azds up`. Например:

1. Если `azds up` по-прежнему выполняется, нажмите клавиши *CTRL+C*.
1. Измените [строку 29 в `src/app/main.go`](https://github.com/Azure/dev-spaces/blob/master/samples/golang/getting-started/webfrontend/src/app/main.go#L29) на:
    
    ```golang
        fmt.Fprintf(w, "Hello from webfrontend in Azure")
    ```

1. Сохраните изменения.
1. Повторно выполните команду `azds up`:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Перейдите к запущенной службе и просмотрите внесенные изменения.
1. Чтобы остановить команду `azds up`, нажмите клавиши *CTRL+C*.

## <a name="clean-up-your-azure-resources"></a>Очистка ресурсов Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-quickstart]


[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations