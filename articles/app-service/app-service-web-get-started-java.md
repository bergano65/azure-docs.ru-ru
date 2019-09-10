---
title: Создание веб-приложения Java в Службе приложений Azure в Windows
description: В этом кратком руководстве описывается, как быстро развернуть свое первое приложение Java Hello World в Службе приложений Azure в Windows.
keywords: azure, служба приложений, веб-приложение, windows, java, maven, краткое руководство
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 483adcccff9f49b3335a453f43fc667dd4f0e61b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171316"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Краткое руководство. Создание приложения Java в Службе приложений Azure на платформе Windows

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Windows. Чтобы развернуть Службу приложений в _Linux_, прочитайте статью [Краткое руководство. Создание приложения Java в Службе приложений на платформе Linux](./containers/quickstart-java.md).
>

[Служба приложений Azure](overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости.  Узнайте, как использовать [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) с [подключаемым модулем Maven для Службы приложений Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), чтобы развернуть файл Java веб-архива (WAR).

> [!NOTE]
> То же самое можно выполнить с помощью популярных интегрированных сред разработки, например IntelliJ и Eclipse. См. подробнее об [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) и [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Пример приложения, выполняющегося в Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Создание приложения Java

В приглашении Cloud Shell выполните следующую команду Maven, чтобы создать новое веб-приложение с именем `helloworld`.

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Настройка подключаемого модуля Maven

Для развертывания из Maven используйте редактор кода в Cloud Shell, чтобы открыть проект файла `pom.xml` в каталоге `helloworld`. 

```bash
code pom.xml
```

Затем добавьте следующее определение подключаемого модуля в элемент `<build>` файла `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> В этой статье мы работаем только с приложениями Java, которые упакованы в WAR-файлы. Подключаемый модуль также поддерживает веб-приложения JAR. См. руководство по [развертыванию файла JAR Java SE в Службе приложений в Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).


Укажите нужные значения вместо следующих заполнителей в конфигурации подключаемого модуля:

| Placeholder | ОПИСАНИЕ |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Уникальный идентификатор подписки, в которой вы хотите развернуть приложение. Идентификатор подписки по умолчанию можно получить в Cloud Shell или CLI с помощью команды `az account show`. Для всех доступных подписок используйте команду `az account list`.|
| `RESOURCEGROUP_NAME` | Имя новой группы ресурсов, в которой создается приложение. Поместив все ресурсы для приложения в группу, вы можете управлять ими совместно. Например, при удалении группы ресурсов все ресурсы, связанные с приложением, также удаляются. Укажите вместо этого значения уникальное имя новой группы ресурсов, например *myResourceGroup*. Это имя группы ресурсов будет использоваться для удаления всех ресурсов Azure в следующем разделе. |
| `WEBAPP_NAME` | Имя приложения будет частью имени узла для приложения, которое будет развернуто в Azure (WEBAPP_NAME.azurewebsites.net). Измените значение этого параметра на уникальное имя нового приложения службы приложений, в котором будет размещено ваше приложение Java, например *contoso*. |
| `REGION` | Регион Azure, в котором размещено приложение, например *westus2*. Список регионов можно получить из Cloud Shell или CLI с помощью команды `az account list-locations`. |

## <a name="deploy-the-app"></a>Развертывание приложения

Разверните приложение Java в Azure, используя команду приведенную ниже.

```bash
mvn package azure-webapp:deploy
```

По завершению развертывания перейдите к развернутому приложению, используя следующий URL-адрес в своем веб-браузере, например `http://<webapp>.azurewebsites.net/`.

![Пример приложения, выполняющегося в Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**Поздравляем!** Вы развернули свое первое приложение Java в Службе приложений на платформе Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Ресурсы Azure для разработчиков Java](/java/azure/)

> [!div class="nextstepaction"]
> [Сопоставление пользовательского домена](app-service-web-tutorial-custom-domain.md)
