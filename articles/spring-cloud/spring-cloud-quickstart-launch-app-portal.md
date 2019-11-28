---
title: Краткое руководство. Запуск приложения Azure Spring Cloud с помощью портала Azure
description: Развертывание примера приложения в Azure Spring Cloud с помощью портала Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: 8a34dcd31ba15ed949fa0a50ec904da96a048a7d
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560064"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Краткое руководство. Запуск приложения Azure Spring Cloud с помощью портала Azure

Azure Spring Cloud позволяет легко выполнять приложения микрослужб Spring Cloud в Azure.

В этом кратком руководстве описано, как развернуть имеющееся приложение Spring Cloud в Azure.  Пример кода приложения, используемый в этом руководстве, можно найти в [репозитории примеров GitHub](https://github.com/Azure-Samples/PiggyMetrics). После завершения работы предоставленный пример приложения будет доступен в сети и готов к управлению с помощью портала Azure.

Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * подготовка экземпляра службы к работе;
> * задание сервера конфигурации для экземпляра;
> * локальное выполнение сборки приложения для микрослужб;
> * развертывание каждой микрослужбы;
> * назначение общедоступной конечной точки для приложения.

## <a name="prerequisites"></a>Предварительные требования

>[!Note]
> Azure Spring Cloud в настоящее время предлагается в качестве общедоступной предварительной версии. Предложения общедоступной предварительной версии позволяют клиентам поэкспериментировать с новыми функциями до официального выпуска.  Функции и службы общедоступной предварительной версии не предназначены для использования в рабочей среде.  Чтобы получить дополнительные сведения о поддержке на этапе использования предварительных версий, ознакомьтесь с разделом [Вопросы и ответы](https://azure.microsoft.com/support/faq/) или оформите [запрос на поддержку](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

>[!TIP]
> Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье.  Она содержит предварительно установленные общие инструменты Azure вместе с новейшими версиями Git, JDK, Maven и Azure CLI. Если вы вошли в подписку Azure, запустите [Azure Cloud Shell](https://shell.azure.com) на сайте shell.azure.com.  Дополнительные сведения об Azure Cloud Shell см. в [нашей документации](../cloud-shell/overview.md)

Для работы с этим кратким руководством сделайте следующее:

1. [установите Git](https://git-scm.com/);
2. [установите JDK версии 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable);
3. [установите Maven 3.0 или более поздней версии](https://maven.apache.org/download.cgi);
4. [установите Azure CLI (версии 2.0.67 или выше)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest);
5. [зарегистрируйтесь для получения подписки Azure](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Установка расширения Azure CLI

Установите расширение Azure Spring Cloud для Azure CLI с помощью следующей команды:

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Подготовка экземпляра службы к работе на портале Azure

1. В веб-браузере откройте [эту ссылку на Azure Spring Cloud на портале Azure](https://ms.portal.azure.com/#create/Microsoft.AppPlatform).

1. Заполните форму на странице **создания** Azure Spring Cloud.  Ознакомьтесь со следующими рекомендациями:
    - Имя службы: Укажите имя экземпляра службы.  Его длина должна быть от 4 до 32 знаков. Имя может содержать только строчные буквы, цифры и дефисы.  Первым символом в имени службы должна быть буква, а последним — буква или цифра.
    - Подписка: Выберите подписку, на которую будет выставляться счет за этот ресурс.  Убедитесь, что эта подписка добавлена в наш список разрешений для Azure Spring Cloud.
    - Группа ресурсов: Советуем создавать группы ресурсов для новых ресурсов.
    - Расположение. Выберите расположение для экземпляра службы. В настоящее время поддерживаются следующие расположения: восточная часть США, западная часть США 2, Западная Европа и Юго-Восточная Азия.

1. Щелкните **Проверка и создание**.

1. Проверьте спецификации и щелкните **Создать**.

Развертывание службы занимает около 5 минут.  После развертывания появится страница **Обзор** для экземпляра службы.

## <a name="set-up-your-configuration-server"></a>Настройка сервера конфигурации

1. Перейдите на страницу **Обзор** службы и выберите **Config Server** (Сервер конфигурации).

1. В разделе **Репозиторий по умолчанию** задайте для параметра **URI** значение "https\://github.com/Azure-Samples/piggymetrics", для параметра **Метка** — значение "config" и щелкните **Применить**, чтобы сохранить изменения.

    ![Снимок экрана, где отображается окно портала ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

## <a name="build-and-deploy-microservice-applications"></a>Создание и развертывание приложений микрослужб

1. Откройте [Azure Cloud Shell](https://shell.azure.com) и клонируйте репозиторий с примером приложения на локальный компьютер.  Перед клонированием приложения мы создадим на нем временный каталог `source-code`.

    ```azurecli
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Выполните сборку клонированного пакета.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```
1. Назначьте имена группе ресурсов и службе. Обязательно замените заполнители ниже именем группы ресурсов и именем службы, подготовленными ранее в этом учебнике.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Создайте приложение `gateway` и выполните развертывание файла JAR.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Следуя той же схеме, создайте приложения `account-service` и `auth-service`, и выполните развертывание их файлов JAR.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Для завершения развертывания приложений потребуется несколько минут. Чтобы убедиться, что они развернуты, перейдите в колонку **Приложения** на портале Azure. Вы увидите строку каждого из трех приложений.

## <a name="assign-a-public-endpoint-to-gateway"></a>Назначение общедоступной конечной точки шлюзу

1. В меню слева откройте вкладку **Приложения**.

1. Выберите приложение `gateway`, чтобы отобразить страницу **Обзор**.

1. Выберите **Назначить домен**, чтобы назначить общедоступную конечную точку шлюзу. Для этого может потребоваться несколько минут.

    ![Снимок экрана, где отображается окно портала ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

1. Введите назначенную общедоступную конечную точку (помеченный **URL-адрес**) в браузере, чтобы просмотреть работающее приложение.

    ![Снимок экрана, где отображается окно портала ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие действия:

> [!div class="checklist"]
> * подготовка экземпляра службы к работе;
> * задание сервера конфигурации для экземпляра;
> * локальное выполнение сборки приложения для микрослужб;
> * развертывание каждой микрослужбы;
> * Назначение общедоступной конечной точки для шлюза приложения

> [!div class="nextstepaction"]
> [Подготовка приложения Azure Spring Cloud к развертыванию](spring-cloud-tutorial-prepare-app-deployment.md)
