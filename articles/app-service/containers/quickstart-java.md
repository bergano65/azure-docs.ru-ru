---
title: Краткое руководство. Создание приложения Java в Linux
description: Начало работы с приложениями Linux в Службе приложений Azure, развернув первое приложение Java в контейнере Linux в Службе приложений.
keywords: azure, app service, web app, linux, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 1bff1e22df1583491bf8d249cdb8752e9a1e4f57
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485386"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Краткое руководство. Создание приложения Java в Службе приложений Azure в Linux

[Служба приложений на Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. Узнайте, как использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) с [подключаемым модулем веб-приложения Azure для Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), чтобы развернуть файл веб-архива (WAR) Java в операционной системе Linux.

> [!NOTE]
>
> То же самое можно выполнить с помощью популярных интегрированных сред разработки, например IntelliJ, Eclipse и VS Code. См. руководства по [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app), [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) и [VS Code](https://code.visualstudio.com/docs/java/java-webapp).
>
![Приложение, работающее в Службе приложений Azure](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Создание приложения Java

В приглашении Cloud Shell выполните следующую команду Maven, чтобы создать новое веб-приложение с именем `helloworld`.

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" -Dversion=1.0-SNAPSHOT
```
Затем измените рабочую папку на папку проекта:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Настройка подключаемого модуля Maven

В процессе развертывания для Службы приложений Azure автоматически используются учетные данные Azure из Azure CLI. Если Azure CLI не установлен, подключаемый модуль Maven будет выполнять вход с помощью имени для входа OAuth или имени пользователя устройства. При необходимости ознакомьтесь со сведениями о [выполнении проверки подлинности с помощью подключаемых модулей Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Чтобы настроить развертывание, выполните в командной строке команду Maven и используйте конфигурации по умолчанию, нажимая клавишу **ВВОД**, пока не появится строка **Confirm (Y/N)** (Подтверждение (Д/Н)), затем нажмите клавишу **Y** и завершите настройку. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
Пример процесса выглядит следующим образом.

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> В этой статье мы работаем только с приложениями Java, которые упакованы в WAR-файлы. Подключаемый модуль также поддерживает веб-приложения JAR. См. руководство по [развертыванию файла JAR Java SE в Службе приложений в Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

Откройте файл `pom.xml`, чтобы посмотреть обновленную конфигурацию.

```bash
code pom.xml
```

При необходимости вы можете изменить другие параметры Службы приложений непосредственно в POM-файле. Некоторые из них перечислены ниже.

 Свойство | Обязательно | Описание | Версия
---|---|---|---
`<schemaVersion>` | false | Указывает версию схемы конфигурации. Поддерживаемые значения: `v1` и `v2`. | 1.5.2
`<resourceGroup>` | Да | Группа ресурсов Azure для вашего веб-приложения. | Версия 0.1.0 и выше
`<appName>` | Да | Название вашего веб-приложения. | Версия 0.1.0 и выше
`<region>` | Да | Указывает регион, в котором будет размещено ваше веб-приложение (значение по умолчанию: **westeurope**). Определить допустимые регионы можно в разделе [Поддерживаемые регионы](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | Версия 0.1.0 и выше
`<pricingTier>` | false | Ценовая категория веб-приложения. Значение по умолчанию: **P1V2**.| Версия 0.1.0 и выше
`<runtime>` | Да | Конфигурация среды выполнения. Дополнительные сведения см. [здесь](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | Версия 0.1.0 и выше
`<deployment>` | Да | Конфигурация развертывания. Дополнительные сведения см. [здесь](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | Версия 0.1.0 и выше

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Развертывание приложения

Разверните приложение Java в Azure, используя команду приведенную ниже.

```bash
mvn package azure-webapp:deploy
```

По завершению развертывания перейдите к развернутому приложению, используя следующий URL-адрес в своем веб-браузере, например `http://<webapp>.azurewebsites.net`. 

![Приложение, работающее в Службе приложений Azure](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Поздравляем!** Вы развернули свое первое приложение Java в службе приложений на платформе Linux.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Очистка ресурсов

На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Если эти ресурсы вам не понадобятся в будущем, вы можете удалить группу ресурсов из портала, или выполнив приведенную ниже команду в Cloud Shell.

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Ее выполнение может занять до минуты.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Подключение к Базе данных SQL Azure на Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Подключение к Базе данных Azure для MySQL на Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Подключение к Базе данных Azure для PostgreSQL на Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Настройка приложения Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD с использованием Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Другие ресурсы по Azure для разработчиков Java](/java/azure/)

> [!div class="nextstepaction"]
> [Дополнительные сведения о подключаемых модулях Maven для Azure](https://github.com/microsoft/azure-maven-plugins)
