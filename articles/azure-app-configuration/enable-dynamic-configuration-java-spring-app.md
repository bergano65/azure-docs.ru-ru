---
title: Использование динамической конфигурации в приложении Spring Boot
titleSuffix: Azure App Configuration
description: Сведения о динамическом обновлении данных конфигурации для приложений Spring Boot.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 653fcb6f6590fd503a97800ec8196025cf14a3b9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121567"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Руководство по использованию динамической конфигурации в приложении Java Spring

Клиентская библиотека Spring Boot для службы "Конфигурация приложений" поддерживает обновление набора параметров конфигурации по запросу без перезапуска приложения. Во избежание слишком большого количества обращений к хранилищу конфигураций клиентская библиотека кэширует каждый параметр. До истечения срока действия кэшированного значения параметра операция обновления не приводит к обновлению значения, даже если оно изменилось в хранилище конфигураций. Срок действия по умолчанию для каждого запроса составляет 30 секунд. При необходимости это значение можно переопределить.

Чтобы проверить наличие обновленных параметров по требованию, вызовите метод `refreshConfigurations()` класса `AppConfigurationRefresh`.

Кроме того, можно использовать пакет `spring-cloud-azure-appconfiguration-config-web`, который применяет зависимость от `spring-web` для управления автоматическим обновлением.

## <a name="use-automated-refresh"></a>Использовать автоматическое обновление

Чтобы использовать автоматическое обновление, запустите приложение Spring Boot, использующее Конфигурацию приложений, например приложение, созданное с помощью [краткого руководства по созданию приложения Spring Boot для службы "Конфигурация приложений"](quickstart-java-spring-app.md).

Затем откройте файл *pom.xml* в текстовом редакторе и добавьте `<dependency>` для `spring-cloud-azure-appconfiguration-config-web`.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Локальный запуск и проверка приложения

1. Создайте приложение Spring Boot с помощью Maven и запустите его.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. В браузере перейдите по адресу `http://localhost:8080`.  Вы увидите сообщение, связанное с ключом. 

    Вы также можете использовать инструмент *curl* для проверки приложения, например следующим образом: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Чтобы проверить динамическую конфигурацию, откройте портал Конфигурации приложений Azure, связанный с вашим приложением. Выберите **Обозреватель конфигураций**и обновите значение отображаемого ключа, например следующим образом:
    | Клавиши | Значение |
    |---|---|
    | application/config.message | Hello - Updated |

1. Обновите страницу браузера, чтобы просмотреть новое отображаемое сообщение.

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого руководства вы включили в приложении Spring Boot динамическое обновление параметров конфигурации из службы "Конфигурация приложения". Чтобы узнать, как с помощью удостоверения, управляемого Azure, упростить доступ к службе "Конфигурация приложений Azure", перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)
