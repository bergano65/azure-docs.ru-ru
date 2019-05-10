---
title: Справочник разработчика Java по Функциям Azure | Документация Майкрософт
description: Информация о разработке функций на языке Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, webhooks, dynamic compute, serverless architecture, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: ce7eb546c342ffd20557a95d5293d83b39ec3afb
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507198"
---
# <a name="azure-functions-java-developer-guide"></a>Руководство разработчика Java по Функциям Azure

Среда выполнения функций Azure поддерживает [Java SE 8 LTS (zulu8.31.0.2 jre8.0.181 win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/).

Это руководство содержит сведения о сложностях написание функций Azure на языке Java.

Функция Java является `public` метод оформлен заметки `@FunctionName`. Этот метод определяет запись для функции java и должно быть уникальным в заданного пакета. 

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Также следует выполнить быстрый запуск функций для создания первой функции с помощью [Visual Studio Code](functions-create-first-function-vs-code.md) или [с помощью maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Модель программирования 

Понятия [триггеров и привязок](functions-triggers-bindings.md) играют решающую роль в Функциях Azure. Триггеры запускают выполнение вашего кода. Привязки предоставляют возможность передавать данные, а также возвращать их из функции без необходимости написания кода доступа к ним.

## <a name="folder-structure"></a>Структура папок

Ниже приведена структура папок проекта на Java в Функциях Azure:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Существует общий файл [host.json](functions-host-json.md), который можно использовать для настройки приложения-функции. У каждой функции есть собственный файл кода (.java) и файл конфигурации привязки (function.json).

Вы можете добавить несколько функций в проект. Не добавляйте функции в отдельные JAR-файлы. Приложение-функция в целевом каталоге является тем, что будет развернуто в вашем приложении-функции в Azure.

## <a name="triggers-and-annotations"></a>Триггеры и заметки

 Функции Azure вызываются триггером, таким как HTTP-запрос, таймер или обновление данных. Функция должна обработать такой триггер и связанные с ним входные данные и создать на их основе одно или несколько выходных значений.

Используйте заметки Java, включенные в пакет [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation), чтобы привязать входные и выходные данные к своим методам. Дополнительные сведения см. в разделе [Java справочные документы](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Чтобы локально выполнять триггеры BLOB-объекта, очереди или таблицы хранилища Azure, необходимо настроить учетную запись хранения Azure в файле [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file).

Пример:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Ниже приведен соответствующий файл `function.json`, созданный с помощью [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Обеспечения доступности и предоставления поддержки времени выполнения пакета JDK 

Для локальной разработки приложений-функций Java загрузите и используйте JDK для Java 8 [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) из [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Функции Azure используют среду выполнения Azul Java 8 JDK, если вы развертываете приложения-функции в облако.

[Поддержка Azure](https://azure.microsoft.com/support/) для устранения проблем с пакетами JDK и приложениями-функциями доступна с [соответствующим планом поддержки](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Настройка виртуальной машины Java

Функции предоставляют возможность настройки виртуальной машины Java (JVM) используется для выполнения функций Java. [Следующие параметры виртуальной машины Java](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) используется по умолчанию:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Можно указать дополнительные аргументы в параметре приложения `JAVA_OPTS`. Можно добавить параметры приложения для приложения-функции развертывания в Azure в одном из следующих способов:

### <a name="azure-portal"></a>Портал Azure

В [портала Azure](https://portal.azure.com), использовать [вкладка "Параметры приложения"](functions-how-to-use-azure-function-app-settings.md#settings) добавление `JAVA_OPTS` параметр.

### <a name="azure-cli"></a>Инфраструктура CLI Azure

[Az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) команда может использоваться для задания `JAVA_OPTS`, как показано в следующем примере:

    ```azurecli-interactive
    az functionapp config appsettings set --name <APP_NAME> \
    --resource-group <RESOURCE_GROUP> \
    --settings "JAVA_OPTS=-Djava.awt.headless=true"
    ```
В этом примере включается режим автоматического входа. Замените `<APP_NAME>` с именем приложения-функции и `<RESOURCE_GROUP> ` с группой ресурсов.

> [!WARNING]  
> При работе в [план потребления](functions-scale.md#consumption-plan), необходимо добавить `WEBSITE_USE_PLACEHOLDER` параметр со значением `0`.  
Этот параметр увеличивает время холодного запуска для функций Java.

## <a name="third-party-libraries"></a>Сторонние библиотеки 

Служба "Функции Azure" поддерживает использование сторонних библиотек. По умолчанию все зависимости, указанные в файле `pom.xml` проекта, будут автоматически связаны во время выполнения [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage). Зависимости библиотек, не указанных как зависимости в файле `pom.xml`, поместите в каталог `lib` в корневой папке функции. Зависимости, размещенные в каталоге `lib`, будут добавлены в загрузчик системного класса во время выполнения.

Зависимость `com.microsoft.azure.functions:azure-functions-java-library` предоставляется по пути класса по умолчанию и не требует включения в `lib` каталог. Кроме того, зависимости, перечисленные [здесь](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies), добавляются в путь к классу с помощью [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker).

## <a name="data-type-support"></a>Поддержка типов данных

Для входных и выходных привязок можно использовать объекты POJO, типы, определенные в `azure-functions-java-library` или примитивные типы данных, такие как String, Integer.

### <a name="plain-old-java-objects-pojos"></a>Объекты POJO

Для преобразования входных данных в POJO [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) использует библиотеку [gson](https://github.com/google/gson). Типам POJO, используемые как входные данные для функций, необходим такой же модификатор доступа `public`.

### <a name="binary-data"></a>Двоичные данные

Чтобы привязать двоичные входные и выходные данные к `byte[]`, в файле function.json задайте для поля `dataType` значение `binary`.

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Если необходимы значения null, используйте `Optional<T>`.

## <a name="bindings"></a>Привязки

Входные и выходные привязки реализуют декларативный способ подключения к данным из кода. У функции может быть несколько входных и выходных привязок.

### <a name="example-input-binding"></a>Пример входной привязки

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Эта функция вызывается с помощью HTTP-запроса. 
- Полезные данные HTTP-запроса передаются как `String` для аргумента `inputReq`.
- Одна запись извлекается из Хранилища таблиц Azure и передается как `TestInputData` в аргумент `inputData`.

Для получения пакета из входных данных, можно привязать к `String[]`, `POJO[]`, `List<String>`, или `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Эта функция запускается всякий раз, когда в настроенном концентраторе событий появляются новые данные. Так как для `cardinality` установлено значение `MANY`, функция получает пакет сообщений из концентратора событий. EventData из концентратора событий преобразуется в `TestEventData` для выполнения функции.

### <a name="example-output-binding"></a>Пример выходной привязки

Вы можете привязать выходную привязку к возвращаемому значению с помощью `$return`. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

При наличии нескольких выходных привязок используйте возвращаемое значение только для одной из них.

Чтобы отправить несколько выходных значений, используйте тип `OutputBinding<T>`, который определен в пакете `azure-functions-java-library`. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Эта функция вызывается на объекты HttpRequest и записывает несколько значений в очередь Azure.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage и HttpResponseMessage

 Типы HttpRequestMessage и HttpResponseMessage определены в `azure-functions-java-library` как вспомогательные типы для работы с функциями HttpTrigger.

| Специализированные типы      |       Цель        | Типичное применение                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Триггер HTTP     | Получение метода, заголовков или запросов |
| `HttpResponseMessage` | Привязка к выходным данным HTTP | Возврат кодов состояния, отличных от 200   |

## <a name="metadata"></a>Метаданные

Несколько триггеров отправляют [метаданные триггеров](/azure/azure-functions/functions-triggers-bindings) вместе с входными данными. Вы можете использовать заметки `@BindingName` для привязки к метаданным триггера.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
В приведенном выше примере значение `queryValue` привязано к параметру строки запроса `name` в URL-адресе HTTP-запроса `http://{example.host}/api/metadata?name=test`. Ниже приведен еще один пример привязки к `Id` из метаданных триггера очереди.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Имя, предоставленное в заметке, должно соответствовать свойству метаданных.

## <a name="execution-context"></a>Контекст выполнения

Контекст `ExecutionContext`, определенный в `azure-functions-java-library`, содержит вспомогательные методы для взаимодействия со средой выполнения функций.

### <a name="logger"></a>Средство ведения журнала

Используйте средство `getLogger`, определенное в `ExecutionContext`, для записи журналов из кода функции.

Пример:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Просмотр журналов и трассировки

Вы можете использовать интерфейс Azure CLI для потоковой передачи журналов со стандартными данными и журналов ошибок в формате Java, а также ведения других журналов приложений. 

Настройте приложение-функцию, чтобы записывать журнал приложений с помощью Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Чтобы передать выходные данные журналов в приложение-функцию с помощью Azure CLI, откройте новое окно командной строки, Bash или сеанс терминала и введите следующую команду:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Команда [az webapp log tail](/cli/azure/webapp/log) позволяет отфильтровать выходные данные с помощью параметра `--provider`. 

Чтобы скачать файлы журналов как отдельный ZIP-файл с использованием интерфейса командной строки Azure, откройте командную строку, Bash или сеанс терминала и введите следующую команду:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Необходимо включить ведение журнала на портале Azure или Azure CLI, перед выполнением этой команды файловой системы.

## <a name="environment-variables"></a>Переменные среды

В Функциях [параметры приложения](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Получить доступ к этим параметрам можно с помощью `System.getenv("AzureWebJobsStorage")`

Пример:

Добавьте [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) с именем testAppSetting и значением testAppSettingValue:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке функций Azure на Java см. в следующих статьях.

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* Разработка и отладка в локальной среде с помощью [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) и [Eclipse](functions-create-maven-eclipse.md). 
* [Remote Debug Java Azure Functions with Visual Studio Code (Удаленная отладка функций Azure на языке Java с помощью Visual Studio Code)](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Подключаемый модуль Maven для функций Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md). Упрощает создание функций благодаря использованию цели `azure-functions:add` и позволяет подготовить промежуточный каталог для [развертывания ZIP-файла](deployment-zip-push.md).
