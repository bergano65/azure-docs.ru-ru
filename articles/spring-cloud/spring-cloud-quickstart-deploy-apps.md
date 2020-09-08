---
title: Краткое руководство. Сборка и развертывание приложений в Azure Spring Cloud
description: Описание развертывания приложения в Azure Spring Cloud
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8931c22c3656cf9708756153268ab1d9d87b8343
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050834"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Краткое руководство. Сборка и развертывание приложений в Azure Spring Cloud

В этом документе объясняется, как создать и развернуть приложения микрослужб в Azure Spring Cloud с помощью следующих средств:
* Azure CLI
* Подключаемый модуль Maven
* Intellij

Перед развертыванием с помощью Azure CLI или Maven выполните примеры, обеспечивающие [подготовку экземпляра Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md) и [настройку сервера конфигурации](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Предварительные требования

* [установите JDK версии 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable);
* [Регистрация для получения подписки Azure](https://azure.microsoft.com/free/)
* (Необязательно.) [Установите Azure CLI версии 2.0.67 или более поздней](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и расширение Azure Spring Cloud с помощью команды `az extension add --name spring-cloud`.
* (необязательно) [установите Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) и [выполните вход](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

## <a name="deployment-procedures"></a>Процедуры развертывания

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Локальное выполнение сборки приложений для микрослужб

1. Клонируйте пример репозитория приложения в облачную учетную запись Azure.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Перейдите в каталог и выполните сборку проекта.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Компиляция проекта занимает около 5 минут. После завершения у вас должны быть отдельные JAR-файлы для каждой службы в соответствующих папках.

### <a name="create-and-deploy-the-apps"></a>Создание и развертывание приложений

1. Задайте стандартные имена группы ресурсов и кластера с помощью следующих команд:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Создайте микрослужбы Azure Spring Cloud с помощью JAR-файлов, полученных на предыдущем шаге. Вы создадите три приложения: **gateway**, **auth-service** и **account-service**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Приложения, созданные на предыдущем шаге, необходимо развернуть в Azure. Чтобы развернуть все три приложения, используйте следующие команды:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Назначение общедоступной конечной точки шлюзу

Нам нужен способ доступа к приложению через веб-браузер. Приложению шлюза требуется общедоступная конечная точка.

1. Назначьте конечную точку, использую следующую команду:

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. Отправьте к приложению **gateway** запрос на получение общедоступного IP-адреса, чтобы убедиться, что приложение запущено:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Клонирование и сборка репозитория примера приложения

1. Клонируйте репозиторий Git, выполнив следующую команду:

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Перейдите в каталог и создайте проект, выполнив следующую команду:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Создание и развертывание конфигураций в Azure Spring Cloud

1. Создайте конфигурации с помощью приведенной ниже команды в корневой папке PiggyMetrics, содержащей родительский файл POM. Если вы уже выполнили вход с помощью Azure CLI, команда автоматически использует учетные данные. В противном случае необходимо будет выполнить вход в соответствии с инструкциями в запросе. Дополнительные сведения см. на [вики-странице](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    Вам будет предложено выбрать:
    * **Модули**. Выберите `gateway`,`auth-service` и `account-service`.
    * **Подписка**: Это ваша подписка, используемая для создания экземпляра Azure Spring Cloud.
    * **Экземпляр службы**. Имя вашего экземпляра Azure Spring Cloud.
    * **Общедоступная конечная точка**. В списке предоставленных проектов введите номер, соответствующий `gateway`.  Это обеспечивает открытый доступ.

1. Теперь файл POM содержит зависимости и конфигурации подключаемого модуля. Разверните приложения с помощью следующей команды. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Импорт примера проекта в IntelliJ

1. Скачайте и распакуйте репозиторий с исходным кодом для этого руководства или клонируйте его с помощью Git: `git clone https://github.com/Azure-Samples/piggymetrics`. 

1. Откройте **диалоговое окно приветствия** IntelliJ и выберите **Import Project** (Импорт проекта), чтобы открыть мастер импорта.

1. Выберите папку `piggymetric`.

    ![Импорт проекта](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Развертывание приложения gateway в Azure Spring Cloud
Чтобы выполнить развертывание в Azure, необходимо войти в учетную запись Azure с помощью Azure Toolkit for IntelliJ и выбрать подписку. Дополнительные сведения см. в статье [Установка и вход](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Щелкните правой кнопкой мыши проект в обозревателе проектов IntelliJ и выберите **Azure** -> **Deploy to Azure Spring Cloud** (Развернуть в Azure Spring Cloud).

    ![Развертывание в Azure (шаг 1)](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. В поле **Name** (Имя) добавьте *:gateway*, чтобы имеющееся **имя** ссылалось на конфигурацию.
1. В текстовом поле **Artifact** (Артефакт) выберите *com.piggymetrics:gateway:1.0-SNAPSHOT*.
1. В текстовом поле **Subscription** (Подписка) проверьте свою подписку.
1. В текстовом поле **Spring Cloud** выберите экземпляр Azure Spring Cloud, который вы создали при [подготовке к работе экземпляра Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Задайте для параметра **Public Endpoint** (Общедоступная конечная точка) значение *Enable* (Включить).
1. В текстовом поле **App:** (Приложение:) выберите **Create app...** (Создать приложение...).
1. Введите *gateway*, а затем нажмите кнопку **ОК**.

    ![Развертывание в Azure, подтверждение](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. В диалоговом окне в разделе **Before launch** (До запуска) дважды щелкните *Run Maven Goal* (Запустить целевой объект).
1. В текстовом поле **Working directory** (Рабочая папка) перейдите к папке *piggymetrics/gateway*.
1. В текстовом поле **Command line** (Командная строка) введите *package -DskipTests*. Нажмите кнопку **ОК**.
1. Запустите развертывание, нажав кнопку **Run** (Выполнить) в нижней части диалогового окна **Deploy Azure Spring Cloud app** (Развертывание приложения Azure Spring Cloud). Подключаемый модуль выполнит команду `mvn package` для приложения `gateway` и развернет JAR-файл, созданный командой `package`.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Развертывание приложений auth-service и account-service в Azure Spring Cloud
Вы можете повторить описанные выше шаги, чтобы развернуть приложения `auth-service` и `account-service` в Azure Spring Cloud.

1. В полях **Name** (Имя) и **Artifact** (Артефакт) укажите, соответственно, приложение `auth-service`.
1. В текстовом поле **App:** (Приложение:) выберите **Create app...** (Создать приложение...), чтобы создать приложение `auth-service`.
1. Убедитесь, что для параметра **Public Endpoint** (Общедоступная конечная точка) установлено значение *Disabled* (Отключено).
1. В диалоговом окне в разделе **Before launch** (Перед запуском) в поле **Working directory** (Рабочая папка) перейдите в папку *piggymetrics/auth-service*.
1. Запустите развертывание, нажав кнопку **Run** (Выполнить) в нижней части диалогового окна **Deploy Azure Spring Cloud app** (Развертывание приложения Azure Spring Cloud). 
1. Повторите эти процедуры, чтобы настроить и развернуть `account-service`.
---

Перейдите по URL-адресу, указанному в результатах выполнения предыдущих шагов, чтобы получить доступ к приложению PiggyMetrics. Например, `https://<service instance name>-gateway.azuremicroservices.io`

![Доступ к PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

URL-адрес можно найти на портале Azure. 
1. Перейдите к службе.
2. Выберите **Приложения**.
3. Выберите **шлюз**.

    ![Переход к приложению](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Найдите URL-адрес на странице **gateway | Обзор**.

    ![Переход к приложению (второй этап)](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Если эти ресурсы вам не понадобятся в будущем, вы можете удалить группу ресурсов из портала, или выполнив приведенную ниже команду в Cloud Shell.
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
При выполнении предыдущих шагов вы также задали имя группы ресурсов по умолчанию. Чтобы очистить поля от заданных по умолчанию значений, воспользуйтесь следующей командой в Cloud Shell:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Журналы, метрики и трассировка](spring-cloud-quickstart-logs-metrics-tracing.md)
