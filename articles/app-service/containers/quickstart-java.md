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
ms.openlocfilehash: 64d70390b3563e17bbe0c70ed426dcda81b50198
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872746"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Краткое руководство. Создание приложения Java в Службе приложений Azure в Linux

[Служба приложений на Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. Узнайте, как использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) с [подключаемым модулем Maven для Службы приложений Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), чтобы развернуть файл Java веб-архива (WAR) в операционной системе Linux.

> [!NOTE]
>
> То же самое можно выполнить с помощью популярных интегрированных сред разработки, например IntelliJ и Eclipse. См. подробнее об [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) и [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Приложение, работающее в Службе приложений Azure](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Создание приложения Java

В приглашении Cloud Shell выполните следующую команду Maven, чтобы создать новое веб-приложение с именем `helloworld`.

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```

## <a name="configure-the-maven-plugin"></a>Настройка подключаемого модуля Maven

В процессе развертывания для Службы приложений Azure используются данные учетной записи из Azure CLI. Прежде чем продолжить, [войдите с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

```azurecli
az login
```

Затем можно настроить развертывание, выполнить в командной строке команду Maven и использовать конфигурации по умолчанию, нажимая клавишу **ВВОД**, пока не появится строка **Confirm (Y/N)** (Подтверждение (Д/Н)), затем нажмите клавишу **Y** и завершите настройку. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
```
Пример процесса выглядит следующим образом.

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.8.0:config (default-cli) @ helloworld ---
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
3. WILDFLY 14
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

Снова перейдите к `pom.xml`, чтобы убедиться, что конфигурация подключаемого модуля обновлена. При необходимости вы можете изменить другие параметры Службы приложений непосредственно в POM-файле. Некоторые из них перечислены ниже:

 Свойство | Обязательно | ОПИСАНИЕ | Version (версия)
---|---|---|---
`<schemaVersion>` | false | Указывает версию схемы конфигурации. Поддерживаемые значения: `v1` и `v2`. | 1.5.2
`<resourceGroup>` | Да | Группа ресурсов Azure для вашего веб-приложения. | Версия 0.1.0 и выше
`<appName>` | Да | Название вашего веб-приложения. | Версия 0.1.0 и выше
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | Да | Указывает регион, в котором будет размещено ваше веб-приложение (значение по умолчанию: **westeurope**). Определить допустимые регионы можно в разделе [Поддерживаемые регионы](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region). | Версия 0.1.0 и выше
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | Ценовая категория веб-приложения. Значение по умолчанию: **P1V2**.| Версия 0.1.0 и выше
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | Да | Конфигурация среды выполнения. Дополнительные сведения см. [здесь](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting). | Версия 0.1.0 и выше
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | Да | Конфигурация развертывания. Дополнительные сведения см. [здесь](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting). | Версия 0.1.0 и выше

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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. по приложению Java Enterprise с PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Настройка приложения Java](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD с использованием Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Другие ресурсы по Azure для разработчиков Java](/java/azure/)

> [!div class="nextstepaction"]
> [Дополнительные сведения о подключаемых модулях Maven для Azure](https://github.com/microsoft/azure-maven-plugins)
