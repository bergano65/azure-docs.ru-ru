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
ms.openlocfilehash: d607d6cd813b23051e1676153cbb134261bcf5bc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960632"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Краткое руководство. Создание приложения Java Spring с помощью службы конфигурации приложений Azure

Конфигурация приложений Azure — это служба для управления конфигурациями в Azure. Она позволяет централизовано хранить и администрировать все конфигурации вашего приложения отдельно от кода. В этом руководстве показано, как интегрировать службу с приложением Java Spring.

Вы можете использовать любой редактор кода для выполнения шагов в этом кратком руководстве. Однако [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, доступным на платформах Windows, MacOS и Linux.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством установите поддерживаемую версию [комплекта разработчика Java (JDK)](https://aka.ms/azure-jdks) версии 8 и [Apache Maven](http://maven.apache.org/) версии 3.0 или выше.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Создание хранилища конфигураций приложений

1. Чтобы создать хранилище конфигураций приложений, сначала войдите на [портал Azure](https://aka.ms/azconfig/portal). В верхней левой части экрана щелкните **Создать ресурс**. В текстовом поле **Поиск в Marketplace** введите **Конфигурация приложений** и нажмите клавишу **ВВОД**.

    ![Поиск службы конфигурации приложений](./media/quickstarts/azure-app-configuration-new.png)

2. В результатах поиска щелкните **Конфигурация приложений** и выберите **Создать**.

3. На странице **Конфигурация приложений** > **Создать** введите следующие параметры:

    | Параметр | Рекомендуемое значение | ОПИСАНИЕ |
    |---|---|---|
    | **Имя ресурса** | Глобально уникальное имя | Введите уникальное имя для ресурса хранилища конфигураций приложений. Имя должно быть строкой длиной от 1 до 63 символов и содержать только цифры, буквы и символ `-`. Имя не может начинаться или заканчиваться символом `-`. Также не допускается последовательное использование символов `-`.  |
    | **Подписка** | Ваша подписка | Выберите подписку Azure, которую нужно использовать для тестирования конфигурации приложений. Если в вашей учетной записи есть только одна подписка, она выбирается автоматически и раскрывающееся меню **Подписка** не отображается. |
    | **Группа ресурсов** | *AppConfigTestResources* | Выберите или создайте группу ресурсов для ресурса хранилища конфигураций приложений. Эта группа может полезна для упорядочения нескольких ресурсов, которые может потребоваться удалить одновременно путем удаления группы ресурсов. Дополнительные сведения см. в разделе [Использование групп ресурсов для управления ресурсами Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Местоположение.** | *Центральная часть США* | В поле **Расположение** укажите, где будет географически располагаться ресурс SignalR. Для наилучшей производительности рекомендуется создать ресурс в одном регионе с другими компонентами приложения. |

    ![Создание хранилища конфигураций приложений](./media/quickstarts/azure-app-configuration-create.png)

4. Нажмите кнопку **Создать**. Завершение развертывания может занять несколько минут.

5. По завершении развертывания щелкните **Параметры** > **Ключи доступа**. Запишите строку подключения для первичного ключа с атрибутом только для чтения или чтения и записи. Она пригодится позже для настройки обмена данными между приложением и только что созданным хранилищем конфигураций приложений. Строка подключения имеет следующий формат:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Используйте всю строку в приложении.

6. Щелкните **Обозреватель ключей и значений** и **+ Создать**, чтобы добавить следующие пары "ключ-значение":

    | Ключ | Значение |
    |---|---|
    | /application/config.message | Привет |

    Поля **Метка** и **Тип контента** пока заполнять не нужно.

## <a name="create-a-spring-boot-app"></a>Создание приложения Spring Boot

Для создания проекта Spring Boot воспользуйтесь [Spring Initializr](https://start.spring.io/).

1. Перейдите по адресу <https://start.spring.io/>.

2. Задайте такие параметры:

   * Выберите в соответствующих полях **Maven Project** (Проект Maven) и **Java**.
   * Выберите версию **Spring Boot** не ниже версии 2.0.
   * Заполните поля **Group** (Группа) и **Artifact** (Артефакт) для приложения.
   * Добавьте зависимость **Web** (Веб).

3. Указав эти параметры, щелкните **Generate Project** (Создать проект). При появлении запроса скачайте проект на локальный компьютер.

## <a name="connect-to-app-configuration-store"></a>Подключение к хранилищу конфигураций приложений

1. После извлечения файлов в локальной системе простое приложение Spring Boot можно будет редактировать. Найдите файл *pom.xml* в корневой папке приложения.

2. Откройте файл *pom.xml* в текстовом редакторе и добавьте начальное приложение Spring Cloud Azure Config в список `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.RC2</version>
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

6. Создайте файл с именем `bootstrap.yaml` в каталоге ресурсов приложения и добавьте в файл следующие строки. Вместо примеров значений укажите соответствующие свойства для хранилища конфигурации приложений.

    ```yaml
    spring:
        cloud:
            azure:
                config:
                    stores:
                        - connection-string: [your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Создайте приложение Spring Boot с помощью Maven и запустите его, например, следующим образом:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. После запуска приложения можно использовать средство *curl*, чтобы протестировать приложение, например:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Вы увидите сообщение, которое указали в хранилище конфигурации приложений.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве описано, как создать хранилище конфигураций приложений и использовать его с приложением Java Spring. См. дополнительные сведения на [начальной странице Spring в Azure](https://docs.microsoft.com/java/azure/spring-framework/).

См. дополнительные сведения об использовании службы конфигурации приложений в следующем руководстве, посвященном проверке подлинности.

> [!div class="nextstepaction"]
> [Управляемые удостоверения для интеграции с ресурсами Azure](./integrate-azure-managed-service-identity.md)