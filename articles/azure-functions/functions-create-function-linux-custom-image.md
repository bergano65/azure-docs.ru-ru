---
title: Создание функции в Linux из пользовательского образа (предварительная версия) | Документация Майкрософт
description: Узнайте, как создавать функции Azure под управлением пользовательского образа Linux.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: de15d1f8c268e80ac1659c53a141ec39cc6d3cb8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564815"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Создание функции в Linux из пользовательского образа (предварительная версия)

Решение "Функции Azure" позволяет размещать в Linux собственные функции в пользовательском контейнере. Кроме того, вы можете [использовать для размещения контейнер по умолчанию в службе приложений Azure](functions-create-first-azure-function-azure-cli-linux.md). Эта функция сейчас доступна в виде предварительной версии. Для ее работы требуется [среда выполнения Функций версии 2.0](functions-versions.md).

Из этого руководства вы узнаете, как развернуть функции в Azure в виде пользовательского образа Docker. Этот метод полезен, если вам нужно настроить встроенный образ контейнера службы приложений. Пользовательский образ может быть удобен, если вы используете конкретную версию языка или особые зависимости и (или) конфигурации, не поддерживаемые в рамках встроенного образа.

Из этого руководства вы узнаете, как с помощью Azure Functions Core Tools создать функцию в пользовательском образе Linux. Вы публикуете этот образ в приложении-функции Azure, которое было создано с помощью Azure CLI.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание приложения-функции и Dockerfile с помощью Core Tools.
> * Сборка пользовательского образа с помощью Docker.
> * Публикация пользовательского образа в реестре контейнеров.
> * Создание учетной записи хранения Azure.
> * Создание плана службы приложений Linux.
> * Развертывание приложения-функции из концентратора Docker.
> * Добавление параметров приложения в приложение-функцию.

Описанные далее действия можно выполнять на компьютерах с Mac, Windows или Linux.  

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением этого примера вам понадобится следующее:

