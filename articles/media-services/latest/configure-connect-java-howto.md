---
title: Подключение к API v3 служб мультимедиа Azure — Java
description: Узнайте, как подключиться к API служб мультимедиа версии 3 с помощью Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: juliako
ms.openlocfilehash: 68e09ec6ce4aeb91e00c2a15caa8ec81f40064c1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997195"
---
# <a name="connect-to-media-services-v3-api---java"></a>Подключение к API v3 служб мультимедиа — Java

В этой статье показано, как соединиться с Java версии 3 пакета SDK служб мультимедиа Azure с помощью субъекта-службы входа, в методе.

В этой статье Visual Studio Code используется для разработки примера приложения.

## <a name="prerequisites"></a>Технические условия

- Выполните [записи Java с помощью Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) для установки:

   - JDK
   - Apache Maven
   - Пакет расширений Java
- Не забудьте задать `JAVA_HOME` и `PATH` переменные среды.
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи служб мультимедиа.
- Выполните действия, описанные в [доступ к API](access-api-cli-how-to.md) раздела. Запишите идентификатор подписки, идентификатор приложения (идентификатор клиента), ключ проверки подлинности (секрет) и идентификатор клиента, вам потребуется в дальнейшем.

Также для просмотра:

- [Java в Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Управление проектами Java в VS Code](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Создание проекта Maven

Откройте средство командной строки и `cd` в каталог, где вы хотите создать проект.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

При выполнении команды, `pom.xml`, `App.java`, и другие файлы создаются. 

## <a name="add-dependencies"></a>Добавление зависимостей

1. В Visual Studio Code откройте папку, где находится проект
1. Поиск и открытие `pom.xml`
1. Добавьте нужные зависимости

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
```

## <a name="connect-to-the-java-client"></a>Подключиться к клиенту Java

1. Откройте `App.java` файл `src\main\java\com\azure\ams` и убедитесь, что ваш пакет включен в верхней:

    ```java
    package com.azure.ams;
    ```
1. Под оператором statement добавьте следующие операторы import:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Чтобы создать учетные данные Active Directory, необходимые для выполнения запросов, добавьте следующий код в метод main класса App и задайте значения, которые вы получили [доступ к API](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Запустите приложение.

## <a name="see-also"></a>См. также

- [Основные понятия служб мультимедиа Azure](concepts-overview.md)
- [пакет SDK для Java](https://aka.ms/ams-v3-java-sdk)
- [Справочник по Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Дальнейшие действия

Теперь можно включать `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` и начать управлять тестируемым сущностей.
