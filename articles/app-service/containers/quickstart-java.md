---
title: Создание веб-приложения Java в Службе приложений Azure в Linux
description: В этом кратком руководстве описывается, как быстро развернуть ваше первое приложение Java Hello World в службе приложений Azure в Linux.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 12/10/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 0c72318f6c80563d138d9c885ea5984a22c5c7fa
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653906"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Краткое руководство. Создание приложения Java в Службе приложений на платформе Linux

[Служба приложений на Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. В этом кратком руководстве показано, как использовать [интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Azure CLI) с [подключаемым модулем Maven для веб-приложений Azure (предварительная версия)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), чтобы развернуть файл веб-архива (WAR) Java.

![Пример приложения, выполняющегося в Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

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
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
      
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
   
            <!-- App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> В этой статье мы работаем только с приложениями Java, которые упакованы в WAR-файлы. Подключаемый модуль также поддерживает веб-приложения JAR. См. руководство по [развертыванию файла JAR Java SE в Службе приложений в Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).


Укажите нужные значения вместо следующих заполнителей в конфигурации подключаемого модуля:

| Placeholder | ОПИСАНИЕ |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Имя новой группы ресурсов, в которой создается приложение. Поместив все ресурсы для приложения в группу, вы можете управлять ими совместно. Например, при удалении группы ресурсов все ресурсы, связанные с приложением, также удаляются. Укажите вместо этого значения уникальное имя новой группы ресурсов, например *TestResources*. Это имя группы ресурсов будет использоваться для удаления всех ресурсов Azure в следующем разделе. |
| `WEBAPP_NAME` | Имя приложения будет частью имени узла для приложения, которое будет развернуто в Azure (WEBAPP_NAME.azurewebsites.net). Измените значение этого параметра на уникальное имя нового приложения службы приложений, в котором будет размещено ваше приложение Java, например *contoso*. |
| `REGION` | Регион Azure, в котором размещено приложение, например `westus2`. Список регионов можно получить из Cloud Shell или CLI с помощью команды `az account list-locations`. |

## <a name="deploy-the-app"></a>Развертывание приложения

Разверните приложение Java в Azure, используя команду приведенную ниже.

```bash
mvn package azure-webapp:deploy
```

По завершению развертывания перейдите к развернутому приложению, используя следующий URL-адрес в своем веб-браузере, например `http://<webapp>.azurewebsites.net/helloworld`. 

![Пример приложения, выполняющегося в Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Поздравляем!** Вы развернули свое первое приложение Java в службе приложений на платформе Linux.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве описано, как использовать Maven для создания приложения Java, настроить [подключаемый модуль Maven для веб-приложений Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) и развернуть веб-архив упакованного приложения Java в службе приложений в Linux. Дополнительные сведения о размещении приложений Java в Службе приложений на платформе Linux см. в следующих руководствах и статьях с инструкциями:

- [Руководство по развертыванию приложения Java Enterprise с использованием PostgreSQL](tutorial-java-enterprise-postgresql-app.md)
- [Настройка источника данных Tomcat](app-service-linux-java.md#connecting-to-data-sources)
- [CI/CD с использованием Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)
- [Настройка средств мониторинга производительности приложения](how-to-java-apm-monitoring.md)

