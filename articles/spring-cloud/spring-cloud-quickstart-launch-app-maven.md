---
title: Краткое руководство. Запуск приложения с помощью Maven в Azure Spring Cloud
description: Запуск примера приложения с помощью Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554556"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Краткое руководство. Запуск приложения Azure Spring Cloud с помощью подключаемого модуля Maven

С помощью подключаемого модуля Maven Azure Spring Cloud можно легко создавать и обновлять приложения службы Azure Spring Cloud. Предварительно определив конфигурацию, можно развернуть приложения в имеющейся службе Azure Spring Cloud. В этой статье на примере приложения PiggyMetrics вы изучите эту функцию.

>[!Note]
> Прежде чем приступить к работе с этим кратким руководством, убедитесь, что ваша подписка Azure имеет доступ к Azure Spring Cloud. Так как служба находится на этапе предварительной версии, свяжитесь с нами, чтобы мы добавили вашу подписку в список разрешенных. Если вы хотите изучить возможности Azure Spring Cloud, заполните и отправьте [форму интересов Azure Spring Cloud (закрытая предварительная версия)](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).

>[!TIP]
> Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять команды, описанные в этой статье. Она содержит предварительно установленные общие инструменты Azure вместе с новейшими версиями Git, комплект разработчика Java (JDK), Maven и Azure CLI. Если вы вошли в подписку Azure, запустите [Azure Cloud Shell](https://shell.azure.com). Дополнительные сведения см. в [обзоре Azure Cloud Shell](../cloud-shell/overview.md).

Для работы с этим кратким руководством сделайте следующее:

1. [Установка Git](https://git-scm.com/).
2. [Установите JDK версии 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Установите Maven 3.0 или более поздней версии](https://maven.apache.org/download.cgi).
4. [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Подпишитесь на бесплатную версию Azure](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Установка расширения Azure CLI

Установите расширение Azure Spring Cloud для Azure CLI с помощью следующей команды:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Подготовка экземпляра службы к работе на портале Azure

1. Откройте в браузере [портал Azure](https://portal.azure.com) и войдите в свою учетную запись.

1. Найдите и выберите **Azure Spring Cloud**. 
1. На странице обзора выберите **Создать** и выполните следующие действия.  

    a. В поле **Имя службы** укажите имя экземпляра службы. Его длина должна быть от 4 до 32 знаков. Имя может содержать только строчные буквы, цифры и дефисы. Первым символом в имени службы должна быть буква, а последним — буква или цифра.  

    b. В раскрывающемся списке **Подписка** выберите подписку, на которую будет выставляться счет за этот ресурс. Убедитесь, что эта подписка добавлена в наш список разрешений для Azure Spring Cloud.  

    c. В поле **Группа ресурсов** создайте группу ресурсов. Советуем создавать группы ресурсов для новых ресурсов.  

    d. В раскрывающемся списке **Расположение** выберите расположение для экземпляра службы. В настоящее время поддерживаются следующие расположения: восточная часть США, западная часть США 2, Западная Европа и Юго-Восточная Азия.
    
Развертывание службы занимает около 5 минут. После развертывания службы появится страница **Обзор** для экземпляра службы.

## <a name="set-up-your-configuration-server"></a>Настройка сервера конфигурации

1. На странице **Обзор** службы выберите **Config Server** (Сервер конфигурации).
1. В разделе **Репозиторий по умолчанию** задайте для параметра **URI** значение **https://github.com/Azure-Samples/piggymetrics** , для параметра **Метки** значение **config** и щелкните **Применить**, чтобы сохранить изменения.

## <a name="clone-and-build-the-sample-application-repository"></a>Клонирование и сборка репозитория примера приложения

1. Клонируйте репозиторий Git, выполнив следующую команду:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Перейдите в каталог и создайте проект, выполнив следующую команду:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Создание и развертывание конфигурации Azure Spring Cloud

1. Добавьте следующий фрагмент кода в файл *pom.xml* или *settings.xml*, чтобы разрешить Maven работу с Azure Spring Cloud.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Создайте конфигурацию, выполнив следующую команду:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Выберите модули `gateway`, `auth-service` и `account-service`.

    b. Выберите свою подписку и кластер служб Azure Spring Cloud.

    c. В списке предоставленных проектов введите номер, соответствующий `gateway`, чтобы предоставить общий доступ.
    
    d. Подтвердите конфигурацию.

1. Разверните приложения с помощью следующей команды:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Доступ к PiggyMetrics можно получить с помощью URL-адреса, который указан в выходных данных предыдущей команды.

## <a name="next-steps"></a>Дополнительная информация

В рамках этого краткого руководства вы развернули приложение Spring Cloud из репозитория Maven. Дополнительные сведения об Azure Spring Cloud см. в учебнике по подготовке приложения к развертыванию.

> [!div class="nextstepaction"]
> [Подготовка приложения Azure Spring Cloud к развертыванию](spring-cloud-tutorial-prepare-app-deployment.md)
