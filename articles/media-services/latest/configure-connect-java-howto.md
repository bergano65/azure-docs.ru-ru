---
title: Подключение к API aPI для мультимедиа Azure Media Services - Java
description: В этой статье описывается, как подключиться к API мультимедиа Azure Media Services v3 с Java.
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
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888501"
---
# <a name="connect-to-media-services-v3-api---java"></a>Подключение к Медиа-сервисам v3 API - Java

В этой статье показано, как подключиться к сервису Azure Media Services v3 Java SDK с помощью основного знака службы в методе.

В этой статье для разработки примера приложения используется код Visual Studio.

## <a name="prerequisites"></a>Предварительные требования

- Следуйте [Написание Java с визуальным кодом студии](https://code.visualstudio.com/docs/java/java-tutorial) для установки:

   - JDK
   - Apache Maven
   - Пакет расширения Java
- Убедитесь в `JAVA_HOME` `PATH` том, чтобы установить и переменные среды.
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Не забудьте запомнить имя группы ресурсов и имя учетной записи Media Services.
- Выполните действия в теме [APIs доступа.](access-api-cli-how-to.md) Запись идентификатора подписки, идентификатора приложения (идентификаторклиента клиента), ключа проверки подлинности (секретного) и идентификатора клиента, который вам нужен на более позднем этапе.

Также обзор:

- [Java в коде визуальной студии](https://code.visualstudio.com/docs/languages/java)
- [Java Управление проектами в VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Просмотр [именования конвенций](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Создание проекта Maven

Откройте инструмент командной `cd` строки и в каталог, где вы хотите создать проект.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

При запуске команды `pom.xml` `App.java`создаются и другие файлы. 

## <a name="add-dependencies"></a>Добавление зависимостей

1. В Visual Studio Code откройте папку, в которой находится ваш проект
1. Найти и открыть`pom.xml`
1. Добавление необходимых зависимостей

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

## <a name="connect-to-the-java-client"></a>Подключение к клиенту Java

1. Откройте `App.java` файл `src\main\java\com\azure\ams` под и убедитесь, что ваш пакет включен в верхней части:

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
1. Чтобы создать учетные данные Active Directory, необходимые для запросов, добавьте следующий код в основной метод класса App и установите значения, полученные из [ApIs Access:](access-api-cli-how-to.md)
   
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
- [Ссылка на Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` можете включить и начать манипулировать сущностями.

Для получения дополнительных [Java SDK samples](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) примеров кода см.
