---
title: Развертывание приложения в Kubernetes с помощью Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Развертывание микрослужбы в AKS с помощью Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 39fb7658140a2eda948cd0dc0e58d71b0b9a053b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710677"
---
# <a name="quickstart-develop-an-application-on-kubernetes-using-azure-dev-spaces"></a>Краткое руководство. Разработка приложения в Kubernetes с помощью Azure Dev Spaces
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

В этой статье описано, как использовать [пример приложения Azure Dev Spaces](https://github.com/Azure/dev-spaces), чтобы продемонстрировать применение Azure Dev Spaces.

Клонируйте приложение из GitHub и перейдите в каталог *dev-spaces/samples/nodejs/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Подготовка приложения

Чтобы запустить приложение в Azure Dev Spaces, вам потребуется документ Dockerfile и диаграмма Helm. Клиентские средства Azure Dev Spaces могут создавать все требуемые ресурсы на таких языках, как [Java][java-quickstart], [.NET core][netcore-quickstart] и [Node.js][nodejs-quickstart]. Во многих языках, например Go, PHP и Python, клиентские средства могут создавать диаграмму Helm, если предоставляется допустимый документ Dockerfile.

Создайте ресурсы диаграмм Docker и Helm для выполнения приложения в Kubernetes с помощью команды `azds prep`:

```cmd
azds prep --public
```

Вам нужно выполнить команду `prep` из каталога *dev-spaces/samples/nodejs/getting-started/webfrontend*, чтобы правильно создать ресурсы диаграмм Docker и Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Сборка и запуск кода в Kubernetes

Создайте код в AKS с помощью команды `azds up` и выполните его:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Вы можете увидеть выполнение службы, открыв общедоступный URL-адрес, который отображается в выходных данных команды `azds up`. В этом примере общедоступный URL-адрес — *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

Если остановить команду `azds up` с помощью сочетания клавиш *CTRL+C*, служба продолжит выполнятся в AKS, а общедоступный URL-адрес останется доступным.

## <a name="update-code"></a>Обновление кода

Чтобы развернуть обновленную версию службы, обновите любой файл в проекте и повторно выполните команду `azds up`. Например:

1. Если `azds up` по-прежнему выполняется, нажмите клавиши *CTRL+C*.
1. Измените [строку 13 в `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) следующим образом:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
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