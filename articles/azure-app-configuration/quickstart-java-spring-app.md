---
title: Руководство по использованию службы конфигурации приложений Azure | Документация Майкрософт
description: Руководство по использованию службы конфигурации приложений Azure с приложениями Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: e27635d153e58f96dad7db6870ed1dc3f640236a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326484"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Краткое руководство. Создание приложения Java Spring с помощью службы "Конфигурация приложений Azure"

В этом кратком руководстве описано, как добавить службу "Конфигурация приложений Azure" в приложение Java Spring, чтобы обеспечить централизованное хранение параметров приложения и управление ими отдельно от кода.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- Поддерживаемый [комплект разработчика Java (JDK)](https://docs.microsoft.com/java/azure/jdk) версии 8.
- [Apache Maven](https://maven.apache.org/download.cgi) версии 3.0 или более поздней.

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Выберите **Configuration Explorer** (Обозреватель конфигураций)  >  **+ Создать**, чтобы добавить следующие пары "ключ-значение".

    | Ключ | Значение |
    |---|---|
    | /application/config.message | Привет |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

## <a name="create-a-spring-boot-app"></a>Создание приложения Spring Boot

Для создания проекта Spring Boot воспользуйтесь [Spring Initializr](https://start.spring.io/).

1. Перейдите по адресу <https://start.spring.io/>.

2. Задайте такие параметры:

   * Выберите в соответствующих полях **Maven Project** (Проект Maven) и **Java**.
   * Выберите **Spring Boot** не ниже версии 2.0.
   * Заполните поля **Group** (Группа) и **Artifact** (Артефакт) для приложения.
   * Добавьте зависимость **Web** (Веб).

3. После указания предыдущих параметров выберите **Создать проект**. При появлении запроса скачайте проект на локальный компьютер.

## <a name="connect-to-an-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. После извлечения файлов в локальной системе простое приложение Spring Boot можно редактировать. Найдите файл *pom.xml* в корневой папке приложения.

2. Откройте файл *pom.xml* в текстовом редакторе и добавьте начальное приложение Spring Cloud Azure Config в список `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M3</version>
    </dependency>
    ```

3. Создайте файл Java с именем *MessageProperties.java* в каталоге пакета приложения. Добавьте следующие строки.

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. Создайте файл Java с именем *HelloController.java* в каталоге пакета приложения. Добавьте следующие строки.

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. Откройте основной файл приложения Java и добавьте `@EnableConfigurationProperties`, чтобы включить эту функцию.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. В каталоге ресурсов создайте файл с именем `bootstrap.properties` и добавьте в него приведенные ниже строки. Замените примеры значений на соответствующие свойства хранилища конфигураций приложений.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. После запуска приложение можно протестировать с помощью средства *curl*, например:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Вы увидите сообщение, которое указали в хранилище конфигураций приложений.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве описано, как создать хранилище конфигураций приложений и использовать его с приложением Java Spring. Дополнительные сведения см. в статье [Spring в Azure](https://docs.microsoft.com/java/azure/spring-framework/).

Ознакомьтесь с дополнительными сведениями об использовании службы "Конфигурация приложений" в следующем учебнике, посвященном проверке подлинности.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)
