---
title: Краткое руководство. Создание приложения Java на платформе Windows
description: Быстрое развертывание первого приложения Hello World на Java в службе приложений Azure в Windows. Подключаемый модуль веб-приложения Azure для Maven упрощает развертывание приложений Java.
keywords: azure, служба приложений, веб-приложение, windows, java, maven, краткое руководство
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: b07ffe92a5dd0c105188fab55bc679c04f660ed2
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300966"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Краткое руководство. Создание приложения Java в Службе приложений Azure на платформе Windows

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Windows. Чтобы развернуть Службу приложений в _Linux_, прочитайте статью [Краткое руководство. Создание приложения Java в Службе приложений на платформе Linux](./containers/quickstart-java.md).
>

[Служба приложений Azure](overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости.  В этом кратком руководстве показано, как использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) с [подключаемым модулем веб-приложения Azure для Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), чтобы развернуть файл веб-архива (WAR) Java.

> [!NOTE]
> То же самое можно выполнить с помощью популярных интегрированных сред разработки, например IntelliJ и Eclipse. См. подробнее об [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) и [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Приложение, работающее в Службе приложений Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Создание приложения Java

В приглашении Cloud Shell выполните следующую команду Maven, чтобы создать новое веб-приложение с именем `helloworld`.

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp -Dversion=1.0-SNAPSHOT
```

Затем измените рабочую папку на папку проекта:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Настройка подключаемого модуля Maven

Вы можете выполнить в командной строке следующую команду maven, чтобы настроить развертывание, выбрать вариант **2** для операционной системы **windows** на первом шаге, затем принять конфигурации по умолчанию, нажимая клавишу **ВВОД**, пока не появится строка **Confirm (Y/N)** ("Подтверждение (Д/Н)"), а затем нажать клавишу **Y**, чтобы завершить настройку. 

```bash
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
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>Развертывание приложения

В процессе развертывания для Службы приложений Azure используются данные учетной записи из Azure CLI. Прежде чем продолжить, [войдите с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

```azurecli
az login
```
После этого вы можете развернуть приложение Java в Azure, используя приведенную ниже команду.

```bash
mvn package azure-webapp:deploy
```

По завершению развертывания перейдите к развернутому приложению, используя следующий URL-адрес в своем веб-браузере, например `http://<webapp>.azurewebsites.net/`.

![Приложение, работающее в Службе приложений Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Поздравляем!** Вы развернули свое первое приложение Java в Службе приложений на платформе Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

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
> [Сопоставление пользовательского домена](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Дополнительные сведения о подключаемых модулях Maven для Azure](https://github.com/microsoft/azure-maven-plugins)