* Установка [основных инструментов Azure версии 2.x](functions-run-local.md#v2).

* Установка [Azure CLI]( /cli/azure/install-azure-cli). В этой статье требуется Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`.  
Можно также использовать [Azure Cloud Shell](https://shell.azure.com/bash).

* Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Создание локального проекта приложения-функции

Выполните следующую команду из командной строки, чтобы создать проект приложения-функции в папке `MyFunctionProj` текущего локального каталога.

```bash
func init MyFunctionProj --docker
```

При включении параметра `--docker` для проекта создается dockerfile. Этот файл используется, чтобы создать пользовательский контейнер, в котором необходимо запустить проект. Используемый базовый образ зависит от выбранного языка среды выполнения рабочей роли.  

При появлении запроса выберите среду выполнения рабочей роли из следующих языков:

* `dotnet`. Создает проект библиотеки классов .NET (CSPROJ-файл).
* `node`. Создает проект JavaScript.

При выполнении команды вы увидите выходные данные примерно следующего содержания.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Чтобы перейти к новой папке проекта `MyFunctionProj`, используйте следующую команду.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Создание образа на основе файла Docker

Просмотрите _Dockerfile_ в корневой папке проекта. В этом файле описана среда для запуска приложения-функции на платформе Linux. Следующий пример — Dockerfile. Он создает контейнер, который запускает приложение-функцию в среде выполнения рабочей роли JavaScript (Node.js): 

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Когда вы размещаете образ в частном реестре контейнеров, не забудьте добавить в приложение-функцию параметры подключения, указав переменные среды (**ENV**) в Dockerfile. Мы не можем быть уверены, что для работы с руководством вы используете частный реестр, поэтому из соображений безопасности файл настроен таким образом, что параметры подключения нужно [добавить через Azure CLI после развертывания ](#configure-the-function-app).

### <a name="run-the-build-command"></a>Выполните команду `build`.
В корневой папке выполните команду [docker build](https://docs.docker.com/engine/reference/commandline/build/), указав имя `mydockerimage` и тег `v1.0.0`. Замените `<docker-id>` идентификатором вашей учетной записи Docker Hub. Эта команда отвечает за создание образа Docker для контейнера.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

При выполнении команды вы увидите выходные данные, которые предоставляются для среды выполнения рабочей роли JavaScript, примерно следующего содержания:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Локальное тестирование образа
Чтобы проверить работоспособность образа, выполните образ Docker в локальном контейнере. Выполните команду [​​docker run](https://docs.docker.com/engine/reference/commandline/run/), указав имя и тег образа. Обязательно укажите порт с помощью аргумента `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Пока пользовательский образ выполняется в локальном контейнере Docker, откройте в браузере адрес <http://localhost:8080> и убедитесь, что приложение-функция и контейнер работают правильно.

![Выполните локальное тестирование приложения-функции.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

При необходимости можно протестировать функцию снова, на этот раз в локальном контейнере с помощью следующего URL-адреса:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Когда вы закончите проверку приложения-функции, остановите обработку в контейнере. Теперь этот пользовательский образ следует передать в учетную запись концентратора Docker.

## <a name="push-the-custom-image-to-docker-hub"></a>Отправка пользовательского образа в концентратор Docker

Реестр — это приложение, которое содержит образы и предоставляет образ служб и службы контейнеров. Чтобы сделать образ общедоступным, необходимо передать его в реестр. Docker Hub — это реестр образов Docker, который позволяет размещать ваши собственные репозитории, как общедоступные, так и частные.

Чтобы отправить образ, вам нужно выполнить вход в концентратор Docker с помощью команды [docker login](https://docs.docker.com/engine/reference/commandline/login/). Вместо `<docker-id>` укажите имя вашей учетной записи, а когда в консоли появится запрос, введите пароль. Другие возможности ввода пароля для концентратора Docker описаны в [документации по команде docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

При успешном выполнении входа появится сообщение "Вход выполнен". После успешного входа примените команду [docker push](https://docs.docker.com/engine/reference/commandline/push/), чтобы передать образ в концентратор Docker.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Убедитесь, что передача завершилось успешно, проверив выходные данные команды.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Теперь вы можете использовать этот образ для развертывания приложения-функции в Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Создание плана службы приложений Linux

Сейчас размещение функций на платформе Linux не поддерживается в планах потребления. Приложения-контейнеры Linux необходимо размещать в плане службы приложений Linux. Дополнительные сведения о размещении см. в статье [Сравнение планов размещения для решения "Функции Azure"](functions-scale.md).

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>Создание и развертывание пользовательского образа

Выполнение функций происходит с помощью приложения-функции. Чтобы создать приложение-функцию из образа, размещенного в концентраторе Docker, используйте команду [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

В следующей команде замените `<app_name>` уникальным именем вашего приложения функции, а `<storage_name>` — именем учетной записи хранения. `<app_name>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. Как и раньше, `<docker-id>` обозначает имя учетной записи Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

После создания приложения-функции в Azure CLI отображаются следующие сведения:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Параметр _deployment-container-image-name_ определяет образ, размещенный в концентраторе Docker, из которого нужно создать приложение-функцию.

## <a name="configure-the-function-app"></a>Настройка приложения-функции

Функции нужно предоставить строку подключения для подключения к учетной записи хранения по умолчанию. Если вы публикуете пользовательский образ в учетной записи частного контейнера, задайте эти параметры приложения через переменные среды в файле Dockerfile, используя [инструкции ENV](https://docs.docker.com/engine/reference/builder/#env) или что-либо подобное.

В нашем случае `<storage_account>` — это имя созданной учетной записи хранения. Получите строку подключения, выполнив команду [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Добавьте эти параметры приложения в приложение-функцию с помощью команды [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Если контейнер является закрытым, необходимо задать также следующие параметры приложения:  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Для получения этих значений необходимо остановить и запустить приложение-функцию.

Теперь вы можете проверить, как выполняются ваши функции на платформе Linux в Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание приложения-функции и Dockerfile с помощью Core Tools.
> * Сборка пользовательского образа с помощью Docker.
> * Публикация пользовательского образа в реестре контейнеров.
> * Создание учетной записи хранения Azure.
> * Создание плана службы приложений Linux.
> * Развертывание приложения-функции из концентратора Docker.
> * Добавление параметров приложения в приложение-функцию.

Узнайте, как включить функцию непрерывной интеграции, встроенную в основную платформу службы приложений. Вы можете настроить приложение-функцию, чтобы контейнер повторно развертывался при обновлении образа в центре Docker.

> [!div class="nextstepaction"] 
> [Непрерывное развертывание с использованием платформы Azure "Веб-приложение для контейнеров"](../app-service/containers/app-service-linux-ci-cd.md).
