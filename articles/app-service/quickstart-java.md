---
title: Краткое руководство. Создание приложения Java в Службе приложений Azure
description: Быстрое развертывание первого приложения Hello World на Java в Службе приложений Azure. Подключаемый модуль веб-приложения Azure для Maven упрощает развертывание приложений Java.
keywords: azure, служба приложений, веб-приложение, windows, linux, java, maven, краткое руководство
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 274228ea5aa9ac9de9725176c8b6221ee9e9542e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182703"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Краткое руководство. Создание приложения Java в Службе приложений Azure

[Служба приложений Azure](overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости.  В этом кратком руководстве показано, как использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) с [подключаемым модулем веб-приложения Azure для Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), чтобы развернуть файл веб-архива (WAR) Java.

> [!NOTE]
> В этой статье мы работаем только с приложениями Java, которые упакованы в WAR-файлы. Подключаемый модуль также поддерживает веб-приложения JAR. См. руководство по [развертыванию файла JAR Java SE в Службе приложений в Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

> [!NOTE]
> То же самое можно выполнить с помощью популярных интегрированных сред разработки, например IntelliJ и Eclipse. См. подробнее об [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) и [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![Приложение, работающее в Службе приложений Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Создание приложения Java

В приглашении Cloud Shell выполните следующую команду Maven, чтобы создать новое веб-приложение с именем `helloworld`.

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Затем измените рабочую папку на папку проекта:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Настройка подключаемого модуля Maven

В процессе развертывания для Службы приложений Azure автоматически используются учетные данные Azure из Azure CLI. Если Azure CLI не установлен локально, подключаемый модуль Maven будет выполнять вход с помощью имени для входа OAuth или имени пользователя устройства. При необходимости ознакомьтесь со сведениями о [выполнении проверки подлинности с помощью подключаемых модулей Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Вы можете выполнить приведенную ниже команду maven для настройки развертывания:
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows" 
Вам будет предложено выбрать: 
* **ОС (по умолчанию: `linux`);**
* **версию Java (по умолчанию: `1.8`);**
* **веб-контейнер (по умолчанию: `tomcat 8.5`).** 
 
Обязательно введите **`2`** , чтобы выбрать ОС **Windows** на первом шаге. Для других настроек можно сохранить значения по умолчанию, нажав клавишу **ВВОД**. Наконец, нажмите **`Y`** при появлении запроса **Confirm (Y/N)** (Подтвердить (Д/Н)), чтобы завершить настройку.

Пример процесса выглядит следующим образом.

```console
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
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  

Вам будет предложено выбрать: 
* **ОС (по умолчанию: `linux`);**
* **версию Java (по умолчанию: `Java 8`);**
* **веб-контейнер (по умолчанию: `Tomcat 8.5`).** 

Для всех остальных настроек можно сохранить значения по умолчанию, нажав клавишу **ВВОД**. Наконец, нажмите **`Y`** при появлении запроса **Confirm (Y/N)** (Подтвердить (Д/Н)), чтобы завершить настройку.
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
::: zone-end

При необходимости вы можете изменить другие параметры Службы приложений непосредственно в `pom.xml`. Некоторые из них перечислены ниже.

 Свойство | Обязательно | Описание | Версия
---|---|---|---
`<schemaVersion>` | false | Указывает версию схемы конфигурации. Поддерживаемые значения: `v1` и `v2`. | 1.5.2
`<resourceGroup>` | Да | Группа ресурсов Azure для вашего веб-приложения. | Версия 0.1.0 и выше
`<appName>` | Да | Название вашего веб-приложения. | Версия 0.1.0 и выше
`<region>` | Да | Указывает регион, в котором будет размещено ваше веб-приложение (значение по умолчанию: **westeurope**). Определить допустимые регионы можно в разделе [Поддерживаемые регионы](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | Версия 0.1.0 и выше
`<pricingTier>` | false | Ценовая категория веб-приложения. Значение по умолчанию: **P1V2**.| Версия 0.1.0 и выше
`<runtime>` | Да | Конфигурация среды выполнения. Дополнительные сведения см. [здесь](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | Версия 0.1.0 и выше
`<deployment>` | Да | Конфигурация развертывания. Дополнительные сведения см. [здесь](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | Версия 0.1.0 и выше

Запишите значения `<appName>` и `<resourceGroup>`(`helloworld-1590394316693` и `helloworld-1590394316693-rg` соответственно в демонстрации), так как они понадобятся позже.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Развертывание приложения

В процессе развертывания для Службы приложений Azure используются данные учетной записи из Azure CLI. Прежде чем продолжить, [войдите с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

```azurecli
az login
```
После этого вы можете развернуть приложение Java в Azure, используя приведенную ниже команду.

```bash
mvn package azure-webapp:deploy
```

После завершения развертывания ваше приложение будет готово к работе по адресу `http://<appName>.azurewebsites.net/`(`http://helloworld-1590394316693.azurewebsites.net` в демонстрации). Откройте URL-адрес в своем локальном веб-браузере. Вы должны увидеть следующее:

![Приложение, работающее в Службе приложений Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Поздравляем!** Вы развернули свое первое приложение Java в службе приложений.

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
> [Ресурсы Azure для разработчиков Java](/java/azure/)

> [!div class="nextstepaction"]
> [Настройка приложения Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD с использованием Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Сопоставление пользовательского домена](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Дополнительные сведения о подключаемых модулях Maven для Azure](https://github.com/microsoft/azure-maven-plugins)
