---
title: Создание Функций Azure на Linux с помощью пользовательского образа
description: Узнайте, как создавать функции Azure под управлением пользовательского образа Linux.
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 54d7dc4e57991f6b773169f539a86fdc8451cbba
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950388"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Создание функции на Linux с помощью пользовательского образа

Решение "Функции Azure" позволяет размещать в Linux собственные функции в пользовательском контейнере. Кроме того, вы можете [использовать для размещения контейнер по умолчанию в службе приложений Azure](functions-create-first-azure-function-azure-cli-linux.md). Эта функциональность требует [среды выполнения Функций 2.x](functions-versions.md).

Из этого руководства вы узнаете, как развернуть функции в Azure в виде пользовательского образа Docker. Этот метод полезен, если вам нужно настроить встроенный образ контейнера. Пользовательский образ может быть удобен, если вы используете конкретную версию языка или особые зависимости и (или) конфигурации, не поддерживаемые в рамках встроенного образа. Поддерживаемые базовые образы для Функций Azure находятся в [репозитории базовых образов Функций Azure](https://hub.docker.com/_/microsoft-azure-functions-base). 

Из этого руководства вы узнаете, как с помощью Azure Functions Core Tools создать функцию в пользовательском образе Linux. Вы публикуете этот образ в приложении-функции Azure, которое было создано с помощью Azure CLI. Позже вы обновите функцию для подключения к Хранилищу очередей Azure. Вы также включите.  

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание приложения-функции и Dockerfile с помощью Core Tools.
> * Сборка пользовательского образа с помощью Docker.
> * Публикация пользовательского образа в реестре контейнеров.
> * Создание учетной записи хранения Azure.
> * Создание плана размещения "Премиум".
> * Развертывание приложения-функции из концентратора Docker.
> * Добавление параметров приложения в приложение-функцию.
> * Включение непрерывного развертывания.
> * Включите SSH-подключения к контейнеру.
> * Добавьте выходную привязку Хранилища очередей. 
> * Включение мониторинга Application Insights.

Описанные далее действия можно выполнять на компьютерах с Mac, Windows или Linux. 

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением этого примера вам понадобится следующее:

* Установка [основных инструментов Azure версии 2.x](functions-run-local.md#v2).

* Установка [Azure CLI]( /cli/azure/install-azure-cli). В этой статье требуется Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`.  
Можно также использовать [Azure Cloud Shell](https://shell.azure.com/bash).

* Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Создание локального проекта

Выполните следующую команду из командной строки, чтобы создать проект приложения-функции в папке `MyFunctionProj` текущего локального каталога. Для проекта Python [необходимо работать в виртуальном окружении](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional).

```bash
func init MyFunctionProj --docker
```

При включении параметра `--docker` для проекта создается dockerfile. Этот файл используется, чтобы создать пользовательский контейнер, в котором необходимо запустить проект. Используемый базовый образ зависит от выбранного языка среды выполнения рабочей роли.  

При появлении запроса выберите среду выполнения рабочей роли из следующих языков:

* `dotnet`. Создает проект библиотеки классов .NET Core (.csproj).
* `node`. Создает проект JavaScript.
* `python`. Создает проект Python.  

Чтобы перейти к новой папке проекта `MyFunctionProj`, используйте следующую команду.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Сборка из файла Docker

Просмотрите _Dockerfile_ в корневой папке проекта. В этом файле описана среда для запуска приложения-функции на платформе Linux. Следующий пример — Dockerfile. Он создает контейнер, который запускает приложение-функцию в среде выполнения рабочей роли JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Полный список поддерживаемых базовых образов для Функций Azure можно найти на [странице базового образа Функций Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Выполните команду `build`.

В корневой папке выполните команду [docker build](https://docs.docker.com/engine/reference/commandline/build/), указав имя `mydockerimage` и тег `v1.0.0`. Замените `<docker-id>` идентификатором вашей учетной записи Docker Hub. Эта команда отвечает за создание образа Docker для контейнера.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

После выполнения команды можно запустить новый контейнер локально.

### <a name="run-the-image-locally"></a>Локальный запуск образа
Чтобы проверить работоспособность образа, выполните образ Docker в локальном контейнере. Выполните команду [​​docker run](https://docs.docker.com/engine/reference/commandline/run/), указав имя и тег образа. Обязательно укажите порт с помощью аргумента `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Пока пользовательский образ выполняется в локальном контейнере Docker, откройте в браузере адрес <http://localhost:8080> и убедитесь, что приложение-функция и контейнер работают правильно.

![Выполните локальное тестирование приложения-функции.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> На этом этапе при попытке вызвать конкретную функцию HTTP выдается ответ с сообщением об ошибке HTTP 401. Это происходит потому, что функция выполняется в локальном контейнере, как если бы это была среда Azure, а это означает, что ключ функции является обязательным. Так как контейнер еще не опубликован в приложении-функции, ключ функции недоступен. Позже вы увидите, что при использовании Core Tools для публикации контейнера будут показаны ключи функции. Если вы хотите протестировать функцию, выполняемую в локальном контейнере, можно изменить [ключ авторизации](functions-bindings-http-webhook.md#authorization-keys) на `anonymous`. 

Когда вы закончите проверку приложения-функции, остановите обработку в контейнере. Теперь этот пользовательский образ следует передать в учетную запись концентратора Docker.

## <a name="push-to-docker-hub"></a>Отправка в Docker Hub

Реестр — это приложение, которое содержит образы и предоставляет образ служб и службы контейнеров. Чтобы сделать образ общедоступным, необходимо передать его в реестр. Docker Hub — это реестр образов Docker, который позволяет размещать ваши собственные репозитории, как общедоступные, так и частные.

Чтобы отправить образ, вам нужно выполнить вход в концентратор Docker с помощью команды [docker login](https://docs.docker.com/engine/reference/commandline/login/). Вместо `<docker-id>` укажите имя вашей учетной записи, а когда в консоли появится запрос, введите пароль. Другие возможности ввода пароля для концентратора Docker описаны в [документации по команде docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

При успешном выполнении входа появится соответствующее сообщение. После успешного входа примените команду [docker push](https://docs.docker.com/engine/reference/commandline/push/), чтобы передать образ в концентратор Docker.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

После завершения отправки можно использовать образ в качестве источника развертывания для нового приложения-функции в Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Создание плана "Премиум"

Размещение в Linux пользовательских контейнеров поддерживается в [выделенных планах (Службы приложений)](functions-scale.md#app-service-plan) и [планах "Премиум"](functions-premium-plan.md#features). В этом руководстве описано, как использовать план "Премиум", который можно масштабировать по мере необходимости. Дополнительные сведения о размещении см. в статье [Сравнение планов размещения для решения "Функции Azure"](functions-scale.md).

В следующем примере создается план "Премиум" с именем `myPremiumPlan` в ценовой категории **Эластичный премиум 1** (`--sku EP1`) в регионе "Западная часть США" (`-location WestUS`) в контейнере Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Создание приложения из образа

Приложение-функция управляет выполнением функций в плане размещения. Чтобы создать приложение-функцию из образа, размещенного в концентраторе Docker, используйте команду [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

В следующей команде замените `<app_name>` уникальным именем вашего приложения функции, а `<storage_name>` — именем учетной записи хранения. `<app_name>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. Как и раньше, `<docker-id>` обозначает имя учетной записи Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Параметр _deployment-container-image-name_ определяет образ, размещенный в концентраторе Docker, из которого нужно создать приложение-функцию. Используйте команду[az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show), чтобы просмотреть сведения об образе, используемом для развертывания. Используйте команду[az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set),чтобы развернуть из другого образа.

## <a name="configure-the-function-app"></a>Настройка приложения-функции

Функции нужно предоставить строку подключения для подключения к учетной записи хранения по умолчанию. Если вы публикуете пользовательский образ в учетной записи частного контейнера, задайте эти параметры приложения как переменные среды в файле Dockerfile, используя [инструкции ENV](https://docs.docker.com/engine/reference/builder/#env) или что-либо подобное.

В нашем случае `<storage_name>` — это имя созданной учетной записи хранения. Получите строку подключения, выполнив команду [az storage account show-connection-string](/cli/azure/storage/account). Добавьте эти параметры приложения в приложение-функцию с помощью команды [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
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

## <a name="verify-your-functions"></a>Проверка функций

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

Для созданной функции, активируемой с помощью HTTP, требуется [ключ функции](functions-bindings-http-webhook.md#authorization-keys) при вызове конечной точки. В настоящее время проще всего получить URL-адрес функции, включая ключ, на [портал Azure]. 

> [!TIP]
> Вы также можете получить ключи функций с помощью [API-интерфейсов управления ключами](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), которые требуют предоставления [маркера носителя для проверки подлинности](/cli/azure/account#az-account-get-access-token).

Найдите новое приложение-функцию на [портал Azure], введя его имя в поле **Поиск** в верхней части страницы и выбрав ресурс **Служба приложений**.

Выберите функцию **MyHttpTrigger** и последовательно щелкните **</> Получить URL-адрес функции** > **по умолчанию (ключ функции)**  > **Копировать**.

![Копирование URL-адреса функции с портала Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

В этом URL-адресе ключ функции является параметром запроса `code`. 

> [!NOTE]  
> Так как приложение-функция развертывается как контейнер, нельзя вносить изменения в код функции на портале. Вместо этого необходимо обновить проект в локальном контейнере и повторно опубликовать его в Azure.

Вставьте URL-адрес функции в адресную строку браузера. Добавьте значение строки запроса `&name=<yourname>` в конец этого URL-адреса и нажмите клавишу `Enter` на клавиатуре, чтобы выполнить этот запрос. В браузере должен отобразиться ответ, возращенный функцией.

Следующий пример демонстрирует ответ в браузере:

![Ответ функции в браузере.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

URL-адрес запроса включает ключ, который по умолчанию необходим для доступа к функции по протоколу HTTP. 

## <a name="enable-continuous-deployment"></a>Включение непрерывного развертывания

Одним из преимуществ использования контейнеров является поддержка непрерывного развертывания. Функции позволяют автоматически развертывать обновления при обновлении контейнера в реестре. Включите непрерывное развертывание с помощью команды [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config).

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Эта команда возвращает URL-адрес веб-перехватчика после включения непрерывного развертывания. Чтобы вернуть этот URL-адрес, вы также можете использовать команду [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url). 

Скопируйте URL-адрес развертывания и перейдите в репозиторий DockerHub. Выберите вкладку **Веб-перехватчики**, введите **Имя веб-перехватчика**, вставьте свой URL-адрес в **URL-адрес веб-перехватчика**, а затем выберите знак плюс ( **+** ).

![Добавление веб-перехватчика в репозиторий DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

При установленном веб-перехватчике любые обновления связанного образа в DockerHub приводят к тому, что приложение-функция скачивает и устанавливает последний образ.

## <a name="enable-ssh-connections"></a>Включение SSH-подключений

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. При включенном протоколе SSH вы можете подключиться к контейнеру с помощью расширенных инструментов Службы приложений (KUDU). Чтобы упростить подключение к контейнеру по SSH, служба "Функции" предоставляет базовый образ, в котором уже включен протокол SSH. 

### <a name="change-the-base-image"></a>Изменение базового образа

В Dockerfile добавьте строку `-appservice` к базовому образу в инструкции `FROM`, которая для проекта JavaScript выглядит следующим образом.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

Различия в двух базовых образах включают SSH-подключения к контейнеру. Эти различия подробно описаны в [учебнике по службам приложений ](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Перестроение и повторное развертывание образа

В корневой папке выполните команду [docker build](https://docs.docker.com/engine/reference/commandline/build/) как и раньше, замените `<docker-id>` идентификатором учетной записи Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Отправьте обновленный образ обратно в Docker Hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Обновленный образ повторно развертывается в приложении-функции.

### <a name="connect-to-your-container-in-azure"></a>Подключение к контейнеру в Azure

В браузере перейдите к следующей конечной точке `scm.` расширенных инструментов (KUDU) для контейнера приложения-функции, заменив `<app_name>` именем приложения-функции.

```
https://<app_name>.scm.azurewebsites.net/
```

Войдите в свою учетную запись Azure, а затем выберите вкладку **SSH**, чтобы создать SSH-подключение к контейнеру.

После установки подключения выполните команду `top`, чтобы просмотреть выполняющиеся в данный момент процессы. 

![Основная команда Linux, выполняемая в рамках сеанса SSH.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Запись в Хранилище очередей

Служба "Функции" позволяет подключать службы Azure и другие ресурсы к функциям без необходимости написания кода для интеграции. Эти *привязки*, которые представляют как входные, так и выходные данные, объявляются в определении функции. Данные привязок предоставляются функции в качестве параметров. *Триггер* является специальным типом входных привязок. Хотя функция обладает только одним триггером, она может состоять из нескольких входных и выходных привязок. Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

В этом разделе показано, как интегрировать функцию с очередью службы Хранилища Azure. Выходная привязка, которая была добавлена в эту функцию, записывает в сообщение очереди данные HTTP-запроса.

### <a name="download-the-function-app-settings"></a>Загрузка параметров приложения-функции

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Включение пакетов расширений

Так как вы используете выходную привязку Хранилища очередей, перед запуском проекта необходимо установить расширение привязок службы хранилища. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

За исключением триггеров HTTP и таймера, привязки реализованы в виде пакетов расширений. Выполните следующую команду [dotnet add package](/dotnet/core/tools/dotnet-add-package) в окне терминала, чтобы добавить пакет расширений службы хранилища в свой проект.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> При использовании Visual Studio для добавления этого пакета можно также применять диспетчер пакетов NuGet.

---

Теперь можно добавить выходную привязку хранилища в проект.

### <a name="add-an-output-binding"></a>Добавление выходной привязки

В службе "Функции" для каждого типа привязок требуется `direction`, `type` и уникальное `name`, которое определяется в файле function.json. Способ определения этих атрибутов зависит от языка приложения-функции.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Добавление кода, который использует выходную привязку

После определения привязки можно использовать `name` привязки для доступа к ней как к атрибуту в сигнатуре функции. После использования выходной привязки вам для проверки подлинности, получения ссылки на очередь или записи данных больше не потребуется код пакета SDK службы хранилища Azure. Вместо вас эти задачи будут выполнены выходной привязкой очереди и средой выполнения функции.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Обновление размещенного контейнера

В корневой папке выполните команду [docker build](https://docs.docker.com/engine/reference/commandline/build/) еще раз и обновите версию в теге до `v1.0.2`. Как и ранее, замените `<docker-id>` идентификатором учетной записи Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Отправьте обновленный образ обратно в репозиторий.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Проверка обновлений в Azure

Используйте тот же URL-адрес, что и в браузере, чтобы активировать функцию. Должен появится тот же ответ. Однако на этот раз строка, которая передается как параметр `name`, записывается в очередь хранилища `outqueue`.

### <a name="set-the-storage-account-connection"></a>Установка подключения к учетной записи хранилища

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Запрос в очередь хранилища

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда пользовательский контейнер успешно развернут в приложении-функции в Azure, ознакомьтесь со следующими статьями:

+ [Monitor Azure Functions](functions-monitoring.md) (Мониторинг Функций Azure)
+ [Azure Functions scale and hosting](functions-scale.md) (Масштабирование и размещение Функций Azure)
+ [Azure Functions on Kubernetes with KEDA](functions-kubernetes-keda.md) (Функции Azure на базе Kubernetes с KEDA)

[портал Azure]: https://portal.azure.com
