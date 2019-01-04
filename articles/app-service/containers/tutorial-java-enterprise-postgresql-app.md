---
title: Разработка веб-приложения Java Enterprise в Службе приложений Azure на платформе Linux | Документация Майкрософт
description: Сведения о создании приложения Java Enterprise, работающего в Wildfly в Службе приложений Azure на платформе Linux.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 3a668783e8257ef9074d12b30ff0afc3a40325f4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539730"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Руководство. Создание веб-приложения Java EE с подключением к базе данных Postgres в Azure

В этом руководстве приведены сведения о создании веб-приложения Java Enterprise Edition (EE) в Службе приложений Azure, а также подключении его к базе данных Postgres. Когда вы выполните инструкции руководства, у вас будет приложение [WildFly](https://www.wildfly.org/about/), данные которого хранятся в [базе данных Azure для Postgres](https://azure.microsoft.com/services/postgresql/), которая работает в [Службе приложений Azure на платформе Linux](app-service-linux-intro.md).

Из этого учебника вы узнаете следующее:
> [!div class="checklist"]
> * Развертывание приложения Java EE в Azure с помощью Maven.
> * Создание базы данных Postgres в Azure
> * Настройка сервера WildFly для использования Postgres.
> * Обновление и повторное развертывание приложения
> * Запуск модульных тестов в WildFly.

## <a name="prerequisites"></a>Предварительные требования

1. [Скачайте и установите Git](https://git-scm.com/)
2. [Скачайте и установите Maven 3](https://maven.apache.org/install.html).
3. [Скачайте и установите Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="clone-and-edit-the-sample-app"></a>Клонирование и изменение примера приложения

На этом шаге вы клонируете пример приложения и настроите объектную модель проекта Maven (POM или pom.xml) для развертывания.

### <a name="clone-the-sample"></a>Клонирования репозитория

В окне терминала перейдите в рабочую папку и клонируйте [пример репозитория](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Обновление объектной модели проекта Maven

Обновите файл объектной модели проекта Maven, указав нужное имя и группу ресурсов вашей службы приложений. Эти значения будут внесены в подключаемый модуль Azure, который находится ниже в файле _pom.xml_. Вам не нужно заранее создавать план или экземпляр службы приложений. При необходимости подключаемый модуль Maven создаст группу ресурсов и службу приложений.

Чтобы проверить подключаемый модуль Azure, прокрутите вниз до раздела `<plugins>` файла _pom.xml_. В разделе конфигурации `<plugin>` в файле _pom.xml_ для azure-webapp-maven-plugin должна быть указана такая конфигурация:

```xml
      <!--*************************************************-->
      <!-- Deploy to WildFly in App Service Linux           -->
      <!--*************************************************-->
 
      <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.0</version>
        <configuration>
 
          <!-- Web App information -->
          <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
          <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
          <appName>${WEBAPP_NAME}</appName>
          <region>${REGION}</region>
 
          <!-- Java Runtime Stack for Web App on Linux-->
          <linuxRuntime>wildfly 14-jre8</linuxRuntime>
 
        </configuration>
      </plugin>
```

Замените значения заполнителей необходимыми именами ресурсов:
```xml
<azure.plugin.appname>YOUR_APP_NAME</azure.plugin.appname>
<azure.plugin.resourcegroup>YOUR_RESOURCE_GROUP</azure.plugin.resourcegroup>
```


## <a name="build-and-deploy-the-application"></a>Создание и развертывание приложения.

Теперь с помощью Maven мы создадим приложение и развернем его в службу приложений.

### <a name="build-the-war-file"></a>Создание WAR-файла

Объектная модель проекта настроена для упаковки приложения в файл Web Archive (WAR). Создайте приложение с помощью Maven:

```bash
mvn clean install -DskipTests
```

Тестовые случаи в этом приложении предназначены для запуска, когда приложение развернуто в WildFly. Мы пропустим тесты для локальной сборки и запустим тесты, когда приложение будет развернуто в службе приложений.

### <a name="deploy-to-app-service"></a>Развертывание в службу приложений

Теперь, когда WAR-файл готов, мы выполним развертывание в службу приложений с использованием подключаемого модуля Azure:

```bash
mvn azure-webapp:deploy
```

По завершении развертывания перейдите к следующему шагу.

### <a name="create-a-record"></a>Создание записи

Откройте веб-браузер и перейдите по адресу `https://<your_app_name>.azurewebsites.net/`. Поздравляем, вы развернули приложение Java EE в Службе приложений Azure!

На этом этапе приложение использует выполняющуюся в памяти базу данных H2. Щелкните Admin (Администрирование) на панели навигации и создайте новую категорию. Если вы перезапустите экземпляр службы приложений, запись в выполняющейся в памяти базе данных будет потеряна. На следующих шагах вы исправите эту проблему, выполнив подготовку базы данных Postgres в Azure и настроив ее использование в WildFly.

![Расположение кнопки администрирования](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Подготовка базы данных Postgres

Чтобы подготовить сервер базы данных Postgres, откройте терминал и выполните следующую команду со своими значениями для имени сервера, имени пользователя, пароля и расположения. Используйте группу ресурсов, в которой находится служба приложений. Запишите свой пароль, так как он понадобится вам позже.

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

Перейдите на портал и найдите свою базу данных Postgres. После отображения колонки скопируйте значения имени сервера и имени для входа администратора сервера (они понадобятся вам позже).

### <a name="allow-access-to-azure-services"></a>Разрешение доступа к службам Azure

На панели **Безопасность подключения** колонки базы данных Azure переведите переключатель "Разрешить доступ к службам Azure" в положение **ВКЛ.**

![Разрешение доступа к службам Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Обновление приложения Java для Postgres

Теперь мы внесем некоторые изменения в приложение Java, чтобы оно использовало нашу базу данных Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Добавление учетных данных Postgres в файл POM

В файле _pom.xml_ замените значения заполнителей собственными именем сервера Postgres, именем входа администратора и паролем. Эти значения будут внесены в качестве переменных среды в ваш экземпляр службы приложений при повторном развертывании приложения.

```xml
<azure.plugin.postgres-server-name>SERVER_NAME</azure.plugin.postgres-server-name>
<azure.plugin.postgres-username>USERNAME@FIRST_PART_OF_SERVER_NAME</azure.plugin.postgres-username>
<azure.plugin.postgres-password>PASSWORD</azure.plugin.postgres-password>
```

### <a name="update-the-java-transaction-api"></a>Обновление API транзакций Java

Теперь необходимо изменить конфигурацию API транзакций Java (JPA), чтобы приложение Java связывалось с Postgres, а не с выполняющейся в памяти базой данных H2, использованной ранее. Откройте в редакторе _src/main/resources/META-INF/persistence.xml_. Замените значение для `<jta-data-source>` значением `java:jboss/datasources/postgresDS`. Теперь в XML-файле JTA должен присутствовать следующий параметр:

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>Настройка сервера приложений WildFly

Перед развертыванием измененного приложения необходимо обновить сервер приложений WildFly для добавления модуля Postgres и его зависимостей. Для настройки сервера нам понадобятся четыре файла в каталоге `wildfly_config/`:

- **postgresql-42.2.5.jar:** JAR-файл, представляющий собой драйвер JDBC для Postgres. Дополнительные сведения см. [на официальном веб-сайте](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml:** XML-файл, объявляющий имя модуля Postgres (org.postgres). Он также указывает ресурсы и зависимости, необходимые для использования модуля.
- **jboss_cli_commands.cl:** файл, содержащий команды конфигурации, которые будут выполняться с помощью интерфейса командной строки JBoss. Эти команды добавляют модуль Postgres в сервер приложений WildFly, предоставляют учетные данные, объявляют имя JNDI, устанавливают порог времени ожидания и т. д. Сведения об интерфейсе командной строки JBoss см. [здесь](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh:** сценарий оболочки, который будет выполняться каждый раз, когда запускается экземпляр Службы приложений. Сценарий выполняет только одну функцию — передача команд в файле `jboss_cli_commands.cli` в интерфейс командной строки JBoss.

Мы настоятельно рекомендуем ознакомиться с содержимым этих файлов, особенно _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>Передача файлов конфигурации по FTP

Нам нужно будет передать содержимое `wildfly_config/` в экземпляр службы приложений по протоколу FTP. Чтобы получить учетные данные FTP, нажмите кнопку **Get Publish Profile** (Получить профиль публикации) в колонке службы приложений на портале Azure. Ваше имя пользователя и пароль FTP будут находиться в скачанном XML-документе. Дополнительные сведения о профиле публикации см. в статье [Настройка учетных данных развертывания службы приложений Azure](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

С помощью любого инструмента FTP перенесите четыре файла из `wildfly_config/` в `/home/site/deployments/tools/`. (Обратите внимание, что нужно перемещать не каталог, а сами файлы.)


### <a name="finalize-app-service"></a>Завершение подготовки службы приложений

В колонке службы приложений перейдите к панели "Параметры приложения". В разделе "Среда выполнения" в поле "Загрузочный файл" укажите значение `/home/site/deployments/tools/startup_script.sh`. Это обеспечит запуск сценария оболочки после создания экземпляра службы приложений, но до запуска сервера WildFly.

Наконец, перезапустите службу приложений. Кнопка находится на панели "Обзор".

## <a name="redeploy-the-application"></a>Повторное развертывание приложения

В окне терминала повторно создайте и разверните приложение.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Поздравляем! Теперь ваше приложение использует базу данных Postgres, а все записи, созданные в приложении, сохраняются в Postgres, а не в выполняющейся в памяти базе данных H3, использованной ранее. Чтобы проверить это, создайте запись и перезапустите службу приложений. После перезагрузки приложения записи останутся там же.

## <a name="clean-up"></a>Очистка

Если эти ресурсы не требуются для изучения другого руководства (см. раздел "Дополнительная информация"), их можно удалить, выполнив следующие команды:

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда у вас есть приложение Java EE, развернутое в службе приложений, [ознакомьтесь со сведениями о настройке служб, устранении неполадок и масштабировании приложения](https://aka.ms/wildfly-quickstart).