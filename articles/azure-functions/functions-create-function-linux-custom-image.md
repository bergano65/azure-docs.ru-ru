---
title: Создание Функций Azure на Linux с помощью пользовательского образа
description: Узнайте, как создавать функции Azure под управлением пользовательского образа Linux.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223731"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Создание функции на Linux с помощью пользовательского контейнера

В этом руководстве вы создадите и развернете код Python в Функциях Azure в качестве пользовательского контейнера Docker, используя базовый образ Linux. Пользовательский образ может быть удобен, если вы используете конкретную версию языка или особые зависимости или конфигурации, не поддерживаемые в рамках встроенного образа.

Вы также можете использовать контейнер Службы приложений Azure по умолчанию, как описано в статье [Краткое руководство. Создание функции, выполняемой в Linux, с помощью инструментов командной строки](functions-create-first-azure-function-azure-cli-linux.md). Поддерживаемые базовые образы для Функций Azure находятся в [репозитории базовых образов Функций Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

В этом руководстве описано следующее.

> [!div class="checklist"]
> * Создание приложения-функции и Dockerfile с помощью Azure Functions Core Tools.
> * Сборка пользовательского образа с помощью Docker.
> * Публикация пользовательского образа в реестре контейнеров.
> * Создание вспомогательных ресурсов в Azure для приложения-функции
> * Развертывание приложения-функции из концентратора Docker.
> * Добавление параметров приложения в приложение-функцию.
> * Включение непрерывного развертывания.
> * Включите SSH-подключения к контейнеру.
> * Добавьте выходную привязку Хранилища очередей. 

Этот учебник можно запускать на любом компьютере под управлением Windows, Mac OS или Linux. Завершение этого учебника приведет к расходам в несколько долларов США в вашей учетной записи Azure.

## <a name="prerequisites"></a>предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 или более поздней версии
- [Azure CLI](/cli/azure/install-azure-cli) 2.0.77 или более поздней версии
- [Среда выполнения Функций Azure версии 2](functions-versions.md)
- Следующие компоненты языковой среды выполнения:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core версии 2.2.x или более поздней](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 — 64-разрядная версия](https://www.python.org/downloads/release/python-3610/) или [Python 3.7 — 64-разрядная версия](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- [Идентификатор Docker](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Проверка предварительных условий

1. В окне терминала или командном окне запустите `func --version`, чтобы убедиться, что используется версия Azure Functions Core Tools 2.7.1846 или более поздняя.
1. Выполните команду `az --version`, чтобы проверить, является ли версия Azure CLI версией 2.0.76 или более поздней.
1. Выполните команду `az login`, чтобы войти в Azure и проверить активную подписку.
1. Запустите `docker login`, чтобы войти в Docker. Эта команда завершается ошибкой, если Docker не запущен. В таком случае запустите Docker и повторите команду.

## <a name="create-and-test-the-local-functions-project"></a>Создание и тестирование локального проекта службы "Функции"

1. В терминале или командной строке создайте папку для этого учебника в соответствующем расположении, а затем перейдите в эту папку.

1. Следуйте инструкциям в разделе [Создание и активация виртуальной среды](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv), чтобы создать виртуальную среду, необходимую для целей для этого учебника.

1. Чтобы создать проект приложения-функции в папке с именем `LocalFunctionsProject`, выполните следующую команду для выбранного языка. Параметр `--docker` создает `Dockerfile` для проекта, который определяет настраиваемый контейнер, который подходит для Функций Azure и выбранной среды выполнения.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Перейдите в папку проекта:

    ```
    cd LocalFunctionsProject
    ```
    
1. Добавьте функцию в проект с помощью следующей команды, где аргумент `--name` — уникальное имя функции, а аргумент `--template` указывает триггер функции. С помощью `func new` создайте вложенную папку с именем функции, которая содержит файл кода на выбранном для проекта языке и файл конфигурации *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Чтобы протестировать функцию локально, запустите локальный узел среды выполнения Функций Azure в папке *LocalFunctionsProject*.
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Когда в выходных данных отобразится конечная точка `HttpExample`, перейдите по `http://localhost:7071/api/HttpExample?name=Functions`. Браузер должен отображать сообщение, например "Hello, Functions" (зависит от выбранного языка программирования).

1. Чтобы остановить хост, используйте клавиши **Ctrl**-**C**.

## <a name="build-the-container-image-and-test-locally"></a>Создание образа контейнера и тестирование его локально

1. (Необязательно) Изучите *файл Docker" в папке *LocalFunctionsProj*. В этом Dockerfile описана среда, требуемая для запуска приложения-функции на платформе Linux. 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > Полный список поддерживаемых базовых образов для Функций Azure можно найти на [странице базового образа Функций Azure](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. В папке *LocalFunctionsProject* выполните команду [docker build](https://docs.docker.com/engine/reference/commandline/build/), указав имя, `azurefunctionsimage`, и тег `v1.0.0`. Замените `<docker_id>` идентификатором вашей учетной записи Docker Hub. Эта команда отвечает за создание образа Docker для контейнера.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    После выполнения команды можно запустить новый контейнер локально.
    
1. Чтобы протестировать сборку, запустите образ в локальном контейнере, используя команду [docker run](https://docs.docker.com/engine/reference/commandline/run/), еще раз заменив `<docker_id>` идентификатором Docker и добавив аргумент портов `-p 8080:80`.

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. После того, как образ будет запущен в локальном контейнере, откройте браузер на `http://localhost:8080`, который должен отобразить изображение-заполнитель, показанный ниже. На этом этапе отображается образ, так как функция выполняется в локальном контейнере, как в Azure. Это означает, что функция защищена ключом доступа, как определено в *function.json* со свойством `"authLevel": "function"`. Однако контейнер еще не опубликован в приложении-функции в Azure, поэтому ключ функции еще недоступен. Если вы хотите выполнить тестирование локально, закройте docker, измените свойство авторизации на `"authLevel": "anonymous"`, перестройте образ и перезапустите docker. Затем сбросьте `"authLevel": "function"` в *function.json*. Дополнительные сведения см. в разделе [Ключи авторизации](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Изображение-заполнитель указывает, что контейнер запущен локально](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Когда вы закончите проверку приложения-функции в контейнере, остановите docker, нажав **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Отправка образа в Docker Hub

Docker Hub — это реестр контейнеров, который содержит образы и предоставляет службы контейнеров и образов. Чтобы предоставить общий доступ к образу, который включает развертывание в Azure, его необходимо отправить в реестр.

1. Если вы еще не вошли в Docker, сделайте это с помощью команды [docker login](https://docs.docker.com/engine/reference/commandline/login/), заменив `<docker_id>` идентификатором Docker. Эта команда запрашивает имя и пароль для входа. При успешном выполнении входа появится соответствующее сообщение.

    ```
    docker login
    ```
    
1. После успешного входа примените команду [docker push](https://docs.docker.com/engine/reference/commandline/push/), чтобы передать образ в Docker Hub, и снова замените `<docker_id>` идентификатором Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. В зависимости от скорости сети передача образа в первый раз может занять несколько минут (отправка последующих изменений выполняется гораздо быстрее). Во время ожидания вы можете перейти к следующему разделу и создать ресурсы Azure в другом терминале.

## <a name="create-supporting-azure-resources-for-your-function"></a>Создание вспомогательных ресурсов Azure для функции

Чтобы развернуть код функции в Azure, необходимо создать три ресурса:

- группу ресурсов — логический контейнер связанных ресурсов;
- учетную запись хранения Azure — для сохранения состояния и других сведений о проектах;
- приложение-функцию Azure — среду для выполнения кода функций. Оно сопоставляется с локальным проектом функций и позволяет группировать функции в логические единицы, чтобы упростить развертывание, масштабирование и совместное использование ресурсов, а также управление ими.

Для создания этих элементов используются команды интерфейса командной строки Azure. Каждая команда предоставляет выходные данные JSON после завершения.

1. Войдите в Azure с помощью команды [az login](/cli/azure/reference-index#az-login).

    ```azurecli
    az login
    ```
    
1. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). В следующем примере создается группа ресурсов с именем `AzureFunctionsContainers-rg` в регионе `westeurope`. (Группу ресурсов и ресурсы целесообразно создавать в ближайшем к вам регионе. Для этого используйте команду `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Вы не можете разместить приложения Windows и Linux в одной группе ресурсов. Если у вас есть группа ресурсов `AzureFunctionsContainers-rg` с приложением-функцией Windows или веб-приложением, необходимо использовать другую группу ресурсов.
    
1. В группе ресурсов создайте учетную запись хранения общего назначения и регион с помощью команды [az storage account create](/cli/azure/storage/account#az-storage-account-create). В следующем примере замените `<storage_name>` подходящим глобально уникальным именем приложения. Имена должны содержать от трех до 24 символов и только в нижнем регистре. `Standard_LRS` обозначает типичную учетную запись общего назначения.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    В этом учебнике с учетной записи хранения будет взиматься плата лишь в несколько центов США.
    
1. Используйте команду, чтобы создать план "Премиум" для Функций Azure с именем `myPremiumPlan` в ценовой категории **эластичный Premium 1** (`--sku EP1`) в регионе Западной Европы (`-location westeurope`, или используйте подходящий регион вблизи) и в контейнере Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Размещение пользовательских контейнеров в Linux поддерживается в [Выделенных планах (Служба приложений)](functions-scale.md#app-service-plan) и [планах цен. категории "Премиум"](functions-premium-plan.md#features). Здесь мы используем план ценовой категории "Премиум", который можно масштабировать по мере необходимости. Дополнительные сведения о размещении см. в статье [Сравнение планов размещения для решения "Функции Azure"](functions-scale.md). Чтобы вычислить затраты, см. [Цены на Функции Azure](https://azure.microsoft.com/pricing/details/functions/).

    Эта команда также подготавливает связанный экземпляр Application Insights Azure в той же группе ресурсов. Его можно использовать для мониторинга приложения-функции и просмотра журналов. Дополнительные сведения см. в разделе [Мониторинг функций Azure](functions-monitoring.md). Этот экземпляр не создает затраты, пока вы не активируете его.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Создание и настройка приложения-функции в Azure с помощью образа

Приложение-функция в Azure управляет выполнением функций в плане размещения. В этом разделе вы используете ресурсы Azure из предыдущего раздела, чтобы создать приложение-функцию на основе образа в Docker Hub и настроите его с помощью строки подключения к службе хранилища Azure.

1. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create). В следующем примере замените `<storage_name>` именем, использованным в предыдущем разделе для учетной записи хранения. Также замените `<app_name>` подходящим уникальным именем приложения, а `<docker_id>` — идентификатором Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Параметр *Deployment-Container-Image-Name* указывает образ, используемый для приложения-функции. Чтобы просмотреть сведения об образе, используемом для развертывания, можно использовать команду[az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show). Кроме того вы можете использовать команду[az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set),чтобы развернуть другой образ.

1. Получите строку подключения для учетной записи хранения, созданной с помощью команды [az storage account show-connection-string](/cli/azure/storage/account), назначив ее переменной оболочки `storageConnectionString`.

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Добавьте этот параметр в приложение-функцию, выполнив команду [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). В следующей команде замените `<app_name>` именем приложения-функции, а `<connection_string>` — строкой подключения из предыдущего шага (длинная зашифрованная строка, которая начинается с "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Теперь функция может использовать эту строку подключения для доступа к учетной записи хранения.

> [!TIP]
> В bash переменную оболочки можно использовать для записи строки подключения вместо использования буфера обмена. Сначала используйте следующую команду, чтобы создать переменную со строкой подключения.
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Затем используйте переменную во второй команде.
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Если вы публикуете пользовательский образ в закрытой учетной записи контейнера, вместо этого для строки подключения следует использовать переменные среды в Dockerfile. Дополнительные сведения см. в инструкции по [ENV](https://docs.docker.com/engine/reference/builder/#env). Необходимо также задать переменные `DOCKER_REGISTRY_SERVER_USERNAME` и `DOCKER_REGISTRY_SERVER_PASSWORD`. Чтобы использовать значения, необходимо перестроить образ, отправить его в реестр, а затем перезапустить приложение-функцию в Azure.

## <a name="verify-your-functions-on-azure"></a>Проверка функций в Azure

Теперь, когда образ развернут в приложении-функции в Azure, вы можете вызывать функцию с помощью запросов HTTP. Так как определение *function.json* содержит свойство `"authLevel": "function"`, необходимо сначала получить ключ доступа (также называемый "ключ функции") и включить его в качестве параметра URL-адреса в любые запросы к конечной точке.

1. Получите URL-адрес функции с помощью ключа доступа (функции), используя портал Azure или с помощью Azure CLI с командой `az rest`.)

    # <a name="portal"></a>[Портал](#tab/portal)

    1. Войдите в портал Azure, а затем найдите приложение-функцию, введя имя приложения-функции в поле **Поиск** в верхней части страницы. В результатах выберите ресурс **Службы приложений Azure**.

    1. Выберите имя функции из панели навигации слева в разделе **Functions (Read Only)** (Функции (только для чтения)).

    1. В области результатов выберите **</> Получить URL-адрес функции**.
    
        ![Команда "Получить URL-адрес функции" на портале Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Во всплывающем окне выберите **default (Function key)** (по умолчанию (ключ функции)), а затем **Копировать**. Ключ — это строка символов, следующая после `?code=`.

        ![Копирование URL-адреса функции с портала Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Так как приложение-функция развертывается как контейнер, нельзя вносить изменения в код функции на портале. Вместо этого необходимо обновить проект в локальном образе, снова отправить образ в реестр, а затем повторно развернуть его в Azure. Непрерывное развертывание можно настроить в более позднем разделе.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Создайте строку URL-адреса в следующем формате, заменив `<subscription_id>`, `<resource_group>` и `<app_name>` идентификатором подписки Azure, группой ресурсов приложения-функции и именем приложения-функции соответственно:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Например, URL-адрес может выглядеть следующим образом.

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Для удобства, вместо этого, URL-адрес можно назначить переменной среды и использовать его в команде `az rest`.
    
    1. Выполните следующую команду `az rest` (доступна в Azure CLI версии 2.0.77 и более поздних версиях), заменив `<uri>` строкой URI из последнего шага, включая кавычки.

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Выходные данные команды — это ключ функции. После этого полным URL-адресом функции будет `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, который заменит `<app_name>`, `<function_name>` и `<key>` конкретными значениями.
    
        > [!NOTE]
        > Полученный здесь ключ — это ключ *узла*, который работает для всех функций в приложении-функции; метод, показанный для портала, получает ключ только для одной функции.

    ---

1. Вставьте URL-адрес функции в адресную строку браузера, добавив параметр `&name=Azure` в конец этого URL-адреса. В браузере должен появиться текст, например "Hello Azure".

    ![Ответ функции в браузере.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Чтобы проверить авторизацию, удалите из URL-адреса параметр code= и убедитесь, что в функции не получен ответ.


## <a name="enable-continuous-deployment-to-azure"></a>Включение непрерывного развертывания в Azure

Для Функций Azure можно настроить автоматическое обновление развертывания образа при каждом обновлении образа в реестре.

1. Включите непрерывное развертывание с помощью команды [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config), заменив `<app_name>` именем приложения-функции.

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Эта команда включает непрерывное развертывание и возвращает URL-адрес веб-перехватчика развертывания. (Этот URL-адрес можно получить в любое время с помощью команды [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url).)

1. Скопируйте URL-адрес веб-перехватчика развертывания в буфер обмена.

1. Откройте [Docker Hub](https://hub.docker.com/), войдите и выберите **Репозитории** на панели навигации. Найдите и выберите образ, перейдите на вкладку **Веб-перехватчики**, укажите **имя веб-перехватчика**, вставьте URL-адрес в **URL-адрес веб перехватчика**, а затем выберите **Создать**:

    ![Добавление веб-перехватчика в репозиторий DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. С помощью набора веб-перехватчика служба Функции Azure повторно развертывает образ при каждом обновлении в Docker Hub.

## <a name="enable-ssh-connections"></a>Включение SSH-подключений

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. При включенном протоколе SSH вы можете подключиться к контейнеру с помощью расширенных инструментов Службы приложений (KUDU). Чтобы упростить подключение к контейнеру по SSH, служба "Функции Azure" предоставляет базовый образ, в котором уже включен протокол SSH. Необходимо только изменить Dockerfile, а затем перестроить и повторно развернуть образ. Затем можно подключиться к контейнеру с помощью Дополнительных инструментов (Kudu)

1. В Dockerfile добавьте строку `-appservice` в базовый образ в инструкции `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    Различия между базовыми образами описаны в руководстве по [созданию и запуску настраиваемого образа в Службе приложений из частного реестра](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Выполните повторную сборку образа с помощью команды `docker build`, заменив `<docker_id>` идентификатором Docker.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Отправьте обновленный образ в Docker Hub (это должно занимать значительно меньше времени, чем первая принудительная отправка только обновленных сегментов образа).

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Функции Azure автоматически повторно развертывает образ в приложении-функции; процесс выполняется менее чем за минуту.

1. В браузере откройте `https://<app_name>.scm.azurewebsites.net/`, заменив `<app_name>` уникальным именем. Это URL-адрес конечной точки Дополнительных инструментов (Kudu) для контейнера приложения-функции.

1. Войдите в свою учетную запись Azure, а затем выберите вкладку **SSH**, чтобы создать подключение к контейнеру. Если Azure по-прежнему находится в процессе обновления образа контейнера, подключение может занять несколько секунд.

1. После установки подключения к контейнеру выполните команду `top`, чтобы просмотреть выполняющиеся в данный момент процессы. 

    ![Основная команда Linux, выполняемая в рамках сеанса SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Запись в очередь Службы хранилища Azure

Служба "Функции Azure" позволяет выполнять подключение функций к службам и ресурсам Azure с необходимостью написания кода для интеграции. Эти *привязки*, которые представляют как входные, так и выходные данные, объявляются в определении функции. Данные привязок предоставляются функции в качестве параметров. *Триггер* является специальным типом входных привязок. Хотя функция обладает только одним триггером, она может состоять из нескольких входных и выходных привязок. Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

В этом разделе показано, как интегрировать функцию с очередью службы Хранилища Azure. Выходная привязка, которая была добавлена в эту функцию, записывает в сообщение очереди данные HTTP-запроса.

## <a name="retrieve-the-azure-storage-connection-string"></a>Получение строки подключения к Службе хранилища Azure

Ранее вы создали учетную запись хранения Azure для использования приложением-функции. Строка подключения данной учетной записи надежно хранится в параметрах приложения в Azure. Скачав параметр в файл *local.settings.json*, вы можете использовать это подключение для записи данных в очередь службы хранилища в той же учетной записи при локальном запуске функции. 

1. В корневом каталоге проекта выполните следующую команду, заменив `<app_name>` именем приложения-функции из предыдущего краткого руководства. Эта команда перезапишет все существующие значения в файле.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Откройте файл *local.settings.json* и найдите значение `AzureWebJobsStorage`, которое является строкой подключения к учетной записи хранения. В других разделах этой статьи вы будете использовать имя `AzureWebJobsStorage` и строку подключения.

> [!IMPORTANT]
> Файл *local.settings.json* содержит секреты, скачанные из Azure, поэтому всегда исключайте этот файл из системы управления версиями. Файл *.gitignore*, созданный с помощью локального проекта функций, по умолчанию исключает файл.

### <a name="add-an-output-binding-to-functionjson"></a>Добавление выходной привязки в файл function.json

В службе "Функции Azure" для каждого типа привязок требуется `direction`, `type` и уникальное `name`, которое определяется в файле *function.json*. *function.json* уже содержит входную привязку для типа "httpTrigger" и выходную привязку для HTTP-ответа. Чтобы добавить привязку к очереди хранилища, измените файл, добавляя выходную привязку для типа "queue", где очередь отображается в коде в качестве входного аргумента с именем `msg`. Для привязки очереди также требуется имя используемой очереди, в данном случае `outqueue`, и имена параметров, в которых содержится строка подключения, в данном случае `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

В проекте библиотеки классов C# привязки определяются как атрибуты привязки в методе функции. Затем на основе этих атрибутов автоматически создается файл *function.json*.

1. Для привязки очереди выполните следующую команду [dotnet add package](/dotnet/core/tools/dotnet-add-package), чтобы добавить Пакет расширений хранилища в свой проект.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Откройте файл *HttpTrigger.cs* и добавьте следующий оператор `using`:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Добавьте следующий параметр в определение метода `Run`:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    Определение метода `Run` теперь должно соответствовать следующему коду:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

Параметр `msg` относится к типу `ICollector<T>`, который представляет собой коллекцию сообщений, записываемых в выходную привязку после завершения функции. В этом случае выходные данные представляют собой очередь хранилища с именем `outqueue`. Строка подключения к учетной записи хранения задана атрибутом `StorageAccountAttribute`. Он указывает параметр, который содержит строку подключения к учетной записи хранения и может применяться на уровне класса, метода или параметра. В этом случае вы можете опустить `StorageAccountAttribute`, так как уже используете учетную запись хранения по умолчанию.

::: zone-end

::: zone pivot="programming-language-javascript"

Обновите *function.json* в соответствии со следующими данными путем добавления привязки очереди после привязки HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Обновите *function.json* в соответствии со следующими данными путем добавления привязки очереди после привязки HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Обновите *function.json* в соответствии со следующими данными путем добавления привязки очереди после привязки HTTP:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Обновите *function.json* в соответствии со следующими данными путем добавления привязки очереди после привязки HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Добавление кода для использования выходной привязки

После определения привязки имя привязки (в данном случае `msg`) отображается в коде функции в качестве аргумента (или в объекте `context` в JavaScript и TypeScript). Затем эту переменную можно использовать для записи сообщений в очередь. Вам нужно писать код для проверки подлинности, получения ссылки на очередь или записи данных. Все эти задачи интеграции удобно осуществляются в среде выполнения Функций Azure и с использованием выходной привязки очереди.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Обновление образа в реестре

1. Еще раз выполните команду `docker build` в корневой папке и обновите версию в теге до `v1.0.1`. Как и ранее, замените `<docker_id>` идентификатором учетной записи Docker Hub.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Отправьте обновленный образ обратно в репозиторий с помощью `docker push`.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Так как вы настроили непрерывную поставку, обновление образа в реестре снова автоматически обновляет приложение-функцию в Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Просмотр сообщения в очереди службы хранилища Azure

Используйте в браузере тот же URL-адрес, что и перед вызовом функции. Браузер должен отобразить тот же ответ, что и раньше, так как вы не изменяли эту часть кода функции. Однако добавленный код написал сообщение, используя параметр URL-адреса `name` в очередь хранилища `outqueue`.

Очередь можно просмотреть на [портале Azure](../storage/queues/storage-quickstart-queues-portal.md) или в [Обозревателе службы хранилища Azure](https://storageexplorer.com/). Очередь также можно просмотреть в интерфейсе командной строки Azure. Для этого выполните приведенные ниже шаги.

1. Откройте файл *local.setting.json* проекта функций и скопируйте значение строки подключения. В окне терминала или командной строки выполните следующую команду, чтобы создать переменную среды с именем `AZURE_STORAGE_CONNECTION_STRING`. Вставьте конкретную строку подключения вместо `<connection_string>`. (Эта переменная среды означает, что вам не нужно указывать строку подключения для каждой последующей команды с помощью аргумента `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Дополнительно) Команду [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) можно использовать для просмотра очередей службы хранилища в учетной записи. В выходных данных этой команды должна быть очередь с именем `outqueue`, созданная при написании функцией первого сообщения в этой очереди.
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Используйте команду [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek), чтобы просматривать сообщения в этой очереди. Необходимо указывать первое имя, использованное ранее при проверке функции. Команда получает первое сообщение в очереди в [кодировке Base64](functions-bindings-storage-queue-trigger.md#encoding), поэтому необходимо также декодировать сообщение, чтобы просмотреть его в виде текста.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Коллекцию сообщений требуется разыменовать и раскодировать из base64, поэтому запустите PowerShell и выполните соответствующую команду PowerShell.

    ---

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите продолжить работу с Функциями Azure, используя созданные в этом руководстве ресурсы, их можно оставить. Поскольку для Функций Azure создан план цен. категории "Премиум", с вас будет взиматься плата в один два доллара США в день.

Чтобы избежать текущих затрат, удалите группу ресурсов `AzureFunctionsContainer-rg`, чтобы очистить все ресурсы в этой группе: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Дальнейшие действия

+ [Monitor Azure Functions](functions-monitoring.md) (Мониторинг Функций Azure)
+ [Azure Functions scale and hosting](functions-scale.md) (Масштабирование и размещение Функций Azure)
+ [Azure Functions on Kubernetes with KEDA](functions-kubernetes-keda.md) (Функции Azure на базе Kubernetes с KEDA)
