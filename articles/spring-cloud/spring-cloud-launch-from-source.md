---
title: Краткое руководство. Запуск приложения с пружинным облаком из исходного кода
description: Узнайте, как запустить облачное приложение Azure весны непосредственно из исходного кода.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: 573baa242c06868326568a82bc358e136f1ece2c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177970"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>Запуск приложения с пружинным облаком из исходного кода

Azure Spring Cloud позволяет легко выполнять приложения микрослужб Spring Cloud в Azure.

Azure Веснного облака позволяет запускать приложение непосредственно из исходного кода Java или из предварительно созданного JAR-файла. В этой статье описаны необходимые шаги.

Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * подготовка экземпляра службы к работе;
> * задание сервера конфигурации для экземпляра;
> * локальное выполнение сборки приложения для микрослужб;
> * развертывание каждой микрослужбы;
> * назначение общедоступной конечной точки для приложения.

## <a name="prerequisites"></a>Технические условия

>[!Note]
> Прежде чем приступить к работе с этим кратким руководством, убедитесь, что ваша подписка Azure имеет доступ к Azure Spring Cloud.  Так как служба находится на этапе предварительной версии, свяжитесь с нами, чтобы мы добавили вашу подписку в список разрешенных.  Если вы хотите изучить возможности Azure Spring Cloud, [заполните эту форму](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).  Хотя Предварительная версия Azure "пружинное облако" доступна в режиме предварительного просмотра, корпорация Майкрософт предлагает ограниченную поддержку без соглашения об уровне  Дополнительные сведения о поддержке во время предварительных версий см. в [разделе часто задаваемые вопросы о поддержке](https://azure.microsoft.com/support/faq/).

Прежде чем начать, убедитесь, что подписка Azure имеет необходимые зависимости:

1. [установите Git](https://git-scm.com/);
2. [установите JDK версии 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html);
3. [установите Maven 3.0 или более поздней версии](https://maven.apache.org/download.cgi);
4. [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [зарегистрируйтесь для получения подписки Azure](https://azure.microsoft.com/free/).

> [!TIP]
> Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье.  Она содержит предварительно установленные общие инструменты Azure вместе с новейшими версиями Git, JDK, Maven и Azure CLI. Если вы вошли в подписку Azure, запустите [Azure Cloud Shell](https://shell.azure.com) на сайте shell.azure.com.  Дополнительные сведения об Azure Cloud Shell см. в [нашей документации](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Установка расширения Azure CLI

Установите расширение Azure Веснного облака для Azure CLI с помощью следующей команды.

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Подготавливает экземпляр службы с помощью Azure CLI

Войдите в Azure CLI и выберите активную подписку. Выбранная активная подписка должна быть добавлена в список разрешений для Azure Spring Cloud.

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Откройте окно Azure CLI и выполните приведенные ниже команды, чтобы подготавливать экземпляр Azure Spring Cloud к работе. Обратите внимание, что вы также указываете Azure Веснное облако, чтобы назначить общедоступный домен.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

Развертывание экземпляра службы займет около пяти минут.

Задайте стандартные имена группы ресурсов и кластера с помощью следующих команд:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>Создание приложения с пружинным облаком

Следующая команда создает приложение с пружинным облаком в подписке.  Будет создана пустая высокопружинная облачная служба, в которую мы можем передать наше приложение.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Развертывание приложения с пружинным облаком

Приложение можно развернуть из предварительно созданного JAR-файла или из репозитория Gradle или Maven.  Найдите инструкции для каждого варианта ниже.

### <a name="deploy-a-built-jar"></a>Развертывание созданного JAR-файла

Чтобы выполнить развертывание из JAR-файла, созданного на локальном компьютере, убедитесь, что сборка создает [файл JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Развертывание файла FAT-JAR в активном развертывании

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Развертывание файла FAT-JAR в определенном развертывании

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Развертывание из исходного кода

Azure Веснного облака использует [кпакк](https://github.com/pivotal/kpack) для создания проекта.  Вы можете использовать Azure CLI для передачи исходного кода, сборки проекта с помощью кпакк и его развертывания в целевом приложении.

> [!WARNING]
> Проект должен создать только один JAR-файл с записью `main-class` в `MANIFEST.MF` в `target` (для развертываний Maven или `build/libs` (для развертываний Gradle).  Несколько JAR-файлов с записями `main-class` приведут к сбою развертывания.

Для одного модуля Maven/Gradle:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Для проектов Maven/Gradle с несколькими модулями повторите эти действия для каждого модуля:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Отображение журналов развертывания

Проверьте журналы сборки кпакк с помощью следующей команды:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> В журналах кпакк будет отображаться только Последнее развертывание, если это развертывание было создано из источника с помощью кпакк.

## <a name="assign-a-public-endpoint-to-gateway"></a>Назначение общедоступной конечной точки шлюзу

1. Откройте страницу **Панель мониторинга приложения**.
2. Выберите приложение `gateway`, чтобы отобразить страницу **Сведения о приложении**.
3. Выберите **Назначить домен**, чтобы назначить общедоступную конечную точку шлюзу. Для этого может потребоваться несколько минут. 
4. Введите назначенный общедоступный IP-адрес в браузере, чтобы просмотреть работающее приложение.

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнить следующие действия:

> [!div class="checklist"]
> * подготовка экземпляра службы к работе;
> * задание сервера конфигурации для экземпляра;
> * локальное выполнение сборки приложения для микрослужб;
> * развертывание каждой микрослужбы;
> * Изменение переменных среды для приложений
> * Назначение общедоступного IP-адреса для шлюза приложений

> [!div class="nextstepaction"]
> [Подготовка приложения Azure Spring Cloud к развертыванию](spring-cloud-tutorial-prepare-app-deployment.md)
