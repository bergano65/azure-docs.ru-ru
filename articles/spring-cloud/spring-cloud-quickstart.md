---
title: Краткое руководство. Развертывание первого приложения Azure Spring Cloud
description: В этом кратком руководстве описывается развертывание приложения Spring Cloud в Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 245516e0a54865d3a6097c4bb566b850cb738ad6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260556"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Краткое руководство. Развертывание первого приложения Azure Spring Cloud

В этом кратком руководстве объясняется, как развернуть простое приложение микрослужбы Azure Spring Cloud для запуска в Azure. 

Код приложения, используемый в этом учебнике, — это простое приложение, созданное с помощью Spring Initializr. После завершения работы с этим примером приложение будет доступно через Интернет и им можно управлять с помощью портала Azure.

В этом кратком руководстве объясняются такие операции:

> [!div class="checklist"]
> * создание простого проекта Spring Cloud;
> * подготовка экземпляра службы к работе;
> * сборка и развертывание приложения с общедоступной конечной точкой;
> * Потоковая передача журналов в режиме реального времени

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

* [установите JDK версии 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable);
* [Регистрация для получения подписки Azure](https://azure.microsoft.com/free/)
* (необязательно) [установите Azure CLI версии 2.0.67 или более поздней](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и расширение Azure Spring Cloud с помощью команды `az extension add --name spring-cloud`;
* (необязательно) [установите Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) и [выполните вход](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

## <a name="generate-a-spring-cloud-project"></a>Создание проекта Spring Cloud

Начните со [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin), чтобы создать пример проекта с рекомендуемыми зависимостями для Azure Spring Cloud. На рисунке ниже показан Initializr, настроенный для этого примера проекта.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Страница Initializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Щелкните **Создать**, если все зависимости заданы. Загрузите и распакуйте пакет, а затем создайте веб-контроллер для простого веб-приложения, добавив `src/main/java/com/example/hellospring/HelloController.java` следующим образом:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Подготовка к работе экземпляра Azure Spring Cloud

Следующая процедура создает экземпляр Azure Spring Cloud с помощью портала Azure.

1. На новой вкладке откройте [портал Azure](https://ms.portal.azure.com/). 

2. В поле поиска сверху введите *Azure Spring Cloud*.

3. Выберите пункт *Azure Spring Cloud* в списке результатов.

    ![Значок ASC — запуск](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. На странице Azure Spring Cloud щелкните элемент **+ Добавить**.

    ![Значок ASC — добавление](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Заполните форму на странице **создания** Azure Spring Cloud.  Ознакомьтесь со следующими рекомендациями:
    - **Подписка**: Выберите подписку, на которую будет выставляться счет за этот ресурс.
    - **Группа ресурсов.** Советуем создавать группы ресурсов для новых ресурсов. Она будет использоваться в дальнейших шагах как **\<resource group name\>** .
    - **Сведения о службе или ее название.** Укажите **\<service instance name\>** .  Его длина должна быть от 4 до 32 знаков. Имя может содержать только строчные буквы, цифры и дефисы.  Первым символом в имени службы должна быть буква, а последним — буква или цифра.
    - **Расположение.** Выберите регион для экземпляра службы.

    ![Начальная страница портала ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Щелкните **Проверка и создание**.

## <a name="build-and-deploy-the-app"></a>Создание и развертывание приложения
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
Следующая процедура используется для сборки и развертывания приложения с помощью Azure CLI. Выполните следующую команду в корневом каталоге проекта.

1. Выполните сборку проекта с помощью Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. Если вы еще не установили его, установите облачное расширение Spring Cloud для Azure CLI:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Создайте приложение с назначенной общедоступной конечной точкой:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Разверните JAR-файл для приложения:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. Для завершения развертывания приложения потребуется несколько минут. Чтобы убедиться, что оно развернуто, перейдите в колонку **Приложения** на портале Azure. В ней должно отобразиться состояние приложения.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Следующая процедура использует подключаемый модуль IntelliJ для Azure Spring Cloud, чтобы развернуть пример приложения в IntelliJ IDEA.  

### <a name="import-project"></a>Импорт проекта

1. Откройте **диалоговое окно приветствия** IntelliJ и выберите **Import Project** (Импорт проекта), чтобы открыть мастер импорта.
1. Выберите папку `hellospring`.

    ![Импорт проекта](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Развертывание приложения
Чтобы выполнить развертывание в Azure, необходимо войти в учетную запись Azure и выбрать подписку.  Дополнительные сведения см. в статье [Установка и вход](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Щелкните правой кнопкой мыши проект в обозревателе проектов IntelliJ и выберите **Azure** -> **Deploy to Azure Spring Cloud** (Развернуть в Azure Spring Cloud).

    [ ![Развертывание в Azure, шаг 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Подтвердите имя для приложения, указанное в поле **Name** (Имя). **Имя** здесь обозначает конфигурацию, а не имя приложения. Обычно пользователям его не нужно изменять.
1. В текстовом поле **Artifact** (Артефакт) выберите *hellospring-0.0.1-SNAPSHOT.jar*.
1. В текстовом поле **Subscription** (Подписка) проверьте свою подписку.
1. В текстовом поле **Spring Cloud** выберите экземпляр Azure Spring Cloud, который вы создали при [подготовке к работе экземпляра Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Задайте для параметра **Public Endpoint** (Общедоступная конечная точка) значение *Enable* (Включить).
1. В текстовом поле **App:** (Приложение:) выберите **Create app...** (Создать приложение...).
1. Введите *hellospring*, а затем нажмите кнопку **ОК**.

    [ ![Развертывание в Azure, подтверждение](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png) ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Запустите развертывание, нажав кнопку **Run** (Выполнить) в нижней части диалогового окна **Deploy Azure Spring Cloud app** (Развертывание приложения Azure Spring Cloud). Подключаемый модуль выполнит команду `mvn package` для приложения `hellospring` и развернет JAR-файл, созданный командой `package`.
---

По завершении развертывания можно получить доступ к `https://<service instance name>-hellospring.azuremicroservices.io/`.

  [ ![Доступ к приложению из браузера](media/spring-cloud-quickstart-java/access-app-browser.png) ](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Потоковая передача журналов в режиме реального времени

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Используйте следующую команду, чтобы получать журналы в режиме реального времени из приложения.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Внешний вид журналов в итоге:

[ ![Журналы потоковой передачи](media/spring-cloud-quickstart-java/streaming-logs.png) ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Используйте `az spring-cloud app logs -h` для просмотра дополнительных параметров и функциональных возможностей потоковой передачи журналов.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Выберите **Azure Explorer** и **Spring Cloud**.
1. Щелкните правой кнопкой мыши запущенное приложение.
1. Выберите **Streaming Logs** (Журналы потоковой передачи) из раскрывающегося списка.
1. Выберите экземпляр.

    [ ![Выбор журналов потоковой передачи](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Журнал потоковой передачи отобразится в окне выходных данных.

    [ ![Вывод журнала потоковой передачи](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Чтобы получить дополнительные возможности аналитики журналов, перейдите на вкладку **Журналы** в меню [портал Azure](https://portal.azure.com/). Журналы отображаются с задержкой в несколько минут.

[ ![Аналитика журналов](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Очистка ресурсов
На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Если эти ресурсы вам не понадобятся в будущем, вы можете удалить группу ресурсов из портала, или выполнив приведенную ниже команду в Cloud Shell.
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

В этом кратком руководстве рассматривались следующие темы:

> [!div class="checklist"]
> * создание базового проекта Azure Spring Cloud;
> * подготовка экземпляра службы к работе;
> * сборка и развертывание приложения с общедоступной конечной точкой;
> * потоковая передача журналов в режиме реального времени.
## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Создание и запуск микрослужб](spring-cloud-quickstart-sample-app-introduction.md)

Дополнительные примеры доступны на GitHub: [Примеры для Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
