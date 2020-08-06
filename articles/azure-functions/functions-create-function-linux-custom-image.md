---
title: Создание Функций Azure на Linux с помощью пользовательского образа
description: Узнайте, как создавать функции Azure под управлением пользовательского образа Linux.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc, tracking-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: d6061cd449a33ccf1bb461ada090c0412d1e94ae
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87485224"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Создание функции на Linux с помощью пользовательского контейнера

В этом руководстве показано, как создать и развернуть код в Функциях Azure в качестве пользовательского контейнера Docker, используя базовый образ Linux. Пользовательский образ может быть удобен, если вы используете конкретную версию языка или особые зависимости или конфигурации, не поддерживаемые в рамках встроенного образа.

Вы также можете использовать контейнер Службы приложений Azure по умолчанию, как описано в статье [Краткое руководство. Создание функции, выполняемой в Linux, с помощью инструментов командной строки](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python). Поддерживаемые базовые образы для Функций Azure находятся в [репозитории базовых образов Функций Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

В этом руководстве описано следующее:

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

Инструкции из этого руководства можно выполнять на компьютере под управлением Windows, macOS или Linux. Завершение этого учебника приведет к расходам в несколько долларов США в вашей учетной записи Azure.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Идентификатор Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Запустите `docker login`, чтобы войти в Docker. Эта команда завершится ошибкой, если вы не запустили Docker. В таком случае запустите Docker и повторите команду.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Создание и тестирование локального проекта службы "Функции"

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Чтобы создать проект приложения-функции в папке с именем `LocalFunctionsProject`, в терминале или в командной строке выполните следующую команду для выбранного языка.  
::: zone-end  
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
::: zone pivot="programming-language-java"  
В пустой папке выполните следующую команду, чтобы создать проект функций из [архетипа Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
---

Maven запрашивает значения, которые позволят завершить создание проекта развертывания.   
Предоставьте следующие значения в ответ на соответствующие запросы:

| prompt | Значение | Описание |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Это значение уникально идентифицирует проект среди всех остальных. Оно должно соответствовать [правилам именования пакетов](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) для Java. |
| **artifactId** | `fabrikam-functions` | Это значение содержит имя JAR-файла, без номера версии. |
| **version** | `1.0-SNAPSHOT` | Выберите значение по умолчанию. |
| **package** | `com.fabrikam.functions` | Это значение определяет пакет Java для создаваемого кода функции. Используйте значение по умолчанию. |

Введите `Y` или нажмите клавишу ВВОД для подтверждения.

Maven создаст файлы проекта в новой папке с именем _artifactId_, то есть `fabrikam-functions` в нашем примере. 

Для запуска на Java 11 в Azure необходимо изменить значения в файле pom.xml. Дополнительные сведения см. в разделе [Версии Java](functions-reference-java.md#java-versions).
::: zone-end
Параметр `--docker` создает `Dockerfile` для проекта, который определяет настраиваемый контейнер, который подходит для Функций Azure и выбранной среды выполнения.

Перейдите в папку проекта:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Добавьте функцию в проект с помощью следующей команды, где аргумент `--name` — уникальное имя функции, а аргумент `--template` указывает триггер функции. С помощью `func new` создайте вложенную папку с именем функции, которая содержит файл кода на выбранном для проекта языке и файл конфигурации *function.json*.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
Чтобы протестировать функцию локально, запустите локальный узел среды выполнения Функций Azure в корневой папке проекта. 
::: zone pivot="programming-language-csharp"  
```
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
mvn clean package  
mvn azure-functions:run
```
::: zone-end
Когда в выходных данных отобразится конечная точка `HttpExample`, перейдите по `http://localhost:7071/api/HttpExample?name=Functions`. Браузер должен показать приветственное сообщение, которое возвращает `Functions`, значение, передаваемое параметру запроса `name`.

Чтобы остановить хост, используйте клавиши **Ctrl**-**C**.

## <a name="build-the-container-image-and-test-locally"></a>Создание образа контейнера и тестирование его локально

(Необязательно.) Изучите файл *Dockerfile* в корневой папке проекта. В этом файле описана среда, необходимая для запуска приложения-функции на платформе Linux.  Полный список поддерживаемых базовых образов для Функций Azure можно найти на [странице базового образа Функций Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

::: zone pivot="programming-language-java"  
Если вы используете Java 11 (предварительная версия), измените аргумент сборки `JAVA_VERSION` в созданном файле Dockerfile, как показано ниже. 

```docker
ARG JAVA_VERSION=11
```
::: zone-end
    
В корневой папке проекта выполните команду [docker build](https://docs.docker.com/engine/reference/commandline/build/), указав имя `azurefunctionsimage` и тег `v1.0.0`. Замените `<DOCKER_ID>` идентификатором вашей учетной записи Docker Hub. Эта команда отвечает за создание образа Docker для контейнера.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

После выполнения команды можно запустить новый контейнер локально.
    
Чтобы протестировать сборку, запустите образ в локальном контейнере, используя команду [docker run](https://docs.docker.com/engine/reference/commandline/run/), еще раз заменив `<DOCKER_ID` идентификатором Docker и добавив аргумент портов `-p 8080:80`.

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
После того, как образ будет запущен в локальном контейнере, откройте браузер на `http://localhost:8080`, который должен отобразить изображение-заполнитель, показанный ниже. На этом этапе отображается образ, так как функция выполняется в локальном контейнере, как в Azure. Это означает, что функция защищена ключом доступа, как определено в *function.json* со свойством `"authLevel": "function"`. Однако контейнер еще не опубликован в приложении-функции в Azure, поэтому ключ функции еще недоступен. Если вы хотите выполнить тестирование для локального контейнера, закройте docker, измените свойство авторизации на `"authLevel": "anonymous"`, перестройте образ и запустите docker. Затем сбросьте `"authLevel": "function"` в *function.json*. Дополнительные сведения см. в разделе [Ключи авторизации](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Изображение-заполнитель указывает, что контейнер запущен локально](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
После того, как образ будет запущен в локальном контейнере, перейдите по адресу `http://localhost:8080/api/HttpExample?name=Functions`, где должно появиться такое же приветственное сообщение, как описано выше. Так как архетип Maven создает функцию, активируемую HTTP, которая использует анонимную авторизацию, вы сможете вызвать функцию, даже если она выполняется в контейнере. 
::: zone-end  

Когда вы закончите проверку приложения-функции в контейнере, остановите docker, нажав клавиши **CTRL**+**C**.

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

    1. Войдите на портал Azure, а затем найдите и выберите **Приложение-функция**.

    1. Выберите функцию, которую нужно проверить.

    1. На панели навигации слева щелкните **Функции**, а затем выберите функцию, которую нужно проверить.

        ![Команда "Получить URL-адрес функции" на портале Azure](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Щелкните **Получить URL-адрес функции**.

        ![Команда "Получить URL-адрес функции" на портале Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. Во всплывающем окне щелкните **по умолчанию (функциональная клавиша)** , а затем скопируйте URL-адрес в буфер обмена. Ключ — это строка символов, следующая после `?code=`.

        ![Команда "Получить URL-адрес функции" на портале Azure](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


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

1. Чтобы проверить авторизацию, удалите из URL-адреса параметр `code=` и убедитесь, что ответа от функции нет.


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
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
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

1. Войдите в свою учетную запись Azure, а затем выберите вкладку **SSH**, чтобы создать подключение к контейнеру. Если Azure все еще обновляет образ контейнера, подключение может занять некоторое время.

1. После установки подключения к контейнеру выполните команду `top`, чтобы просмотреть выполняющиеся в данный момент процессы. 

    ![Основная команда Linux, выполняемая в рамках сеанса SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Запись в очередь Службы хранилища Azure

Служба "Функции Azure" позволяет выполнять подключение функций к службам и ресурсам Azure без необходимости написания кода для интеграции. Эти *привязки*, которые представляют как входные, так и выходные данные, объявляются в определении функции. Данные привязок предоставляются функции в качестве параметров. *Триггер* является специальным типом входных привязок. Хотя функция обладает только одним триггером, она может состоять из нескольких входных и выходных привязок. Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

В этом разделе показано, как интегрировать функцию с очередью службы Хранилища Azure. Выходная привязка, которая была добавлена в эту функцию, записывает в сообщение очереди данные HTTP-запроса.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>Добавление кода для использования выходной привязки

Определив привязку очереди, можно обновлять функцию, чтобы она получала выходной параметр `msg` и записывала сообщения в очередь.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
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

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

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
